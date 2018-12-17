**********
docker run --volume="$(pwd)/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml" --volume="$(pwd)/filebeat/results:/jmeter/results" docker.elastic.co/beats/filebeat:6.5.3
**********
on linux:
modify /etc/sysctl.conf, and the following
    vm.max_map_count=262144
**********
on macos:
screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
return
// now in linuxkit-025000000001:~#
sysctl -w vm.max_map_count=262144
// exit screen
ctrl+A ctrl+\
**********
// run the "docker-elk-cluster"
docker-compose up
**********
// sample jmeter jtl
timeStamp,elapsed,label,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect
2018/12/06 02:54:08,50,markets,200,OK,Thread Group 1-1,text,true,,534,197,1,1,50,0,37
**********
// elasticsearch index template
PUT _template/jmeter_template
{
  "index_patterns": [
    "jmeter_*"
  ],
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 0
  },
  "mappings": {
    "_doc": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "elapsed": {
          "type": "long"
        },
        "requestURL": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "responseCode": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "responseMessage": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "threadName": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "dataType": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },        
        "success": {
          "type": "boolean"
        },
        "failureMessage": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "bytes": {
          "type": "long"
        },
        "sentBytes": {
          "type": "long"
        },
        "grpThreads": {
          "type": "long"
        },
        "allThreads": {
          "type": "long"
        },
        "Latency": {
          "type": "long"
        },
        "IdleTime": {
          "type": "long"
        },
        "Connect": {
          "type": "long"
        }
      }
    }
  }
}
**********