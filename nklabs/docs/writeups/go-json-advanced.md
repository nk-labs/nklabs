## `UseCase`: 

Have a known given set of json keys say wantKeys. wantKeys := []string{"name", "age"} . Task is to check keys in a target object against "wantKeys". If they are same, it means received data is per our expectation, otherwise we got unexpected data.  

```
givenKeys := []string{"name", "age"}
	var receivedKeys []string
	
	// Assume the data we are getting is in []byte format. Create a test data. 
	
	testDataByte := []byte(`{"name": "john", "age":55}`)

	
	// Note: We expect our data may be different, so we can't define struct with fieldnames and unmarshal. 
	// Let's use empty interface to deal our arbitrary data.
	
	var data interface{}
	
	json.Unmarshal(testDataByte, &data)
	
	// Actual fun starts! 
	// How do I get all the keys from this data to compare with given keys?
	
	// Use type assertion to access data's map[string]interface{}

	m := data.(map[string]interface{}) 
	
	for k, _ := range m {
	  receivedKeys = append(receivedKeys, k)
	}
	
	if len(givenKeys) != len(receivedKeys) {
	  fmt.Printf("got number of keys in response %d, but want %d", len(receivedKeys), len(givenKeys))
	}
	
	sort.Strings(givenKeys)
	sort.Strings(receivedKeys)
	
	if sameKeys := reflect.DeepEqual(givenKeys, receivedKeys); !sameKeys {
          fmt.Printf("got keys in response %s, but want %s", receivedKeys, givenKeys)
	}
	
	fmt.Println("Yes receivedKeys and givenKeys are exactly same!")

```

<font color="olive"> Go Playground link </font>
[https://play.golang.org/p/uByO3tR1qs9]( https://play.golang.org/p/uByO3tR1qs9)


<b> Reference </b>
- https://blog.golang.org/json-and-go
