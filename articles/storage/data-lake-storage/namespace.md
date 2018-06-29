---
title: Az Azure Data Lake tárolási Gen2 Preview hierarchikus Namespace
description: A hierarchikus névtér ismerteti az Azure Data Lake tárolási Gen2 előzetes
services: storage
author: jamesbak
manager: twooley
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: f7c3820624a4ef27e2ece4d902f2c033b6a6f48f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061220"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Az Azure Data Lake tárolási Gen2 előzetes hierarchikus névtér

A kulcs mechanizmus, amely lehetővé teszi az Azure Data Lake tárolási Gen2 Preview fájl rendszerteljesítmény objektum storage méretezési és árakat, így a parancs egy **hierarchikus névtér**. Ez lehetővé teszi a gyűjteményt, objektumok vagy fájl a partner könyvtárak és beágyazott alkönyvtárak hierarchiáját, hogy a fájlrendszer a számítógépen vannak rendezve ugyanúgy kell rendezni. A hierarchikus névtér engedélyezve van Data Lake tárolási Gen2 nyújt a méretezhetőség és a költséghatékonyság objektum a tárhely, fájlrendszer szemantikáját, amelyek analytics motorok és keretrendszerek számára.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>A hierarchikus névtér előnyei

> [!NOTE]
> Az Azure Data Lake tárolási Gen2 nyilvános előzetes az alább felsorolt funkciók némelyike változhat a rendelkezésre állásuk. Új szolgáltatásokat és régiók kiadott során a programot, a tájékoztatni kell dedikált Yammer csoportunk keresztül.  

A következő előnyöket társítva, amelyek megvalósítják a hierarchikus névtér Blobadatok keresztül fájlrendszerek:

- **Atomi Directory adatkezelési:** objektum tárolja a könyvtár-hierarchia hozzávetőleges olyan konvenciót perjelet (/) beágyazása az objektum neve szegmenst jelöléséhez elfogadásával. Az egyezmény objektumok sorolására kielégítően működik, amíg az egyezmény segítség nélkül például áthelyezését, átnevezése vagy könyvtárak törlése. Valós könyvtárak nélkül alkalmazásokat fel kell dolgoznia potenciálisan egyes blobok directory szintű feladatok eléréséhez több millió. Ezzel szemben a hierarchikus névtér dolgozza fel ezeket a feladatokat egy-egy bejegyzésnek (a szülőkönyvtárat) frissítése. 

    Az drámai optimalizálás különösen fontos sok big Data típusú adatok elemzési keretrendszerek. Eszközök, például a Hive, Spark, stb. gyakran írni a kimeneti ideiglenes helyre, és nevezze át a helyet, a feladat befejezését. A hierarchikus névtér nélküli az Átnevezés gyakran több időt vesz igénybe, mint az analytics folyamat saját magát. Alacsonyabb feladat késés alacsonyabb teljes birtoklási költség (TCO) analytics munkaterhelések egyenlő.

- **Ismeri felület stílus:** fájlrendszerek is fejlesztők és a felhasználók számára is azt értelmezni tudja. Nincs szükség további egy új tárolási összeállítást, amikor helyezi át a felhő által Data Lake tárolási Gen2 fájl rendszer felülete a nagy- és a számítógépek által használt azonos paradigma áll.

Az oka, hogy objektum tárolók támogatott van-e nem hagyományosan hierarchikus névtér egyik, hogy a hierarchikus névtér méretezési korlátozott. Azonban a Data Lake tárolási Gen2 hierarchikus névtér lineárisan méretezi, és nem befolyásolja az adatok kapacitás vagy a teljesítmény.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Ha a hierarchikus névtér engedélyezése

A hierarchikus névtér bekapcsolásával tárolási munkaterhelések fájlrendszerek, könyvtárak kezelésére kifejlesztett ajánlott. Ez magában foglalja az összes elsősorban analytics feldolgozásra munkaterhelések. A szervezet magas fokú igénylő adatkészletek is előnyösek azáltal, hogy a hierarchikus névtér.

A hierarchikus névtér engedélyezése okait egy TCO analysis határozza meg. Általánosságban véve a munkaterhelés késés miatt tárolási gyorsítás fejlesztései számítási erőforrásokat igényel rövidebb idő alatt. Számos különféle munkaterheléshez késés miatt a hierarchikus névtér által engedélyezett atomi directory adatkezelési növelhető. Számos különféle munkaterheléshez a számítási erőforrással jelenti. a teljes költség > 85 % és munkaterhelések késése még így mérsékelt csökkenése TCO megtakarítások jelentős mennyiségű megfelel. Olyan esetekben, ahol a hierarchikus névtér engedélyezése növeli tárolási költségek, még akkor is, a teljes Birtoklási kevesebb számítási költségek miatt továbbra is csökken.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Ha a hierarchikus névtér letiltása

Bizonyos objektum áruházbeli alkalmazások és szolgáltatások engedélyezése a hierarchikus névtér nem szerezhetnek hasznos. Az ilyen terhelések közé biztonsági mentések, lemezkép tárolási és más alkalmazások objektum szervezet tároló külön-külön a magukat az objektumokat a (*pl.*, külön adatbázisban).

> [!NOTE]
> Előzetes verziójára Ha engedélyezi a hierarchikus névtér esetén nem együttműködési adatok vagy a műveletek Blob és a Data Lake tárolási Gen2 REST API-k között. Ez a funkció előzetes lesz hozzáadva.

## <a name="next-steps"></a>További lépések

- [A Storage-fiók létrehozása](./quickstart-create-account.md)