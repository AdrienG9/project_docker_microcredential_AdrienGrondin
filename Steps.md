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

## Storing images on Dockerhub 

12. I then logged in to dockerhub via vs code
`docker login` 

13. Then properly retagged my images for docker hub
`docker tag train:version2 agrondin1/train:version2`
`docker tag infer:version1 agrondin1/infer:version1`

14. Finally proceeded to push images to docker Hub
`docker push agrondin1/train:version2`
`docker push agrondin1/infer:version1`

## Building an Apptainer image on the HPC

15. I first connected to https://login.hpc.ugent.be

16. Proceeded to start a shell session with 1 node 4 cores for 4 hours using the donphan cluster

17. I then enter my scratch directory:
`cd scratch/gent/491/vsc49179`

18. Then went one to build the apptainer images:
`nano build_apptainer_images.sh`
`#!/bin/bash`
`#SBATCH --job-name=apptainer_build_all`
`#SBATCH --output=apptainer_build.log`
`#SBATCH --time=01:00:00`
`#SBATCH --ntasks=1`

`# Apptainer is available system-wide so no need to load module`

`# Build training image`
`apptainer build train_container.sif docker://agrondin1/train:version2`

`# Build inference image`
`apptainer build infer_container.sif docker://agrondin1/infer:version1`

19. Submitted the job to slurm
`sbatch build_apptainer_images.sh`

This did manage to create the infer container but not the train so I relaunch it with only the build training image.



