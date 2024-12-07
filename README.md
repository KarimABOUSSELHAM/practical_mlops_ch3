This repository exposes a solution to both the exercises n° 3 and 4 of the third chapter of Gift, N., & Deza, A. (2021). [*Practical MLOps Operationalizing Machine Learning Models*](https://www.oreilly.com/library/view/practical-mlops/9781098103002/). O'Reilly Media.  

# Exercise n° 3 statment  

*`Create a new container image, based on the Flask example, that serves a model and that provides examples on a GET request to interact with the model. Create another endpoint that provides useful metadata about the model.`*

# Answer steps  

The example cited by the exercise lies between pages 76 and 80, included.  
Now here are the suggested steps to solve the exercise:  

1) In the flask application script you should add `/example` endpoint which returns a static example of your choice. In my case I chose the docstring of the `predict` function defined in
   page 76;
   
2) Add another endpoint in the same application, called `/metadata` which returns the details of the model: like title, title of the dataset, the link of the dataset, the version,
   the description of the model, the features of the dataset in a list and the output feature;
   
3) Now if you follow exactly the structure of the `Dockerfile` cited in page 76 it it must work properly but you could be surprised that the resulting image is heavy sized (more than 2 GB). The challenge therefore is to follow some best practices to reduce the size and consquently reduce the attack surface and make the image more secure. Here are some key ideas I tried before enclosing the dockerfile located in this repo:

     - The suggested base image in the book is oversized fot he project. So I tried both with `python:3.12-slim` and `python:alpine3.19`. The latter is much smaller but can cause issues
       when it comes to the installation of large frameworks such as `scikit-learn`. If building an image with `python:alpine3.19` works fine go with. In my case I chose `python:3.12-slim`;
       
     - Separate the stage of building the required frameworks from the packaging of the application: this what is called "multi-stage building". By the way, I used `pipreqs` to create
       the `requirements.txt` file;

     - Build the image named `flask-predict` through the command `docker build --build-arg VERSION=AutoML_287f444c -t flask-predict .` and notice the image size by listing all the               existing images through `docker images` in your terminal or in your docker engine desktop. The image I've created is near 680 MB (if yo have found another to optimize it more             please let me know);
       ![image](https://github.com/user-attachments/assets/142811c1-b6a8-4cc3-bbd1-095644b837ee)

     -  Now you can run you container, exposing port 5001 as you mgiht have noticed in the dockerfile. You can do so bu typing the following command in your terminal `docker run -p               5001:5001 -d --name flask-predict flask-predict`. Once created the container can be reached through your `https://localhost:5001` for the `GET` methods or through other HTTP              request testing tools such as [Postman](https://www.postman.com/) to test the `POST` method;
       ![image](https://github.com/user-attachments/assets/8d0452ca-796c-4f34-a24d-4e5395279dca)
       ![image](https://github.com/user-attachments/assets/15b29338-2500-47ed-ac6a-8f84c80ed94d)
       ![image](https://github.com/user-attachments/assets/a2608efe-f046-42c3-8006-2a34a3dbe80e)

With this let's go to the next exercise!

# Exercise n° 4 statment  

*`Publish the newly created image to a container registry like Docker Hub.`*

# Answer steps 

Before publishing the the image let's try to reduce once more its size as the one obtained before will make it not that easy for potential users to download it quickly. Reducing the image size will strike a double objective: The first one is to elevate its portability and the second one is to reduce its attack suface and make it more secure.  

You can proceed wtih the following steps:

1) Use a tool to optimize once more the size of the existing image. In my case I chose [Slim](https://github.com/slimtoolkit/slim). Take a look on the documentation page of this             practical tool to learn how to install it and use it in your desktop. Once `Slim` is installed you can optimize the size of your image with this command `slim build flask-predict`,       this will build a new image by adding the suffix `.slim` to the original one. Thus, the newly created image will be called `flask-predict.slim`. You can notice that we managed in our     case to reduce the image size by more than half;
   ![image](https://github.com/user-attachments/assets/2800e1ba-d5cd-4ce8-a723-7d69191a6ad2)

2) Now it's time to check vulenrabilities in the `flask-predict.slim` image before pushing it to Docker Hub. There are several tools including ones which are integrated in Docker itself     but I preferred here to use `Snyk` as it is widely known for its performance and its ability to suggest solutions to fight against vulnerabilities. You can easily install this tool by    searching it in `extensions` section of Docker engine desktop as shown below;
   ![image](https://github.com/user-attachments/assets/4b216895-a29b-43ce-9518-5cb854757338)
   Now after opening the extension choose the image and click on `Test image`. Hopefully the image contains no critical vulnerability and can be pushed without great concerns. By the way    try your best not to push images which contain crtical vlenrabilities to docker hub;
   ![test works](https://github.com/user-attachments/assets/8da793bb-bd7c-48cd-b827-c62428b0aee2)

3) The image is ready to be pushed. Type the command `docker tag flask-predict.slim <username>/<docker hub image tag>:latest` followed by the command `docker push <username>/<docker hub     image tag>:latest` and the job is done!
   ![image](https://github.com/user-attachments/assets/d35d17f7-1a7b-4295-aeb5-b1c379286e51)
