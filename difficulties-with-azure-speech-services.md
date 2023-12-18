Title: Difficulties with Azure Speech Services
Date: 2023-09-15
Tags: Azure Speech Services

I am building an application that requires text to speech. I chose Azure Speech Services because of its generous free tier, but after some experimentation it looks like there are issues that will make me reconsider.

At a high level I want to use Azure Speech Services to convert a text into an audio encoded string, and then send the string to downstream applications. I can call the API via the python SDK or via http requests.

**Python SDK approach**

```python
import azure.cognitiveservices.speech as speechsdk
import base64

subscription_key = ""
service_region = ""

speech_config = speechsdk.SpeechConfig(subscription=subscription_key, region=service_region)
synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)

text = "Hello, this is a test."

# Perform text-to-speech synthesis and get the audio data
result = synthesizer.speak_text_async(text_to_speak).get()
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    audio_data = result.audio_data
    encoded_audio = base64.b64encode(audio_data).decode('utf-8')
    print("Encoded audio: " + encoded_audio)
else:
    print("Speech synthesis failed: {}".format(result.reason))
```

**Rest API approach with Requests library**

```python
import requests
import base64

def generate_auth_token():

    headers = {
        'Ocp-Apim-Subscription-Key': AUTH_SUBSCRIPTION_KEY, 
        'Content-Type': 'application/x-www-form-urlencoded'
    }
    response = requests.request("POST", AUTH_URL, headers=headers)
    return response.text

def generate_tts(text:str):
    headers = {
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'Content-Type': 'application/ssml+xml',
        'Authorization': f'Bearer {generate_auth_token()}'
    }

    payload = f"""
    <speak version='1.0' xml:lang='en-US'>
    <voice xml:lang='en-US' xml:gender='Female' name='en-US-JennyNeural'>
        {text}
    </voice>
    </speak>
    """
    
    # call
    response = requests.post(url, headers=headers, data=payload)
    print(f'response status code: {response.status_code}')
    if response.status_code == 200:
        audio_data = response.content
        base64_audio = base64.b64encode(audio_data).decode('utf-8')
        return base64_audio
    else:
        print("received an error response status code")
        return None
```

The major descripancy I found is that there can be strings that cause the Requests approach to fail but the SDK approach is successful. I went through a rabbit hole of cleaning the input text to get rid of special characters, confirming the encoding type, and building the xml payload with a python xml library but the request approach still fails. Unfortunately the speech service api endpoint only returns a 400 status code but with no helpful messages. There must be an issue with how I am calling the API with the requests library because I do not get an issue if I make the same API call with Postman.

In general this is not a problem because one would just go with the SDK approach. The SDK already works and a lot of the implementation details are abstracted away. *However*, I want to run the tts in an AWS Lambda function. If I want to use the python SDK, then I need to create a custom Lambda layer to accomodate the SDK; this could be doable but will take some time. I think the best course of action right now is to use AWS Polly for tts. 