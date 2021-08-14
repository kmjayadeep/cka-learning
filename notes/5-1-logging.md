# Cluster and node logging

kuebctl logs command is used to retrieve logs from a running pod

Kubernetes captures stdout and stderr logs to a log file. This is what
retrieved using kubectl logs command

In docker, this is in `/var/log/containers` folder. It contains the log
file for each container. there is another folder `/var/logs/pod` which
categorizes log files at pod level. The files in container is just a
symlink to what is inside pod folder

Use `journalctl` to access systemd log.

Cluster components are logged into systemd, as they are usually not run
as containers (for eg: kubelet)

use `k get events` to check events happening in the cluster. It shows
errors and all something goes wrong

There is something called kubernetes audit logging which is used to log
all kind of operations at api server level.

Fluentd, ELK and slackdriver (google) are used for centralized logging

For containers which doesn't expose logs to stdout.. for example, if the
container is writing logs to a file, it is a common pattern to add a
sidecar and expose the logs using tail -f on stdout
