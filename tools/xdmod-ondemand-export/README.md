# xdmod-ondemand-export
Python script for parsing [Open OnDemand](https://openondemand.org/) Apache access logs and sending them via POST requests to an HTTPS endpoint on a web server for inclusion in [XDMoD](https://open.xdmod.org).

## Installation
The steps below should be run on the system with the Open OnDemand Apache access log files by a user that has read access to the log files. If you are on Ubuntu, the first two commands will also need to be run with sudo or as root.

### If you are on Ubuntu
If you are on Ubuntu, run the two commands below with sudo or as root to install the Python virtual environment package. If you are not on Ubuntu, these two commands can be skipped.
```
# python_version=$(python3 --version | awk '{print $2}' | cut -d'.' -f1-2)
# apt install python${python_version}-venv
```

### Create and activate a Python virtual environment
```
$ env_dir="${HOME}/xdmod-ondemand-export/env"
$ python3 -m venv ${env_dir}
$ source ${env_dir}/bin/activate
```

### Install the package
```
(env) $ python3 -m pip install git+https://github.com/ubccr/xdmod-ondemand#subdirectory=tools/xdmod-ondemand-export
(env) $ package_dir="${env_dir}/lib/python3.*/site-packages/xdmod_ondemand_export"
```

### Deactivate the virtual environment
```
(env) $ deactivate
```

### Set file permissions
The Python script is run from a Bash script; this Bash script sets your API token as an environment variable. Set the file permissions on the Bash script such that the script is executable by your user and such that others cannot read, write, or execute it:
```
$ chmod 700 ${package_dir}/xdmod-ondemand-export.sh
```

The Python script reads and writes to a configuration file as it runs. One of the values written is the last line of the last log file that was successfully POSTed. Because this line contains sensitive information (mapping of username to IP address), set the configuration file to be readable and writable only by your user.
```
$ chmod 600 ${package_dir}/conf.ini
```

### Set the API token
Obtain your API token from the admins of the web server. For ACCESS XDMoD, this is the [XDMoD team](mailto:ccr-xdmod-help@buffalo.edu).

Edit the script at `${package_dir}/xdmod-ondemand-export.sh` to set the value of the `XDMOD_ONDEMAND_EXPORT_TOKEN` environment variable to be the value of the token.

### Possibly edit the configuration file
The configuration file at `${package_dir}/conf.ini` will be read and written by the Python script as it runs.

The default values assume that the logs will be POSTed to `https://data.ccr.xdmod.org/ondemand/logs`, that they are located on your machine at `/etc/httpd/logs`, that the files to process match the filename pattern `*access*.log*`, that the LogFormat of the files is the Combined Log Format, `%h %l %u %t "%r" %>s %b "%{Referer}i" "%{User-Agent}i"`, and that the files are not gzip compressed.

If any of these values are different for your configuration of Open OnDemand, change them in the file `${package_dir}/conf.ini`.

By default, all files matching the filename pattern will be processed. For finer control over which files should be processed, set the values of `filename_pattern` and `last_request_time` accordingly. See the instructions in `${package_dir}/conf.ini` for how the value of `last_request_time` is used.

### Check the configuration
Run the Bash script in check-config mode to make sure there are no warnings or errors; this will check the file permissions, configuration script, and that the API token environment variable exists, but it will not attempt to parse or POST any log files.
```
$ ${package_dir}/xdmod-ondemand-export.sh --check-config -l INFO
```

### Create a cron job to run the script daily
Open your user's crontab:
```
$ crontab -e
```
Add the following line, replacing `${package_dir}` with its expanded value (the result of running `$ echo ${package_dir}`. This will set up the script to run daily at 2:01am.
```
1 2 * * * ${package_dir}/xdmod-ondemand-export.sh
```
An example of the expanded value for the root user in Python 3.6 is:
```
1 2 * * * /root/xdmod-ondemand-export/env/lib/python3.6/site-packages/xdmod_ondemand_export/xdmod-ondemand-export.sh
```

## Troubleshooting
The `xdmod-ondemand-export.sh` script can be run with any of the following log levels by passing it the `-l` or `--log-level` option:
* `DEBUG`
* `INFO`
* `WARNING` (the default)
* `ERROR`

Contact the [XDMoD team](mailto:ccr-xdmod-help@buffalo.edu) for additional assistance.
