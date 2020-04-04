---
title: Az Azure Data Lake Storage Gen2-ben elérhető Blob-tárolási funkciók | Microsoft dokumentumok
description: Ismerje meg, hogy mely Blob storage-funkciókhasználhatók az Azure Data Lake Storage Gen2 szolgáltatással
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637215"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2-ben elérhető Blob-tárolási funkciók

A Blob Storage olyan szolgáltatásai, mint a [diagnosztikai naplózás,](../common/storage-analytics-logging.md) [a hozzáférési szintek](storage-blob-storage-tiers.md)és a Blob Storage [életciklus-kezelési házirendjei](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a Blob storage-fiókok hierarchikus névtereket engedélyezhet anélkül, hogy elveszítené a hozzáférést ezekhez a szolgáltatásokhoz.

Ez a táblázat az Azure Data Lake Storage Gen2-vel használható Blob storage-funkciókat sorolja fel. Az ezekben a táblákban megjelenő elemek idővel változnak, ahogy a támogatás tovább bővül.

## <a name="supported-blob-storage-features"></a>A Blob-tároló támogatott szolgáltatásai

> [!NOTE]
> A támogatási szint csak arra utal, hogy a szolgáltatás a Data Lake Storage Gen2 szolgáltatással hogyan támogatott.

|A Blob Storage szolgáltatás |Támogatási szint |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Gyakran használt adatok hozzáférési szintje|Általánosan elérhető|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Ritkán használt adatok hozzáférési szintje|Általánosan elérhető|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Események|Általánosan elérhető|[Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)|
|Metrikák (klasszikus)|Általánosan elérhető|[Azure Storage-elemzési metrikák (klasszikus)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrikák az Azure Monitorban|Általánosan elérhető|[Azure Storage-metrikák az Azure Monitorban](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob storage PowerShell-parancsok|Általánosan elérhető|[Gyorsútmutató: Blobok feltöltése, letöltése és listázása a PowerShell használatával](storage-quickstart-blobs-powershell.md)|
|Blob storage Azure CLI-parancsok|Általánosan elérhető|[Rövid útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](storage-quickstart-blobs-cli.md)|
|Blob tárolási API-k|Általánosan elérhető|[Rövid útmutató: Azure Blob storage-ügyféltár v12-es a .NET-hez](storage-quickstart-blobs-dotnet.md)<br>[Gyorsútmutató: Blobok kezelése Java v12 SDK-val](storage-quickstart-blobs-java.md)<br>[Gyorsútmutató: Blobok kezelése python v12 SDK-val](storage-quickstart-blobs-python.md)<br>[Gyorsútmutató: Blobok kezelése JavaScript v12 SDK-val a Node.js-ben](storage-quickstart-blobs-nodejs.md)|
|Archív hozzáférési szint|Előzetes verzió|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Életciklus-kezelési irányelvek|Előzetes verzió|[Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)|
|Diagnosztikai naplók|Általánosan elérhető|[Azure Storage Analytics-naplózás](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Csatorna módosítása|Még nem támogatott|[Hírcsatorna-támogatás módosítása az Azure Blob storage-ban](storage-blob-change-feed.md)|
|Fiók feladatátvétele|Még nem támogatott|[Vész-helyreállítás és fiókfeladat-átvétel](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob tároló ACL|Még nem támogatott|[Tároló acl beállítása](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Egyéni tartományok|Még nem támogatott|[Egyéni tartomány hozzárendelése Azure Blob-tároló végponthoz](storage-custom-domain-name.md)|
|Nem módosítható tárhely|Még nem támogatott|[Üzleti legkritikusabb blobadatok tárolása állandó tárolóval](storage-blob-immutable-storage.md)|
|Pillanatképek|Még nem támogatott|[Blob-pillanatkép létrehozása és kezelése a .NET-ben](storage-blob-snapshots.md)|
|Helyreállítható törlés|Még nem támogatott|[Az Azure Storage-blobok helyreállítható törlése](storage-blob-soft-delete.md)|
|Statikus webhelyek|Még nem támogatott|[Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)|
|Naplózás az Azure Monitorban|Még nem támogatott|Egyelőre nem érhető el|
|Prémium szintű blokkblobok|Még nem támogatott|[BlockBlobStorage-fiók létrehozása](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Lásd még

- [Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban](data-lake-storage-known-issues.md)
- [Az Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [Az Azure Data Lake Storage Gen2 szolgáltatást támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Többprotokollos hozzáférés az Azure Data Lake Storage szolgáltatásban](data-lake-storage-multi-protocol-access.md)