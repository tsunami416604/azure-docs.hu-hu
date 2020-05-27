---
title: Adatfolyamok vizuális figyelésének leképezése
description: Azure Data Factory adatfolyamatok vizuális monitorozása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 9594a2ddfaa0103e171618925ba6974bf9ad7f00
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833982"
---
# <a name="monitor-data-flows"></a>Adatfolyamatok figyelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyamatok kiépítésének és hibakeresésének befejezése után az adatfolyamatot úgy kell ütemezni, hogy egy folyamat kontextusában hajtsa végre az ütemtervet. Az eseményindítók használatával ütemezhet Azure Data Factory folyamatot. Vagy használhatja az aktiválás most lehetőséget a Azure Data Factory pipeline Builder-ből úgy, hogy egyetlen futtatású végrehajtást hajtson végre az adatfolyamatok folyamaton belüli teszteléséhez.

A folyamat végrehajtásakor nyomon követheti a folyamatot és az összes olyan tevékenységet, amely a folyamat során szerepel, beleértve az adatfolyam tevékenységét is. Kattintson a figyelés ikonra a bal oldali Azure Data Factory felhasználói felület paneljén. Egy, az alábbihoz hasonló képernyő jelenik meg. A Kiemelt ikonok lehetővé teszik a folyamat tevékenységeinek részletezését, beleértve az adatfolyam tevékenységeit is.

![Adatfolyam-figyelés](media/data-flow/mon001.png "Adatfolyam-figyelés")

Ezen a szinten láthatja a statisztikát is, beleértve a futtatási időpontokat és az állapotot. A futtatási azonosító a tevékenység szintjén eltér a folyamat szintjén lévő futtatási AZONOSÍTÓtól. A futtatási azonosító az előző szinten a folyamathoz szükséges. A szemüvegek kiválasztásával részletes információkhoz jutunk az adatfolyamok végrehajtásáról.

![Adatfolyam-figyelés](media/data-flow/mon002.png "Adatfolyam-figyelés")

Ha a grafikus csomópont figyelési nézetében van, az Adatfolyam-diagram egyszerűsített, csak megtekintésre kész verzióját láthatja.

![Adatfolyam-figyelés](media/data-flow/mon003.png "Adatfolyam-figyelés")

Az alábbi videó áttekintést nyújt az adatfolyamatok teljesítményének figyeléséről az ADF-figyelés képernyőn:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Adatfolyam-végrehajtási tervek megtekintése

Ha az adatfolyamot a Sparkban hajtja végre, Azure Data Factory meghatározza a kódok optimális elérési útját az adatfolyam teljes egészében. Emellett a végrehajtási útvonalak különböző kibővíthető csomópontokon és adatpartíción is előfordulhatnak. Ezért a figyelési gráf a folyamat kialakítását jelképezi, figyelembe véve az átalakítások végrehajtási útvonalát. Az egyes csomópontok kijelölésekor a fürtön együtt végrehajtott kódokat jelölő "Csoportosítások" láthatók. Az időzítések és a megjelenő számadatok ezeket a csoportokat jelölik, a terv egyes lépéseivel szemben.

![Adatfolyam-figyelés](media/data-flow/mon004.png "Adatfolyam-figyelés")

* Ha kijelöli a Megnyitás helyet a figyelés ablakban, az alsó ablaktáblán látható statisztika megjeleníti az egyes fogadók időzítését és sorait, valamint az átalakítási vonal számára a fogadó adatokhoz vezető átalakításokat.

* Ha egyéni átalakításokat választ ki, akkor a jobb oldali panelen további visszajelzések jelennek meg, amelyek a partíciós statisztikát, az oszlopok számát, a torzítást (azaz a partíciók közötti elosztott adatok egyenletes eloszlását) és a csúcsosságát (azaz a tüskés adatok) jelenítik meg.

* Amikor kijelöli a fogadót a csomópont nézetben, megtekintheti az oszlop Lineage elemét. Három különböző módszer áll rendelkezésre az oszlopok összegyűjtéséhez a fogadóban lévő összes adatfolyamban. Ezek a következők:

  * Számított: az oszlopot a feltételes feldolgozáshoz, vagy egy kifejezésen belül használja az adatfolyamatban, de a fogadóban nem landol
  * Származtatott: az oszlop egy olyan új oszlop, amelyet a folyamatában hozott létre, azaz nem volt jelen a forrásban.
  * Leképezve: az oszlop a forrástól származik, és a leképezést egy fogadó mezőhöz rendeli hozzá
  * Adatfolyam állapota: a végrehajtás aktuális állapota
  * Fürt indítási ideje: a JIT Spark számítási környezet beolvasásához szükséges idő az adatfolyam-végrehajtáshoz
  * Átalakítások száma: hány átalakítási lépést hajt végre a folyamaton belül
  
![Adatfolyam-figyelés](media/data-flow/monitornew.png "Adatfolyam-figyelés – új")  
  
## <a name="monitor-icons"></a>Ikonok figyelése

Ez az ikon azt jelenti, hogy az átalakítási adatgyűjtés már gyorsítótárazva van a fürtön, így az időzítések és a végrehajtás elérési útja figyelembe vette a következőt:

![Adatfolyam-figyelés](media/data-flow/mon004.png "Adatfolyam-figyelés")

Az átalakításban a zöld kör ikonjai is megjelennek. Ezek a mosdók számát jelölik, amelyekben az adatforgalom beáramlik.
