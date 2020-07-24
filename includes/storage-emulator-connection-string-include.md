---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070554"
---
A Azurite egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a megosztott kulcsos hitelesítéshez. Ez a fiók és a kulcs az egyetlen megosztott kulcs hitelesítő adatai, amelyek használata engedélyezett a Azurite. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A Azurite által támogatott hitelesítési kulcs kizárólag az ügyfél-hitelesítési kód működésének tesztelésére szolgál. Nem szolgál biztonsági céllal. Az üzemi Storage-fiók és a kulcs nem használható a Azurite. A fejlesztési fiókot ne használja éles adatszolgáltatással.
> 
> A Azurite csak HTTP-n keresztül támogatja a kapcsolatokat. Azonban a HTTPS az a javasolt protokoll, amellyel a termelési Azure Storage-fiók erőforrásaihoz férhet hozzá.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Kapcsolódás az Emulator-fiókhoz parancsikon használatával
Az alkalmazás Azurite való csatlakoztatásának legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely hivatkozik a parancsikonra `UseDevelopmentStorage=true` . Az alábbi példa egy *app.config* fájlban Azurite kapcsolódási sztringet mutat be: 

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
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ez az érték megegyezik a fent látható parancsikonnal `UseDevelopmentStorage=true` .
