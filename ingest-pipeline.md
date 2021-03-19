<h3>Ingest Pipeline in Elasticsearch</h3>

Creating Ingest pipeline requires various processors to be defined.
<pre>
<code>
PUT _ingest/pipeline/pipeline_1
{
  "processors": [
    {
      "remove": {
        "ignore_missing": true,
        "field": [
          "contributors",
          "coordinates",
          "display_text_range",
          "entities.media"
        ]
      }
    },
    {
      "script": {
        "lang": "painless",
        "source": "some script",
        "description": "companies"
       }
    }
   ]
}
</code>
</pre>

After creating the pipeline, we have to use it as a parameter while loading data to our index
<pre>
<code>
POST:
index/_doc/123?pipeline=pipeline_1
{
  "field1" : "value1"
}
</code>
</pre>

We can even setup enrich pipeline, where the value can be mapped from a different enrich index.
This involves following steps:
<ul>
  <li>Creating enrich index that has some key/values</li>
  <li>Creating & Executing enrich policy</li>
  <li>Creating pipeline with enrich processor</li>
  <li>Ingest and enrich data/documents</li>
</ul>

<b>Creating enrich index that has some key/values</b>
<pre>
<code>
POST:
enrich_index/_doc/123
{
  "key" : "1",
  "field1" : "value1",
  "field2" : "value2",
  "field3" : "value3"
}
</code>
</pre>

<b>Creating & Executing enrich policy</b>
<pre>
<code>
 PUT:
_enrich/policy/my-enrich-policy
{
  "match": {
  "indices": "enrich_index",
  "match_field": "key",
  "enrich_fields": ["value1", "value2"]
  }
}
</code>
</pre>

<pre>
<code>
 POST:
_enrich/policy/my-enrich-policy/_execute
</code>
</pre>

<b>Creating pipeline with enrich processor</b>
<pre>
<code>
PUT _ingest/pipeline/enrich_pipeline
{
  "processors": [
    {
      "enrich": {
        "policy_name": "my-enrich-policy",
        "field": "new_index_key",
        "target_field": "enriched_field",
        "shape_relation": "INTERSECTS"
      }
    }
  ]
}
</code>
</pre>

<b>Ingest and enrich data/documents</b>
<pre>
<code>
POST:
enrich_index/_doc/123?pipeline=enrich_pipeline
{
  "key" : "1",
  "value" : "value1"
}
</code>
</pre>

References:
<br/>
https://www.elastic.co/guide/en/elasticsearch/reference/master/ingest.html
https://www.elastic.co/guide/en/elasticsearch/reference/master/enrich-setup.html
