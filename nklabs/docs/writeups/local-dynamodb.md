#  Setting up dynamodb locally (docker)

References

- docker: https://hub.docker.com/r/amazon/dynamodb-local/
- aws cli: https://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html
- https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/configuring-sdk.html

<h4> Pull and run docker on port 7000 </h4>
`docker pull amazon/dynamodb-local`
`docker run -p 7000:8000 amazon/dynamodb-local`


$ cat ~/.aws/credentials
```
[default]
aws_access_key_id = fakeid
aws_secret_access_key = fakekey
```
$ cat ~/.aws/config
```
[default]
region = x

```
[default]
aws_access_key_id = fakeMyKeyId
aws_secret_access_key = fakeSecret

<h1> AWS CLI Commands </h1>
<b> List Tables </b>
`aws dynamodb list-tables --endpoint-url http://localhost:7000`

<b> Create a table </b>

KeyType - The role of the attribute:
  HASH - partition key
  RANGE - sort key


1- Creating a table MusicCollection with partition field 'Artist' and sort field 'SongTitle'
```
aws dynamodb create-table --table-name MusicCollection --attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=SongTitle,AttributeType=S --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 --endpoint-url http://localhost:7000
```


2- Creating a table Computers with partition field 'Vendor' only
```
aws dynamodb create-table --table-name Computers --attribute-definitions AttributeName=Vendor,AttributeType=S --key-schema AttributeName=Vendor,KeyType=HASH  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 --endpoint-url http://localhost:7000
```


<h3>  Table details </h3>
```
aws dynamodb describe-table --table-name Computers --endpoint-url http://localhost:7000
```





<h1> Dynamodb Go SDK </h1>
$ cat main.go
$ go run main.go

```

package main

import (
  "fmt"

  "github.com/aws/aws-sdk-go/aws"
  "github.com/aws/aws-sdk-go/aws/session"
  "github.com/aws/aws-sdk-go/service/dynamodb"
)

const (
  tablename = "Blogs"
)

func main() {

  ////////////////////////// create table ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  // creating a session with Dynamodb
  awsConfig := aws.Config{
    Endpoint: aws.String("http://localhost:7000"),
  }

  sess := session.Must(session.NewSessionWithOptions(session.Options{
    SharedConfigState: session.SharedConfigEnable,
    Config:            awsConfig,
  }))

  // Create dynamodb service using the above created session
  svc := dynamodb.New(sess, aws.NewConfig())

  // Create table schema
  input := &dynamodb.CreateTableInput{
    AttributeDefinitions: []*dynamodb.AttributeDefinition{
      {
        AttributeName: aws.String("Title"),
        AttributeType: aws.String("S"),
      },
    },
    KeySchema: []*dynamodb.KeySchemaElement{
      {
        AttributeName: aws.String("Title"),
        KeyType:       aws.String("HASH"),
      },
    },
    ProvisionedThroughput: &dynamodb.ProvisionedThroughput{
      ReadCapacityUnits:  aws.Int64(5),
      WriteCapacityUnits: aws.Int64(5),
    },
    TableName: aws.String(tablename),
  }

  result, err := svc.CreateTable(input)
  if err != nil {
    fmt.Println("-------- -----------")
    fmt.Println(err)
  }

  fmt.Println("+++++++++ 1 ++++++++")
  fmt.Println(sess)
  fmt.Println(result)

  ////////////////////////// list tables ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  listInput := &dynamodb.ListTablesInput{}
  tables, err := svc.ListTables(listInput)
  fmt.Println("+++++++++ 2 ++++++++")
  fmt.Println(tables)

  ////////////////////////// insert item ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  putInput := &dynamodb.PutItemInput{
    Item: map[string]*dynamodb.AttributeValue{
      "Title": {
        S: aws.String("No One You Know22"),
      },
    },
    ReturnConsumedCapacity: aws.String("TOTAL"),
    TableName:              aws.String(tablename),
  }

  item, err := svc.PutItem(putInput)
  fmt.Println("+++++++++ 3 ++++++++")
  fmt.Println(item, err)
  ////////////////////////// read all items  ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  params := &dynamodb.ScanInput{
    TableName: aws.String(tablename),
  }
  scanResult, err := svc.Scan(params)
  if err != nil {
    fmt.Errorf("failed to make Query API call, %v", err)
  }

  fmt.Println("+++++++++ 4 ++++++++")
  fmt.Println(scanResult)

  ////////////////////////// read an item  ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  getInput := &dynamodb.GetItemInput{
    Key: map[string]*dynamodb.AttributeValue{
      "Title": {
        S: aws.String("No One You Know"),
      },
    },
    TableName: aws.String(tablename),
  }

  itemQ, err := svc.GetItem(getInput)
  fmt.Println("+++++++++ 5 ++++++++")
  fmt.Println(itemQ)
  ////////////////////////// delete item  ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////

  delItemInput := &dynamodb.DeleteItemInput{
    Key: map[string]*dynamodb.AttributeValue{
      "Title": {
        S: aws.String("No One You Know"),
      },
    },
    TableName: aws.String(tablename),
  }
  delItem, err := svc.DeleteItem(delItemInput)
  fmt.Println("+++++++++ 6 ++++++++")
  fmt.Println(delItem, err)

  ////////////////////////// update item  ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////



var params = {
    TableName:tablename,
    Key:{
        "Title": "No One You Know"
    },
    UpdateExpression: "set info.rating = :r, info.plot=:p, info.actors=:a",
    ExpressionAttributeValues:{
        ":r":5.5,
        ":p":"Everything happens all at once.",
        ":a":["Larry", "Moe", "Curly"]
    },
    ReturnValues:"UPDATED_NEW"
};
  ////////////////////////// delete table  ////////////////////////////////////
  ////////////////////////////////////////////////////////////////////////////
  delTabInput := &dynamodb.DeleteTableInput{
    TableName: aws.String(tablename),
  }
  delTable, err := svc.DeleteTable(delTabInput)

  fmt.Println("+++++++++ 7 ++++++++")
  fmt.Println(delTable, err)
}
```
<h3> Bug Fix </h3>
Note, if you find error like "table x does not exist", then run dynamodb with sharedb turned on.

```
docker run -p 8000:8000 -v $(pwd)/local/dynamodb:/data/ amazon/dynamodb-local -jar DynamoDBLocal.jar -sharedDb -dbPath /data
```

https://forums.aws.amazon.com/message.jspa?messageID=869395




