# DHS Form G-28 System

## Project Goals
To build a suite of Docker Microservices, and an associated User Interface, for capturing data from the DHS Form G-28 in a modern UI, and for demonstrating a fully automated CI/CD Pipeline and Enterprise Deployment System.

- Project Start: 11/21/2017
## UI Module
### Environment Notes
- Angular CLI: 1.5.3
- Node: 6.11.2
- OS: win32 x64
- Angular: 5x

### Running API For Development
1.  cd API
1.  npm install
1.  Set the 'DHS_G28_ENV' environment variable per environment:
    1.  export DHS_G28_ENV=DEV
1.  The following environment variables are needed for connecting to PostgreSQL:
    1.  PGDATABASE
    1.  PGHOST
    1.  PGPASSWORD
    1.  PGPORT
    1.  PGUSER
1.  npm run server

### Running API In Docker
1.  cd API
1.  docker build -t dhsformg28-api:latest -f ./docker/container/Dockerfile .
1.  Set the 'DHS_G28_ENV' environment variable per environment:
    1.  export DHS_G28_ENV=DEV
1.  The following environment variables are needed for connecting to PostgreSQL:
    1.  PGDATABASE
    1.  PGHOST
    1.  PGPASSWORD
    1.  PGPORT
    1.  PGUSER
1.  docker run -p 3000:3000 -e DHS_G28_ENV -e PGDATABASE -e PGHOST -e PGPASSWORD -e PGPORT -e PGUSER dhsformg28-api:latest

### Running UI For Development
1.  cd UI
1.  npm install
1.  ng serve --open | ng serve --host=0.0.0.0 --open (makes access from mobile emulator easier)

### Building and Running UI In Docker
1.  cd UI
1.  ng build
1.  docker build -f docker/container/Dockerfile -t g28form:latest .
1.  docker run -p 8000:80 g28form:latest
