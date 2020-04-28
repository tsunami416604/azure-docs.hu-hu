---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179323"
---
A Storage Emulator egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a megosztott kulcsos hitelesítéshez. Ez a fiók és a kulcs az egyetlen megosztott kulcs hitelesítő adatai, amelyek használata engedélyezett a Storage Emulator használatával. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A Storage Emulator által támogatott hitelesítési kulcs kizárólag az ügyfél-hitelesítési kód működésének tesztelésére szolgál. Nem szolgál biztonsági céllal. Az üzemi Storage-fiók és a kulcs nem használható a Storage Emulator használatával. A fejlesztési fiókot ne használja éles adatszolgáltatással.
> 
> A Storage Emulator csak a HTTP protokollon keresztül támogatja a kapcsolatokat. Azonban a HTTPS az a javasolt protokoll, amellyel a termelési Azure Storage-fiók erőforrásaihoz férhet hozzá.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Kapcsolódás az Emulator-fiókhoz parancsikon használatával
Az alkalmazásból a Storage emulatorhoz való kapcsolódás legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely hivatkozik a parancsikonra `UseDevelopmentStorage=true`. Az alábbi példa egy *app. config* fájlban lévő Storage emulatorhoz tartozó kapcsolódási sztringet mutat be: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Kapcsolódjon az Emulator-fiókhoz a jól ismert fiók neve és kulcsa alapján
Az Emulator-fiók nevére és kulcsára hivatkozó kapcsolódási karakterlánc létrehozásához meg kell adnia a végpontokat minden olyan szolgáltatáshoz, amelyet az emulátorban szeretne használni a kapcsolódási karakterláncban. Erre azért van szükség, hogy a kapcsolatok karakterlánca hivatkozzon az emulátor végpontokra, amelyek nem azonosak egy éles tárolóeszköz-fiókkal. Például a következőhöz hasonló módon fog kinézni a kapcsolatok karakterláncának értéke:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ez az érték megegyezik a fent látható parancsikonnal `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>HTTP-proxy meghatározása
Megadhat egy HTTP-proxyt is, amelyet akkor használhat, ha a szolgáltatást a Storage Emulator használatával teszteli. Ez hasznos lehet a HTTP-kérések és a válaszok megfigyelésére a tárolási szolgáltatásokkal kapcsolatos műveletek hibakeresése során. Proxy megadásához adja hozzá a `DevelopmentStorageProxyUri` kapcsolót a kapcsolati karakterlánchoz, és állítsa az értékét a proxy URI-ra. Itt látható például egy olyan kapcsolódási sztring, amely a Storage emulatorra mutat, és egy HTTP-proxyt konfigurál:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

