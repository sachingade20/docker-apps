Intro to CI/CD Hands-on Lab

For this hands-on lab, we're going to build a simply CI/CD pipeline using GitHub and Docker Hub and Play with Docker. We will...

Host a project in GitHub
Configure the project to build and deploy automatically to Docker Hub
Run a deployment stack locally
Update the app
Roll out the update
Requirements

A GitHub account
A Docker Hub account
Docker 1.13+ installed locally
Git installed locally
Setting up the GitHub project

You're welcome to use whatever project you want, but feel free to fork my mikesir87/cats.

Go to https://github.com/mikesir87/cats

Click the "Fork" button at the top-right corner.

Clone the repository locally (doesn't matter SSH vs HTTP)

git clone [your repository path]
Configuring an Automated Build on Docker Hub

Login/create an account at https://hub.docker.com/
In the top-right corner, hit the Create link and hit the Create Automated Build option in the dropdown.
Choose the GitHub option for your build's source.
If you haven't yet, you will be prompted to link your GitHub account to Docker Hub.
Once back on Docker Hub, navigate to the repository you setup earlier.
Configure the repository namespace/name as desired. This will be the name in which you will pull the image. For example, if I set the namespace to mikesir87 and name to cats, the image would be found at mikesir87/cats.
Trigger the first build

Docker Hub is now configured to receive webhook notifications whenever you push code to your repository on GitHub. However, it doesn't trigger build once setup. So... let's do that!

In your project, go to the Build Settings tab.
Click the Trigger button.
Then, go to the Build Details tab. You should see that the build has been queued.
You can drill into the build to see the console output and progress.
Once done, you should see a build with the tag latest
Launch a stack with your newly built image

In the mikesir87/cats repository is a docker-compose-stack.yml file that we will use to launch a new stack.

Modify the docker-compose-stack.yml to point to your new image... mikesir87/cats to your_namespace/your_image_name

Open up https://play-with-docker.com/

Create a new instance by hitting the New Instance in the left corner.

Create a docker-compose-stack.yml file and paste in the contents of your updated docker-compose-stack.yml

vi docker-compose-stack.yml
[Press 'i' to enter INSERT mode; paste contents; hit ESC; type ':x'; press enter]
You may have to re-align the contents

Start a Swarm manager

docker swarm init
This will most likely fail with an error stating it doesn't know what address to use. Copy the 10.0.* address and run...

docker swarm init --advertise-addr=[the address]
and copy the docker swarm join command.

Create another instance using the button in the left sidebar.

Run the docker swarm join command.

Feel free to create another instance.

On your manager node (has a blue user icon in the list of instances), launch the stack...

docker stack deploy -c docker-compose-stack.yml cats
After a brief moment, you should see results when checking on the service

$ docker service ls
ID            NAME          MODE        REPLICAS  IMAGE
c7tfd9aufcxk  cats_cat-app  replicated  3/3       mikesir87/cats:latest
You should notice a 5000 link at the top of the page, on the same line as the IP. Clicking that will open that instance. Notice that refreshing will rotate through the running containers (yah routing mesh!!).
Make a code change

Make a change to the templates/index.html. Feel free to change the body background color to another color.

Commit and push the code.

git add .
git commit
git push
If you jump into the Docker Hub Build Details panel, you should see a build trigger shortly.

Update your service

In the Play with Docker panel, go to your Swarm Manager (instance with blue user icon).

Update the service to use the latest image (swap out mikesir87/cats with the name of your image):

docker service update cats_cat-app --image=mikesir87/cats:latest --force
Wait for the deploy to roll out! If you refresh, you might get the new version or the old.

Clean up

Simply delete the instances in Play with Docker and close your session.
How to Improve/Actually Use This

In most environments, you don't want to just use the latest tag. You'll most likely want to create tags for each version (1.0.0, 1.0.1, etc.)
For projects that you don't public...
Use something other than GitHub (with public repos) for your code
Use GitLab, Jenkins, Travis, etc. to do the build. All can watch repos or receive webhooks from your repos
Use either private Docker Hub repos or another private repo solution (Nexus, AWS ECR, etc., or run your own)
