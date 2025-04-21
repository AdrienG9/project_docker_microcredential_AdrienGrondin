## Containerize and run training the machine learning model

0. I didn't pull an image with the dependencies needed because I already had one from the training session with the librairies that are going to be use (Repository: jupyter/scipy-notebook;  TAG: python-3.11.5 )

2. I then modified the recipe Dockerfile.train with the proposed advices on the dockerfile 

3. Proceed to create a first version, but this was missing some info so I created a second one

4. Built the train model using 
`docker build . --tag train:version2 -f Dockerfile.train`

5. Ran the train model using 
`docker run --rm   --volume "$PWD"/app/models:/app/models   train:version2`

6. Once completed this resulted in: iris_model.pkl

## Then onto the containerizing and serving the ml model

7. Started by pulling the proposed image using 
`docker pull python:3.9-slim`

8. Modified the dockerfile.infer 

9. Built the image using
`docker build . --tag infer:version1 -f Dockerfile.infer`

10. Ran and mounted the serving recipe
`docker run --rm -- p 8080:8080 -v "$PWD"/app/models:/app/models infer:version1`

11. Tested it on another terminal using
`curl http://localhost:8080/` which returned "Welcome to Docker Lab"