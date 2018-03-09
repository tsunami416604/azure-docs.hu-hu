Az alábbi táblázat az Azure Functions futtatókörnyezettel két fő verziói által támogatott kötéseket.

| Típus | 1.x | 2.x | Eseményindító | Input (Bemenet) | Kimenet |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Esemény rács](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Külső fájl](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [A külső tábla](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [A Microsoft Graph<br/>Excel-táblázatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [A Microsoft Graph<br/>OneDrive-fájlokhoz](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [A Microsoft Graph<br/>Outlook e-mail](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [A Microsoft Graph<br/>események](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [A Microsoft Graph<br/>hitelesítési jogkivonatok](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Értesítési központ](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Időzítő](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> 2.x, a HTTP, a időzítő és az Azure Storage kivételével minden kötés regisztrálva kell lennie. Lásd: [kötés bővítmények regisztrálása](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> kísérleti &mdash; nem támogatott, és előfordulhat, hogy a jövőben elhagyásra kerül.
