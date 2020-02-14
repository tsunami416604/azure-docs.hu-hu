---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205695"
---
## <a name="local-settings-file"></a>Local settings file (Helyi beállításfájl)

A local. Settings. JSON fájl az alkalmazás beállításait, a kapcsolatok karakterláncait és a helyi Fejlesztőeszközök által használt beállításokat tárolja. A local. Settings. JSON fájlban lévő beállításokat csak akkor használja a rendszer, ha helyileg futtat projekteket. A helyi beállítások fájlja a következő struktúrával rendelkezik:

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

Ezek a beállítások akkor támogatottak, ha helyileg futtatja a projekteket:

| Beállítás      | Leírás                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Ha a beállítás értéke `true`, a rendszer minden értéket a helyi számítógép kulcsával titkosít. `func settings` parancsokkal használható. Az alapértelmezett érték `false`. |
| **`Values`** | Az Alkalmazásbeállítások és a kapcsolódási karakterláncok tömbje, amely akkor használatos, amikor egy projekt helyileg fut. Ezek a kulcs-érték (String-String) párok megfelelnek az Azure-beli Function alkalmazás beállításainak, például [`AzureWebJobsStorage`]. Számos eseményindító és kötés olyan tulajdonsággal rendelkezik, amely egy kapcsolati sztring alkalmazási beállításra hivatkozik, például a [blob Storage-eseményindítóhoz](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)`Connection`. Ezen tulajdonságok esetében szükség van egy, az `Values` tömbben definiált alkalmazás-beállításra. <br/>a [`AzureWebJobsStorage`] a HTTP-n kívüli eseményindítók kötelező alkalmazási beállítása. <br/>A functions Runtime 2. x és újabb verziójához a [`FUNCTIONS_WORKER_RUNTIME`] beállítás szükséges, amelyet a projekthez a Core Tools generál. <br/> Ha az [Azure Storage-emulátor](../articles/storage/common/storage-use-emulator.md) helyileg van telepítve, és a [`AzureWebJobsStorage`] `UseDevelopmentStorage=true`, a Core Tools az emulátort használja. Az emulátor a fejlesztés során hasznos, de a telepítés előtt tesztelni kell egy tényleges tárolási kapcsolatban.<br/> Az értékeknek karakterláncnak és nem JSON-objektumoknak vagy tömböknek kell lenniük. A nevek beállítása nem tartalmazhat kettőspontot (`:`) vagy dupla aláhúzást (`__`). Ezeket a karaktereket a futtatókörnyezet foglalta le.  |
| **`Host`** | Az ebben a szakaszban található beállítások testre szabják a functions gazdagép folyamatát, amikor helyileg futtat projekteket. Ezek a beállítások eltérhetnek a gazdagép. JSON beállításaitól, amelyek akkor is érvényesek, amikor projekteket futtatnak az Azure-ban. |
| **`LocalHttpPort`** | Beállítja a helyi functions-gazdagép (`func host start` és `func run`) futtatásakor használt alapértelmezett portot. A `--port` parancssori kapcsoló elsőbbséget élvez ezzel a beállítással szemben. |
| **`CORS`** | Meghatározza a [több eredetű erőforrás-megosztás (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)számára engedélyezett származási erőforrásokat. Az eredetek vesszővel tagolt listaként vannak megadva, szóközök nélkül. A helyettesítő karakteres érték (\*) támogatott, amely bármilyen forrásból engedélyezi a kérelmeket. |
| **`CORSCredentials`** |  Ha `true`értékre van állítva, engedélyezi `withCredentials` kérelmeket. |
| **`ConnectionStrings`** | Egy gyűjtemény. Ne használja ezt a gyűjteményt a függvények kötései által használt kapcsolati sztringekhez. Ezt a gyűjteményt csak olyan keretrendszerek használják, amelyek jellemzően a konfigurációs fájl `ConnectionStrings` szakaszának a kapcsolatok karakterláncait kapják meg, például [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Az objektumban található kapcsolódási karakterláncok a [System. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). szolgáltatói típussal lesznek hozzáadva a környezethez. Az ebben a gyűjteményben lévő elemek nem jelennek meg az Azure-ban más Alkalmazásbeállítások használatával. Explicit módon fel kell vennie ezeket az értékeket a Function app-beállítások `Connection strings` gyűjteményéhez. Ha [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) hoz létre a függvény kódjában, a kapcsolati sztring értékét a portál **alkalmazás beállításai** között lévő többi kapcsolattal együtt kell tárolnia. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
