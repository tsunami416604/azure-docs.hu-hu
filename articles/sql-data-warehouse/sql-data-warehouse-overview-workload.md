---
title: "További tudnivalók Azure SQL Data Warehouse műveletek |} Microsoft Docs"
description: "Az SQL Data Warehouse rugalmassága lehetővé teszi, hogy növelje, csökkentse vagy szüneteltesse a számítási teljesítményt az adattárházegységek (DWU-k) csúszkájával. Ez a cikk az adatraktárak mérőszámait ismerteti, és azt, hogy azok milyen kapcsolatban vannak a DWU-kkal. "
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: cadffa9c-589d-4db7-888a-1f202a753bc5
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 629ce22bf669a760d041bbd006b836d2da5d237b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="data-warehouse-workload"></a>Adatraktár-számítási feladat
Az adatraktár-számítási feladat minden olyan műveletre utal, amelyet egy adatraktárral végeznek. Az adatraktár-számítási feladat magában foglalja az adatok betöltését az adatraktárba, az elemzések végrehajtását, a jelentések készítését az adatraktárról, az adatraktárban lévő adatok kezelését és az adatok adatraktárból történő exportálását. Az összetevők terjedelme és mélysége gyakran arányos az adatraktár érettségi szintjével.

## <a name="new-to-data-warehousing"></a>Most ismerkedik az adatraktározással?
Az adatraktár egy vagy több adatforrásból betöltött adatok gyűjteménye, amelyet üzletiintelligencia-feladatok elvégzésére, például jelentéskészítésre és adatelemzésre használnak.

Az adatraktárakat olyan lekérdezések jellemzik, amelyek nagy mennyiségű sort és nagy adattartományokat vizsgálnak át, és viszonylag nagy terjedelmű, elemzéshez és jelentéskészítéshez használt eredményt adhatnak vissza. Az adatraktárakra emellett a viszonylag nagy mennyiségű adatbetöltés és a kis mennyiségű tranzakciószintű beszúrás/frissítés/törlés a jellemző.

* Egy adatraktár teljesítménye akkor a legjobb, ha az adatokat oly módon tárolják, ami optimalizálja a nagy mennyiségű sort vagy nagyobb adattartományokat átvizsgáló lekérdezéseket. Ez a vizsgálati típus akkor működik a legjobban, ha a rendszer az adatokat sorok helyett oszlopokban tárolja, és a sorokra keres rá.

> [!NOTE]
> A memórián belüli, oszlopos tárolást alkalmazó oszlopcentrikus index jelentés- és elemzési lekérdezések esetén akár 10x nagyobb szintű tömörítésre és 100x nagyobb lekérdezési teljesítményre képes, mint a hagyományos bináris fák. Az adatraktárakban az oszlopcentrikus indexeket tekintjük standardnak a nagyméretű adatok tárolásánál és vizsgálatánál.
> 
> 

* Az adatraktáraknak követelményei különböznek az online tranzakció-feldolgozásra (OLTP) optimalizált rendszerekétől. Az OLTP rendszerek számos beszúrási, frissítési és törlési művelettel rendelkeznek. Ezek a műveletek adott sorokra keresnek rá a táblában. A tábla átvizsgálása akkor a legeredményesebb, ha az adatokat soronként tárolják. Az adatok rendezését és gyors keresését egy bináris fának, vagy bináris fa keresésnek nevezett oszd meg és uralkodj megközelítés biztosítja.

## <a name="data-loading"></a>Az adatok betöltése
Az adatbetöltés jelenős részét teszi ki az adatraktár-számítási feladatoknak. A vállalkozások általában rendelkeznek egy forgalmas OLTP rendszerrel, amely a nap folyamán követi az üzleti tranzakciók által előidézett változásokat. Rendszeres időközönként, gyakran éjszaka, a karbantartási időszak alatt a rendszer áthelyezi vagy átmásolja a tranzakciókat az adatraktárba. Amint az adatok átkerültek az adatraktárba, az elemzők elvégezhetik az elemzést, és üzleti döntéseket hozhatnak az adatok alapján.

* A betöltési folyamatot hagyományosan ETL-nek nevezik, ami az Extract, Transform és Load (kinyerés, átalakítás és betöltés) rövidítése. Az adatokat általában át kell alakítani, hogy konzisztensek legyenek az adatraktár többi adatával. Korábban a vállalkozások dedikált ETL-kiszolgálókat használtak az átalakításokhoz. A nagymértékben párhuzamos feldolgozás gyorsaságának köszönhetően most már első lépésként betöltheti az adatokat az SQL Data Warehouse szolgáltatásba, majd végrehajthatja az átalakításokat. A folyamat neve ETL (Extract, Load, and Transform – kinyerés, betöltés és átalakítás). Egyre inkább ez válik új szabvánnyá az adatraktár-számítási feladatoknál.

