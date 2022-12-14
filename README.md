# heartbeat

A long-running python script that writes heartbeat logs to Cloud Logging on a schedule. The idea is to setup a [log-based alert](https://cloud.google.com/logging/docs/alerting/log-based-alerts) that will fire if no heartbeat log is received in a certain period of time.

Also creates a local directory called `logs` in the project root and logs to a file.

## Getting Started

Copy google cloud service account private key file to the project root as "gcloud-logger-key.json". The only required permission is `logging.write`.

Create a virtual env:

```
python -m venv env
```

* NOTE: The virtual env directory must be called "env" as this is hard coded into the batch file

Install dependencies (in the virtual env):

```
pip install -r requirements.txt
```

See usage:

```
> python heartbeat.py --help
usage: heartbeat.py [-h] [--interval INTERVAL] service_account_file

Write heartbeat log to google cloud on an interval

positional arguments:
  service_account_file  path/to/service_account_file.json

options:
  -h, --help            show this help message and exit
  --interval INTERVAL   Write log every interval seconds
```

## Windows Service

The python script is meant to be run as a Windows service using [NSSM](https://nssm.cc). The NSSM binary must be globally available on the system (in PATH).

To register and start windows service:

1. Right click on `setup_service.bat` and click "Run as Administrator".

2. Verify that service is running: `nssm status heartbeat` should return `SERVICE_RUNNING`

### Troubleshooting

If there are any issues getting the service started.

NSSM logs to Windows System Event Log so you can view logs in Event Viewer > Event Viewer (Local) > Windows Logs > Application then look for source=nssm.

You can redirect stdout and stderr of the service to a file, which may provide more insight into the issue. To do so, run `nssm edit heartbeat`, go to the "I/O" tab, and set the "Output (stdout)" to a file. This will automatically set stderr to the same file as well. Then try to start the service again, and the log file should populate.
