---
title: Az Azure Spring Cloud mutatóinak ismertetése
description: Ismerje meg, hogyan tekintheti át a metrikákat az Azure Spring Cloud ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256756"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Az Azure Spring Cloud mutatóinak megismerése

Az Azure Metrics Explorer a Microsoft Azure portal egyik összetevője, amely lehetővé teszi a diagramok nyomtatását, a trendek vizuális korrelációját, valamint a metrikák kiugrásainak és csökkenésének vizsgálatát. A metrikakezelő segítségével vizsgálja meg az erőforrások állapotát és kihasználtságát. 

Az Azure Spring Cloud két szempontpontot metrikák.
* Diagramok az egyes alkalmazások áttekintő oldalain
* Gyakori mérőszámok oldal

 ![Mérőszámok grafikonjai](media/metrics/metrics-1.png)

Az alkalmazás **diagramjai – Áttekintés,** gyors állapotellenőrzéseket biztosítanak az egyes alkalmazásokhoz. A közös **Metrikák** oldal tartalmazza az összes rendelkezésre álló metrikák. Saját diagramokat hozhat létre a közös mérőszámok oldalon, és rögzítheti őket az irányítópulton.

## <a name="application-overview-page"></a>Alkalmazás áttekintő lapja
Válasszon ki egy alkalmazást az **Alkalmazáskezelésben** a diagramok kereséséhez az áttekintő lapon.  

 ![Alkalmazásmetrikák kezelése](media/metrics/metrics-2.png)

Minden alkalmazás **alkalmazás áttekintése** oldal bemutatja a metrikák diagram, amely lehetővé teszi, hogy az alkalmazás gyors állapot-ellenőrzését.  

 ![Alkalmazási mutatók áttekintése](media/metrics/metrics-3.png)

Az Azure Spring Cloud ezt az öt diagramot percenként frissített mérőszámokkal biztosítja:

* **Http Server hibák:** Az alkalmazásnak küldött HTTP-kérelmek hibaszáma
* **Adatok az alkalmazásban:** Az alkalmazás által fogadott bájtok
* **Adatok kiküldése**: Az alkalmazás által küldött bájtok
* **Kérések**: Az alkalmazás által fogadott kérések
* **Átlagos válaszidő:** Az alkalmazás átlagos válaszideje

A diagramhoz egy óra és hét nap közötti időtartományt választhat.

## <a name="common-metrics-page"></a>Gyakori mérőszámok oldal

A bal oldali navigációs ablakban található **mérőszámok** a közös metrikák lapra mutatnak.

Először válassza ki a megtekinteni kívánt mutatókat:

![Metrikus nézet kiválasztása](media/metrics/metrics-4.png)

