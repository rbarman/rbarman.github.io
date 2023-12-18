Title: Host PWA with AWS Amplify
Date: 2023-10-10
Tags: mkcert, progressive web app, AWS Amplify

Previous post talked about using mkcert to run localhost as https. Unfortunately this does not work if I want to acess the PWA on my phone. I get a message back stating that the connection is not secure and thus I can not download the PWA.

The simple explanation is that my phone does not trust the self-signed certificate created by mkcert. My phone has its own list of trusted Certified Authorities (CA) but it does not know about the one I created on my computer.

One cited work around is to export the root certificate (rootCA.pem) to my phone...

- run mkcert -CAROOT on computer to find location of root certificate
- securely transfer the file to phone
- On phone, go to "install network certificate" and select the transfered file (https://support.google.com/pixelphone/answer/2844832?hl=en)
- restart phone
- access PWA link

Unfortunately this does not work. There may be additional phone settings I must modify.

Instead of tinkering with mkcert and phone settings, I decided to pivot to AWS Amplify. I just have to zip all the PWA files (do not zip the folder containing the files), upload it to AWS Amplify, and it generates a url to the PWA. Then I can go to the url on my phone and download.

My real intention was to validate PWA caching on mobile. I needed to go through these steps because I can only download a PWA on mobile if the PWA is served on HTTPs. Initial testing looks promising. I will create a separate post dedicated to caching in the future.

