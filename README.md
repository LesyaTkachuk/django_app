# Django demo application

This is just example app described in [Django documentation](https://docs.djangoproject.com/en/5.2/intro/tutorial01/) and dockerized in order to use with **Elastic Container Service** or **Elastic Beanstalk**.

## Environment variables

| Name                   | Required? | Default value | Description                                                                                                                                                                                                                        |
| :--------------------- | :-------: | :------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DJANGO_SECRET_KEY`    |    Yes    | -             | A secret key for a particular Django installation. This is used to provide cryptographic signing, and should be set to a unique, unpredictable value.                                                                              |
| `DEBUG`                |    No     | False         | A boolean that turns on/off debug mode.                                                                                                                                                                                            |
| `DJANGO_ALLOWED_HOSTS` |    No     | "127.0.0.1"   | A list of strings representing the host/domain names that this Django site can serve. This is a security measure to prevent HTTP Host header attacks, which are possible even under many seemingly-safe web server configurations. |
| `DJANGO_INTERNAL_IPS`  |    No     | "127.0.0.1"   | The Debug Toolbar is shown only for IP addresses listed in this setting.                                                                                                                                                           |
| `DATABASE_ENGINE`      |    No     | "postgresql"  | The database backend to use.                                                                                                                                                                                                       |
| `DATABASE_NAME`        |    No     | "polls"       | The name of the database to use.                                                                                                                                                                                                   |
| `DATABASE_USERNAME`    |    Yes    | -             | The username to use when connecting to the database.                                                                                                                                                                               |
| `DATABASE_PASSWORD`    |    Yes    | -             | The password to use when connecting to the database.                                                                                                                                                                               |
| `DATABASE_HOST`        |    No     | "127.0.0.1"   | Which host to use when connecting to the database.                                                                                                                                                                                 |
| `DATABASE_PORT`        |    No     | 5432          | The port to use when connecting to the database.                                                                                                                                                                                   |


