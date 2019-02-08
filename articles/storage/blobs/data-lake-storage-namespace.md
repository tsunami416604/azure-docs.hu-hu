---
title: Az Azure Data Lake Storage Gen2 hierarchikus Namespace
description: Az Azure Data Lake Storage Gen2 a hierarchikus névtér ismerteti
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b423d40884dd9132312e79ba0cbff00b8771b207
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877031"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Az Azure Data Lake Storage Gen2 hierarchikus névtér

Olyan mechanizmus, amely lehetővé teszi az Azure Data Lake Storage Gen2, adja meg a fájlt a rendszer teljesítménye objektum storage méretezési és az árak is egy **hierarchikus névtér**. Ez lehetővé teszi az objektumok vagy fájlokat a partner könyvtárak és beágyazott alkönyvtárak hierarchiává, hogy a fájlrendszer, a számítógépen van rendezve ugyanúgy szervezett gyűjteménye. A hierarchikus névtér esetében engedélyezve van a storage-fiók lesz képes a méretezhetőség és költséghatékonyság objektum tárolókészlet, így a fájlrendszer szemantikáját, amelyek elemzési motor és a keretrendszereket.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>A hierarchikus névtér előnyei

A következő előnyöket blob adatok hierarchikus névtér megvalósító fájlrendszerek tartoznak:

- **Atomi directory adatkezelési:** Az objektum egy könyvtár-hierarchia megbecsülheti egy konvencióját perjelek (/) beágyazása az objektum nevét, elérési út szegmensek jelölésére elfogadásával. Az egyezmény objektumok sorolására működik, amíg az egyezmény nem például az áthelyezés, átnevezés, vagy könyvtárak törlése segítségnyújtást kínál a. Valós könyvtárak nélkül alkalmazásokat fel kell dolgoznia potenciálisan több millió, az egyes blobok elérése érdekében a directory-szintű feladatok. A hierarchikus névtér ezzel szemben az egy-egy bejegyzésnek (a szülő könyvtár) frissítésével dolgozza fel ezeket a feladatokat.

    Drámai optimalizációt különösen fontos számos big data-elemzési keretrendszer. Eszközök, mint például a Hive, a Spark és az egyéb gyakran kiírhatja a kimenetet ideiglenes helyre, és nevezze át a helyet, a feladat befejezésekor. A hierarchikus névtér nélkül az Átnevezés gyakran több időt vesz igénybe, mint amennyit az analytics dolgozni a saját maga. Feladat kisebb késés egyenlő alacsonyabb teljes birtoklási költségét (TCO) adatelemzési feladatokhoz.

- **Ismerős felületet stílusa:** Fájlrendszerek megértsük a fejlesztők és a felhasználók számára is. Hiba esetén nem kell további egy új tárolási költségei, lép, a felhőben, a Data Lake Storage Gen2 által elérhetővé tett fájlrendszer felületen a kis és nagy a számítógépek által használt azonos költségei.

Az okok egyike, hogy az objektum korábban még nem támogatott a hierarchikus névtér egy hierarchikus névtér a skálázási korlátokat. Azonban a Data Lake Storage Gen2 hierarchikus névtér a költségráfordításokkal egyenes arányban, és nem befolyásolja az adatok kapacitás vagy a teljesítmény.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Ha a hierarchikus névtér engedélyezése

Azt javasoljuk, hogy kapcsolja be a hierarchikus névtér kezelheti a címtárakat fájlrendszerek tervezett tárolási számítási feladatok számára. Ez magában foglalja az összes számítási feladatokat, amelyek elsősorban elemzési feldolgozás céljából. Magas szintű szervezeti igénylő adatkészleteket is előnyeit a hierarchikus névtér engedélyezésével.

A hierarchikus névtér engedélyezése okait a teljes birtoklási költségének elemzése határozza meg. Általánosan fogalmazva a számítási feladatok késés miatt storage acceleration fejlesztései számítási erőforrásokat igényel kevesebb időt. Számos munkaterhelések késése miatt atomi könyvtár kezelését, amely szerint a hierarchikus névtér engedélyezve van Előfordulhat, hogy javítani. Számos számítási feladatok, a számítási erőforrásokat jelenti > 85 %-a a teljes költség, és tehát még a számítási feladatok késés szerény csökkentését-adatbázisnak felel meg a teljes birtoklási költségének-megtakarítást jelentős mennyiségű. Olyan esetekben, ahol a hierarchikus névtér engedélyezése növeli a tárolási költségeket, még akkor is, a teljes bekerülési Költséget továbbra is csökkenteni miatt a kisebb számítási költségeket.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Mikor érdemes letiltani a hierarchikus névtér

Néhány objektum store számítási feladatok nem telepítőcsomaghoz semmilyen a hierarchikus névtér engedélyezésével. Ilyenek például a biztonsági mentések, lemezkép tárolási és egyéb objektum szervezeti tároló külön-külön magukat az objektumokat az alkalmazások (például: önálló adatbázis).

## <a name="next-steps"></a>További lépések

- [Storage-fiók létrehozása](./data-lake-storage-quickstart-create-account.md)
