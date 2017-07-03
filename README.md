# docker-apps

Important commands
    1  docker swarm init --advertise-addr=10.0.18.3
    2  docker stack deploy -c docker-compose-flask.yml flask
    3  docker service ls
    4  docker service update flask_flask-app --image=sachinpgade/docker-apps:flask-latest --force
