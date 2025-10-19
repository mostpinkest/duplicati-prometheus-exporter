# Duplicati prometheus exporter
This is an simple exporter made for [Duplicati backup](https://duplicati.com).

## How it works
On your Duplicati backup interface or cli, you can configure in advanced options parameters for `"send-http"`, with this configuration, Duplicati will send an post request to this exporter; so then exporter will gather metrics receved and expose a /metrics for `prometheus`.

```bash
# Cli configuration example
--send-http-url=http://<duplicati-exporter-instance>:5000/
--send-http-result-output-format=Json
--send-http-any-operation=true
```

## Setup demo
For this demo is needed to be installed docker in your machine. The demo sets up the duplicati exporter, prometheus and grafana. There is also a configuration container that configures grafana's datasource and dashboard, and then send an example post to the duplicati exporter. You can see the configuration in the [docker compose file](docker-compose.yml). 
- In repository root, run:
```sh
docker compose up --force-recreate --build --always-recreate-deps
```
- After ~15 seconds the address `http://localhost:3000` should be ready. Go to [Grafana in your localhost](http://localhost:3000/d/ddmio2e27ctmod/duplicati-backup-dashboard)

![Grafana dashboard example](https://github.com/mostpinkest/duplicati-prometheus-exporter/raw/main/docs/static/grafan-dash.png)

~This dashboard can change while i'm developing and improving. It's current vesion can be found [here](https://github.com/mostpinkest/duplicati-prometheus-exporter/tree/main/config/grafana).

## Prometheus scrap config example
```yaml
global:
  scrape_interval: 60s
  evaluation_interval: 30s
scrape_configs:
- job_name: duplicati_backup
  honor_labels: true
  static_configs:
  - targets: ['duplicati-prometheus-exporter:5000'] # you can change to set your exporter instance
```

## Run duplicati prometheus exporter using docker
Docker is the better way to execute this application. If you prefer, you can build your own
container image based on my [Dockerfile](Dockerfile) to change anythong you want and store your
container artifact where you need; but you also can use my docker image that is on my [dockerhub](https://hub.docker.com/repository/docker/mostpinkest/duplicati-prometheus-exporter/general)

- Running docker image
```sh
docker run -p 5000:5000 mostpinkest/duplicati-prometheus-exporter
```

- After container run successfully you can access http://localhost:5000/metrics.

## Running with python 
For this you have to install python3.9 or higher. You can change service port by setting a environment variable `DUPLICATI_EXPORTER_PORT=PORT`, default is 5000.

```bash
# In repository root, create an python venv
python -m venv .venv

# Then activate this venv
# Linux
$ source .venv/bin/activate 
# Window
> C:\ .venv\Scripts\activate.bat

# Install python packages required
$ pip install -r requirements.txt

# Finally
$ python duplicat-prometheus-exporter
```

## DEBUG
Debug can be done by using LOG_LEVEL environment variable, supported values:
`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`.

## PULL REQUESTS
Hello everyone, i'm developing from scratch and I have no much experience with prometheus library, feel free to send any issues or open any pull requests to improve the source code. Any help is welcome (:

