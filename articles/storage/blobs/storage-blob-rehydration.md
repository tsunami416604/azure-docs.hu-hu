---
title: Blobadatok rehidratálása az archív rétegből
description: Rehydrate a blobok archív tárolóból, így hozzáférhet az adatokhoz.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684098"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Blobadatok rehidratálása az archív rétegből

Míg egy blob az archív hozzáférési rétegben, offline állapotban van, és nem olvasható vagy módosítható. A blob metaadatai online maradnak és elérhetők maradnak, így felsorolhatja a blobot és annak tulajdonságait. A blobadatok olvasása és módosítása csak online rétegekkel érhető el, például a gyakori vagy ritka elérésű. Az archív hozzáférési szinten tárolt adatok beolvasása és elérése két lehetőség közül választhat.

1. [Az archivált blob hidratálása egy online szintre](#rehydrate-an-archived-blob-to-an-online-tier) – az archív blob újrahidratatása a gyakori vagy ritka elérésű állapotba a [Blobszint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) művelet használatával.
2. [Archivált blob másolása egy online rétegbe](#copy-an-archived-blob-to-an-online-tier) – Hozzon létre egy új másolatot egy archív blob segítségével a [Blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) művelet. Adjon meg egy másik blobnevet és egy gyakori vagy ritka elérésű célréteget.

 A rétegekről további információt az [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek című témakörben talál.](storage-blob-storage-tiers.md)

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Archivált blob hidratálása online rétegre

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Archivált blob másolása online szintre

Ha nem szeretné rehidratálni az archív blobot, választhat, hogy egy [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveletet. Az eredeti blob változatlan marad az archívumban, miközben egy új blob jön létre az online gyakori vagy ritka elérésű rétegben, ahol dolgozhat. A Blob másolása műveletben a választható *x-ms-rehidratálás-prioritású* tulajdonságot is beállíthatja Normál vagy Magas beállításra, hogy megadja azt a prioritást, amelynél a blobmásolatot létre szeretné hozni.

Blob másolása az archívumból órákat vehet igénybe a kiválasztott rehidratálási prioritástól függően. A színfalak mögött a **Blob másolása** művelet beolvassa az archív forrásblobot, hogy hozzon létre egy új online blobot a kiválasztott célszinten. Az új blob látható lehet, ha blobok listája, de az adatok nem érhetők el, amíg a forrás archív blob ból történő olvasás befejeződött, és az adatok írása az új online cél blob. Az új blob független másolatként, és bármilyen módosítás vagy törlés nem befolyásolja a forrás archív blob.

Az archív blobok csak ugyanazon a tárfiókon belül másolhatók online célrétegekre. Az archív blob másolása egy másik archív blob nem támogatott. Az alábbi táblázat a CopyBlob képességeit mutatja.

|                                           | **Gyakori elérésű réteg forrása**   | **Ritka elérésű forrás** | **Archív szint forrása**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Gyakori elérésű szint célhelye**                  | Támogatott             | Támogatott            | Ugyanazon a fiókon belül támogatott; függőben lévő rehidratálás               |
| **Ritka elérésű célállomás**                 | Támogatott             | Támogatott            | Ugyanazon a fiókon belül támogatott; függőben lévő rehidratálás               |
| **Archiválási szint célhelye**              | Támogatott             | Támogatott            | Nem támogatott         |

## <a name="pricing-and-billing"></a>Árak és számlázás

Az archiválási blobok gyors vagy ritka elérésű szintekre történő újrahidratálása olvasási műveletek és adatlekérésként kerül felszámításra. A magas prioritású használata magasabb működési és adatlekérési költségekkel jár, mint a normál prioritás. A magas prioritású rehidratálás külön sorként jelenik meg a számlán. Ha egy néhány gigabájtból álló archív blob visszaadására irányuló magas prioritású kérelem több mint 5 órát vesz igénybe, nem kell fizetnie a magas prioritású lekérési arányt. A normál lekérési díjak azonban továbbra is érvényesek, mivel a rehidratálást előnyben tartották a többi kérésekkel szemben.

Blobok másolása az archívumból a gyakori vagy ritka elérésű rétegek díja olvasási műveletek és az adatok lekérése. Az új blobmásolat létrehozásáért írási művelet et számítunk fel. A korai törlési díjak nem vonatkoznak, ha egy online blobba másol, mert a forrásblob változatlan marad az archív rétegben. Ha be van jelölve, a magas prioritású visszakeresési díjak érvényesek.

Az archív rétegben lévő blobokat legalább 180 napig kell tárolni. Az archivált blobok 180 nap előtt történő törlése vagy rehidratálása korai törlési díjat von maga után.

> [!NOTE]
> A blokkblobok díjszabásáról és az adatok rehidratálásáról az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakörben talál további információt. A kimenő adatátviteli díjakról az [Adatátviteli díjak részletei című témakörben talál további információt.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Archív blob újrahidratálása online rétegre
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portalon keresse meg és válassza az **Összes erőforrás lehetőséget.**

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **Blob tulajdonságai párbeszédpanelen**válassza a **Szint módosítása**lehetőséget.

1. Válassza ki a **Gyakori vagy** **Ritka elérésű** hozzáférési szintet. 

1. Válassza ki a **Normál** vagy a **Magas**rehidratálási prioritást.

1. Válassza a **Mentés** gombot alul.

![Tárfiók-szint](media/storage-tiers/blob-access-tier.png)
![módosítása A rehidratálás állapotának ellenőrzése](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancsfájl segítségével módosíthatja az archív blob blob szintje. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a tárfiók nevével. A `$containerName` változót inicializálni kell a tároló nevével. A `$blobName` változót inicializálni kell a blob nevével. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Archív blob másolása egy új blobba egy online réteggel
A következő PowerShell-parancsfájl segítségével egy archív blob ot másolhat egy új blobugyanabban a tárfiókon belül. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a tárfiók nevével. A `$srcContainerName` `$destContainerName` és a változókat a tárolónevekkel kell inicializálni. A `$srcBlobName` `$destBlobName` és a változók at a blob neveket kell inicializálni. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Következő lépések

* [További információ a Blob storage-szintekről](storage-blob-storage-tiers.md)
* [A blobstorage- és GPv2-fiókok gyors, hűvös és archív díjszabásának ellenőrzése régiónként](https://azure.microsoft.com/pricing/details/storage/)
* [Az Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
* [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
