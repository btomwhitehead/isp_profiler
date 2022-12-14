# ISP Profiler

After being repeatedly gaslit by my ISP, it was time to gather data of my home fibre
network's performance.

The system makes use of several Docker containers to gather, store and graph
timeseries network data. It consists of:

1. [Prometheus](https://prometheus.io/)
2. [Grafana](https://grafana.com/)
3. [speedtest exporter](https://github.com/MiguelNdeCarvalho/speedtest-exporter): 
   A Prometheus exporter using [Ookla Speedtest](https://speedtest.net)
4. [smokeping exporter](https://github.com/SuperQ/smokeping_prober): A smokeping implementation
   for Prometheus and Grafana used to visualise packet loss
5. [Replacing smokeping with Prometheus](https://anarc.at/blog/2020-06-04-replacing-smokeping-prometheus/):
   A [Prometheus blackbox exporter](https://github.com/prometheus/blackbox_exporter)
   implementation of Smokeping

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

Three job configurations for the speedtest exporter, the smokeping exporter and Prometheus itself.

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
[do not support variables for connection names](https://github.com/grafana/grafana/issues/10786)
and so all connection names are hardcoded to `prometheus` to be consistent
with the provisioned connection name.

The default account credentials have not been changed and are username `admin`
and password `admin`.

### Exporters

#### Speetest exporter

The speedtest exporter has no config and the Ookla Speedtest integer server ID,
`SPEEDTEST_SERVER`is set via an environment variable in
[docker-compose.yml](./docker-compose.yml). This should be adjusted to a speedtest server of interest using
and the server ID can be found using [this guide](https://www.dcmembers.com/skwire/how-to-find-a-speedtest-net-server-id/).

#### Smokeping exporter

The smokeping exporter is configured using the [smokeping config](./config/smokeping_exporter.yml).
The `targets.hosts` list should be updated to include endpoints of interest.

### Prometheus blackbox exporter ICMP implementation

The blackbox exporter is, at present, configured in the [prometheus config](./config/prometheus.yml).
The default blackbox exporter configuration is used. The `targets` list should be updated to
include endpoints of interest.

## Sceenshots

![Speedtest dashboard](./images/speedtest.png "Speedtest dashboard")
![Smokeping dashboard](./images/smokeping.png "Smokeping dashboard")
![ICMP dashboard](./images/icmp.png "ICMP dashboard")
