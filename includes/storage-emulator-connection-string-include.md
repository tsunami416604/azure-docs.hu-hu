---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582600"
---
Az emulátor egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a megosztott kulcsos hitelesítéshez. Ez a fiók és a kulcs az az egyetlen megosztott kulcsú hitelesítő adat, amelyet az emulátorsal való használatra engedélyeznek. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Az emulátor által támogatott hitelesítési kulcs kizárólag az ügyfél-hitelesítési kód működésének tesztelésére szolgál. Nem szolgál biztonsági céllal. A termelési Storage-fiók és a kulcs nem használható az emulátorral. A fejlesztési fiókot ne használja éles adatszolgáltatással.
> 
> Az emulátor csak a HTTP protokollon keresztül támogatja a kapcsolatokat. Azonban a HTTPS az a javasolt protokoll, amellyel a termelési Azure Storage-fiók erőforrásaihoz férhet hozzá.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Kapcsolódás az Emulator-fiókhoz parancsikon használatával
Az emulátorhoz az alkalmazásból való kapcsolódás legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely hivatkozik a parancsikonra `UseDevelopmentStorage=true` . Íme egy példa egy *app.config* -fájlban lévő emulátorhoz tartozó kapcsolódási karakterláncra: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

A (z) megegyezik a fiók nevének, a fiók kulcsának és a végpontoknak a kapcsolódási sztringben használni kívánt emulátor-szolgáltatásokkal való teljes megadásával. Erre azért van szükség, hogy a kapcsolatok karakterlánca hivatkozzon az emulátor végpontokra, amelyek nem azonosak egy éles tárolóeszköz-fiókkal. Például a következőhöz hasonló módon fog kinézni a kapcsolatok karakterláncának értéke:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
