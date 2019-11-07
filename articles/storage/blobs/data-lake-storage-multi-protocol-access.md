---
title: Több protokollos hozzáférés a Azure Data Lake Storageon | Microsoft Docs
description: Használjon olyan blob API-kat és alkalmazásokat, amelyek blob API-kat használnak Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f3e852d178a6720f8d567f4ecb042f0b83bcd56c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583145"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Több protokollos hozzáférés Azure Data Lake Storage

A blob API-k mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezzel feloldja az eszközök, alkalmazások és szolgáltatások ökoszisztémáját, valamint számos blob Storage-szolgáltatást a hierarchikus névtérrel rendelkező fiókokhoz.

A legutóbbi időpontig előfordulhat, hogy külön tárolási megoldásokat kellett volna fenntartania az objektumok tárolásához és az elemzések tárolásához. Ennek az az oka, hogy Azure Data Lake Storage Gen2 korlátozott az ökoszisztéma-támogatással. Emellett korlátozott hozzáféréssel rendelkezik Blob service-funkciókhoz, például a diagnosztikai naplózáshoz. A töredezett tárolási megoldás nehezen kezelhető, mert a különböző forgatókönyvek elvégzése érdekében át kell helyeznie az adatátvitelt a fiókok között. Már nem kell ezt megtennie.

A Data Lake Storage többprotokollos hozzáférése révén az adataival az eszközök, alkalmazások és szolgáltatások ökoszisztémájának használatával dolgozhat. Ez magában foglalja a harmadik féltől származó eszközöket és alkalmazásokat is. Azokat olyan fiókokra irányíthatja, amelyek hierarchikus névtérrel rendelkeznek anélkül, hogy módosítani kellene őket. Ezek az alkalmazások *ugyanúgy működnek, mint a* blob API-k meghívásával, mivel a blob API-k mostantól hierarchikus névtérrel rendelkező fiókokban is működhetnek.

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatai](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a fontos funkciókhoz kellene hozzáférni. 

> [!NOTE]
> A több protokollon keresztüli hozzáférés Data Lake Storage általánosan elérhető, és minden régióban elérhető. A több protokollos hozzáférés által engedélyezett Azure-szolgáltatások vagy blob Storage-funkciók előzetes verzióban maradnak. További információért tekintse meg a jelen cikk egyes szakaszaiban található táblázatokat. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>A multi-Protocol-hozzáférés a (z) adatközpont-tárolóban

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek, mint a hierarchikus névtérrel rendelkező Storage-fiókok. Data Lake Storage Gen2 a blob API-kat a hierarchikus névtéren keresztül irányítja, így az első osztályú címtár-műveletek és a POSIX-kompatibilis hozzáférés-vezérlési listák (ACL-ek) előnyeit érheti el. 

![Több protokollos hozzáférés Data Lake Storage fogalmi](./media/data-lake-storage-interop/interop-concept.png) 

A blob API-t használó meglévő eszközök és alkalmazások automatikusan hozzáférhetnek ezekhez az előnyökhöz. A fejlesztőknek nem kell módosítaniuk azokat. Data Lake Storage Gen2 konzisztensen alkalmazza a címtár-és a fájl szintű ACL-eket, függetlenül attól, hogy az eszközök és az alkalmazások milyen protokollt használnak az adateléréshez. 

## <a name="blob-storage-feature-support"></a>BLOB Storage szolgáltatás támogatása

A több protokollon keresztüli hozzáférés Data Lake Storage lehetővé teszi, hogy több blob Storage-szolgáltatást használjon a Data Lake Storage. Ez a táblázat felsorolja azokat a funkciókat, amelyeket az Data Lake Storage több protokollos hozzáférése engedélyez. 

Az ebben a táblázatban megjelenő elemek a blob Storage-szolgáltatások támogatása során továbbra is változnak. 

> [!NOTE]
> Bár a több protokollos hozzáférés a Data Lake Storage általánosan elérhető, a szolgáltatások némelyikének támogatása előzetes verzióban marad. 

|BLOB Storage szolgáltatás | Támogatási szint |
|---|---|
|[Lassú elérési szint](storage-blob-storage-tiers.md)|Általánosan elérhető|
|BLOB SDK-k |Általánosan elérhető|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Általánosan elérhető|
|[Parancssori felület](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Általánosan elérhető|
|[Diagnosztikai naplók](../common/storage-analytics-logging.md)| Előzetes verzió|
|[Életciklus-kezelési szabályzatok](storage-lifecycle-management-concepts.md)| Előzetes verzió|
|[Értesítések Azure Event Grid keresztül](data-lake-storage-events.md)|Előzetes verzió|
|[Archivált adatok hozzáférési szintje](storage-blob-storage-tiers.md)| Előzetes verzió|
|[blobfuse](storage-how-to-mount-container-linux.md)|Még nem támogatott|
|[Megváltoztathatatlan tároló](storage-blob-immutable-storage.md)|Még nem támogatott|
|[Pillanatképek](storage-blob-snapshots.md)|Még nem támogatott|
|[Helyreállítható törlés](storage-blob-soft-delete.md)|Még nem támogatott|
|[Statikus webhelyek](storage-blob-static-website.md)|Még nem támogatott|

Ha többet szeretne megtudni az általános ismert problémákról és a Azure Data Lake Storage Gen2ekkel kapcsolatos korlátozásokról, tekintse meg az [ismert problémák](data-lake-storage-known-issues.md)című témakört.

## <a name="azure-ecosystem-support"></a>Azure ökoszisztéma-támogatás

A többprotokollos hozzáférés Data Lake Storage is lehetővé teszi, hogy további Azure-szolgáltatásokat csatlakoztasson a Data Lake Storage. Ez a táblázat felsorolja azokat a szolgáltatásokat, amelyeket a Data Lake Storage a multi-Protocol hozzáférése engedélyez. 

Akárcsak a támogatott blob Storage-funkciók listája, az ebben a táblázatban megjelenő elemek idővel változnak, ahogy az Azure-szolgáltatások támogatása továbbra is bővül. 

> [!NOTE]
> Bár a több protokollos hozzáférés Data Lake Storage általánosan elérhető, a szolgáltatások némelyikének támogatása előzetes verzióban marad. 

|Azure-szolgáltatás | Támogatási szint |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Általánosan elérhető|
|[Azure Event Hubs rögzítés](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Általánosan elérhető|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Általánosan elérhető|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Általánosan elérhető|
|[Logikai alkalmazások](https://azure.microsoft.com/services/logic-apps/)|Általánosan elérhető|
|[Azure Search](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|Előzetes verzió|

Az Data Lake Storage Gen2 Azure ökoszisztéma-támogatásának teljes listájáért lásd: a [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](data-lake-storage-integrate-with-azure-services.md).

Ha többet szeretne megtudni az általános ismert problémákról és a Azure Data Lake Storage Gen2ekkel kapcsolatos korlátozásokról, tekintse meg az [ismert problémák](data-lake-storage-known-issues.md)című témakört.

## <a name="next-steps"></a>További lépések

[Ismert problémák](data-lake-storage-known-issues.md)




