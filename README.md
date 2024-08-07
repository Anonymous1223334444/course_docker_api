# course_docker_api
course_docker_api

# The commands

## Sart the django project [our service into our docker-compose.yml file]

### Build the image
docker build . \n

### Build the container
docker-compose build \n

### Settings up a linting tool
docker-compose run --rm app sh -c "flake8" \n

### Auto generate all file needed for the django project
docker-compose run --rm app sh -c "django-admin startproject app ." \n

### Run the django app
docker-compose up                     ---->  Django is running on http://127.0.0.1:8000/ \n

### Run test with django and docker-compose
docker-compose run --rm app sh -c "python manage.py test" \n

## Manage the database side [our db into our docker-compose.yml file]
docker-compose up
docker-compose down
docker-compose build

## Create a django app
docker-compose run --rm app sh -c "python manage.py startapp core" \n
docker-compose run --rm app sh -c "python manage.py startapp user" \n

## Make migrations
docker-compose run --rm app sh -c "python manage.py makemigrations" \n

## Apply migrations to the database
docker-compose run --rm app sh -c "python manage.py wait_for_db && python manage.py migrate" \n

## Clear the volume
docker volume ls   --> the_output
docker volume rm the_output     # if not work, run docker-compose down at first

## Create superuser
docker-compose run --rm app sh -c "python manage.py createsuperuser" \n

## Remote adding the project to github
git add . \n
git commit -am "Added Github Actions." \n
git push origin \n