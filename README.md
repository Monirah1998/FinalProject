# Capstone

## Casting Agency Protected API

The application demonstrates create, read, update, delete APIs for Movies and Actors and also demonstrates Many to Many relationship for Movies Casts having multiple

## Motivation for project

This project gives me a great opportunity to learn python flask framework, Auth0 authentication, authorization, role based access control (RBAC). While implementing the project, I learned about python decorators, error / exception handling and general coding guidelines. Since the project is live and can be access via https URL on Heroku, it also gave me a chance to learn website hosting and herkou internals.

## Tech stack

- Python 3.x
- Flask - Flask is used for developing web applications using python
- SQL Alchemy - SQLAlchemy is a library that facilitates the communication between Python programs and databases
- PostgreSQL - Relational Database.
- Heroku - Hosting for web applications.

## Tasks

1. Installing dependencies and environment variables
2. Setup Authentication
3. Deploy application in Heroku.

### 1. Installing dependencies, setting environment variables and starting server locally
1. Install python version 3.9 from https://www.python.org/downloads/release/python-390/.
2. Install dependencies needed for this project using the following command:
   python -m pip3 install -r requirements.txt
3. For running the server locally, export environment variables by running following commands -
   export AUTH0_DOMAIN='dev-i2ernxx1.us.auth0.com'
   export ALGORITHMS='RS256'
   export API_AUDIENCE='casting-agency'
4. Start flask server locally using following command on http://localhost:5000/ -
   python -m flask run
5. Open another terminal and cd to the directory where app.py is located and run this command -
   python -m unittest test_app.py
   This will run unit tests to make sure app is running as expected.


### 2. Setup Authentication

For Authentication and authorization we use Auth0. Following steps need to be performed:
1. Create an account in Auth0, you get a default tenant.
2. Create API and name it casting-agency
3. Add Permissions in the API 
   - read:actors
   - add:actor
   - edit:actor
   - delete:actor
   - read:movies
   - add:movie
   - edit:movie
   - delete:movie
4. Create an application for testing which will give you Client ID and Client Sectret.
4.1 Enable grant type password in advanced setting for the test application.
5. Create 3 roles and assign appropriate permissions
   - Casting Assistant (read:actors, read:movies)
   - Casting Director (add:actor, delete:actor, edit:actor, edit:movie, read:actors, read:movies)
   - Executive Producer (add:actor, delete:actor, edit:actor, edit:movie, read:actors, read:movies, add:movie, delete:movie)
5. Under User Management > Users, create three users:
   - casting_assistant@castingagency.com and assign role Casting Assistant
   - casting_director@castingagency.com and assign role Casting Director
   - executive_produceer@castingagency.com and assign role Executive Producer
6. Configure app.py to support authentication and authorization

AUTH0_DOMAIN = '<domain from auth0>'
ALGORITHMS = ['RS256']
API_AUDIENCE = 'casting-agency'


eg
```
@app.route('/api/v1/actors', methods=['POST'])
    @requires_auth
    def create_actor(payload):
        if requires_scope("add:actor"):
            data = request.get_json()
            print(data)
            actor = Actor(first_name=data['firstName'], last_name=data['lastName'], gender=data['gender'], date_of_birth=data['dateOfBirth'], age = data['age'])
            actor.create()
            return make_response(jsonify({"result": "Actor created successfuly."}), 200)
        raise AuthError({
            "code": "Unauthorized",
            "description": "You don't have access to this resource"
        }, 403)
```


### 3. Hosting application in Heroku

To run the application on Heroku.
1. Get an account on Heroku.
2. In project directory, do following steps
2.1 git init
2.2 heroku login
3.3 git commit -m "Commit code"
3.4 git push heroku master

The application is hosted on https://fsnd-heroku-app.herokuapp.com


### 4. API documentation

1. POST '/actors'
Creates an actor based on the parameters first name, last name, gender, date of birth and age. This API will return HTTP 200 for success if the user is allowed to create the actor. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request POST 'https://fsnd-heroku-app.herokuapp.com/api/v1/actors' \
--header 'Authorization: Bearer {{token}}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "firstName": "Jane1",
    "lastName": "Doe",
    "gender": "Female",
    "dateOfBirth": "2018-01-01",
    "age": 30
}'
```

Sample response
```
{
    "result": "Actor created successfuly."
}
```


2. PATCH '/actors/<actor-id>'
Update actor by passing the corresponding id and the details you want to update. This API will return HTTP 200 for success if the user is allowed to update the actor. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request PATCH 'https://fsnd-heroku-app.herokuapp.com/api/v1/actors/1' \
--header 'Authorization: Bearer {{token}}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "firstName": "John1",
    "lastName": "Doe",
    "gender": "Female",
    "dateOfBirth": "2020-01-01",
    "age": 30
}'
```

Sample response
```
{
    "age": 30,
    "dateOfBirth": "Wed, 01 Jan 2020 00:00:00 GMT",
    "firstName": "John1",
    "gender": "Female",
    "id": 1,
    "lastName": "Doe"
}
```



