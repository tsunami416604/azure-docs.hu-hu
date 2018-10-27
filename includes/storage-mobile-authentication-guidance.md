---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 6911e06dc023027ab32b99387b9f7d3f5e708f86
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164519"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Hozzáférés az Azure Storage-alkalmazás konfigurálása
Két módon hitelesítheti az alkalmazását a tárolási szolgáltatásokhoz való hozzáférést:

* Megosztott kulcs: A megosztott kulcs használata csak tesztelési célokra
* Közös hozzáférésű Jogosultságkód (SAS): Használata SAS éles üzemű alkalmazás számára

### <a name="shared-key"></a>Megosztott kulcsos
Megosztott kulcsos hitelesítés azt jelenti, hogy az alkalmazás fogja használni a fiók nevét és kulcsát tárolási szolgáltatásokhoz való hozzáférést. Ebben a tárban történő gyors bemutató céljából fogjuk használni az első lépések a megosztott kulcsos hitelesítés.

> [!WARNING] 
> **Csak tesztelési célra használja a megosztott kulcsos hitelesítés!** A fiók nevét és kulcsát, teljes írási/olvasási hozzáférést biztosíthat a társított Storage-fiók, amely minden olyan személy, amely letölti az alkalmazás jut el. Ez a **nem** egy jó gyakorlat, mivel így fennáll a veszélye a kulcs biztonsága sérült, nem megbízható ügyfelek kellene.
> 
> 

Megosztott kulcsos hitelesítés használatakor létrehozhat egy [kapcsolati karakterlánc](../articles/storage/common/storage-configure-connection-string.md). A kapcsolati karakterlánc áll:  

* A **DefaultEndpointsProtocol** – kiválaszthatja a HTTP vagy HTTPS PROTOKOLLT. HTTPS-en keresztül azonban erősen ajánlott.
* A **fióknév** – a tárfiók nevét
* A **Fiókkulcs** – az a [az Azure Portal](https://portal.azure.com), lépjen a tárfiókhoz, és kattintson a **kulcsok** ikonra kattintva keresse meg ezt az információt.
* (Nem kötelező) **EndpointSuffix** -tárolási szolgáltatások különböző végpont utótagok, például az Azure China vagy az Azure Cégirányítási régiókban szolgál.

Íme egy példa megosztott kulcsos hitelesítéssel kapcsolati karakterlánc:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS)
Egy mobilalkalmazás az ajánlott módszer az ügyfelek számára az Azure Storage szolgáltatás kérelem hitelesítéséhez van egy közös hozzáférésű Jogosultságkód (SAS) használatával. SAS lehetővé teszi, hozzáférést kell adni egy ügyfél egy erőforrást egy megadott időtartam elteltéig, az engedélyek bizonyos készletét.
A tárolási fiók tulajdonosának kell felhasználni a mobilügyfelek részére SAS létrehozása. SAS létrehozása, valószínűleg érdemes egy külön szolgáltatás, amely létrehozza az SAS elosztani az ügyfelek írni. Tesztelési célokra használhatja a [Microsoft Azure Storage Explorer](http://storageexplorer.com) vagy a [az Azure Portal](https://portal.azure.com) SAS létrehozásához. A SAS létrehozásakor megadhatja a keresztül, amely a SAS érvényes az időtartam alatt, és az engedélyeket, az ügyfél számára biztosít hozzáférést az SAS.

Az alábbi példa bemutatja, hogyan hozható létre SAS a Microsoft Azure Storage Explorer használatával.

1. Ha még nem tette, [a Microsoft Azure Storage Explorer telepítése](http://storageexplorer.com)
2. Csatlakozzon az előfizetéséhez.
3. Kattintson a Storage-fiókjába, és kattintson a bal alsó "Műveletek" fülre. Kattintson a "Get közös hozzáférésű Jogosultságkód" egy "kapcsolati karakterlánc" létrehozni az SAS.
4. Íme egy példa egy SAS-kapcsolati karakterlánc, hogy olvasási és írási engedélyeket a szolgáltatás, a tároló és a tárfiók a blob szolgáltatás objektum szintjén.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Amint láthatja, amikor a SAS használatával, akkor nem teszi ki a fiókkulcs az alkalmazásban. További biztonsági Társítások és ajánlott eljárások a biztonsági Társítások ablakról [közös hozzáférésű Jogosultságkódok: az SAS-modell ismertetése](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

