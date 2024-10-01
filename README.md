# Overleaf Community Edition with LDAP and OAuth2

This repo contains an improved, free LDAP/OAuth2 authentication and authorization 
for [Overleaf](https://github.com/overleaf/overleaf) Community Edition.

The inital idea for this implementation was taken from [worksasintended](https://github.com/worksasintended).

I use this repo to provide a self-hosted Overleaf instance for myself, with GitHub OAuth2 only.


## Building

The following commands will prepare the Overleaf container with our LDAP/OAuth2 customizations:

```
bash scripts/extract_files.sh 5.0.6
bash scripts/apply_diffs.sh
cd ldap-overleaf-sl
docker build -t ldap-overleaf-sl:5.0.6 .
```


## Deployment
 
1. Create `.env` for `.env.template` by filling in all values for the required variables.

2. Start Docker containers:

``` 
docker-compose up -d
```

3. For Git integration, launch the `mongo` shell in the Mongo container and execute the following in it:

```
db.oauthApplications.insert({
  "clientSecret" : "e6b2e9eee7ae2bb653823250bb69594a91db0547fe3790a7135acb497108e62d",
  "grants" : [
    "password"
  ],
  "id" : "264c723c925c13590880751f861f13084934030c13b4452901e73bdfab226edc",
  "name" : "Overleaf Git Bridge",
  "redirectUris" : [],
  "scopes" : [
    "git_bridge"
  ]
})
```

It will be committed automatically with the following response:

```
WriteResult({ "nInserted" : 1 })
```


4. Restart Docker containers:

```
docker-compose down
docker-compose up -d
```

5. Now everything is ready, open the public HTTPS proxy URL for your Overleaf instance in the browser!


## Debug

1. Set the env variable `LOG_LEVEL` to `debug` (default is info - you can do this in the docker-compose file)
2. Check the logs in Overleaf container at `/var/log/overleaf/web.log`, or by running:

```
docker exec overleaf cat /var/log/overleaf/web.log
```


## Development

1. Cloning this repo
2. Extract files from image using `bash scripts/extract_files OVERLEAD_VERSION`
3. Generate modified files using `bash scripts/apply_patches.sh`
4. Development
5. Create diff files using `bash script/make_diffs.sh` and commit

