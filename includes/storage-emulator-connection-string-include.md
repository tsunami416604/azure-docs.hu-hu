---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166148"
---
A storage emulator egy olyan rögzített fiók és a egy jól ismert hitelesítési kulcs megosztott kulcsos hitelesítést támogatja. A fiók és a kulcsot olyan engedélyezett, a storage emulator való használatra csak megosztott kulcsos hitelesítő adatait. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A hitelesítési kulcs a storage emulator által támogatott ügyfél-hitelesítési kódját funkciójának csak tesztelésre szolgál. Bármilyen biztonsági célt nem szolgál. A storage-fiók éles és a kulcs a storage emulator nem használható. A fejlesztői fiókot ne használjon termelési adatokkal.
> 
> A storage emulator csak a HTTP Protokollon keresztül kapcsolatot támogat. Viszont a HTTPS, a javasolt protokollt egy Azure storage-fiók éles környezetben erőforrások eléréséhez.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Csatlakozás használatával egy helyi emulátor-fiókhoz
Csatlakozás a storage emulator az alkalmazás a legegyszerűbb módja a kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájlban, amely hivatkozik a helyi `UseDevelopmentStorage=true`. Íme egy példa egy kapcsolati karakterlánc, a storage emulator a egy *app.config* fájlt: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Csatlakozás az emulátor fiókhoz a jól ismert fióknevet és a kulcs használatával
Hozzon létre egy kapcsolati karakterláncot, amely az emulátor nevének és kulcsának hivatkozik, meg kell adnia a végpontok az emulátorban, a kapcsolati karakterláncban a használni kívánt szolgáltatások mindegyikéhez. Ez azért szükséges, hogy a kapcsolati karakterláncot a használatával hivatkozik a emulátor végpontok, amelyek eltérő storage-fiókok. Ha például a kapcsolati karakterlánc értékét fog kinézni:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ez az érték megegyezik a parancsikont a fent bemutatott `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Adjon meg egy HTTP-proxy
Megadhat egy HTTP-proxyt használja, ha a szolgáltatás a storage emulatorban tesztelt is. Ez lehet hasznos, ha a tárolási szolgáltatások kapcsolatos művelet-végrehajtási hibakeresése közben figyelje a HTTP-kérelmeket és válaszokat. Proxy megadásához adja hozzá a `DevelopmentStorageProxyUri` a kapcsolati karakterlánc lehetőséget, majd az értékét állítsa a proxy URI. Ha például a következő egy kapcsolati karakterláncot, a storage emulator mutat, és konfigurál egy HTTP-proxy:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

