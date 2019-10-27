---
title: Több protokollos hozzáférés Azure Data Lake Storage (előzetes verzió) | Microsoft Docs
description: Használjon olyan blob API-kat és alkalmazásokat, amelyek blob API-kat használnak Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6e3b368d1f5615c34a49b155d288d3c37c28bc26
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933133"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Több protokollos hozzáférés Azure Data Lake Storage (előzetes verzió)

A blob API-k mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezzel feloldja az eszközök, alkalmazások és szolgáltatások teljes ökoszisztémáját, valamint a blob Storage összes funkcióját a hierarchikus névtérrel rendelkező fiókokhoz.

A legutóbbi időpontig előfordulhat, hogy külön tárolási megoldásokat kellett volna fenntartania az objektumok tárolásához és az elemzések tárolásához. Ennek az az oka, hogy Azure Data Lake Storage Gen2 korlátozott az ökoszisztéma-támogatással. Emellett korlátozott hozzáféréssel rendelkezik Blob service-funkciókhoz, például a diagnosztikai naplózáshoz. A töredezett tárolási megoldás nehezen kezelhető, mert a különböző forgatókönyvek elvégzése érdekében át kell helyeznie az adatátvitelt a fiókok között. Már nem kell ezt megtennie.

> [!NOTE]
> A több protokollon keresztüli hozzáférés Data Lake Storage nyilvános előzetes verzióban érhető el, és minden régióban elérhető. Nem kell regisztrálnia a nyilvános előzetes verzióban, mert az automatikusan elérhető minden olyan fiók számára, amely hierarchikus névtérrel rendelkezik. A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket.

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Az alkalmazások, eszközök és szolgáltatások teljes ökoszisztémájának használata

A Data Lake Storage többprotokollos hozzáférése révén az összes adatait használhatja az eszközök, alkalmazások és szolgáltatások teljes ökoszisztémájának használatával. Ide tartoznak az olyan Azure-szolgáltatások, mint a [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), az [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), a [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)és sok más. A teljes listát lásd: [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](data-lake-store-integrate-with-azure-services.md).

Ez magában foglalja a harmadik féltől származó eszközöket és alkalmazásokat is. Azokat olyan fiókokra irányíthatja, amelyek hierarchikus névtérrel rendelkeznek anélkül, hogy módosítani kellene őket. Ezek az alkalmazások *ugyanúgy működnek, mint a* blob API-k meghívásával, mivel a blob API-k mostantól hierarchikus névtérrel rendelkező fiókokban is működhetnek.

> [!NOTE]
> A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket.

## <a name="use-all-blob-storage-features"></a>A blob Storage összes funkciójának használata

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatai](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a fontos funkciókhoz kellene hozzáférni. 

> [!NOTE]
> A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket.

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>A multi-Protocol-hozzáférés a (z) adatközpont-tárolóban

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek, mint a hierarchikus névtérrel rendelkező Storage-fiókok. Data Lake Storage Gen2 a blob API-kat a hierarchikus névtéren keresztül irányítja, így az első osztályú címtár-műveletek és a POSIX-kompatibilis hozzáférés-vezérlési listák (ACL-ek) előnyeit érheti el. 

![Több protokollos hozzáférés Data Lake Storage fogalmi](./media/data-lake-storage-interop/interop-concept.png) 

A blob API-t használó meglévő eszközök és alkalmazások automatikusan hozzáférhetnek ezekhez az előnyökhöz. A fejlesztőknek nem kell módosítaniuk azokat. Data Lake Storage Gen2 konzisztensen alkalmazza a címtár-és a fájl szintű ACL-eket, függetlenül attól, hogy az eszközök és az alkalmazások milyen protokollt használnak az adateléréshez. 

## <a name="next-steps"></a>Következő lépések

[Ismert problémák](data-lake-storage-known-issues.md)




