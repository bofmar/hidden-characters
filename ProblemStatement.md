# Hidden Characters

An app similar to a phototagging app that asks the user to locate a series of characters on a busy image.

## Steps
* Choose a photo and identify each character is using their pixel position. Save the positions to a DB.
* When the user clicks on the picture, draw a circle around the area they clicked, then place a box with the possible characters.
* The user should select wich character they found, and the backend should verify that the correct selection was made. User gets feedback on their choise via the circle turning red and disappearing if wrong, or green and persisting if correct.
* The box is removed.
* Keep track of the elapsed time in the backend. (a web tokken could be usefull here?)
* When all characters are found, ask the user for a name and save their time and score on a leaderboard.

## Plan
* Get the frontend build first. Make sure that a targeting circle is placed on click and that the selection box appears and disappears properly. No calls to the backend yet.
* Design the API. Assume that we will add more photos when designing the DB.
* The validation should happen like this:
    * On stage load send the user a JWT with a timestamp, and character fileds set to false. eg
    ```
        {
            "token": .......,
            "chracters": {
                "waldo": false,
                "magician": false,
                "wilma": false,
                ...
            }
        }
    ```
    * In the db, a stage should be defined as so:
    ```
        {
            _id: ...,
            characters: {
                "name": "waldo",
                "x": 12,
                "y": 24
            }
        }
    ```
    * The field names for the characters in the initial response, is populated like this. First the frontend will send a request to /:pictureId. The server will fetch that picture from the db and use the characters field to populate the JSON.
    * When the user selects a character send the backend a JSON like so:
    ```
        {
            "waldo": {x: 32, y: 46},
            "magician": false,
            "wilma": false
            ...,
            timestamp: ....
        }
    ```
    * Validate the input by fetching the positions of each character from the db and checking them against the user input. If the input is correct (aka all characters are either set to false, or have their correct position) send a 200 response. If the validation fails send a 400 response. In either case, send the JSON back to the frontend.
    * If all character's fields are set, and all validations pass, send the user a 203 response
    * When the user sends their username, derive the times elapsed by subtracting the JWT's creation timestamp from the last request's timestamp. Save the user's name in the DB along with their time.
    * Revoke the JWT from the backend, and delete it from the user's local storage on the frontend.

## Expansions:
* It should be possible to get multiple "stages" build into this. We should aim for at least 4.