3. DELETE '/actors/<actor-id>'
Delete an actor by passing the corresponding id. This API will return HTTP 200 for success if the user is allowed to delete the actor. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request DELETE 'https://fsnd-heroku-app.herokuapp.com/api/v1/actors/1' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "result": "Actor deleted successfuly."
}
```

4. GET '/actors/<actor-id>'
Get actor details by passing the corresponding id. This API will return HTTP 200 for success if the user is allowed to get the actor. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request GET 'https://fsnd-heroku-app.herokuapp.com/api/v1/actors/4' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "age": 30,
    "dateOfBirth": "Wed, 01 Jan 2020 00:00:00 GMT",
    "firstName": "John1",
    "gender": "Female",
    "id": 1,
    "lastName": "Doe"
}
```

5. GET '/actors'
Get all actors. This API will return HTTP 200 for success if the user is allowed to get all actor. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request GET 'https://fsnd-heroku-app.herokuapp.com/api/v1/actors' \
--header 'Authorization: Bearer {{token}}'
```
```
{
    "actors": [
        {
            "age": 30,
            "dateOfBirth": "Mon, 01 Jan 2018 05:00:00 GMT",
            "firstName": "Jane",
            "gender": "Female",
            "id": 2,
            "lastName": "Doe"
        },
        {
            "age": 30,
            "dateOfBirth": "Mon, 01 Jan 2018 05:00:00 GMT",
            "firstName": "Jane1",
            "gender": "Female",
            "id": 4,
            "lastName": "Doe"
        },
        {
            "age": 30,
            "dateOfBirth": "Wed, 01 Jan 2020 05:00:00 GMT",
            "firstName": "John1",
            "gender": "Female",
            "id": 1,
            "lastName": "Doe"
        },
        {
            "age": 32,
            "dateOfBirth": "Tue, 01 Jan 2019 05:00:00 GMT",
            "firstName": "Jane1",
            "gender": "Female",
            "id": 7,
            "lastName": "Doe1"
        },
        {
            "age": 32,
            "dateOfBirth": "Tue, 01 Jan 2019 05:00:00 GMT",
            "firstName": "xyiwtugwij",
            "gender": "Female",
            "id": 10,
            "lastName": "wczdjdrlfu"
        },
        {
            "age": 32,
            "dateOfBirth": "Tue, 01 Jan 2019 05:00:00 GMT",
            "firstName": "rfghrflhan",
            "gender": "Female",
            "id": 11,
            "lastName": "bfdgpppnvp"
        },
        {
            "age": 32,
            "dateOfBirth": "Tue, 01 Jan 2019 05:00:00 GMT",
            "firstName": "zobptdsnqe",
            "gender": "Female",
            "id": 12,
            "lastName": "kusrehtuyu"
        }
    ]
}
```


6. POST '/movies'
Create movie by passing title, genre, rating and release date. This API will return HTTP 200 for success if the user is allowed to create the movie. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request POST 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies' \
--header 'Authorization: Bearer {{token}}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "title": "Harry Potter",
    "genre": "Fantasy",
    "rating": "5",
    "releaseDate": "2020-01-01"
}'
```

Sample response
```
{
    "result": "Movie created successfuly."
}
```

7. PATCH '/movies/<movie-id>'
Update movie by passing the corresponding id and the details you want to update. This API will return HTTP 200 for success if the user is allowed to update the movie. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request PATCH 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies/1' \
--header 'Authorization: Bearer {{token}}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "genre": "Fantasy",
    "rating": "5",
    "releaseYear": 2020,
    "title": "Harry Potter 2"
}'
```

Sample response
```
{
    "movie": {
        "cast": [],
        "genre": "Fantasy",
        "id": 6,
        "rating": "5",
        "releaseDate": "Wed, 01 Jan 2020 00:00:00 GMT",
        "title": "Harry Potter 2"
    },
    "result": "Movie updated successfuly."
}
```

8. DELETE '/movies/<movie-id>'
Delete a movie by passing the corresponding id. This API will return HTTP 200 for success if the user is allowed to delete the movie. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request DELETE 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies/1' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "result": "Movie created successfuly."
}
```

9. GET '/movies/<movie-id>'
Get movie details by passing the corresponding id. This API will return HTTP 200 for success if the user is allowed to get the movie. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request GET 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies/1' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "cast": [],
    "genre": "Fantasy",
    "id": 6,
    "rating": "5",
    "releaseDate": "Wed, 01 Jan 2020 00:00:00 GMT",
    "title": "Harry Potter 2"
}
```

10. GET '/movies'
Get all movies. This API will return HTTP 200 for success if the user is allowed to get all movies. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request GET 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "movies": [
        {
            "cast": [],
            "genre": "Fantasy",
            "id": 9,
            "rating": "5",
            "releaseDate": "Wed, 01 Jan 2020 00:00:00 GMT",
            "title": "Harry Potter1234"
        },
        {
            "cast": [],
            "genre": "Fantasy",
            "id": 6,
            "rating": "5",
            "releaseDate": "Wed, 01 Jan 2020 00:00:00 GMT",
            "title": "Harry Potter 2"
        }
    ]
}
```

11. POST '/movies/<movie-id>/actor/<actor-id>'
Create cast of a movie by passing movie id and actor id. Multiple actors can be added to a movie. This API will return HTTP 200 for success if the user is allowed to create cast of a movie. Otherwise, failure will be returned. Replace the bearer token in the {{token}} field. Sample CURL -

```
curl --location --request POST 'https://fsnd-heroku-app.herokuapp.com/api/v1/movies/1/actor/2' \
--header 'Authorization: Bearer {{token}}'
```

Sample response
```
{
    "result": "Movie cast created successfuly."
}
```
