---
title: Az Azure Data Lake Storage Gen2 előzetes hierarchikus Namespace
description: Ismerteti a hierarchikus névtér az Azure Data Lake Storage Gen2 előzetes verzió
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 967e24ae6e004fe6ce2b1c0aa6c039f46be2598c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244504"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Az Azure Data Lake Storage Gen2 előzetes verzió hierarchikus névtér

Olyan mechanizmus, amely lehetővé teszi az Azure Data Lake Storage Gen2 előzetes verziójához biztosít a fájlt a rendszer teljesítménye objektum storage méretezési és az árak is egy **hierarchikus névtér**. Ez lehetővé teszi az objektumok vagy fájlokat a partner könyvtárak és beágyazott alkönyvtárak hierarchiává, hogy a fájlrendszer, a számítógépen van rendezve ugyanúgy szervezett gyűjteménye. A hierarchikus névtér esetében engedélyezve van a storage-fiók lesz képes a méretezhetőség és költséghatékonyság objektum tárolókészlet, így a fájlrendszer szemantikáját, amelyek elemzési motor és a keretrendszereket.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>A hierarchikus névtér előnyei

A következő előnyöket blob adatok hierarchikus névtér megvalósító fájlrendszerek tartoznak:

- **Atomi directory adatkezelési:** Az objektum egy könyvtár-hierarchia megbecsülheti egy konvencióját perjelek (/) beágyazása az objektum nevét, elérési út szegmensek jelölésére elfogadásával. Az egyezmény objektumok sorolására működik, amíg az egyezmény nem például az áthelyezés, átnevezés, vagy könyvtárak törlése segítségnyújtást kínál a. Valós könyvtárak nélkül alkalmazásokat fel kell dolgoznia potenciálisan több millió, az egyes blobok elérése érdekében a directory-szintű feladatok. A hierarchikus névtér ezzel szemben az egy-egy bejegyzésnek (a szülő könyvtár) frissítésével dolgozza fel ezeket a feladatokat.

    Drámai optimalizációt különösen fontos számos big data-elemzési keretrendszer. Eszközök, mint például a Hive, a Spark és az egyéb gyakran kiírhatja a kimenetet ideiglenes helyre, és nevezze át a helyet, a feladat befejezésekor. A hierarchikus névtér nélkül az Átnevezés gyakran több időt vesz igénybe, mint amennyit az analytics dolgozni a saját maga. Feladat kisebb késés egyenlő alacsonyabb teljes birtoklási költségét (TCO) adatelemzési feladatokhoz.

- **Ismerős felületet stílusa:** Fájlrendszerek megértsük a fejlesztők és a felhasználók számára is. Hiba esetén nem kell további egy új tárolási költségei, lép, a felhőben, a Data Lake Storage Gen2 által elérhetővé tett fájlrendszer felületen a kis és nagy a számítógépek által használt azonos költségei.

Az okok egyike, hogy az objektum rendelkezik nem régebben támogatott hierarchikus névterek, hogy a hierarchikus névterek méretezési korlátozott. Azonban a Data Lake Storage Gen2 hierarchikus névtér a költségráfordításokkal egyenes arányban, és nem befolyásolja az adatok kapacitás vagy a teljesítmény.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Ha a hierarchikus névtér engedélyezése

Ne tudják bekapcsolni a hierarchikus névtér a fájlrendszerek könyvtárak kezeléséhez tervezett tárolási számítási feladatok esetében ajánlott. Ez magában foglalja az összes számítási feladatokat, amelyek elsősorban elemzési feldolgozás céljából. Magas szintű szervezeti igénylő adatkészleteket is előnyeit a hierarchikus névtér engedélyezésével.

A hierarchikus névtér engedélyezése okait a teljes birtoklási költségének elemzése határozza meg. Általánosan fogalmazva a számítási feladatok késés miatt storage acceleration fejlesztései számítási erőforrásokat igényel kevesebb időt. Számos munkaterhelések késése miatt atomi könyvtár kezelését, amely szerint a hierarchikus névtér engedélyezve van Előfordulhat, hogy javítani. Számos számítási feladatok, a számítási erőforrásokat jelenti > 85 %-a a teljes költség, és tehát még a számítási feladatok késés szerény csökkentését-adatbázisnak felel meg a teljes birtoklási költségének-megtakarítást jelentős mennyiségű. Olyan esetekben, ahol a hierarchikus névtér engedélyezése növeli a tárolási költségeket, még akkor is, a teljes bekerülési Költséget továbbra is csökkenteni miatt a kisebb számítási költségeket.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Mikor érdemes letiltani a hierarchikus névtér

Néhány objektum store számítási feladatok nem tehetnek szert a semmilyen a hierarchikus névtér engedélyezésével. Ilyen számítási feladat például biztonsági mentések képtárolás és más alkalmazások objektum szervezeti tároló külön-külön magukat az objektumokat a (*például*, egy különálló adatbázis).


## <a name="next-steps"></a>További lépések

- [Storage-fiók létrehozása](./data-lake-storage-quickstart-create-account.md)
