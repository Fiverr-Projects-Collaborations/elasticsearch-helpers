<h3>1. Creating Data Stream in Elasticsearch</h3>
<p>For Creating a data steam in elasticsearch, we need to create:</p>
<ul>
  <li>Index Lifecycle Policy</li>
  <li>Index Template</li>
  <li>Data Stream</li>
</ul>

<h4>1.1 Creating Index Lifecycle Policy</h4>
<pre>
<code>
PUT _ilm/policy/my-stream-policy
  {
    "policy": {
      "phases": {
        "hot": {
          "min_age": "0ms",
          "actions": {
            "rollover": {
              "max_age": "1d"
            },
            "set_priority": {
              "priority": 100
            }
          }
        }
      }
    }
  }
</code>
</pre>


<h4>1.2 Creating Index Template</h4>
<pre>
<code>
PUT _index_template/my-stream-template
{
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "is_quote_status": {
          "type": "keyword"
        },
        "org_tweet_date": {
          "type": "date"
        },
        "retweeted": {
          "type": "keyword"
        }
      }
    }
  },
  "index_patterns": [
    "my-stream*"
  ],
  "data_stream": {}
}
</code>
</pre>

<h4>1.3 Creating Data Stream</h4>
<p>We can create data stream by inserting data to the index pattern mentioned in the index template.</p>
<pre>
<code>
PUT my-stream/_create/123
{
  "field1": "value1"
}
</code>
</pre>

<p>This can also be done using bulk api.</p>
<pre>
<code>
PUT my-stream/_bulk
{ "create" : {"_id": "1"}} }
{ "field1": "value1" }
{ "create" : {"_id": "2"}} }
{ "field1": "value2" }
</code>
</pre>
<b>NOTE:</b> Only "create" would work for data stream and not "index".

References:
<br/>
https://www.elastic.co/guide/en/elasticsearch/reference/master/set-up-a-data-stream.html#create-a-data-stream-template
https://www.elastic.co/guide/en/elasticsearch/reference/current/index-lifecycle-management.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/index-templates.html
