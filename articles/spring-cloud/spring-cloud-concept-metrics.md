---
title: Az Azure Spring Cloud metrikáinak ismertetése
description: Ismerje meg, hogyan tekintheti át a metrikákat az Azure Spring Cloud-ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: ed8a46683d79211559b4af7972c6d1bb784e1f02
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434175"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Az Azure Spring Cloud metrikáinak ismertetése

Az Azure Metrics Explorer a Microsoft Azure Portal egy olyan összetevője, amely lehetővé teszi a diagramok ábrázolását, a trendek vizuális korrelációját, valamint a tüskék és a kiugró adatok kivizsgálása mérőszámokban. Az erőforrások állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. 

Az Azure Spring Cloud-ban két nézőpont látható a metrikák esetében.
* Diagramok az egyes alkalmazások áttekintő oldalán
* Gyakori metrikák lap

 ![Metrikák diagramjai](media/metrics/metrics-1.png)

Az alkalmazások **áttekintésében** szereplő diagramok gyors állapot-ellenőrzéseket biztosítanak az egyes alkalmazásokhoz. A gyakori **mérőszámok** lap az összes rendelkezésre álló metrikát tartalmazza. Saját diagramokat hozhat létre a közös mérőszámok oldalon, és rögzítheti őket az irányítópulton.

## <a name="application-overview-page"></a>Alkalmazás – Áttekintés lap
Válasszon ki egy alkalmazást az **alkalmazásokban** a diagramok kereséséhez az Áttekintés oldalon.  

 ![Alkalmazás-metrikák kezelése](media/metrics/metrics-2.png)

Az egyes alkalmazások **alkalmazás-áttekintő** lapja egy mérőszámokat tartalmazó diagramot jelenít meg, amely lehetővé teszi az alkalmazás gyors állapot-ellenőrzésének elvégzését.  

 ![Az alkalmazás metrikáinak áttekintése](media/metrics/metrics-3.png)

Az Azure Spring Cloud az alábbi öt diagramot biztosítja a percenként frissülő metrikákkal:

* **Http-kiszolgálóhiba: hibák**száma az alkalmazás http-kéréseinél
* **Adat**: az alkalmazás által fogadott bájtok száma
* **Kimenő adatmennyiség**: az alkalmazás által elküldett bájtok száma
* **Kérelmek**: az alkalmazás által fogadott kérelmek
* **Átlagos válaszidő**: az alkalmazás átlagos válaszideje

A diagram esetében egy óra és hét nap közötti időtartományt választhat.

## <a name="common-metrics-page"></a>Gyakori metrikák lap

A bal oldali navigációs panelen a **metrikák** a közös mérőszámok lapra mutatnak.

Először válassza ki a megtekinteni kívánt metrikákat:

![Metrika nézet kiválasztása](media/metrics/metrics-4.png)

