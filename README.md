Image which can serve as a base Docker image for dockerizing [Meteor](meteor.com) applications.

In the root directory of your Meteor application (the one with `.meteor` directory) create a `Dockerfile` file
with the following content:

```
FROM tozd/meteor
```

And your Meteor application is dockerized.

The intended use of this image is that it is run alongside the
[tozd/meteor-mongodb](https://github.com/tozd/docker-meteor-mongodb) image for MongoDB database for your Meteor
application.

When running Docker image with your Meteor application, you have to configure the following environment variables:

* `ROOT_URL` – used by Meteor to construct [absolute URLs](http://docs.meteor.com/#/full/meteor_absoluteurl), it
  should not contain a trailing `/`; example: `http://example.com`
* `MAIL_URL` – used to configure [e-mail server](http://docs.meteor.com/#/full/email);
  example: `smtp://user:password@mailhost:port/`
* `METEOR_SETTINGS` – JSON string of your [Meteor settings](http://docs.meteor.com/#/full/meteor_settings)
* `MONGO_URL` - MongoDB database URL; example: `mongodb://mongodb/meteor`
* `MONGO_OPLOG_URL` – MongoDB database oplog URL; example: `mongodb://mongodb/local`

You can specify those environment variables when running an image, but you can also export them from the script
file volume mounted under `/etc/service/meteor/run.config`.

Example of a `run.config` file:

```bash
MONGODB_ADMIN_PWD='<pass>'
MONGODB_CREATE_PWD='<pass>'
MONGODB_OPLOGGER_PWD='<pass>'

export MONGO_URL="mongodb://meteor:${MONGODB_CREATE_PWD}@mongodb/meteor"
export MONGO_OPLOG_URL="mongodb://oplogger:${MONGODB_OPLOGGER_PWD}@mongodb/local?authSource=admin"
```

Only `export` lines are necessary for this image, but others are used by `tozd/meteor-mongodb` image.
You can export also other environment variables.

When you are extending this image, you can add a script `/etc/service/meteor/run.initialization`
which will be run at a container startup, after the container is initialized, but before the
Meteor application is run.
