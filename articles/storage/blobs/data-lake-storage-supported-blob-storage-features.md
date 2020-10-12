---
title: A blob Storage funkciói elérhetők a Azure Data Lake Storage Gen2ban | Microsoft Docs
description: Ismerje meg, hogy mely blob Storage-funkciókat használhatja a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a0cba1c3a7a3b713fa5f0603ad9e116068d8df66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618471"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el

A [diagnosztikai naplózást](../common/storage-analytics-logging.md), a [hozzáférési szinteket](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési házirendeket](storage-lifecycle-management-concepts.md) blob Storage funkciók mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a funkciókhoz hozzáférjenek.

Ez a táblázat a blob Storage azon funkcióit sorolja fel, amelyeket a Azure Data Lake Storage Gen2 használhat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül. Ha többet szeretne megtudni a szolgáltatás előzetes verziójának állapotával kapcsolatos problémákról, tekintse meg az [ismert problémákról](data-lake-storage-known-issues.md) szóló cikket.

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a szolgáltatás hogyan támogatott a Data Lake Storage Gen2. 
>
> A [Premium-Performance BlockBlobStorage-fiókok](storage-blob-create-account-block-blob.md) Data Lake Storage Gen2 jelenleg nyilvános előzetes verzióban érhetők el. Az ilyen típusú fiókok támogatása a **BlockBlobStorage (prémium)** oszlopban jelenik meg.

|Blob Storage funkció |Általános célú v2 |BlockBlobStorage (prémium) |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Gyakori elérésű tárolási szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Ritka elérésű tárolási szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Események|Általánosan elérhető|Előnézet|[Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)|
|Metrikák (klasszikus)|Általánosan elérhető|Nem támogatott|[Azure Storage Analytics-metrikák (klasszikus)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrikák az Azure Monitorban|Általánosan elérhető|Előnézet|[Azure Storage-metrikák az Azure Monitorban](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB Storage PowerShell-parancsok|Általánosan elérhető|Előnézet|[Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel](storage-quickstart-blobs-powershell.md)|
|BLOB Storage – Azure CLI-parancsok|Általánosan elérhető|Előnézet|[Gyors útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](storage-quickstart-blobs-cli.md)|
|BLOB Storage API-k|Általánosan elérhető|Előnézet|[Rövid útmutató: Az Azure Blob Storage .NET-hez készült ügyféloldali kódtára (v12)](storage-quickstart-blobs-dotnet.md)<br>[Gyors útmutató: Blobok kezelése a Java V12 SDK-val](storage-quickstart-blobs-java.md)<br>[Gyors útmutató: Blobok kezelése a Python V12 SDK-val](storage-quickstart-blobs-python.md)<br>[Gyors útmutató: Blobok kezelése a JavaScript V12 SDK-val Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnosztikai naplók|Általánosan elérhető|Előnézet <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure Storage Analytics-naplózás](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Archív hozzáférési szint|Általánosan elérhető|Nem támogatott|[Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív hozzáférési szintek](storage-blob-storage-tiers.md)|
|Életciklus-kezelési szabályzatok|Általánosan elérhető|Még nem támogatott|[Az Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)|
|Bejelentkezés Azure Monitor|Előnézet |Még nem támogatott|[Az Azure Storage figyelése](../common/monitor-storage.md)|
|Pillanatképek|Előnézet|Még nem támogatott|[BLOB-Pillanatképek](snapshots-overview.md)|
|Statikus webhelyek|Előnézet|Még nem támogatott|[Statikus webhely üzemeltetés az Azure Storage-ban](storage-blob-static-website.md)|
|Megváltoztathatatlan tároló|Előnézet|Még nem támogatott|[Üzleti szempontból kritikus fontosságú blob-alapú adattárolás tárolása a nem módosítható tárolóval](storage-blob-immutable-storage.md)|
|Tároló – Soft delete|Előnézet|Előnézet|[Tárolók törlése (előzetes verzió)](soft-delete-container-overview.md)|
|Blobos törlés|Még nem támogatott|Még nem támogatott|[Blobok helyreállítható törlése](storage-blob-soft-delete.md)|
|Blobfuse|Előnézet|Még nem támogatott|[BLOB Storage csatlakoztatása fájlrendszerként a blobfuse-mel](storage-how-to-mount-container-linux.md)|
|Fiók feladatátvétele|Még nem támogatott|Még nem támogatott|[Vész-helyreállítási és-fiók feladatátvétele](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB Container ACL|Még nem támogatott<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Még nem támogatott<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Tároló ACL beállítása](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Ügyfél által biztosított kulcsok|Még nem támogatott|Még nem támogatott|[Titkosítási kulcs megadása a blob Storage-kérelemben](encryption-customer-provided-keys.md)|
|Egyéni tartományok|Még nem támogatott|Még nem támogatott|[Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)|
|Titkosítási hatókörök|Még nem támogatott|Még nem támogatott|[Titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](encryption-scope-manage.md)|
|Csatorna módosítása|Még nem támogatott|Még nem támogatott|[A hírcsatornák támogatásának módosítása az Azure Blob Storage-ban](storage-blob-change-feed.md)|
|Objektumreplikáció|Még nem támogatott|Még nem támogatott|[Objektumok replikálásának konfigurálása a blokk Blobok számára (előzetes verzió)](object-replication-configure.md)|
|BLOB verziószámozása|Még nem támogatott|Még nem támogatott|[BLOB verziószámozásának engedélyezése és kezelése (előzetes verzió)](versioning-enable.md)|

<div id="diagnostic-logging"><sup>1</sup> a prémium szintű blokk blob Storage-fiókok esetében a Azure Portal használatával nem engedélyezhető a diagnosztikai naplók (klasszikus). Engedélyezze őket a PowerShell használatával.</div><br>

<div id="blob-container-ACL"><sup>2</sup> az ACL-eket beállíthatja a tároló gyökérkönyvtárában, a tárolót azonban nem.</div><br>

<div id="preview-form"><sup>3</sup> Ha a pillanatképeket, a nem módosítható tárolókat vagy a statikus webhelyeket Data Lake Storage Gen2 használatával szeretné használni, regisztrálnia kell az előzetes verzióban az <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>űrlap</a>kitöltésével.  </div>

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
