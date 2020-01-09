The elkctl tool allows Elasticsearch users to search their logs like
proper IT guys: from the command line.  It provides a slightly journaldctl-like
experience (in a good way) and will hopefully help to cure Tourette-like
outbursts that are so common amongst admins who decide to send all their logs
to an ELK and then realise that they have to search them from a shitty web
interface.

## Quickstart

1 .Install requirements:

```
sudo apt install python3 python3-elasticsearch
```

or 

```
pip3 install elasticsearch
```

2. Copy the example`elkctl.conf` from the repo to `~/.confg/`

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

## Ad-hoc commands and filters
`elkctl` can run ad-hoc commands or predefined filters. Ad-hoc commands are useful for simple queries, but also less powerful and convenient than pre-defined filters. 

### Ad-hoc commands

Ad-hoc commands can be useful for queries like the following:

Show all logs from servers `spam00` and `eggs00`:

```
elkctl host=spam00,host=eggs00
```

Show all logs from the `silly-walks` Docker container:

```
elkctl container_name=/silly-walks
```

### Filters

Filters are defined in the `elkctl.conf`.

```
      {                                                                                                                                                       
          "name": "breakfast",                                                                                                                                
          "desc": "Show example logs from hosts spam00 and eggs00",                                                                                           
          "query":                                                                                                                                            
                {                                                                                                                                             
                  "query": {                                                                                                                                  
                    "bool": {                                                                                                                                 
                      "should": [                                                                                                                             
                        {                                                                                                                                     
                          "match_phrase": {                                                                                                                   
                            "host": "spam00"                                                                                                                  
                          }                                                                                                                                   
                        },                                                                                                                                    
                        {                                                                                                                                     
                          "match_phrase": {                                                                                                                   
                            "host": "eggs00"                                                                                                                  
                          }                                                                                                                                   
                        }                                                                                                                                     
                      ],                                                                                                                                      
                      "minimum_should_match": 1                                                                                                               
                    }                                                                                                                                         
                  }                                                                                                                                           
                }                                                                                                                                             
      }
```      

The `query` part of the filter definition can be any valid query in [Elasticsearch's Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

Afterwards, all you need to do is to run `elkctl breakfast` to execute the query. 

## Times 

The default behaviour is to show the logs of the last 5 minutes. The `-s --since` and `-u --until` options allow to specifiy time ranges as required.

 - The logs for the last hour: `elkctl -s 'now-1h' ...`
 - The logs for the last 30 minutes: `elkctl -s 'now-30m' ...`
 - All logs since October 26: `elkctl -s '2019-10-26' ...`
 - All logs from October 26 to October 27: `elkctl -s 2019-10-26 -u 2019-10-27 ...`
 - All logs from October 26 between 4 and 5am: `elkctl -s 2019-10-26T04:00 -u 2019-10-26T05:00 ...`

See the [official Elasticsearch documention](https://www.elastic.co/guide/en/elasticsearch/reference/master/common-options.html#date-math) for more details.

## Tailing logs

Simply add `-f`  or `--follow` to your query and `elkctl` will query Elasticsearch every second. You can change the query interval with the `-i --interval` setting.

## Output fields

The default behaviour is to show only the following fields in the output, which is what most people want to see: `host,log,message`

If that isn't sufficient, simply specify the output fields with the `-o --output` option. It's also possible to show all logs with `-a --all`.

## Debug

`-d --debug` shows the exact query `elkctl` sends to Elasticsearch. This is useful when you get unexpected or empty results. 