> [!NOTE]
> Az SQL Server 2016 használatával már valós időben is elemezheti az OLTP-táblákat. Ez nem teszi szükségtelenné az adatok tárolását és elemzését az adatraktárban, viszont lehetővé teszi a valós idejű elemzést.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Jelentés- és elemzéslekérdezések
A jelentés- és elemzési lekérdezéseket a kicsi, közepes és nagy kategóriákba sorolják, esetenként a feltételek száma, de legtöbbször az időtartam alapján. A legtöbb adatraktárban vegyes számítási feladatok futnak, amelyet gyorsan és lassan futó lekérdezések alkotnak. Minden esetben fontos meghatározni ezt a keveréket, és annak gyakoriságát (óránként, naponta, hónap végén, negyedév végén stb.). Fontos megérteni, hogy a vegyes lekérdezésekből álló számítási feladatok és a párhuzamosság együtt megfelelő kapacitástervezést igényelnek.

* A kapacitástervezés vegyes lekérdezésekből álló számítási feladatok esetén összetett feladat lehet, főleg ha az adatraktár kapacitásának bővítéséhez hosszú átfutási időre van szükség. Az SQL Data Warehouse szolgáltatásban a kapacitástervezés nem olyan sürgős, mivel a számítási teljesítmény bármikor növelhető vagy csökkenthető, a tárolási és számítási teljesítményt pedig egymástól függetlenül lehet méretezni.

### <a name="data-management"></a>Adatkezelés
Az adatkezelés fontos, különösen akkor, ha előre lehet tudni, hogy hamarosan el fog fogyni a szabad lemezterület. Az adatraktárak az adatokat általában értelmezhető tartományokra osztják, amelyeket a rendszer partíciókként tárol a táblákban. Az SQL Server szolgáltatáson alapuló termékek lehetővé teszik a partíciók táblák közötti áthelyezését. Ez a partícióváltás lehetővé teszi, hogy a régebbi adatokat át lehessen helyezni egy kevésbé költséges tárhelyre, és az online tárhelyen a legfrissebb adatok legyenek elérhetők.

* Az oszlopcentrikus indexek támogatják a particionált táblákat. Az oszlopcentrikus indexek esetében a particionált táblákat adatkezelésre és archiválásra használják. A soronként tárolt tábláknál a partícióknak a lekérdezési teljesítményben van nagyobb szerepük.  
* A PolyBase fontos szerepet játszik az adatkezelésben. A PolyBase használatával archiválhatja a régebbi adatokat a Hadoopba vagy az Azure Blob Storage szolgáltatásba.  Ez sok lehetőséget biztosít, mivel az adatokat továbbra is el lehet érni az interneten.  Az adatok lekérése a Hadoopból több időt vehet igénybe, de a hosszabb lekérési időt ellensúlyozhatja a tárolási költség.

### <a name="exporting-data"></a>Az adatok exportálása
Az adatok jelentéskészítés és elemzés számára történő elérhetővé tételének egyik módja az, hogy adatokat küldenek az adatraktárból a jelentések és elemzések futtatására szolgáló kiszolgálóknak. Ezeket a kiszolgálókat hívják adatpiacnak. Például a jelentési adatokat előre fel lehet dolgozni, majd exportálni lehet az adatraktárból számos kiszolgálóra világszerte, hogy az ügyfelek és az elemzők széles köre el tudja érni őket.

* A jelentések létrehozásához az írásvédett jelentéskészítő kiszolgálókra minden éjszaka fel lehet tölteni egy pillanatképet a napi adatokról. Ez nagyobb sávszélességet biztosít az ügyfelek számára, miközben csökkenti az adatraktár számítási erőforrásigényét. Biztonsági szempontjából az adatpiacok lehetővé teszik az adatraktárhoz hozzáférő felhasználók számának csökkentését.
* Az elemzéshez ki lehet építeni az adatraktáron egy elemzési adatkockát, és le lehet futtatni az elemzést az adatraktáron, vagy előre fel lehet dolgozni az adatokat és exportálni lehet őket az elemzési kiszolgálóra további elemzés céljából.

## <a name="next-steps"></a>Következő lépések
Most, hogy jobban megismerte az SQL Data Warehouse szolgáltatást, tudjon meg többet az [SQL Data Warehouse gyors létrehozásáról][create a SQL Data Warehouse] és a [mintaadatok betöltéséről][load sample data].

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
