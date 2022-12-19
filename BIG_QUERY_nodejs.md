```javascript
'use strict';

function main() {

    const projectId = 'exampleprojectid';
    const datasetId = 'exampleprojectid_dataset';
    const tableId = 'animals';
    const datasetLocation = 'asia-southeast2';

  // [START bigquery_client_json_credentials]
  // Create a BigQuery client explicitly using service account credentials.
  // by specifying the private key file.
  const {BigQuery} = require('@google-cloud/bigquery');

  const bogQueryOptions = {
    keyFilename: 'myc_sa.json',
    projectId:  projectId,
  };

  const bigquery = new BigQuery(bogQueryOptions);
  // [END bigquery_client_json_credentials]

  async function insert() {
    const rows = [
        {Name: 'Tom', Age: 30, Weight: 78, IsMagic: true},
        {Name: 'Jane', Age: 22, Weight: 56, IsMagic: true},
        {Name: 'Bonny', Age: 30, Weight: 78, IsMagic: false}
      ];
  
      // Insert data into a table
      await bigquery
        .dataset(datasetId)
        .table(tableId)
        .insert(rows);
      console.log(`Inserted ${rows.length} rows`);
  }

  async function query() {

    const query = `SELECT name
      FROM \`${projectId}.${datasetId}.${tableId}\`
      WHERE IsMagic = true
      LIMIT 100`;

    // For all options, see https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs/query
    const options = {
      query: query,
      // Location must match that of the dataset(s) referenced in the query.
      location: datasetLocation,
    };

    // Run the query as a job
    const [job] = await bigquery.createQueryJob(options);
    console.log(`Job ${job.id} started.`);

    // Wait for the query to finish
    const [rows] = await job.getQueryResults();

    // Print the results
    console.log('Rows:');
    rows.forEach(row => console.log(row));
  }

  async function createTable() {
    const schema = [
        {name: 'Name', type: 'STRING', mode: 'REQUIRED'},
        {name: 'Age', type: 'INTEGER'},
        {name: 'Weight', type: 'FLOAT'},
        {name: 'IsMagic', type: 'BOOLEAN'},
    ]

    // Creates a new table named "my_table" in "my_dataset".

    /**
     * TODO(developer): Uncomment the following lines before running the sample.
     */
    // const datasetId = "my_dataset";
    // const tableId = "my_table";
    // const schema = 'Name:string, Age:integer, Weight:float, IsMagic:boolean';

    // For all options, see https://cloud.google.com/bigquery/docs/reference/v2/tables#resource
    const options = {
      schema: schema,
      location: datasetLocation,
    };

    // Create a new table in the dataset
    const [table] = await bigquery
      .dataset(datasetId)
      .createTable(tableId, options);

    console.log(`Table ${table.id} created.`);
  }

    // createTable();

    // insert();

    query();
}

main(...process.argv.slice(2));
```
