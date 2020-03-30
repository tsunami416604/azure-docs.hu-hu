---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205695"
---
## <a name="local-settings-file"></a>Local settings file (Helyi beállításfájl)

A local.settings.json fájl tárolja a helyi fejlesztői eszközök által használt alkalmazásbeállításokat, kapcsolati karakterláncokat és beállításokat. A local.settings.json fájl beállításai csak akkor használatosak, ha helyileg futtat projekteket. A helyi beállításfájl nak ez a szerkezete van:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Ezek a beállítások a projektek helyi futtatásakor támogatottak:

| Beállítás      | Leírás                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Ha ez a `true`beállítás a beállítás, akkor a rendszer minden értéket helyi számítógépkulccsal titkosít. Parancsokkal `func settings` használva. Az alapértelmezett `false`érték a . |
| **`Values`** | A projekt helyi futtatásakor használt alkalmazásbeállítások és kapcsolati karakterláncok tömbje. Ezek a kulcs-érték (karakterlánc-karakterlánc) párok megfelelnek az azure-beli függvényalkalmazás alkalmazásbeállításainak, például. [`AzureWebJobsStorage`] Számos eseményindító és kötés rendelkezik egy tulajdonsággal, amely `Connection` egy kapcsolati karakterlánc-alkalmazásbeállításra hivatkozik, például a [Blob storage eseményindítójára.](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration) Ezekhez a tulajdonságokhoz a `Values` tömbben definiált alkalmazásbeállításra van szükség. <br/>[`AzureWebJobsStorage`]a HTTP-től eltérő eseményindítókhoz szükséges alkalmazásbeállítás. <br/>A Functions futásidejű 2.x-es és`FUNCTIONS_WORKER_RUNTIME`újabb verziója megköveteli a [ ] beállítást, amelyet a Core Tools hoz létre a projekthez. <br/> Ha az [Azure storage emulátor](../articles/storage/common/storage-use-emulator.md) telepítve van [`AzureWebJobsStorage`] `UseDevelopmentStorage=true`a helyileg, és be van állítva, core eszközök az emulátort használja. Az emulátor hasznos a fejlesztés során, de az üzembe helyezés előtt tényleges tárolási kapcsolattal kell tesztelni.<br/> Az értékek karakterláncok lehetnek, nem JSON-objektumok vagy tömbök. A beállításnevek nem tartalmazhatnak`:`kettőspontot (`__`) vagy dupla aláhúzást ( ). Ezeket a karaktereket a futásidejűek tartják fenn.  |
| **`Host`** | Ebben a szakaszban a beállítások testre szabják a Functions gazdafolyamatot, amikor helyileg futtatja a projekteket. Ezek a beállítások elkülönülnek a host.json beállításaitól, amelyek az Azure-ban lévő projektek futtatásakor is érvényesek. |
| **`LocalHttpPort`** | A helyi Functions állomás (`func host start` és `func run`a ) futtatásakor használt alapértelmezett portbeállítása. A `--port` parancssori beállítás elsőbbséget élvez ezzel a beállítással szemben. |
| **`CORS`** | A kereszteredetű erőforrások megosztásához engedélyezett eredeteket [(CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)határozza meg. Az eredeteket vesszővel tagolt listaként szállítjuk szóközök nélkül. A helyettesítő karakter\*értéke ( ) támogatott, amely lehetővé teszi a kérelmeket bármilyen eredetű. |
| **`CORSCredentials`** |  Ha a `true`beállítás `withCredentials` a , engedélyezi a kérelmeket. |
| **`ConnectionStrings`** | Egy gyűjtemény. Ne használja ezt a gyűjteményt a függvénykötések által használt kapcsolati karakterláncok. Ezt a gyűjteményt csak olyan keretrendszerek használják, `ConnectionStrings` amelyek általában kapcsolati karakterláncokat kapnak egy konfigurációs fájl szakaszából, például [az Entitáskeretrendszerből.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) Az objektumban lévő kapcsolati karakterláncok a [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)szolgáltatótípussal kerülnek a környezetbe. Ebben a gyűjteményben lévő elemek nem jelennek meg az Azure-ban más alkalmazásbeállításokkal. Ezeket az értékeket explicit `Connection strings` módon hozzá kell adnia a függvényalkalmazás-beállítások gyűjteményéhez. Ha létrehoz egy [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) függvénykódot, akkor a kapcsolati karakterlánc értékét a többi kapcsolattal együtt kell tárolnia a portál **alkalmazásbeállítások** ban. |

["AzureWebJobsStorage"]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
