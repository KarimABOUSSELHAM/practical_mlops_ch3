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
3) Now if you follow exactly the structure of the `Dockerfile` cited in page 76 it lust work but you could be surprised that the resulting image heavy sized (more than 2 GB). The challenge
   therefore is to follow some best practices to reduce the size and consquently reduce the attack surface and make the image more secure. Here are some key ideas I tried before enclosing
   the dockerfile located in this repo:
     - The suggested base image in the book is oversized fot he project. So I tried both with `python:3.12-slim` and `python:alpine3.19`. The latter is much smaller but can cause issues
       when it comes to the installation of large frameworks such as `scikit-learn`. If building an image with `python:alpine3.19` works fine go with. In my case I chose `python:3.12-slim`;
     - Separate the stage of building the required frameworks from the packaging of the application: this what is called "multi-stage building". By the way, I used `pipreqs` to create
       the `requirements.txt` file;
