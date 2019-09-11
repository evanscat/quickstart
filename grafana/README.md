#grafana

###search and pull grafana image

```
docker search grafana
```

###create and run container

```shell
docker run -d -p 3000:3000 --name=grafana -v $PWD/data/grafana:/var/lib/grafana grafana/grafana
```

###copy config from Grafana container,rewrite it and overwrite orginal config back

```shell
docker cp grafana:/etc/grafana/grafana.ini .
vim grafana.ini
docker cp ./grafana.ini grafana:/etc/grafana/
```

###default user/password can be find in Grafana.ini(admin/admin)

###copy all Grafana data from a container

```shell
docker cp grafana:/var/lib/grafana .
docker cp grafana:/etc/grafana/grafana.ini .
```

### templates

