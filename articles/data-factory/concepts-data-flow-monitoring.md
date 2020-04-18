---
title: Adatfolyam leképezése Vizuális figyelés
description: Az Azure Data Factory adatfolyamainak vizuális figyelése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: dea0f9a038958ea747147a179020545f2f6922a2
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605343"
---
# <a name="monitor-data-flows"></a>Adatfolyamok figyelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Miután befejezte az adatfolyam kiépítését és hibakeresését, ütemezni szeretné az adatfolyamot egy ütemterv szerint egy folyamat környezetében. Ütemezheti a folyamatot az Azure Data Factory eseményindítók használatával. Vagy használhatja az Előindító most beállítást az Azure Data Factory Pipeline Builder egy egyfutású végrehajtás a folyamatkörnyezetben az adatfolyam teszteléséhez.

A folyamat végrehajtásakor képes lesz figyelni a folyamatot és a folyamatban lévő összes tevékenységet, beleértve az adatfolyam-tevékenységet is. Kattintson a monitor ikonra a bal oldali Azure Data Factory felhasználói felületén. Az alábbihoz hasonló képernyőjelenik meg. A kiemelt ikonok lehetővé teszik, hogy a folyamatban lévő tevékenységeket, köztük az adatfolyam-tevékenységet is befúrja.

![Adatfolyam figyelése](media/data-flow/mon001.png "Adatfolyam-figyelés")

Látni fogja statisztikák ezen a szinten is, beleértve a futási idő és az állapot. A futtatási azonosító a tevékenység szintjén eltér, hogy a futóazonosító a folyamat szintjén. Az előző szintű futtatási azonosító a folyamathoz van. A szemüvegre kattintva részletes részleteket talál az adatáramlás végrehajtásáról.

![Adatfolyam figyelése](media/data-flow/mon002.png "Adatfolyam-figyelés")

Amikor a grafikus csomópont figyelési nézetben van, az adatfolyam-diagram egyszerűsített, csak nézetre vonatkozó verziója jelenik meg.

![Adatfolyam figyelése](media/data-flow/mon003.png "Adatfolyam-figyelés")

## <a name="view-data-flow-execution-plans"></a>Adatfolyam-végrehajtási tervek megtekintése

Amikor az adatfolyam végrehajtása a Sparkban történik, az Azure Data Factory az adatfolyam teljes egésze alapján határozza meg az optimális kódútvonalakat. Emellett a végrehajtási útvonalak különböző kibővített csomópontokon és adatpartíciókon is előfordulhatnak. Ezért a figyelési grafikon a folyamat tervezését jelöli, figyelembe véve az átalakítások végrehajtási útvonalát. Ha az egyes csomópontokra kattint, olyan "csoportosítások" jelennek meg, amelyek a fürtön együtt végrehajtott kódot jelölik. Az időzítések és a számok, hogy látod képviseli ezeket a csoportokat, szemben az egyes lépéseket a design.

![Adatfolyam figyelése](media/data-flow/mon004.png "Adatfolyam-figyelés")

* Ha a figyelési ablakban a nyitott területre kattint, az alsó ablaktáblában lévő statisztikák megjelenítik az egyes fogadók időzítését és sorszámát, valamint az átalakítás lefolyásának a fogadó adataihoz vezető átalakítások adatait.

* Amikor kiválasztja az egyes átalakítások, akkor kap további visszajelzést a jobb oldali panelen, amely megmutatja partíció statisztika, oszlop számít, ferdeség (mennyire egyenletesen az adatok között elosztott partíciók), és kurtosis (milyen tüskés az adatok).

* Ha a csomópont nézetben a Mosogató ra kattint, látni fogja az oszlopvonalat. Három különböző módszerek, oszlopok halmozódnak fel az adatfolyam ban leszállni a fogadó. Ezek a következők:

  * Számított: Az oszlopot feltételes feldolgozásra vagy egy kifejezésen belül használja az adatfolyamban, de ne tegye le a fogadóban
  * Származtatott: Az oszlop egy új oszlop, amelyet a folyamatsorán hozott létre, azaz nem volt jelen a Forrás
  * Leképezve: Az oszlop a forrásból származik, és a rendszer egy fogadómezőhöz rendeli le.
  * Adatfolyam állapota: A végrehajtás aktuális állapota
  * Fürt indítási ideje: A JIT Spark számítási környezetének beszerzése az adatfolyam-végrehajtáshoz
  * Átalakítások száma: Hány átalakítási lépés van végrehajtás folyamatban a folyamatban?
  
![Adatfolyam figyelése](media/data-flow/monitornew.png "Adatáramlás figyelése Új")  
  
## <a name="monitor-icons"></a>Monitor ikonok

Ez az ikon azt jelenti, hogy az átalakítási adatok már gyorsítótárazva voltak a fürtön, így az időzítések és a végrehajtási útvonal figyelembe vették ezt:

![Adatfolyam figyelése](media/data-flow/mon004.png "Adatfolyam-figyelés")

Azt is látni fogja a zöld kör ikonok az átalakulás. Ezek azoknak a fogadóknak a számát jelentik, amelyekbe az adatok áramlanak.
