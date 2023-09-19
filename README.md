## What is EazyRAG?

EazyRAG is a Dead simple API for Retrieval Augmented Generation, In simple words its like Algolia for Retrieval Augmented Generation. Our API overcomes intricate vector DB challenges, enabling accurate answer generation and seamless citations.

## How to use EazyRAG?

We have three simple endpoints for you to use.

#### 1. Add/Index Content
#### 2. Generate Answer
#### 3. Retrieve relevant chunks (Soon)

Here is a example of how to use our API.

### 1. Add/Index Content
``` 
curl --location 'https://api.eazyrag.com/v1/add' \
--header 'Authorization: <Your token>' \
--header 'Content-Type: application/json' \
--data '{
 "contents": [
   {
       "content":"Bun 1.0 was released on September 8, 2023. (This could be the entire document)",
       "title":"Bun",
       "meta": {
         "id": "1",
         "author":"Jarred Sumner"
       }
   }
 ],
 "collection": "example"
}'
```
👇
**Output**
```
{
    "message":"Success",
    "suggestion":null
}
```

**Parameters in table below**

| Parameter | Type | Description |
| :--- | :--- | :--- |
| contents | array | Array of content objects |
| collection | string | Name of the collection |
| content | string | Content of the document, You can add entire document in one string :) |
| title | string | Title of the document (optional)|
| meta | object | Metadata of the document (optional): You can add any keys inside the metadata that are useful for filtering in the answer endpoint|

### 2. Answer

```
curl --location 'https://api.eazyrag.com/v1/answer' \
--header 'Authorization: <Your token>' \
--header 'Content-Type: application/json' \
--data '{
    "query":"When was Bun released?",
    "collection": "example"
}'
```
👇
**Output**
```
{
  "answer": "Bun was released on September 8, 2023 [1].",
  "citations": [
    {
      "citation_number": 1,
      "meta": {
        "type": "title", // If a title is present, then the entire document is added as context into llm.
        "parent_uuid": "47ea1047-3c65-427d-89d7-194f7cada753",
        "id": "1",
        "text_content": "Bun",
        "author": "Jarred Sumner",
        "chunk_uuid": "de181f04-cef7-44b5-8e32-aa2c2ff5201d",
        "parent_token_count": 19
      },
      "relevance": 0.88854927
    }
  ]
}
```
**Answer with filter**
```
curl --location 'https://api.eazyrag.com/v1/answer' \
--header 'Authorization: <Your token>' \
--header 'Content-Type: application/json' \
--data '{
    "query":"When was Bun released?",
    "collection": "example",
    "filter":{
       "id":{
           "$match":"1"
         }
     }
}'
```
**Answer with stream**
```
curl --location 'https://api.eazyrag.com/v1/answer' \
--header 'Authorization: <Your token>' \
--header 'Content-Type: application/json' \
--data '{
    "query":"When was Bun released?",
    "collection": "example",
    "stream":true
}'
```

**Answer generation in browser**

We also support answer generation in the browser, but you should use a client-side key which starts with 'ck-<Your client-side key>'. You can find your client-side key in your dashboard.
https://eazyrag.com/dashboard/tokens

```
var myHeaders = new Headers();
myHeaders.append("Authorization", "ck-<Your client side key>");

var requestOptions = {
  method: 'GET',
  headers: myHeaders,
};

fetch("https://api.eazyrag.com/v1/answer?query=What is bun?&collection=bun_docs&stream=false", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| query | string | User question
| collection | string | Name of the collection |
| filter | object | Filter the chunks or documents based on metadata. You can use any key from the metadata that you already used while indexing the content (Optional)| 
| stream | boolean | If true, the answer will be streamed as soon as it is available (Optional). The stream will end with chunk "[DONE]"|
| $match | <ul> <li>string</li> <li>number</li> <li>string[]</li> <li>number[]</li> <ul>| Match the value of the key |

### 3. Retrieve relevant chunks (Soon)

We are working on it, Stay tuned!