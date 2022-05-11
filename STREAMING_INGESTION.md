# Streaming Ingestion

## Configurations

We still use the same configurations, but the `host` configuration needs to be changed so that it will point to our Ingestion API.

The `host` configuration will have the following values, depending on which environment you want to ingest data.

- AWS Asia Pacific (Seoul)
  - **ap02.records.in.treasuredata.com**

- AWS Asia Pacific (Tokyo):
  - **ap01.records.in.treasuredata.com**

- AWS East
  - **us01.records.in.treasuredata.com**

- AWS EU
  - **eu01.records.in.treasuredata.com**

Example:

```javascript
  var foo = new Treasure({
    database: 'foo',
    writeKey: 'your_write_only_key',
    host: 'us01.records.in.treasuredata.com'
  });
```