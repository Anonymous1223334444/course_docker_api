# course_docker_api
course_docker_api

# The commands
docker build .
docker-compose build
docker-compose run --rm app sh -c "flake8"
docker-compose run --rm app sh -c "django-admin startproject app ."
docker-compose up                                       ---->  Django is running on http://127.0.0.1:8000/


git add .
git commit -am "Added Github Actions."
git push origin