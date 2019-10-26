The elkctl tool allows Elasticsearch users to search their logs like
proper IT guys: from the command line.  It provides a slightly journaldctl-like
experience (in a good way) and will hopefully also a cure for Tourette-like
outbursts that are so common amongst admins who decide to send all their logs
to an ELK and then realise that they have to search them from a shitty web
interface.

## Quickstart

1 .Install requirements:

```
sudo apt install python3 python3-elasticsearch
```

2. Copy `elkctl.conf` to `~/.confg/`

3. Configure the hostname and, if required, login data in `~/.config/elkctl.conf`

4. Execute `elkctl`; you should see the following output:

```

ERROR: No filter provided

Provide a filter from the list:

Name     | Description
-------------------------------------------------
example1 | Show example logs from host1 and host2
example2 | Show all docker container logs

Or define an ad-hoc filter in the following format:
field=value1,field=value2,...

Run ./elkctl -h for more help

```

5. Run an ad-hoc query

```
elkctl host=myhost
```

Replace `myhost` with a host that sends its logs to your Elasticsearch and you should see logs.

