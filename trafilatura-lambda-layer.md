Title: Trafilatura AWS Lambda Layer
Date: 2023-08-30
Tags: AWS Lambda, Trafilatura

I want to create an AWS Lambda function that can extract the text from a web article. One way to do this is to create a Lambda Layer with the required package dependencies and then have a Lambda function connect to the Lambda Layer and execute the extraction code. 

I found three promising python libraries that can extract text:  [trafilatura](https://github.com/adbar/trafilatura), [goose3](https://github.com/goose3/goose3), and [newspaper3k](https://github.com/codelucas/newspaper). These libraries functionally work well locally, *however* I had difficulties in properly creating a lambda layer. In the end I was only successful with trafilatura. Below is a quick summary of how I built the lambda layer.

---

1) Create an EC2 instance

In general, we should create the layer on the same operating system as the Lambda runtime. One easy way to do this is to create a small EC2 instance and then build all dependencies on the EC2 instance. Make sure that the EC2 instance has permissions to call the PublishLayerVersion API operation.

2) Install library

```bash
pip install \
  --platform manylinux2014_x86_64 \
  --target python \
  --python-version 3.10 \
  --only-binary=:all: --upgrade \
   trafilatura
```

 3) Put everything in `layer.zip`.  The zip file will be used to create the lambda layer

```bash
zip -r layer.zip python
```

4) Publish as a lambda layer

The below code creates lambda layer called trafilatura. If the layer already exists, then the code will create a new version of the lambda layer. 

```bash
aws lambda publish-layer-version  --layer-name trafilatura --zip-file fileb://layer.zip --compatible-runtimes python3.8 python3.9 python3.10 python3.11 --region us-east-1
```

5) Attach lambda layer to lambda function
Make sure you attach the most recent lambda layer version! Also make sure that the lambda function python runtime matches with the layer's compatible runtimes.

---

Relevant links

- https://repost.aws/knowledge-center/lambda-python-package-compatible 
- https://repost.aws/knowledge-center/lambda-import-module-error-python
- https://docs.aws.amazon.com/cli/latest/reference/lambda/publish-layer-version.html

