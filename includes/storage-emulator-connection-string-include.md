---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179323"
---
A tároló-emulátor egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a megosztott kulcs hitelesítéséhez. Ez a fiók és kulcs az egyetlen megosztott kulcs hitelesítő adatok használata engedélyezett a tároló emulátor. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A tárolóemulátor által támogatott hitelesítési kulcs csak az ügyfélhitelesítési kód működésének tesztelésére szolgál. Nem szolgál semmilyen biztonsági célt. Az éles tárfiók és a kulcs nem használható a storage emulátor. Ne használja a fejlesztési fiókot éles adatokkal.
> 
> A tárolóemulátor csak HTTP-n keresztül támogatja a kapcsolatot. A HTTPS azonban az ajánlott protokoll az éles Azure-tárfiók ban lévő erőforrások eléréséhez.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Csatlakozás az emulátorfiókhoz egy parancsikon használatával
Az alkalmazásból a tárolóemulátorhoz való csatlakozás legegyszerűbb módja, ha az alkalmazás konfigurációs fájljában `UseDevelopmentStorage=true`beállít egy kapcsolati karakterláncot, amely a parancsikonra hivatkozik. Íme egy példa egy *alkalmazás.config* fájltároló-emulátorához való kapcsolati karakterláncra: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Csatlakozás az emulátorfiókhoz a jól ismert fióknév és kulcs használatával
Ha olyan kapcsolati karakterláncot szeretne létrehozni, amely az emulátorfiók nevére és kulcsára hivatkozik, meg kell adnia a végpontokat az emulátortól a kapcsolati karakterláncban használni kívánt szolgáltatások mindegyikéhez. Erre azért van szükség, hogy a kapcsolati karakterlánc az emulátor végpontjaira hivatkozzon, amelyek eltérnek az éles tárfiókhoz tartozóktól. A kapcsolati karakterlánc értéke például a következőkkel fog kinézni:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ez az érték megegyezik a `UseDevelopmentStorage=true`fenti parancsikonnel.

#### <a name="specify-an-http-proxy"></a>HTTP-proxy megadása
Megadhat egy HTTP-proxyt is, amelyet akkor használ, amikor a szolgáltatást a tároló emulátorával teszteli. Ez hasznos lehet a HTTP-kérések és válaszok megfigyelése közben a tárolási szolgáltatások kal kapcsolatos műveletek hibakeresése közben. Proxy megadásához adja `DevelopmentStorageProxyUri` hozzá a beállítást a kapcsolati karakterlánchoz, és állítsa annak értékét a proxy URI-jára. Itt van például egy kapcsolati karakterlánc, amely a tárolóemulátorra mutat, és http-proxyt állít be:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

