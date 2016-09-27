# DockerRegistryWithSelfSigning

1. Generate self certificate
```
mkdir -p certs && openssql req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
```

2. Generate password
```
mkdir auth
docker run --entrypoint htpasswd registry:2 -Bbn <userId> <userpassword> > auth/htpasswd
```

3. Run docker registry
```
docker run -d -p 5000:5000 --restart=always --name registry \
  -v `pwd`/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v `pwd`/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

4. Copy self certificate
```
cp /path/../../domain.crt /etc/docker/certs.d/domain:5000/domain.crt
```

5. Restart docker
```
service docker restart
```

6. Docker login
```
docker login domain:5000
```



