---
title: A blob Storage funkciói elérhetők a Azure Data Lake Storage Gen2ban | Microsoft Docs
description: Ismerje meg, hogy mely blob Storage-funkciókat használhatja a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196008"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el

A [diagnosztikai naplózást](../common/storage-analytics-logging.md), a [hozzáférési szinteket](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési házirendeket](storage-lifecycle-management-concepts.md) blob Storage funkciók mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a funkciókhoz hozzáférjenek.

Ez a táblázat a blob Storage azon funkcióit sorolja fel, amelyeket a Azure Data Lake Storage Gen2 használhat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül.

## <a name="supported-blob-storage-features"></a>Támogatott blob Storage-funkciók

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a szolgáltatás hogyan támogatott a Data Lake Storage Gen2.

|Blob Storage funkció |Támogatási szint |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Gyakran használt adatok hozzáférési szintje|Általánosan elérhető|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Ritkán használt adatok hozzáférési szintje|Általánosan elérhető|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Események|Általánosan elérhető|[Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)|
|Metrikák (klasszikus)|Általánosan elérhető|[Azure Storage Analytics-metrikák (klasszikus)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrikák az Azure Monitorban|Általánosan elérhető|[Azure Storage-metrikák az Azure Monitorban](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB Storage PowerShell-parancsok|Általánosan elérhető|[Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel](storage-quickstart-blobs-powershell.md)|
|BLOB Storage – Azure CLI-parancsok|Általánosan elérhető|[Gyors útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](storage-quickstart-blobs-cli.md)|
|A BLOB storage API-k|Általánosan elérhető|[Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a .NET-hez](storage-quickstart-blobs-dotnet.md)<br>[Gyors útmutató: Blobok kezelése a Java V12 SDK-val](storage-quickstart-blobs-java.md)<br>[Gyors útmutató: Blobok kezelése a Python V12 SDK-val](storage-quickstart-blobs-python.md)<br>[Rövid útmutató: Blobok kezelése a JavaScript V12 SDK-val a Node. js-ben](storage-quickstart-blobs-nodejs.md)|
|Archív hozzáférési szint|Előzetes verzió|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Életciklus-kezelési szabályzatok|Előzetes verzió|[Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)|
|Diagnosztikai naplók|Előzetes verzió|[Az Azure Storage Analytics naplózása](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Csatorna módosítása|Még nem támogatott|[A hírcsatornák támogatásának módosítása az Azure Blob Storage-ban](storage-blob-change-feed.md)|
|Fiók feladatátvétele|Még nem támogatott|[Vész-helyreállítási és-fiók feladatátvétele](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB Container ACL|Még nem támogatott|[Tároló ACL beállítása](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Egyéni tartományok|Még nem támogatott|[Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)|
|Megváltoztathatatlan tároló|Még nem támogatott|[Üzleti szempontból kritikus fontosságú blob-alapú adattárolás tárolása a nem módosítható tárolóval](storage-blob-immutable-storage.md)|
|Pillanatképek|Még nem támogatott|[BLOB-pillanatkép létrehozása és kezelése a .NET-ben](storage-blob-snapshots.md)|
|Helyreállítható törlés|Még nem támogatott|[Az Azure Storage-blobok helyreállítható törlése](storage-blob-soft-delete.md)|
|Statikus webhelyek|Még nem támogatott|[Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)|
|Bejelentkezés Azure Monitor|Még nem támogatott|Még nem érhető el|
|Prémium blokk Blobok|Még nem támogatott|[BlockBlobStorage-fiók létrehozása](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)