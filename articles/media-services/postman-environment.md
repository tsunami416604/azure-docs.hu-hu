 ---
cím: importálja a Postman környezet az Azure Media Services REST-hívások leírása: Ez a témakör a Postman környezet definíciójának az Azure Media Services REST-hívások.
szolgáltatások: media-services documentationcenter: "Szerző: Juliako manager: cfowler szerkesztőben:"

MS.Service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: a következő cikket: ms.date: 01/04/2017 ms.author: juliako

---

# <a name="import-the-postman-environment"></a>Importálja a Postman környezet 

Ez a cikk a definíciót tartalmaz a **Postman** környezeti változókat, amelyek a [Postman gyűjtemény](postman-collection.md) , amely tartalmazza a Media Services REST API-k hívása csoportosított HTTP-kérelmekre. A környezet és a gyűjtés fájlokat használja a [konfigurálása Postman Media Services REST API-hívások](media-rest-apis-with-postman.md) oktatóanyag.

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
