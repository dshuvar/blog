---
published: true
categories: work
tags: docker
date: '2017-09-15 20:08:00 +0300'
---
## A New Friday pain Post

Hello.
Today I has knows some litle pain about docker and improve self attitude to docker stack.

If you break your container for example make new change in config service and reload container after and   see status of then as infinitylly restarting: `Restarting (1) 5 seconds ago, ``Restarting (1) 42 seconds ago` and feel worry - first keep calm.


What happend with my container:

```
sudo docker exec -it my_favorite_container vi /my_favorite_container-config/config.yml
sudo docker restart my_favorite_container
```

But something was wrong.
Opps, i made mistake in my configutration file and now my container restarts again and again.

I can saw it in log-files:

```
sudo docker logs --tail 5 my_favorite_container
time="2017-09-15T15:20:03Z" level=info msg="Starting my_favorite_container (version=1.7.2, branch=master, revision=pd1182d29f461c39544f943c822830e1c64cf78b)" source="main.go:75" 
time="2017-09-15T15:20:03Z" level=info msg="Build context (go=go1.7.5, user=root@1a01c5f68840, date=20170210-16:23:28)" source="main.go:76" 
time="2017-09-15T15:20:03Z" level=info msg="Loading configuration file /my_favorite_container-config/config.yml" source="main.go:248" 
time="2017-09-15T15:20:03Z" level=error msg="Error loading config: couldn't load configuration (-config.file=/my_favorite_container-config/config.yml): found multiple scrape configs with job name \"new_server\"" source="main.go:150" 
time="2017-09-15T15:20:05Z" level=info msg="Starting my_favorite_container (version=1.7.2, branch=master, revision=pd1182d29f461c39544f943c822830e1c64cf78b)" source="main.go:75" 

```

I'm find data volumes in directory /var/lib/docker/containers/ID_CONTAINER
And find my configuration file in /var/docker-services/my_favorite_container/config/config.yml
And sure i made rigth changes in that times.
