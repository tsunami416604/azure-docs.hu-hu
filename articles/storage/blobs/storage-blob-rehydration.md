---
title: BLOB-adatok rehidratálása az archív szintről
description: Távolítsa el a blobokat az archív tárolóból, hogy hozzáférjen az adatokhoz.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 1c06c1d0403e526e1ed58a193cfe9b57bb9fe561
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780239"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-adatok rehidratálása az archív szintről

Míg a blob az archív hozzáférési szinten található, offline állapotba kerül, és nem olvasható és nem módosítható. A blob metaadatai online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. A Blobok beolvasása és módosítása csak online szinteken érhető el, például a gyakori vagy a ritka elérésű. Az archív hozzáférési szinten tárolt adatok lekérdezésére és elérésére két lehetőség áll rendelkezésre.

1. [Archivált Blobok rehidratálása online szintre](#rehydrate-an-archived-blob-to-an-online-tier) – az archív blobot gyors vagy ritka állapotba állíthatja, ha megváltoztatja a rétegét a [blob-réteg beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) művelettel.
2. [Archivált blob másolása online szintre](#copy-an-archived-blob-to-an-online-tier) – hozzon létre egy archív blob új másolatát a [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) művelet használatával. Adjon meg egy másik blob-nevet és egy gyors vagy ritka elérésű célként megadott szintet.

 A rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Archivált Blobok rehidratálása online szintre

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Archivált blob másolása online szintre

Ha nem szeretné kiszáradni az archív blobot, dönthet úgy, hogy [másolási blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveletet hajt végre. Az eredeti blob változatlan marad az archívumban, miközben új blob jön létre az online gyakori vagy ritka elérésű szinten, hogy működjön. A blob másolása műveletben az opcionális *x-MS-rehidratált-priority* tulajdonságot a standard vagy a High (előzetes verzió) értékre is beállíthatja annak a prioritásnak a megadásához, amelyen a blob-példányt létre kívánja hozni.

Az archív Blobok csak ugyanazon a Storage-fiókon belüli online célhelyekre másolhatók. Az archív Blobok másik archív blobba való másolása nem támogatott.

A Blobok archívumból való másolása a kiválasztott rehidratálás prioritástól függően órákig elvégezhető. A háttérben a **blob másolása** művelet beolvassa az archív forrás blobját, hogy létrehozzon egy új online blobot a kiválasztott célhelyen. Előfordulhat, hogy az új blob látható a Blobok listázásakor, de az adatok nem érhetők el, amíg a forrás archív blobból való olvasás be nem fejeződik, és az adatok bekerülnek az új online cél blobba. Az új blob független másolat, és minden módosítás vagy törlés nem befolyásolja a forrás archív blobot.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az olvasási műveletek és az adatok lekérése során a rendszer felszámítja, hogy az archivált Blobok a gyakori vagy a ritka elérésű szintekre kerülnek. A magas prioritású (előzetes verzió) használata esetén a standard prioritáshoz képest magasabb a művelet és az adatok beolvasása. A magas prioritású rehidratálás a számlán külön tételként jelenik meg. Ha a magas prioritású kérelem egy pár gigabájt archiválási blobjának visszaadására irányul, 5 órát vesz igénybe, nem számítunk fel díjat a magas prioritású beolvasási arányért. A normál lekérési díjak azonban továbbra is érvényben maradnak, mivel a rehidratálás elsőbbséget élvez más kérelmekkel szemben.

A Blobok archívumból gyors vagy ritka elérésű szintekre való másolása olvasási műveletként és adatlekérdezésként történik. Az új blob másolatának létrehozásakor a rendszer írási műveletet számít fel. A korai törlési díjak nem érvényesek az online blobra másoláskor, mert a forrás blobja változatlan marad az archiválási szinten. Ha be van jelölve, a magas prioritású beolvasási díjak érvényesek.

Az archiválási szinten lévő blobokat legalább 180 napig kell tárolni. Az archivált Blobok törlésére vagy újraszárítására az 180 nap előtt a korai törlési díjat kell fizetni.

> [!NOTE]
> A blokk-blobok és az adattisztítások díjszabásával kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/). A kimenő adatátviteli díjakkal kapcsolatos további információkért tekintse meg az [adatátviteli díjszabás részleteit](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Archív blob rehidratálása online szintre
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás**elemet.

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **blob tulajdonságainál**válassza a lehetőség **módosítása**lehetőséget.

1. Válassza ki **a** gyakori **vagy ritka** elérésű hozzáférési szintet. 

1. Válassza a **standard** vagy a **magas**rehidratálás prioritást.

1. Kattintson a **Save (Mentés** ) gombra a lap alján.

![Storage-fiók szintjeinek módosítása](media/storage-tiers/blob-access-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Az alábbi PowerShell-parancsfájl segítségével módosíthatja az archív Blobok blob-szintjét. Az `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$containerName` változót inicializálni kell a tároló nevével. A `$blobName` változót inicializálni kell a blob nevével. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Archív blob másolása egy új blobba egy online szinttel
Az alábbi PowerShell-szkripttel másolhatja az archív blobokat egy új blobba ugyanazon a Storage-fiókon belül. Az `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$srcContainerName` és `$destContainerName` változókat a tároló nevével kell inicializálni. A `$srcBlobName` és `$destBlobName` változókat inicializálni kell a blob nevével. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName == ""
$destBlobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a Blob Storage szintjeiről](storage-blob-storage-tiers.md)
* [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
* [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)
