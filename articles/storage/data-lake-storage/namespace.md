---
title: Az Azure Data Lake Storage Gen2 előzetes hierarchikus Namespace
description: Ismerteti a hierarchikus névtér az Azure Data Lake Storage Gen2 előzetes verzió
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 44eec21f4687d2df64c59d41cdb02c6ef2268f82
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528697"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Az Azure Data Lake Storage Gen2 előzetes verzió hierarchikus névtér

Olyan mechanizmus, amely lehetővé teszi az Azure Data Lake Storage Gen2 előzetes verziójához biztosít a fájlt a rendszer teljesítménye objektum storage méretezési és az árak is egy **hierarchikus névtér**. Ez lehetővé teszi az objektumok vagy fájlokat a partner könyvtárak és beágyazott alkönyvtárak hierarchiává, hogy a fájlrendszer, a számítógépen van rendezve ugyanúgy szervezett gyűjteménye. A hierarchikus névtér esetében engedélyezve van a Data Lake Storage Gen2 biztosítja a méretezhetőség és költséghatékonyság objektum tárhely, a fájlrendszer szemantikáját, amelyek elemzési motor és a keretrendszereket.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>A hierarchikus névtér előnyei

> [!NOTE]
> Az Azure Data Lake Storage Gen2 a nyilvános előzetes verzióban az alább felsorolt funkciók némelyike változhat, a rendelkezésre állásuk. Új szolgáltatások és régiók az előzetes programhoz során kiadott, ezt az információt kommunikálja a dedikált Yammer-csoportban keresztül.  

A következő előnyöket blob adatok hierarchikus névtér megvalósító fájlrendszerek tartoznak:

- **Atomi Directory adatkezelési:** az objektum egy könyvtár-hierarchia megbecsülheti egy konvencióját perjelek (/) beágyazása az objektum nevét, elérési út szegmensek jelölésére elfogadásával. Az egyezmény objektumok sorolására működik, amíg az egyezmény nem például az áthelyezés, átnevezés, vagy könyvtárak törlése segítségnyújtást kínál a. Valós könyvtárak nélkül alkalmazásokat fel kell dolgoznia potenciálisan több millió, az egyes blobok elérése érdekében a directory-szintű feladatok. A hierarchikus névtér ezzel szemben az egy-egy bejegyzésnek (a szülő könyvtár) frissítésével dolgozza fel ezeket a feladatokat. 

    Drámai optimalizációt különösen fontos számos big data-elemzési keretrendszer. Eszközök, mint például a Hive, a Spark és az egyéb gyakran kiírhatja a kimenetet ideiglenes helyre, és nevezze át a helyet, a feladat befejezésekor. A hierarchikus névtér nélkül az Átnevezés gyakran több időt vesz igénybe, mint amennyit az analytics dolgozni a saját maga. Feladat kisebb késés egyenlő alacsonyabb teljes birtoklási költségét (TCO) adatelemzési feladatokhoz.

- **Ismerős felületet stílusa:** fájlrendszerek megértsük a fejlesztők és a felhasználók számára is. Hiba esetén nem kell további egy új tárolási költségei, lép, a felhőben, a Data Lake Storage Gen2 által elérhetővé tett fájlrendszer felületen a kis és nagy a számítógépek által használt azonos költségei.

Az okok egyike, hogy az objektum rendelkezik nem régebben támogatott hierarchikus névterek, hogy a hierarchikus névterek méretezési korlátozott. Azonban a Data Lake Storage Gen2 hierarchikus névtér a költségráfordításokkal egyenes arányban, és nem befolyásolja az adatok kapacitás vagy a teljesítmény.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Ha a hierarchikus névtér engedélyezése

Ne tudják bekapcsolni a hierarchikus névtér a fájlrendszerek könyvtárak kezeléséhez tervezett tárolási számítási feladatok esetében ajánlott. Ez magában foglalja az összes számítási feladatokat, amelyek elsősorban elemzési feldolgozás céljából. Magas szintű szervezeti igénylő adatkészleteket is előnyeit a hierarchikus névtér engedélyezésével.

A hierarchikus névtér engedélyezése okait a teljes birtoklási költségének elemzése határozza meg. Általánosan fogalmazva a számítási feladatok késés miatt storage acceleration fejlesztései számítási erőforrásokat igényel kevesebb időt. Számos munkaterhelések késése miatt atomi könyvtár kezelését, amely szerint a hierarchikus névtér engedélyezve van Előfordulhat, hogy javítani. Számos számítási feladatok, a számítási erőforrásokat jelenti > 85 %-a a teljes költség, és tehát még a számítási feladatok késés szerény csökkentését-adatbázisnak felel meg a teljes birtoklási költségének-megtakarítást jelentős mennyiségű. Olyan esetekben, ahol a hierarchikus névtér engedélyezése növeli a tárolási költségeket, még akkor is, a teljes bekerülési Költséget továbbra is csökkenteni miatt a kisebb számítási költségeket.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Mikor érdemes letiltani a hierarchikus névtér

Néhány objektum store számítási feladatok nem tehetnek szert a semmilyen a hierarchikus névtér engedélyezésével. Ilyen számítási feladat például biztonsági mentések képtárolás és más alkalmazások objektum szervezeti tároló külön-külön magukat az objektumokat a (*például*, egy különálló adatbázis).

> [!NOTE]
> Az előzetes kiadással engedélyezi a hierarchikus névtér van-e nem együttműködési adat-vagy Blob és a Data Lake Storage Gen2 REST API-k közötti műveleteket. Ez a funkció előzetes során fog bővülni.

## <a name="next-steps"></a>További lépések

- [Storage-fiók létrehozása](./quickstart-create-account.md)