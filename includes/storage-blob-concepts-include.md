---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/17/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7889fbc9373cbdfdfab891bf8b1cd610523c7032
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088091"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatok tárolására van optimalizálva.

A Blob Storage a következőkhöz ideális:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók vagy ügyfélalkalmazások URL-eken, az [Azure Storage REST API-n](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShellen](https://docs.microsoft.com/powershell/module/azure.storage), az [Azure CLI-n](https://docs.microsoft.com/cli/azure/storage) vagy Azure Storage-ügyfélkódtárakon keresztül &mdash;a világon bárhonnan&mdash; elérhetik a Blob Storage tárolóban lévő objektumokat HTTP- vagy HTTPS-kapcsolaton keresztül. A tároló ügyfélkódtárai több nyelven érhetők el, beleértve a [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) és [Ruby](http://azure.github.io/azure-storage-ruby) nyelveket is.

## <a name="blob-service-concepts"></a>A Blob szolgáltatással kapcsolatos fogalmak

A Blob Storage háromféle erőforrást tesz elérhetővé: a tárfiókot, a fiókban lévő tárolókat és a tárolókban lévő blobokat. Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob (objektum) tárolóarchitektúrájának ábrája](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Tárfiók

Az Azure Storage-ben lévő adatobjektumokhoz való mindenféle hozzáférés tárfiókokon keresztül valósul meg. További információkat az [Azure Storage-fiókok áttekintésében](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) találhat.

### <a name="container"></a>Tároló

A tárolók blobokat rendszereznek a fájlrendszerek mappáihoz hasonlóan. Minden blob egy tárolóban található. Egy tárfiók korlátlan számú tárolót tartalmazhat, egy tároló pedig korlátlan számú blob tárolására használható. 

  > [!NOTE]
  > A tárolók nevei csak kisbetűket tartalmazhatnak.

### <a name="blob"></a>Blob
 
Az Azure Storage három különböző típusú blobot kínál: &mdash;blokkblobokat, hozzáfűző blobokat és [lapblobokat](../articles/storage/blobs/storage-blob-pageblob-overview.md) (ezek a VHD-fájlokhoz használatosak).

* A blokkblobok szövegeket és bináris adatokat tárolnak legfeljebb nagyjából 4,7 TB méretig. A blokkblobok önállóan felügyelhető adatblokkokból állnak.
* A hozzáfűző blobok a blokkblobokhoz hasonlóan blokkokból állnak, azonban a hozzáfűzési műveletekhez vannak optimalizálva. A hozzáfűző blobok ideálisak például a virtuális gépek adatainak naplózásához és hasonló forgatókönyvekhez.
* A lapblobok véletlen hozzáférésű fájlokat tárolnak legfeljebb 8 TB méretig. A virtuális gépek alapját képező VHD-fájlokat lapblobok tárolják.

Minden blob egy tárolóban található. A tárolók hasonlóak a fájlrendszerek mappáihoz. A blobokat tovább rendezheti virtuális könyvtárakba, és úgy navigálhat közöttük, mint egy fájlrendszerben. 

A nagy adathalmazoknál és bizonyos hálózati korlátozásoknál előfordulhat, hogy az adatok vezetékes feltöltése a Blob Storage-ba irreális lenne. Az [Azure Data Box Disk](../articles/databox/data-box-disk-overview.md) használatával SSD-meghajtókat kérhet a Microsofttól. Ezután az adatokat a meghajtókra másolhatja, azokat pedig visszaküldheti, hogy a Microsoft feltöltse az adatokat a Blob Storage-ba.

Ha nagy mennyiségű adatot kell exportálnia a tárfiókjából, a [Use the Microsoft Azure Import/Export Service to Transfer Data to Blob Storage](../articles/storage/common/storage-import-export-service.md) (A Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage-ba) című részben talál további információt.
  
A tárolók és blobok elnevezésével kapcsolatos részletekért lásd: [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Tárolók, blobok és metaadatok elnevezése és hivatkozása).