Az összes metrikai beállítás részletei az alábbi [részben](#user-metrics-options) találhatók.

Ezután válassza ki az egyes metrikák összesítési típusát:

![Metrika összesítése](media/metrics/metrics-5.png)

Az összesítési típus azt jelzi, hogyan összesíthető a diagram metrikai pontjainak ideje. Percenként egy nyers metrikai pont található, és egy percen belül előre definiált összesítési típust a metrikák típusa határozza meg.
* Sum: az összes értéket célként megadott kimenetként összesíti.
* Átlag: az időszakban az átlagos értéket célként megadott kimenetként használja.
* Max/min: használja a maximális/legkisebb értéket az időszakban célként megadott kimenetként.

Az időtartomány az elmúlt 30 percről az utolsó 30 napra vagy egy egyéni időtartományra is beállítható.

![Metrika módosítása](media/metrics/metrics-6.png)

Az alapértelmezett nézet magában foglalja az Azure Spring Cloud Service összes application's metrikáját. Egy alkalmazás vagy példány metrikája szűrhető a kijelzőn.  Kattintson a **szűrő hozzáadása**gombra, állítsa a tulajdonságot **alkalmazás**értékre, majd válassza ki a figyelni kívánt célalkalmazás **értékét az értékek** szövegmezőben. 

Kétféle szűrőt használhat (Tulajdonságok):
* Alkalmazás: szűrés az alkalmazás neve szerint
* Példány: szűrés alkalmazás-példány alapján

![Metrikai szűrők](media/metrics/metrics-7.png)

Használhatja a **felosztás alkalmazása** lehetőséget is, amely egy alkalmazás több sorát készíti el:

![Metrika felosztása](media/metrics/metrics-8.png)

>[!TIP]
> Saját diagramokat hozhat létre a metrikák lapon, és rögzítheti őket az **irányítópulton**. Először nevezze el a diagramot.  Ezután válassza **a rögzítés az irányítópulton lehetőséget a jobb felső sarokban**. Mostantól a portál **irányítópultján**is megtekintheti az alkalmazását.

## <a name="user-metrics-options"></a>Felhasználói mérőszámok beállításai

Az alábbi táblázatokban az elérhető metrikák és részletek láthatók.

### <a name="error"></a>Hiba
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat globális hiba (elavult)<br><br>(a 2020. július 1. után el kell távolítani)  | tomcat. Global. error | Darabszám | A feldolgozott kérelmekben előforduló hibák száma |
>| tomcat. Global. error | tomcat. Global. error | Darabszám | A feldolgozott kérelmekben előforduló hibák száma |

### <a name="performance"></a>Teljesítmény
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Rendszercpu-használat százalékos aránya (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | System. CPU. használat | Százalék | A legújabb CPU-használat a teljes rendszer számára. Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy az összes CPU tétlen volt a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta az idő 100%-át az elmúlt időszak során.|
>| System. CPU. használat | System. CPU. használat | Százalék | A legújabb CPU-használat a teljes rendszer számára. Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy az összes CPU tétlen volt a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta az idő 100%-át az elmúlt időszak során.|
>| Alkalmazás CPU-kihasználtságának százalékos aránya (elavult)<br><br>(eltávolítva július 1-től 2020) | Alkalmazás CPU-kihasználtságának százalékos aránya | Százalék | A Java virtuális gép folyamat legutóbbi CPU-használata. Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy egyik processzor sem futtatott szálakat a JVM folyamat során a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta a szálakat a JVM 100%-ában az elmúlt időszak során. A JVM található szálak közé tartoznak az alkalmazási szálak, valamint a JVM belső szálak.|
>| Process. CPU. használat | Alkalmazás CPU-kihasználtságának százalékos aránya | Százalék | A Java virtuális gép folyamat legutóbbi CPU-használata. Ez az érték a [0.0, 1.0] intervallumban megjelenő Double. A 0,0 érték azt jelenti, hogy egyik processzor sem futtatott szálakat a JVM folyamat során a legutóbbi megfigyelt időszakban, míg a 1,0 érték azt jelenti, hogy minden CPU aktívan futtatta a szálakat a JVM 100%-ában az elmúlt időszak során. A JVM található szálak közé tartoznak az alkalmazási szálak, valamint a JVM belső szálak.|
>| Az alkalmazáshoz hozzárendelt memória (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. Memory. véglegesített | Bájt | A JVM számára elérhetővé tett memória mennyiségét jelöli. Előfordulhat, hogy a JVM memóriát szabadít fel a rendszeren, és az előjegyzett memória a kezdeti memóriánál kisebb lehet. A lefoglalt memória mindig nagyobb vagy egyenlő a felhasznált memóriával. |
>| JVM. Memory. véglegesített | JVM. Memory. véglegesített | Bájt | A JVM számára elérhetővé tett memória mennyiségét jelöli. Előfordulhat, hogy a JVM memóriát szabadít fel a rendszeren, és a véglegesítése kisebb lehet, mint az init. a véglegesítés mindig nagyobb vagy egyenlő, mint a használat. |
>| Alkalmazáshoz használt memória (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. Memory. felhasználva | Bájt | A bájtban jelenleg használt memória mennyiségét jelöli. |
>| JVM. Memory. felhasználva | JVM. Memory. felhasználva | Bájt | A bájtban jelenleg használt memória mennyiségét jelöli. |
>| Az alkalmazás memóriájának maximális száma (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. Memory. max | Bájt | A memória-kezeléshez használható maximális memóriát jelöli. A felhasznált és az előjegyzett memória mennyisége mindig kisebb vagy egyenlő, ha a maximális érték meg van adva. Előfordulhat, hogy a memória kiosztása meghiúsul, ha a használt memóriát úgy próbálja megjavítani, hogy az <= Max használata esetén is igaz legyen (például ha a rendszer kevés a virtuális memóriában), ha a használni próbált >. |
>| JVM. Memory. max | JVM. Memory. max | Bájt | A memória-kezeléshez használható maximális memóriát jelöli. A felhasznált és az előjegyzett memória mennyisége mindig kisebb vagy egyenlő, ha a maximális érték meg van adva. Előfordulhat, hogy a memória kiosztása meghiúsul, ha a használt memóriát úgy próbálja megjavítani, hogy az <= Max használata esetén is igaz legyen (például ha a rendszer kevés a virtuális memóriában), ha a használni próbált >. |
>| Az elérhető régi létrehozási adatméret maximális száma (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. max. az adatméret | Bájt | A régi generációs memória maximális kihasználtsága a Java virtuális gép elindítása óta. |
>| JVM. GC. max. az adatméret | JVM. GC. max. az adatméret | Bájt | A régi generációs memória maximális kihasználtsága a Java virtuális gép elindítása óta. |
>| A régi generációs adatméret (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. Live. Resize | Bájt | A régi generációs memória-készlet mérete teljes GC után. |
>| JVM. GC. Live. Resize | JVM. GC. Live. Resize | Bájt | A régi generációs memória-készlet mérete teljes GC után. |
>| A régi generációs adatméret (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. Memory. előléptetve | Bájt | A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC-t a GC után. |
>| JVM. GC. Memory. előléptetve | JVM. GC. Memory. előléptetve | Bájt | A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC-t a GC után. |
>| Előléptetés a fiatal generáció adatméretére (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. Memory. lefoglalt | Bájt | Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután az egyik GC a következőre kerül. |
>| JVM. GC. Memory. lefoglalt | JVM. GC. Memory. lefoglalt | Bájt | Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután az egyik GC a következőre kerül. |
>| GC-szüneteltetések száma (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. pause (összesen-Count) | Darabszám | A JMV elindítását követő összes GC-szám, beleértve a fiatal és a régi GC-t. |
>| JVM. GC. pause. Total. Count | JVM. GC. pause (összesen-Count) | Darabszám | A JMV elindítását követő összes GC-szám, beleértve a fiatal és a régi GC-t. |
>| GC felfüggesztésének teljes ideje (elavult)<br><br>(a 2020. július 1. után el kell távolítani) | JVM. GC. pause (teljes idő) | Ezredmásodpercben | A JMV elindítását követően felhasznált teljes GC-idő, beleértve a fiatal és a régi GC-t. |
>| JVM. GC. pause. Total. Time | JVM. GC. pause (teljes idő) | Ezredmásodpercben | A JMV elindítását követően felhasznált teljes GC-idő, beleértve a fiatal és a régi GC-t. |

### <a name="request"></a>Kérés
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat teljes elküldési bájtjai (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Global. elküldve | Bájt | Elküldve a Tomcat-webkiszolgálók mennyisége |
>| tomcat. Global. elküldve | tomcat. Global. elküldve | Bájt | Elküldve a Tomcat-webkiszolgálók mennyisége |
>| Tomcat összesen fogadott bájt (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Global. Received | Bájt | A lekéréses tomcat-webkiszolgáló mennyisége |
>| tomcat. Global. Received | tomcat. Global. Received | Bájt | A lekéréses tomcat-webkiszolgáló mennyisége |
>| Tomcat-kérelem teljes ideje (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Global. Request (teljes idő) | Ezredmásodpercben | A Tomcat webkiszolgáló teljes ideje a kérelmek feldolgozására |
>| Tomcat-kérelem teljes száma (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Global. Request (összesen-Count) | Darabszám | A Tomcat webkiszolgáló által feldolgozott kérelmek teljes száma |
>| tomcat. Global. Request. Total. Count | tomcat. Global. Request (összesen-Count) | Darabszám | A Tomcat webkiszolgáló által feldolgozott kérelmek teljes száma |
>| Tomcat-kérelem maximális ideje (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Global. Request. max | Ezredmásodpercben | A Tomcat-webkiszolgáló maximális ideje a kérelem feldolgozására |
>| tomcat. Global. Request. max | tomcat. Global. Request. max | Ezredmásodpercben | A Tomcat-webkiszolgáló maximális ideje a kérelem feldolgozására |

### <a name="session"></a>Munkamenet
>[!div class="mx-tdCol2BreakAll"]
>| Name | Rugós indítószerkezet metrikájának neve | Unit (Egység) | Részletek |
>|----|----|----|------------|
>| Tomcat-munkamenet maximális aktív száma (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Sessions. Active. max | Darabszám | Egy időben aktív munkamenetek maximális száma |
>| tomcat. Sessions. Active. max | tomcat. Sessions. Active. max | Darabszám | Egy időben aktív munkamenetek maximális száma |
>| Tomcat-munkamenet maximális élettartama (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Sessions. Alive. max | Ezredmásodpercben | A lejárt munkamenet élettartamának leghosszabb ideje (másodpercben) |
>| tomcat. Sessions. Alive. max | tomcat. Sessions. Alive. max | Ezredmásodpercben | A lejárt munkamenet élettartamának leghosszabb ideje (másodpercben) |
>| Tomcat-munkamenet létrehozva száma (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Sessions. created | Darabszám | A létrehozott munkamenetek száma |
>| tomcat. Sessions. created | tomcat. Sessions. created | Darabszám | A létrehozott munkamenetek száma |
>| A Tomcat-munkamenet lejárt száma (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Sessions. lejárt | Darabszám | Lejárt munkamenetek száma |
>| tomcat. Sessions. lejárt | tomcat. Sessions. lejárt | Darabszám | Lejárt munkamenetek száma |
>| Tomcat-munkamenet visszautasítva darabszáma (elavult)<br><br>(eltávolítva július 1-től 2020) | tomcat. Sessions. elutasítva | Darabszám | Azon munkamenetek száma, amelyek nem lettek létrehozva, mert elérte az aktív munkamenetek maximális számát. |
>| tomcat. Sessions. elutasítva | tomcat. Sessions. elutasítva | Darabszám | Azon munkamenetek száma, amelyek nem lettek létrehozva, mert elérte az aktív munkamenetek maximális számát. |
>| tomcat. Sessions. Active. Current | tomcat. Sessions. Active. Current | Darabszám | Tomcat-munkamenet aktív száma |

## <a name="see-also"></a>Lásd még:
* [Bevezetés az Azure Metrikaböngésző használatába](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Naplók és mérőszámok elemzése diagnosztikai beállításokkal](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Következő lépések
* [Oktatóanyag: tavaszi Felhőbeli erőforrások figyelése riasztások és műveleti csoportok használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvóták és szolgáltatási csomagok az Azure Spring Cloud-hoz](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

