---
title: Az Azure Data Factory leképezés adatfolyam Optimalizálás lap
description: Adatfolyamok optimalizálása lapon partíció beállításokkal leképezése az Azure Data Factory optimalizálása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 31aca6f4ccfe9991a1dcf2808815a4543e2c6fe6
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326459"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Hozzárendelési folyamat átalakítását Optimalizálás lap

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Minden egyes adatfolyam átalakítása rendelkezik egy "Optimalizálás" fülre. Az Optimalizálás lap nem kötelező beállítások konfigurálása az adatfolyamok particionálási sémát tartalmaz.

<img src="media/data-flow/opt001.png" width="800">

Az alapértelmezett érték "aktuális particionálás használata". Aktuális particionálás arra utasítja az Azure Data Factory használandó natívan adatfolyamok futó Spark az Azure Databricksben particionálási sémát. Általában ez nem az ajánlott eljárás.

Vannak azonban előfordulhat, hogy szeretne módosítani, a particionálás példányok. Például ha azt szeretné, a kimenetben az átalakításokat a Lake egy fájlba, majd választott "egyetlen partíció" az Optimalizálás lapján a fogadó átalakításában szerepel a particionálást.

Egy másik eset, ahol érdemes felügyeljék a particionálási sémákat a adatátalakítások használt a teljesítmény tekintetében. Az adatok particionálása módosításával biztosít felügyelet alatt tartani az adatokat a terjesztési számítási csomópontokat és az adatok helye optimalizálásokat a a teljes folyamat teljesítményét a pozitív és is negatív hatással lehetnek.

Ha úgy szeretné módosítani a bármely átalakítása a particionálás, egyszerűen kattintson az Optimalizálás fülre, és válassza a "Particionálás beállítása" választógombot. Ezután megjelenik több lehetőségei a particionálást. Particionálás végrehajtására a legjobb módszer az adatkötetek, a keresési kulcs, a null értékeket és a számossága alapján eltérnek. Ajánlott eljárás, hogy indítsa el az alapértelmezett particionálási, és ismételje meg a különböző particionálási beállításokat. A hibakeresési folyamat futtatása, és nézze meg, töltött időt a csoportosítás és a figyelés nézetből partíció használata minden egyes átalakításában használatával tesztelhet.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Ciklikus időszeletelés

Ciklikus időszeletelés egyszerű partíció, amely automatikusan elosztja az adatokat egyaránt partíciók között. Használja ciklikus időszeletelés, ha nem rendelkezik egy szilárd, intelligens particionálási stratégia megvalósításához kulcs deduplikációra. Beállíthatja, hogy a fizikai partíciók számát.

### <a name="hash"></a>Kivonat

Az Azure Data Factory egy kivonatot az oszlopok egységes partíciók előállítására úgy, hogy a sorok hasonló értékekkel fog esni ugyanazon a partíción állítja elő. A kivonatoló lehetőség használatakor ellenőrizze a lehetséges torzulása partíció. Beállíthatja, hogy a fizikai partíciók számát.

### <a name="dynamic-range"></a>Dinamikus tartomány

Dinamikus tartomány Spark oszlopokat vagy Ön által megadott kifejezések alapján dinamikus címtartományokat fogja használni. Beállíthatja, hogy a fizikai partíciók számát. 

### <a name="fixed-range"></a>Rögzített tartomány

Egy kifejezés, amely értékek, particionált adatok az oszlopokban a rögzített kínál kell létrehozni. Az adatok beható ismerete, mielőtt ezt a beállítást annak érdekében, hogy partíció döntés kell rendelkeznie. Adja meg a kifejezés értékét a partíciós függvények részeként használható. Beállíthatja, hogy a fizikai partíciók számát.

### <a name="key"></a>Kulcs

Ha az adatok a számosságot beható ismerete, kulcs particionálás egy jó partíció stratégia lehet. Kulcs particionálás az oszlopban található létrehozza a partíciók minden egyedi értékére vonatkozóan. A partíciók száma nem állítható, mert az a szám az adatok egyedi értékeket fog alapulni.
