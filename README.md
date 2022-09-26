# ISP Profiler

After being repeatedly gaslit by my ISP, it was time to quantify my home fibre
network's performance.

The system makes use of Docker, Prometheus, Grafana and several probes to gather
timeseries data on my line's performance.

## Usage

Run:

```
docker compose up -d
```

The Prometheus service will be available on [localhost:9090](http://localhost:9090)
and the Grafana service will be available on [localhost:3000](http://localhost:3000).

## Configuration

All containers share a common Docker network, `prometheus-network`.
The Docker network DNS service is used to access each container.
Configs are bind mounted into each container and all state is stored in volumes.
Prometheus and Grafana services are exposed on their default ports.
Should any of the above need to change, they will likely require additional config updates to work.

### Prometheus

All prometheus configuration is set in the [prometheus config](./config/prometheus.yml).
Metrics are set to be scraped every 1 minute by default and the config will be refreshed every 1 mimnute.

Three job configurations for the speedtest probe, the smokeping probe and Prometheus itself.

### Grafana

TODO

### Probes

#### Speetest probe

The speedtest probe has no config and the [Ookla Speedtest](https://speedtest.net) integer
server ID, `SPEEDTEST_SERVER`is set via an environment variable in
[docker-compose.yml](./docker-compose.yml). This should be adjusted to a speedtest server of interest.

#### Smokeping probe

The smokeping probe config is located at [config/smokeping-probe.yml](./config/smokeping_probe.yml).

The `targets.hosts` list should be updated to include endpoints of interest.
