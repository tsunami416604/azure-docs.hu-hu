---
title: Az Azure Data Factory-térképadatokat Flow vizuális figyelés
description: Vizuálisan az Azure Data Factory adatokat folyamatok figyelése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737858"
---
# <a name="monitor-data-flows"></a>A figyelő-adatfolyamok

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Létrehozása és hibakeresése során az adatokat a folyamat befejezése után célszerű az adatfolyama hajthat végre egy folyamat keretében ütemezés ütemezése. A folyamat az Azure Data Factory eseményindítók használatával is ütemezhető. Vagy a Aktiválás most lehetőséget az Azure Data Factory folyamat Builder segítségével hajtható végre egy egyszeri futtatás végrehajtási folyamat keretein belül az adatok folyamat teszteléséhez.

A folyamat futtatásakor lesz figyelheti a folyamatot, és az összes többek között az adatfolyam tevékenységet a folyamatban foglalt tevékenység. Kattintson a monitor ikonra a bal oldali panelen az Azure Data Factory felhasználói felületén. Az alábbihoz hasonló képernyő jelenik meg. A kiemelt ikonnal lehetővé teszi, hogy a folyamat, beleértve az adatfolyam tevékenységet a tevékenységek részletes.

<img src="media/data-flow/mon001.png" width="800">

Látni fogja, jól inculding. Ezzel stats szintű a futtatási időt és állapotát. A tevékenység szinten futtatás azonosítója eltér, hogy a Futtatás AZONOSÍTÓJÁT a folyamat szintjén. A futtatás azonosítója, az előző szintjén van, a folyamat. Kattintson a szemüveg kap részletes részletei az adatokat a folyamat végrehajtását a.

<img src="media/data-flow/mon002.png" width="800">

Ha a figyelési nézet grafikus csomópontban, látni fogja az adatokat a folyamat graph egyszerűsített csak megtekintési verziója.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Data Flow végrehajtási tervek megtekintése

Az adatfolyam végrehajtásakor a Databricks Azure Data Factory optimális kódhoz tartozó elérési út a entirity a adatfolyama alapján határozza meg. Ezenkívül a végrehajtási elérési utak a horizontális felskálázás különböző csomópontokon és adatpartíciókra fordulhat elő. Ezért a figyelési graph jelöli a folyamat, figyelembe véve az átalakításokat végrehajtási útvonalát kialakítását. Amikor az egyes csomópontokon kattint, látni fogja a "Csoportosítás", amelyek a kód végrehajtott együtt a fürtön. A időzítés és számát, láthatja, amelyeket jelölik ezeket a csoportokat az egyes lépések a tervezés szemben.

<img src="media/data-flow/mon004.png" width="800"> 

* Kattintva nyissa meg a helyet, a megfigyelési ablakban, a statisztikák alsó ablaktábláján megjelenik a időzítési, és minden egyes fogadó- és a fogadó adatait az átalakítási leszármaztatási vezettek átalakítások sor száma.

* Amikor kiválasztja az egyes átalakítások, kapni fog a további visszajelzés a jobb oldali panelen, amely megjeleníti a partíció statisztikái, oszlopok számát, az eltéréseket (hogyan van az adatok egyenletesen partíciók között), és (hogyan spikey van az adatok) értékek.

* A csomópontnézet a fogadó kattint, látni fogja oszlop leszármaztatási. Három különböző módszerrel, hogy oszlopokat az adatfolyam a fogadó partra egész összegzi a rendszer. Ezek a következők:

  * Számított: A feltételes feldolgozást vagy egy kifejezés használata az oszlop az adatfolyamot, de nem kerülnek, a fogadó
  * Származtatott: Az oszlop egy olyan új oszlop, a flow-ban létrehozott, azaz nem szerepel a forrás
  * Csatlakoztatott: Az oszlop a forrás- és a rendszer származik, a fogadó mező leképezése
  
## <a name="monitor-icons"></a>A figyelő ikonok

Ez az ikon azt jelenti, hogy az átalakítási adatok már gyorsítótárazva lett a fürtön, így az időzítésüket végrehajtási útvonalat kell figyelembe venni és, amely:

<img src="media/data-flow/mon005.png" width="800"> 

Az átalakítást a zöld körön ikonok is megjelenik. Képviselik, amelyek az adatok beérkeznek fogadóként darabszámát.
