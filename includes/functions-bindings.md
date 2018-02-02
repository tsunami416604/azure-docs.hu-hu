Az alábbi táblázat az Azure Functions futtatókörnyezettel két fő verziói által támogatott kötéseket.

| Típus | 1.x | 2.x | Eseményindító | Input (Bemenet) | Kimenet |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Esemény rács](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Külső fájl](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [A külső tábla](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [A Microsoft Graph<br/>Excel-táblázatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔<sup>1</sup>| |✔|✔|
| [A Microsoft Graph<br/>OneDrive-fájlokhoz](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔<sup>1</sup>| |✔|✔|
| [A Microsoft Graph<br/>Outlook e-mail](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔<sup>1</sup>| | |✔|
| [A Microsoft Graph<br/>események](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔<sup>1</sup>|✔|✔|✔|
| [A Microsoft Graph<br/>hitelesítési jogkivonatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔<sup>1</sup>| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔<sup>1</sup>| |✔|✔|  
| [Értesítési központ](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔<sup>1</sup>| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔<sup>1</sup>|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Időzítő](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔<sup>1</sup>| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> 2.x a kötés bővítményként regisztrálni kell. Lásd: [ismert problémák a 2.x](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

<sup>2</sup> kísérleti &mdash; nem támogatott, és előfordulhat, hogy a jövőben elhagyásra kerül.

