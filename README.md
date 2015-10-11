# Minecraft Buildpack

This is a [Heroku buildpack](https://devcenter.heroku.com/articles/buildpacks) that allows you to host a lightweight minecraft server for a few friends using [ngrok](http://ngrok.com) (for TCP proxy) and [heroku](http://heroku.com) (for the hosting and hardware)

## Usage

Create a free [ngrok](https://ngrok.com/) account and copy your authentication token. Then create a new git project with an `eula.txt` file:

```bash
echo 'eula=true' > eula.txt
git init
git add eula.txt
git commit -m "Initial commit"
```

Then, install the [Heroku toolbelt](https://toolbelt.heroku.com/). To create a Heroku app, set your ngrok token, and push

```bash
heroku create --buildpack https://github.com/jkutner/heroku-buildpack-minecraft
heroku config:set NGROK_API_TOKEN="xxxxxxxxxxxxxxx"
git push heroku master
```

Finally, open the app:

```bash
heroku open
```

This will open a page in your web browser with the connection address of your server.

```
tcp://ngrok.com:45010
```

Copy the `ngrok.com:45010` part, and paste it into your Minecraft game as the server adress.

## Syncing to S3

The Heroku filesystem is [ephemeral](https://devcenter.heroku.com/articles/dynos#ephemeral-filesystem), which means files written to the file system will be destroyed when the server is restarted.

Minecraft keeps all of the data for the server in flat files on the file system.
Thus, if you want to keep you world, you'll need to sync it to S3.

First, create an [AWS account](https://aws.amazon.com/) and an S3 bucket. Then configure the bucket
and your AWS keys like this:

```bash
heroku config:set AWS_BUCKET=your-bucket-name
heroku config:set AWS_ACCESS_KEY=xxx
heroku config:set AWS_SECRET_KEY=xxx
```

The buildpack will sync your world to the bucket every 60 seconds, but this is configurable by setting the `AWS_SYNC_INTERVAL` config var.

### Configuration

You can choose the Minecraft version by setting the `MINECRAFT_VERSION` like so:

```bash
heroku config:set MINECRAFT_VERSION="1.8.3"
```

You can also configure the server properties by creating a `server.properties`
file in your project and adding it to git. The various options available are
described on the [Minecraft wiki](http://minecraft.gamepedia.com/server.properties).

You can add files such as `banned-players.json`, `banned-ips.json`, `ops.json`,
`whitelist.json` to your git repository and the Minecraft server will pick them up.

### Ngrok

You can customize ngrok by setting the `NGROK_OPTS` config variable. For example:

```bash
heroku config:set NGROK_OPTS="-subdomain=my-subdomain"
```
