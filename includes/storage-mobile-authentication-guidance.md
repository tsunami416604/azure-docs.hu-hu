---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673368"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Az alkalmazás konfigurálása az Azure Storage eléréséhez
Kétféleképpen hitelesítheti az alkalmazást a Storage-szolgáltatások eléréséhez:

* Megosztott kulcs: A megosztott kulcs használata csak tesztelési célokra
* Megosztott hozzáférésű aláírás (SAS): SAS használata éles alkalmazásokhoz

### <a name="shared-key"></a>Megosztott kulcsos
A megosztott kulcsú hitelesítés azt jelenti, hogy az alkalmazás a fiók nevét és a fiókkulcsot fogja használni a Storage-szolgáltatások eléréséhez. A tár használatának gyors bemutatása céljából a megosztott kulcsú hitelesítést fogjuk használni az első lépésekben.

> [!WARNING] 
> **Csak tesztelési célokra használjon megosztott kulcsos hitelesítést!** A fiók nevét és a fiókkulcsot, amely teljes olvasási/írási hozzáférést biztosít a társított storage-fiókhoz, minden olyan személy megkapja, aki letölti az alkalmazást. Ez **nem** jó gyakorlat, mivel azt kockáztatja, hogy a kulcs sérült a nem megbízható ügyfelek.
> 
> 

A megosztott kulcsú hitelesítés használataesetén létrehoz egy [kapcsolati karakterláncot.](../articles/storage/common/storage-configure-connection-string.md) A kapcsolati karakterlánc a következőkből áll:  

* A **DefaultEndpointsProtocol** - választhat HTTP vagy HTTPS. A HTTPS használata azonban erősen ajánlott.
* A **fiók neve** - a tárfiók neve
* A **fiókkulcs** – Az [Azure Portalon](https://portal.azure.com)keresse meg a tárfiókot, és kattintson a **Kulcsok** ikonra az adatok megkereséséhez.
* (Nem kötelező) **EndpointSuffix** – Ez a tárolási szolgáltatások különböző végpont-utótagok, például az Azure China vagy az Azure Governance tárolószolgáltatások.

Íme egy példa a megosztott kulcsú hitelesítést használó kapcsolati karakterláncra:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS)
Egy mobilalkalmazás esetében az ajánlott módszer az ügyfél által az Azure Storage szolgáltatás sal szembeni kérelem hitelesítéséhez egy megosztott hozzáférésű aláírás (SAS) használatával. A SAS lehetővé teszi, hogy egy adott ideig hozzáférést biztosítson egy erőforráshoz egy adott ideig, adott engedélyekkel.
A tárfiók tulajdonosaként létre kell hoznia egy SAS-t a mobilügyfelek számára. A SAS létrehozásához érdemes írni egy külön szolgáltatást, amely létrehozza a SAS kell terjeszteni az ügyfelek számára. Tesztelési célokra használhatja a [Microsoft Azure Storage Explorer](https://storageexplorer.com) vagy az Azure [Portal](https://portal.azure.com) sAS létrehozásához. A SAS létrehozásakor megadhatja azt az időintervallumot, amely alatt a SAS érvényes, és a SAS által az ügyfélnek adott engedélyeket.

A következő példa bemutatja, hogyan használhatja a Microsoft Azure Storage Explorer segítségével egy SAS létrehozásához.

1. Ha még nem tette meg, [telepítse a Microsoft Azure Storage Explorert](https://storageexplorer.com)
2. Csatlakozzon az előfizetéséhez.
3. Kattintson a Tárfiókra, és kattintson a bal alsó sarokban található "Műveletek" fülre. Kattintson a "Közös hozzáférésű jogosultságjegyaláírás beszereznie" gombra a SAS "kapcsolati karakterlánc" létrehozásához.
4. Íme egy példa egy SAS-kapcsolati karakterláncra, amely olvasási és írási engedélyeket ad a szolgáltatás, a tároló és az objektum szintjén a Storage-fiók blob szolgáltatásához.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Mint látható, sas használatakor nem teszi ki a fiókkulcsot az alkalmazásban. A SAS-ről és a SAS használatának ajánlott eljárásairól a [Megosztott hozzáférésű aláírások: A SAS-modell ismertetése](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)című témakörben olvashat bővebben.

