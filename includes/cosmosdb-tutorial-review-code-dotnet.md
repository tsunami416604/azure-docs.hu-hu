Tekintsük át, hogy mi is történik az alkalmazásban. Nyissa meg a DocumentDBRepository.cs fájlt. Az itt található kódsorok hozzák létre a DocumentDB-erőforrásokat. 

* A rendszer inicializálja a DocumentClient ügyfelet.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* A rendszer létrehozza az új adatbázist.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* A rendszer létrehozza az új gyűjteményt.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
