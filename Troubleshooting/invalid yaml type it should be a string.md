## Issue

    ERROR: The Compose file './docker-compose.yml' is invalid because:
    services.db.environment contains {"POSTGRES_USER": "postgres"}, which is an invalid type, it should be a string

<br>

## Reason

- The syntax is mixed up.

<br>

## Solution

- solution: use the correct syntax.

Use either:

      environment:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres

or

      environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres

<br>

### References

- docker services.web.working_dir contains an invalid type, it should be a string https://stackoverflow.com/questions/56128696/docker-services-web-working-dir-contains-an-invalid-type-it-should-be-a-string/59127687
