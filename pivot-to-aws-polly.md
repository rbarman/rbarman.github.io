Title: Pivot to AWS Polly
Date: 2023-09-18
Tags: Azure Speech Services, AWS Polly

My previous post described my difficulties with azure speech services. Fortunately I was able to pivot to aws very quickly.

The only tricky part of Polly is that it has a limit on how much characters can be converted at once. So I need to split up my entire text into chuncks.

The process looks like:

```python
    polly_client = boto3.client('polly')
    text_chunks = split_text(input_text)
    audio_segments = []

    for text_chunk in text_chunks:
        # process text
        response = polly_client.synthesize_speech(
            Text=chunk,
            OutputFormat='mp3',
            VoiceId='Joanna'
        )

        # store the audio
        audio_data = response['AudioStream'].read()
        audio_segments.append(audio_data)

    # combine all audio segments as bytes and return an encoded base64 string
    concatenated_audio = b''.join(audio_segments)
    audio_encoded = base64.b64encode(concatenated_audio).decode('utf-8')
```
I running this in an AWS Lambda function, so the function also needs an IAM role that can call polly.
