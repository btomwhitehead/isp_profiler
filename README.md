# ISP Profiler

After being repeatedly gaslit by my ISP, it was time to gather data of my home fibre
network's performance.https://github.com/grafana/grafana/issues/10786

The system makes use of several Docker containers to gather, store and graph
timeseries network data. It consists of:

1. [Prometheus](https://prometheus.io/)
2. [Grafana](https://grafana.com/)
3. [speedtest exporter](https://github.com/MiguelNdeCarvalho/speedtest-exporter): 
   A Prometheus probe using [Ookla Speedtest](https://speedtest.net)
4. [smokeping prober](https://github.com/SuperQ/smokeping_prober): A smokeping implementation
   for Prometheus and Grafana

## Usage

Run:

```
docker compose up -d
```

The Prometheus service will be available at [localhost:9090](http://localhost:9090)
and the Grafana service will be available at [localhost:3000](http://localhost:3000).

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

Grafana is configured using the
[provisioning](https://grafana.com/docs/grafana/latest/administration/provisioning/)
system.

The connection to prometheus is configured using the
[provisioning datasources](./config/grafana/provisioning/datasources/datasources.yaml)
file.

Dashboards located in the 
[provisioning dashboards directory](./config/grafana/provisioning/dashboards/)
are automatically loaded. Dashboards that are created via this process
do not support variables for connection names and so all connection names are
hardcoded to `prometheus` to be consistent with the provisioned connection name.

### Probes

#### Speetest probe

The speedtest probe has no config and the Ookla Speedtest integer server ID,
`SPEEDTEST_SERVER`is set via an environment variable in
[docker-compose.yml](./docker-compose.yml). This should be adjusted to a speedtest server of interest.

#### Smokeping probe

The smokeping probe is configured using the [smokeping config](./config/smokeping_probe.yml).
The `targets.hosts` list should be updated to include endpoints of interest.