Az összes metrika lehetőség részletei az alábbi [szakaszban](#user-metrics-options) találhatók.

Ezután válassza ki az összesítés típusát az egyes metrikákhoz:

![Metrikus összesítés](media/metrics/metrics-5.png)

Az összesítés típusa azt jelzi, hogyan lehet a diagram on-réspontjait idő szerint összesíteni. Percenként egy nyers metrikapont van, és az előzetes összesítési típus egy percen belül előre meghatározott metrikák típusa.
* Összeg: Az összes érték összegzése célkimenetként.
* Átlag: Használja az átlagos értéket az időszakban célkimenetként.
* Max/Min: Használja a Max/Min értéket az időszakban célkimenetként.

A megjelenítendő időtartomány is módosítható. Az időtartomány az utolsó 30 perctől az utolsó 30 napig, vagy egyéni időtartomány közül választhat.

![Metrikus módosítás](media/metrics/metrics-6.png)

Az alapértelmezett nézet tartalmazza az Azure Spring Cloud-szolgáltatás alkalmazásának összes metrikáját. Egy alkalmazás vagy példány metrikája szűrhető a kijelzőn.  Kattintson **a Szűrő hozzáadása**gombra, állítsa be a tulajdonságot az **Alkalmazás**értékre, és jelölje ki a figyelni kívánt célalkalmazást az **Értékek** szövegmezőben. 

Kétféle szűrőt (tulajdonságot) használhat:
* Alkalmazás: szűrés alkalmazásnév szerint
* Példány: szűrő alkalmazáspéldány szerint

![Metrikus szűrők](media/metrics/metrics-7.png)

Használhatja a **Felosztás alkalmazása** lehetőséget is, amely több vonalat rajzol egy alkalmazáshoz:

![Metrikus felosztás](media/metrics/metrics-8.png)

>[!TIP]
> Saját diagramokat hozhat létre a mérőszámok oldalon, és rögzítheti őket az **irányítópulton.** Először nevezze el a diagramot.  Ezután válassza a Rögzítés az irányítópulthoz lehetőséget **a jobb felső sarokban.** Most már ellenőrizheti az alkalmazást a portál **irányítópultján.**

## <a name="user-metrics-options"></a>Felhasználói mutatók beállításai

Az alábbi táblázatok a rendelkezésre álló mutatókat és részleteket mutatják be.

### <a name="error"></a>Hiba
>[!div class="mx-tdCol2BreakAll"]
>| Név | Tavaszi aktuátor metrikus neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat globális hiba | tomcat.global.error | Darabszám | Feldolgozott kérelmek hibáinak száma |

### <a name="performance"></a>Teljesítmény
>[!div class="mx-tdCol2BreakAll"]
>| Név | Tavaszi aktuátor metrikus neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>|Rendszerprocesszor-használat százaléka | system.cpu.usage | Százalék | Legutóbbi CPU-használat az egész rendszerszámára. Ez az érték a [0,0,1,0] intervallum kétszerese. A 0,0 érték azt jelenti, hogy az elmúlt időszakban az összes CPU tétlen volt, míg az 1,0 érték azt jelenti, hogy az összes CPU aktívan futott az elmúlt időszakban.|
>| Alkalmazás PROCESSZOR-használati százaléka | Alkalmazás PROCESSZOR-használati százaléka | Százalék | A Java virtuálisgép-folyamat legutóbbi processzorhasználata. Ez az érték a [0,0,1,0] intervallum kétszerese. A 0,0 érték azt jelenti, hogy az elmúlt időszakban egyik CPU sem futott a JVM folyamatból, míg az 1,0 érték azt jelenti, hogy az összes CPU aktívan futtatta a JVM-ből származó szálakat az elmúlt időszak során. A JVM-ből származó szálak tartalmazzák az alkalmazásszálakat, valamint a JVM belső szálait.|
>| Hozzárendelt alkalmazásmemória | jvm.memory.committed | Bájt | Azt a memóriamennyiséget jelöli, amely garantáltan elérhető a JVM számára. A JVM felszabadíthatja a memóriát a rendszerbe, és a véglegesítés kisebb lehet, mint az init. a felhasználtnál mindig nagyobb vagy egyenlő lesz. |
>| Használt alkalmazásmemória | jvm.memory.used | Bájt | A bájtban jelenleg használt memória mennyiségét jelöli. |
>| Alkalmazásmemória max. | jvm.memory.max | Bájt | A memóriakezeléshez használható maximális memóriamennyiséget jelöli. A felhasznált és a lekötött memória mennyisége mindig kisebb vagy egyenlő, mint a maximális érték, ha a max érték meg van adva. A memóriafoglalás sikertelen lehet, ha megpróbálja növelni a használt memóriát, hogy a használt > akkor is véglegesített, ha a használt <= max továbbra is igaz (például, ha a rendszer kevés a virtuális memória). |
>| Maximálisan elérhető régi generációs adatméret | jvm.gc.max.data.size | Bájt | A java virtuális gép elindítása óta a régi generációs memóriakészlet memóriahasználatának csúcsértéke. |
>| Régi generációs adatok mérete | jvm.gc.live.data.size | Bájt | A régi generációs memóriakészlet mérete a teljes globális katalógus után. |
>| Előléptetés régi generációs adatméretre | jvm.gc.memory.promoted | Bájt | A gc és a globális katalógus utáni régi generációs memóriakészlet méretének pozitív növekedésének száma. |
>| Népszerűsítés fiatal generációs adatméretre | jvm.gc.memória.lefoglalt | Bájt | Növekmény növekedése a méret a fiatal generációs memória medence után egy GC a következő előtt. |
>| Gc szünetszáma | jvm.gc.pause (összes szám) | Darabszám | Teljes GC szám után JMV kezdődött, beleértve a fiatal és az öreg GC. |
>| Globális katalógus szüneteltetési teljes ideje | jvm.gc.pause (teljes idő) | Ezredmásodperc | A JMV kezdete után felhasznált teljes GC idő, beleértve a Young and Old GC-t is. |

### <a name="request"></a>Kérés
>[!div class="mx-tdCol2BreakAll"]
>| Név | Tavaszi aktuátor metrikus neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat összes elküldött bájt | tomcat.global.sent | Bájt | A Tomcat webkiszolgáló által küldött adatok mennyisége |
>| Tomcat teljes fogadott bájt | tomcat.global.received | Bájt | Fogadott adatok mennyisége Tomcat webszerver |
>| Tomcat kérelem teljes ideje | tomcat.global.request (teljes idő) | Ezredmásodperc | A Tomcat webkiszolgáló teljes ideje a kérések feldolgozásához |
>| Tomcat kérelem teljes száma | tomcat.global.request (összes szám) | Darabszám | A Tomcat webkiszolgáló feldolgozott kérelmeinek teljes száma |
>| Tomcat-kérelem maximális ideje | tomcat.global.request.max | Ezredmásodperc | A Tomcat webkiszolgáló maximális ideje a kérelem feldolgozásához |

### <a name="session"></a>Munkamenet
>[!div class="mx-tdCol2BreakAll"]
>| Név | Tavaszi aktuátor metrikus neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat-munkamenet maximális aktív száma | tomcat.sessions.active.max | Darabszám | Az egy időben aktív munkamenetek maximális száma |
>| Tomcat session max élő idő | tomcat.sessions.alive.max | Ezredmásodperc | A lejárt munkamenet élettartamának leghosszabb ideje (másodpercben) |
>| Tomcat munkamenet létrehozva száma | tomcat.sessions.created | Darabszám | Létrehozott munkamenetek száma |
>| Tomcat-munkamenet lejárt száma | tomcat.sessions.lejárt | Darabszám | Lejárt munkamenetek száma |
>| Tomcat munkamenet elutasított száma | tomcat.sessions.elutasítva | Darabszám | Azon munkamenetek száma, amelyek azért jöttek létre, mert az elért aktív munkamenetek száma elérte a maximális számot. |

## <a name="see-also"></a>Lásd még
* [Bevezetés az Azure Metrikaböngésző használatába](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Naplók és metrikák elemzése diagnosztikai beállításokkal](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>További lépések
* [Oktatóanyag: A Spring Cloud erőforrásainak figyelése riasztások és műveletcsoportok használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

