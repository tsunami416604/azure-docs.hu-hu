---
title: Többprotokollos hozzáférés az Azure Data Lake Storage szolgáltatásban | Microsoft dokumentumok
description: Blob API-k és alkalmazások, amelyek blob API-k at az Azure Data Lake Storage Gen2 használatával.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914858"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Többprotokollos hozzáférés az Azure Data Lake Storage szolgáltatásban

A blob API-k mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ez feloldja az eszközök, alkalmazások és szolgáltatások ökoszisztémáját, valamint számos Blob storage-szolgáltatást a hierarchikus névtérrel rendelkező fiókok számára.

Egészen a közelmúltig előfordulhat, hogy külön tárolási megoldásokat kellett fenntartania az objektumtárolási és elemzési tároláshoz. Ennek az az oka, hogy az Azure Data Lake Storage Gen2 korlátozott ökoszisztéma-támogatással rendelkezett. Emellett korlátozott hozzáféréssel rendelkezett a Blob szolgáltatás szolgáltatásaihoz, például a diagnosztikai naplózáshoz. A töredezett tárolási megoldás nehezen karbantartható, mert az adatokat a fiókok között kell áthelyezni a különböző forgatókönyvek elvégzéséhez. Nem kell többé ezt csinálnod.

A Data Lake Storage többprotokollos hozzáférésével az eszközök, alkalmazások és szolgáltatások ökoszisztémájának használatával dolgozhat az adatokkal. Ez magában foglalja a harmadik féltől származó eszközöket és alkalmazásokat is. A hierarchikus névtérrel rendelkező fiókokra is rámutathat anélkül, hogy módosítania kellene őket. Ezek az alkalmazások akkor is *működnek,* ha blob API-kat hívnak meg, mert a Blob API-k mostantól hierarchikus névtérrel rendelkező fiókok ban is működhetnek.

A Blob storage-szolgáltatások, például [a diagnosztikai naplózás,](../common/storage-analytics-logging.md)a hozzáférési szintek és a [Blob-tárolási életciklus-kezelési](storage-lifecycle-management-concepts.md) [házirendek](storage-blob-storage-tiers.md)mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért hierarchikus névtereket engedélyezhet a blob Storage-fiókokon anélkül, hogy elveszítené a hozzáférést ezekhez a fontos funkciókhoz. 

> [!NOTE]
> A Data Lake Storage többprotokollos hozzáférése általánosan elérhető, és minden régióban elérhető. Egyes Azure-szolgáltatások vagy blob storage-funkciók által engedélyezett több protokollos hozzáférés továbbra is előzetes verzióban marad.  Ezek a cikkek összefoglalják a Blob storage-funkciók és az Azure-szolgáltatásintegrációk jelenlegi támogatását. 
>
> [Az Azure Data Lake Storage Gen2 szolgáltatásában elérhető Blob Storage-funkciók](data-lake-storage-supported-blob-storage-features.md)
>
>[Az Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>A többprotokollos hozzáférés a data lake storage-ban?

A Blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adaton működhetnek a hierarchikus névtérrel rendelkező tárfiókokban. A Data Lake Storage Gen2 a hierarchikus névtéren keresztül irányítja a Blob API-kat, így az első osztályú címtárműveletek és a POSIX-kompatibilis hozzáférés-vezérlési listák (ACL-ek) előnyeit is élvezheti. 

![Többprotokollos hozzáférés a Data Lake Storage fogalmi](./media/data-lake-storage-interop/interop-concept.png) 

A Blob API-t használó meglévő eszközök és alkalmazások automatikusan megszerzik ezeket az előnyöket. A fejlesztőknek nem kell módosítaniuk őket. Data Lake Storage Gen2 consistently applies directory and file-level ACLs regardless of the protocol that tools and applications use to access the data. 

## <a name="see-also"></a>Lásd még

- [Az Azure Data Lake Storage Gen2 szolgáltatásában elérhető Blob Storage-funkciók](data-lake-storage-supported-blob-storage-features.md)
- [Az Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)
- [Az Azure Data Lake Storage Gen2 szolgáltatást támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban](data-lake-storage-known-issues.md)




