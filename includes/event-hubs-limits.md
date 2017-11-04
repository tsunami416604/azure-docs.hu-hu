A következő táblázat felsorolja a kvóták, és korlátozza adott [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Az Event Hubs árazással kapcsolatos információkért lásd: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Hatókör | Típus | Viselkedés túllépésekor | Érték |
| --- | --- | --- | --- | --- |
| Az event hubs névtér másodpercenkénti száma |Namespace |Statikus |A program elutasítja későbbi kérelmeket egy új eseményközpont létrehozásához. |10 |
| Az event hubs egy partíciók száma |Entitás |Statikus |- |32 |
| Az event hubs egy fogyasztói csoportok száma |Entitás |Statikus |- |20 |
| Névtér AMQP-kapcsolatok száma |Namespace |Statikus |További kapcsolatokat későbbi kérelmek vissza kell utasítani, és kivételt megkapta a hívó kód. |5,000 |
| Az Event Hubs esemény maximális mérete|Rendszerszintű |Statikus |- |256 KB |
| Az eseményközpont neveként maximális mérete |Entitás |Statikus |- |50 karakter hosszú lehet |
| Nem epoch fogadók egyes fogyasztói csoportok száma |Entitás |Statikus |- |5 |
| Az eseményadatok maximális megőrzési időtartam |Entitás |Statikus |- |1-7 nap |
| Kapacitásegységek maximális száma |Namespace |Statikus |Az átviteli egység túllépése okozza az adatokat, hogy lehet halmozódni és állít elő, egy  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Átviteli egységek a szokásos nagyobb számú kérhet bejelentés réteghez egy [támogatási kérelem](/azure/azure-supportability/how-to-create-azure-support-request). [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) véglegesített beszerzési alapon 20 blokkok érhetők el. |20 |
| Egy névtér engedélyezési szabályok száma |Namespace|Statikus |A program elutasítja az engedélyezési szabályok létrehozása későbbi kérelmeket.|12 |
