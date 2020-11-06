# Data Science Festival X ASOS
## Data Science Festival Workshop 7 November 2020 – Building a fashion recommender using Tensorflow/Keras with ASOS.

First we will build a recommender model together in Google Colab. Then we will deploy the model into production.

Before the workshop you can open the notebook by visiting [Google Colab](https://colab.research.google.com/notebooks/intro.ipynb) and clicking on 'File/Open Notebook'. In the orange window that opens click on "GitHub". Then in the 'Enter a GitHub URL or search by organization or user' line enter 'asos/dsf2020'. Finally click on "DSF-ASOS_Build_and_Deploy_a_Recommender_in_3_Hours.ipynb".

## A note about the data
This repo contains four data files which will be used for training a recommender model. They contain product ids and fake user ids.

## Serving a fashion recommender using Tensorflow serving

#### About Tensorflow serving
Tesnorflow serving is a flexible, high performance serving system for machine learning models.

Tensorflow serving serves model without running original Python code.

In order to serve a Tensorflow model, SavedModel is exported from a Python program.

A SavedModel contains a complete TensorFlow program, including weights and a graph definitions.

The recommended way of running Tensorflow serving is with Docker image.

##### Environment setup
- docker engine installed and running to run a serve
    General installation instructions are on the [Docker site](https://docs.docker.com/get-docker/), but some quick links here:
    [Docker for macOS](https://docs.docker.com/docker-for-mac/install/)
    [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
- http client installed to run a client 
    [Curl for mac](https://curl.haxx.se/dlwiz/?type=source&os=Mac+OS+X)  
    
### Running tensorflow serving in local Docker container

CMD 1

````
cd {recommender-model-folder}
````

CMD 2 

````
docker pull tensorflow/serving
````

CMD 3

````
_**Windows**_

docker run -d -p 8501:8501 -v "$PWD/:/models/recommender" -e MODEL_NAME=recommender tensorflow/serving

````

```

_**Mac**_

docker run -d -p 8501:8501 --mount type=bind,source=${PWD}/,target='/models/recommender' -e MODEL_NAME=recommender tensorflow/serving

````

CMD 4

````

_**Windows**_

$rec_request = @"
{
"signature_name" : "call_item_item",
"inputs" : {
"item": [123123]
}
}
"@


$rec_response = Invoke-RestMethod -Uri "http://localhost:8501/v1/models/recommender:predict" -Method Post -Body $rec_request -ContentType "application/json"
$rec_response | convertto-json

````

````
_**Mac**_
curl --header "Content-Type: application/json" --request POST --data '{"signature_name":"call_item_item","inputs": {"item": [123123] } }' http://localhost:8501/v1/models/recommender:predict
````

CMD 5 

````
_**Windows**_

$output = Invoke-RestMethod http://localhost:8501/v1/models/recommender/metadata
$output | convertto-json
````

````
_**Mac**_

curl http://localhost:8501/v1/models/recommender/metadata

````
