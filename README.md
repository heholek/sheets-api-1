# Create an API backed by Google Sheets

## Setup
First clone and install
```
git clone https://github.com/DataFire-flows/sheets-api && cd sheets-api
npm install
npm install -g datafire
```

To register a Google Sheets client, visit
[console.developers.google.com](https://console.developers.google.com/apis/api/sheets.googleapis.com/overview)
* click "Enable API"
* click "Credentials"
* click "Create Credentials" -> "OAuth Client ID"
* Choose "web application"
* add `http://localhost:3000` as an "Authorized redirect URI"

Now run:
```
datafire integrate google-sheets
datafire authenticate google-sheets --generate_token
# Copy your client_id and client_secret generated above,
# then visit the URL generated
```

### Create a new sheet
Run this command, replacing "Pet Store" with your title
```
datafire call google-sheets \
  -o spreadsheets.create \
  --p.body='{"properties": {"title": "Pet Store"}}' \
  --as default

# spreadsheetId: abcd
# properties:
#   title: Pet Store
# ...
```

Copy `spreadsheetId` and paste it into `spreadsheet.js`:

```
spreadsheet.id="abcd";
```

While you're in `spreadsheet.js`, you can edit the fields available and the
regexen that validate them.

## Running
```
datafire run create -p.name Lucy -p.age 2 -p.animal_type dog
datafire run retrieve
```

### Serverless
You can use [Serverless](https://github.com/serverless/serverless) to
deploy your API to AWS. Edit `serverless.yml` to control which endpoints
are exposed:

```yaml 
functions:
  create:
    handler: create.handler
    events:
      - http:
          method: post
          path: pets
  retrieve:
    handler: retrieve.handler
    events:
      - http:
          method: get
          path: pets
      - http:
          method: get
          path: pets/{id}
```


```
curl -X POST "https://id.execute-api.us-east-1.amazonaws.com/dev/pets" \
    -d '{"name": "Lucy", "age": 2}'
    
curl "https://id.execute-api.us-east-1.amazonaws.com/dev/pets"
```