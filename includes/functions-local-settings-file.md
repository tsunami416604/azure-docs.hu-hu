---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455149"
---
## <a name="local-settings-file"></a>Local settings file (Helyi beállításfájl)

A fájl local.settings.json tárolja az alkalmazásbeállításokat, a kapcsolati karakterláncok és a helyi fejlesztői eszközök által használt beállítások. A local.settings.json fájlban beállításokat csak helyi futtatás során használatosak. A helyi beállításokat fájlt az alábbi struktúrával rendelkezik:

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

Helyi futtatás során az alábbi beállításokat támogatja:

| Beállítás      | Leírás                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Ha a beállítása `true`, minden értéket a helyi gép kulcsa segítségével titkosítja. A használt `func settings` parancsokat. Alapértelmezett érték `false`. |
| **`Values`** | Alkalmazásbeállítások és a helyi futtatás során használt kapcsolati karakterláncok tömbje. A (string, string) kulcs-érték párok felelnek meg az alkalmazásbeállításokat a függvényalkalmazáshoz az Azure-ban, mint például [ `AzureWebJobsStorage` ]. Számos eseményindítók és kötések rendelkezik olyan tulajdonságot, amely hivatkozik a kapcsolati karakterlánc Alkalmazásbeállítás, például `Connection` számára a [Blob storage-eseményindító](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Az ilyen tulajdonságok szüksége lesz a megadott alkalmazás-beállítás a `Values` tömb. <br/>[`AzureWebJobsStorage`] az eseményindítók nem HTTP beállítás kötelező alkalmazást. <br/>Verzió 2.x verzióját a Functions futtatókörnyezete van szükség a [`FUNCTIONS_WORKER_RUNTIME`] beállítást, amely a projekt Core Tools által generált. <br/> Ha rendelkezik a [az Azure storage emulator](../articles/storage/common/storage-use-emulator.md) telepítve helyben, beállíthatja [ `AzureWebJobsStorage` ] való `UseDevelopmentStorage=true` és Core Tools használja az emulátorban. Ez akkor hasznos, a fejlesztés során, de a telepítés előtt egy tényleges storage-kapcsolattal kell tesztelni.<br/> Értékek a karakterláncokat, és nem a JSON-objektumok vagy tömbök kell lennie. Beállítás neve nem tartalmazhat kettőspontot (`:`) vagy dupla aláhúzás (`__`); ezek a futtatókörnyezet számára van fenntartva.  |
| **`Host`** | Ebben a szakaszban beállítások testre szabhatja a Functions gazdafolyamat helyi futtatás során. Ezek a elkülönül az host.json beállításokat, amelyeket is érvényesek, ha az Azure-ban futtatja. |
| **`LocalHttpPort`** | Beállítja az alapértelmezett portot használja, amikor a függvények helyi állomás (`func host start` és `func run`). A `--port` parancssori kapcsoló elsőbbséget élvez ezt az értéket. |
| **`CORS`** | Meghatározza az engedélyezett eredetek [eltérő eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Források szóközök nélküli szövegláncként egy vesszővel tagolt lista formájában vannak megadva. A helyettesítő karaktert tartalmazó értéket (\*) támogatott, amely lehetővé teszi a kérelmek bármilyen forrásból. |
| **`CORSCredentials`** |  Ha true értékre állítsa `withCredentials` kérelmeket. |
| **`ConnectionStrings`** | Ne használja a gyűjtemény a kapcsolati karakterláncokat a függvénykötésnek használják. Ez a gyűjtemény csak használják, amely általában a kapcsolati karakterláncok keretrendszereket a `ConnectionStrings` szakaszában egy konfigurációs fájlba, például [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Kapcsolati karakterláncok ezt az objektumot a rendszer felveszi a környezetbe, a szolgáltató típusát [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Ebben a gyűjteményben lévő elemek az Azure-ban nincs közzétéve a többi alkalmazás beállításokkal. Ezekre az értékekre, explicit módon kell hozzáadnia a `Connection strings` gyűjteménye, a függvényalkalmazás-beállításokat. Létrehozásakor egy [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) a függvénykódban, tárolja a kapcsolati karakterlánc értékét a **Alkalmazásbeállítások** a portálon a kapcsolatokkal. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
