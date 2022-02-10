- [Terraform](#orgb5eef2d)
  - [OLD WAY](#orgafdcdc6)
  - [NEW WAY](#org3d2695f)
- [Python](#org7c0fc80)
  - [intall (MacOS)](#orgd402a5b)
  - [Code](#orgde2702a)
    - [\_<sub>init</sub>\_\_](#orgfb55e58)
- [Linux](#org20eb0fd)
  - [TOP](#orgbeaf4f1)
    - [Order by RAM usage](#orgd737dcf)
  - [ZSH and aliases](#org633af11)
    - [ZSH function for SAML2AWS](#orge3bf96c)
    - [None new line returns](#org5f88ae4)
  - [Bash Commands](#org49ff7f8)
    - [Get base64 of a string](#org95a88e0)
- [SQL](#orgc985119)
  - [Redshift](#org67004af)
    - [User and groups](#orgdf65313)
- [Kubernetes](#org791142b)
  - [Init containers](#org51b509f)
- [MacOs](#org9c0f3d0)
  - [Brew](#org39755e2)
    - [upgrade](#orgf56162c)
- [AWS](#orgf8872ba)
  - [Secret Manager](#org6911505)
    - [Get secerets from jenkins file](#orgf4505b0)
  - [EKS](#org9ee831b)
- [Docker](#org8ca4034)
  - [Repo config](#org78fecd4)
    - [Add docker auth to `.docker`](#orge103e26)
  - [Tag and push](#org2c5051d)
  - [Runing an image](#orgb215b91)
  - [SSH to running local image](#orgc0f18cc)
- [Git](#org83ed00c)
  - [Removing all tags on local and on remote](#orgc09b8cd)



<a id="orgb5eef2d"></a>

# Terraform     :terraform:


<a id="orgafdcdc6"></a>

## OLD WAY

```
rm -rf .terraform && terraform init -backend-config="key=nonprod-1/east/terraform.tfstate" -var profile=saml2aws
terraform plan -var instance=1 -var bastion_user=admin -target=module.logging
terraform apply -var environment=prod -var bastion_user=admin -target=module.logging
```


<a id="org3d2695f"></a>

## TODO NEW WAY     :makefile:


<a id="org7c0fc80"></a>

# Python


<a id="orgd402a5b"></a>

## intall (MacOS)

Leave the system version of OS alone and intall the python using `pyenv`. It should take care of everything.

```bash
     brew install pyenv
     pyenv install <version>
     pyenv global <version>
```

Add the the following to `/.zshrc` or `/.bash_profile` depending on what you're using.

```bash
   #Python
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
```


<a id="orgde2702a"></a>

## Code


<a id="orgfb55e58"></a>

### \_<sub>init</sub>\_\_

This is the constructor.


<a id="org20eb0fd"></a>

# Linux


<a id="orgbeaf4f1"></a>

## TOP


<a id="orgd737dcf"></a>

### Order by RAM usage

`f` then select `%MEM`

```
    29605 root      20   0  386252  66528      4 D  0.0  3.3   0:00.51 /usr/bin/python2 -s /usr/bin/aws s3 cp /home/ec2-user/geoip/GeoIP2 City/GeoIP2-City-Blocks-IPv4.csv s3://applic+
 1875 root      20   0  386252  66524      4 D  0.0  3.3   0:00.72 /usr/bin/python2 -s /usr/bin/aws s3 cp /home/ec2-user/geoip/GeoIP2 City/GeoIP2-City.mmdb s3://application-proce+
32342 root      20   0  386248  66524      4 D  0.0  3.3   0:00.75 /usr/bin/python2 -s /usr/bin/aws s3 cp /home/ec2-user/geoip/GeoIP2 City/GeoIP2-City.mmdb s3://application-proce+
 8013 root      20   0  386248  66520      4 D  0.0  3.3   0:00.84 /usr/bin/python2 -s /usr/bin/aws s3 cp /home/ec2-user/geoip/GeoIP2 City/GeoIP2-City.mmdb s3://application-proce+
21472 root      20   0  386248  66512      4 D  0.0  3.3   0:00.85 /usr/bin/python2 -s /usr/bin/aws s3 cp /home/ec2-user/geoip/GeoIP2 City/GeoIP2-City.mmdb s3://application-proce+
```

The `D` state indicates that these processes are stuck waiting data.


<a id="org633af11"></a>

## ZSH and aliases


<a id="orge3bf96c"></a>

### ZSH function for SAML2AWS

```
function prof() {
  unset role
  case $1 in
    nonprod)
      role='arn:aws:iam::758317246284:role/ADFS-Developer'
      ;;
    prod)
      role='arn:aws:iam::086397520578:role/ADFS-Developer'
      ;;
  esac
  if [[ -n $role ]]
  then
    saml2aws login --skip-prompt --role=$role --profile=$1 --session-duration 14400
    export AWS_PROFILE=$1
    export AWS_DEFAULT_REGION='us-east-1'
  fi

  printenv | grep -i ^aws
}

```


<a id="org5f88ae4"></a>

### None new line returns     :token:

zsh adds `%\n` when commands like curl don't return with new line. You'd see `%` at the end of the ruturned string. Have to remember this when dealing with tokens.


<a id="org49ff7f8"></a>

## Bash Commands


<a id="org95a88e0"></a>

### Get base64 of a string

```bash
     echo -n 'svc-webanalytics:AKCp5ek8CdUn5rxr8EG3Gi1UKi7E9Jtj56KcykrjnMWetgwayvhFLKJVa2F4kyneEh1ahT12v' | base64
```

The `-n` tells echo not to add a new like at the end.


<a id="orgc985119"></a>

# SQL


<a id="org67004af"></a>

## Redshift


<a id="orgdf65313"></a>

### User and groups

1.  Add users

    ```sql
    -- Getting all users
     select * from pg_user_info;
    --Create new user with password (option: allow createdb and limit number of connections)
     create user sistum with password 'U45f5uQHCEHvhETm' createdb connection limit 10;
    
    --List all users and what groups they blong to
     SELECT usename, groname 
     FROM pg_user, pg_group
     WHERE pg_user.usesysid = ANY(pg_group.grolist)
     AND pg_group.groname in (SELECT DISTINCT pg_group.groname from pg_group);
    
    --Add a user to a group
     alter group e2e_users
     add user sistum;
    
    
    ```


<a id="org791142b"></a>

# Kubernetes


<a id="org51b509f"></a>

## Init containers

These are containers compe up just before the main container starting. they can be used to setup environment. Here is an example that was used to bebug secerets inject:

```yaml
        spec:
    {{- if .Values.imagePullSecrets }}
      imagePullSecrets:
        - name: {{ .Values.imagePullSecrets }}
      {{- end }}
      serviceAccountName: {{ include "helm.serviceAccountName" . }}
      securityContext:
        {{- toYaml .Values.podSecurityContext | nindent 8 }}
      containers:
        - name: {{ .Chart.Name }}
          env:
            - name: POSTGRES_URL
              value: {{.Values.app.postgresUrl}}
            - name: REDSHIFT_URL
              value: {{.Values.app.redshiftUrl}}
            - name:  env
              value: {{.Values.app.env}}
            - name: spring_profiles_active
              value:  {{.Values.app.springProfile}}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
          livenessProbe:
            httpGet:
              path: {{ .Values.app.healthCheckUrl }}
              port: http
            initialDelaySeconds: 60
            periodSeconds: 60
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
      initContainers:
        - name: init-environment
          image: busybox
          command: ["/bin/sh","-c"]
          args:
            - echo $POSTGRES_PASSWORD
            - echo $REDSHIFT_PASSWORD

```


<a id="org9c0f3d0"></a>

# MacOs


<a id="org39755e2"></a>

## Brew


<a id="orgf56162c"></a>

### upgrade

```bash
brew upgrade
```

If this asks for password rpeatedly. run `sudo visudo` and set `Defaults timestamp_timeout=` to 5. This makes sure to ask for passowrd every 5 mins.


<a id="orgf8872ba"></a>

# AWS     :aws:


<a id="org6911505"></a>

## Secret Manager

Create a new secret manager: First you have Create a config.json file in the following format:

```json
    {
      "auths": {
        "<DOCKER REPO>": {
          "auth": "<BASE64 ENCODED username:password>"
        }
      }
    }

```

```bash
    aws --profile nonprod secretsmanager create-secret --name usage-artifactory-realease-repo --secret-string "$(cat config.json | base64)"
```

Put a secret value:

```bash
    aws secretsmanager put-secret-value --secret-id <SECRET ARN>    --secret-string <STRING SECRET> 
```

Put secret as file (Better option):

```bash
   aws secretsmanager put-secret-value --secret-id arn:aws:secretsmanager:us-east-1:758317246284:secret:usage-artifactory-docker-auth-xpXpKP --secret-binary fileb://config.json
```

Get secret from as file

```bash
   aws secretsmanager get-secret-value --secret-id <SECRET ARN> --query SecretBinary --output text | base64 --decode >> usage-util-prod.pem
```


<a id="orgf4505b0"></a>

### Get secerets from jenkins file     :jenkins:

Add the following step the top of `stages`

```groovy
           stage('Init') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'usage-artifactory-docker-creds', variable: 'artif_auths')]) {
                       sh '''
                          mkdir -p -m 755 "$HOME/.docker"
                          printf %s "$artif_auths" | base64 --decode > "$HOME/.docker/config.json"
                       '''
                    }
                }
            }
        }
```


<a id="org9ee831b"></a>

## EKS     :kubernetes:

Get context from AWS

```bash
aws eks --region us-east-1 update-kubeconfig --name epic-eks-prod --profile epicprod
```

sometimes we might need to rename the context.

```bash
kubectl config rename-context <cluster ARN> <New name>

```


<a id="org8ca4034"></a>

# Docker     :aws:makefile:docker:secretsmanager:


<a id="org78fecd4"></a>

## Repo config

This is what the docker make file should have to download authentication in `.docker` file.

```make
build: config

config: CONFIG = $(call get_secret,usage-artifactory-docker-creds)
config:
	@printf '%s' '$(CONFIG)' | base64 --decode > config.json
```


<a id="orge103e26"></a>

### Add docker auth to `.docker`

```bash
 aws secretsmanager get-secret-value --secret-id <secrets ARN> --query SecretBinary --output text | base64 -D >> conf.json
```


<a id="org2c5051d"></a>

## Tag and push

```shell
docker tag counter-data-service docker-dp-analytics-usage-snapshots-virtual.rt.artifactory.tio.systems/counter-data-service:0.2.52
docker push docker-dp-analytics-usage-snapshots-virtual.rt.artifactory.tio.systems/counter-data-service:0.2.52
```


<a id="orgb215b91"></a>

## Runing an image

Run an image that's on the repo

```shell
docker run --rm -p 8888:8080 docker-dp-analytics-usage-snapshots-virtual.rt.artifactory.tio.systems/counter-data-service:0.2.47

```

Run interactivly local image

```bash
docker run -it counter-data-service:latest
```


<a id="orgc0f18cc"></a>

## SSH to running local image

```shell
docker exec -it b71133dc40a8  bash
docker exec -it 524e40b0fe30  sh
```


<a id="org83ed00c"></a>

# Git


<a id="orgc09b8cd"></a>

## Removing all tags on local and on remote

1 - Delete all local tags (Optional Recomended)

```
git tag -d $(git tag -l)
```

2 - Fetch remote All tags (Optional Recomeded)

```
git fetch
```

3 - Delete All remote tags

```
git push origin --delete $(git tag -l) # Pushing once should be faster than multiple times
```

4 - Delete local tags

```
git tag -d $(git tag -l)
```
