---
title: Felhőszolgáltatás teljesítményének tesztelése |} A Microsoft Docs
description: A Visual Studio profilerével használó felhőszolgáltatás teljesítményének tesztelése
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: aa8416bb6883a1aa76ddd370bf7061d7013904eb
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969481"
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Felhőszolgáltatás teljesítményének tesztelése
## <a name="overview"></a>Áttekintés
Felhőszolgáltatás teljesítményének tesztelheti a következő módon:

* Azure diagnosztikai eszköz használata a kérelmek és a kapcsolatok adatainak gyűjtésére, és tekintse át a webhely-statisztikái azt mutatják be, hogy a szolgáltatás hajtja végre, az ügyfél szempontjából. Első lépések, tekintse meg a [diagnosztika beállítása az Azure Cloud Services és Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* A Visual Studio profilerével használatával hogyan a szolgáltatás fut-e a számítási aspektusait részletes elemzését. Ez a témakör ismerteti, mivel a profiler használatával mérhetik a teljesítményt, mivel szolgáltatásként fut az Azure-ban. A profiler teljesítmény mérésére, egy szolgáltatás helyben fut a compute emulator használatával kapcsolatos információkért lásd: [teljesítményét az Azure Cloud Service helyi tesztelése a Compute Emulator használatával a Visual Studio Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Teljesítménytesztelési módszer kiválasztása
### <a name="use-azure-diagnostics-to-collect"></a>Azure Diagnostics segítségével gyűjtése:
* Statisztika weblapok vagy szolgáltatások, például kérelmeket, és a kapcsolatok.
* Statisztika a szerepköröket, például, hogy milyen gyakran a szerepkör újraindul.
* Általános információ a memóriahasználat, például, hogy a szemétgyűjtő idő vagy a memória aránya futó szerepkörök beállítása.

### <a name="use-the-visual-studio-profiler-to"></a>A Visual Studio profilerével, használja:
* Határozza meg, mely függvények a legtöbb időt vesz igénybe.
* Mennyi időt vesz igénybe a nagy számítási igényű program egyes részei mérjük.
* Hasonlítsa össze a két verziója, a szolgáltatás részletes teljesítmény-jelentések.
* Elemezze a lefoglalt memória részletesebben, mint az egyes memórialefoglalások szintjét.
* A többszálú code egyidejűségi problémák elemzése.

A profiler használatakor is lekérdezhet adatokat, amikor egy felhőalapú szolgáltatás fut, helyileg vagy az Azure-ban.

### <a name="collect-profiling-data-locally-to"></a>A profilkészítési adatok helyi gyűjtése:
* A felhőalapú szolgáltatások, például az adott feldolgozói szerepkör, egy realisztikus szimulált terhelés nem igénylő végrehajtásának részét teljesítményének a tesztelésére.
* Felhőszolgáltatás teljesítményének tesztelése elkülönítve, ellenőrzött feltételek.
* Felhőszolgáltatás teljesítményének tesztelése az Azure-bA a telepítése előtt.
* Felhőszolgáltatás teljesítményének tesztelése közvetlenül a Microsoftnak, a meglévő központi telepítéseknél megzavarása nélkül.
* A szolgáltatás teljesítményének tesztelése az Azure-ban futó nem.

### <a name="collect-profiling-data-in-azure-to"></a>Az Azure-ban profilkészítési adatok gyűjtése:
* Egy szimulált vagy tényleges terhelés alatt felhőszolgáltatás teljesítményének tesztelése.
* Profilkészítési adatok összegyűjtési kialakítási módszert használja, mivel ebben a témakörben később ismertetjük.
* A szolgáltatás teljesítményének tesztelése ugyanabban a környezetben, ha a szolgáltatás fut-e éles környezetben.

Általában szimulálása a Betöltés a test cloud services normál alatt vagy terhelés esetén.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilkészítés egy felhőalapú szolgáltatás, az Azure-ban
Amikor közzéteszi a felhőszolgáltatás a Visual Studióban, a szolgáltatás profil, és adja meg az információkat, amelyeket szeretne profilkészítési beállításokat. A profilkészítési munkamenetét a szerepkör minden egyes példányánál. Közzéteszi a szolgáltatást a Visual Studióval kapcsolatos további információkért lásd: [közzététele a Visual Studióból az Azure Cloud Service](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Ismerje meg jobban teljesítmény profilkészítés a Visual Studióban, tekintse meg [teljesítmény profilkészítés útmutató kezdőknek](https://msdn.microsoft.com/library/azure/ms182372.aspx) és [alkalmazásteljesítmény elemzése a profilkészítés eszközök használatával](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> IntelliTrace vagy a felhőszolgáltatás közzétételekor profilkészítés engedélyezheti. Mindkettő nem engedélyezhető.
> 
> 

### <a name="profiler-collection-methods"></a>Profiler gyűjtési módszerek
Módszerekkel másik gyűjteményt ameddig a profilkészítést, a teljesítménnyel kapcsolatos problémákat alapján:

* **CPU-mintavételezési** – Ez a módszer gyűjt, amelyek hasznosak a CPU-kihasználtság problémákat kezdeti elemzésének alkalmazás statisztikái. CPU-mintavételezési a legtöbb teljesítmény vizsgálatok megkezdéséhez javasolt módszer. Nincs egy kis hatás az alkalmazást, amelyet a rendszer a profilkészítés CPU-mintavételezési adatok összegyűjtésében.
* **A rendszerállapot** – Ez a módszer részletes adatokat gyűjt, amelyek akkor hasznos, célzott elemzéshez és a bemeneti és kimeneti teljesítménybeli problémák elemzéséhez. A rendszerállapot metódus rögzíti minden bejegyzést, kilépési és függvény hívásához szükséges a funkciók egy modulban levő futtatni a profilkészítés során. Ez a módszer akkor hasznos, a kód egy szakasz tartalmával kapcsolatos részletes információk összegyűjtéséhez és a bemeneti és kimeneti műveleteit alkalmazásteljesítményről hatásának megismerése. Ez a módszer egy 32 bites operációs rendszert futtató számítógép le van tiltva. Ez a beállítás csak akkor, ha futtatja a felhőszolgáltatás az Azure-ban, helyileg nem a compute emulator érhető el.
* **.NET-memóriafoglalás** – ezt a módszert a .NET-keretrendszer memória foglalási adatokat gyűjt a mintavételezés profilkészítési módszer használatával. A gyűjtött adatokat a számát és méretét, lefoglalt objektumokat tartalmaz.
* **Egyidejűségi** – Ez a módszer gyűjti az erőforrás-versengés adatokat, és a folyamat és a szál végrehajtási hasznos adatok elemzéséhez, többfolyamatú, és több szálon futó alkalmazások. Az egyidejűség módszer a kódok, például ha egy hozzászóláslánc megvárja, amíg a blokkok végrehajtási hozzáférés egy alkalmazás-erőforráshoz szabadítható fel benne, minden egyes esemény adatait gyűjti. Ez a módszer hasznos több szálon futó alkalmazások elemzése.
* Is engedélyezheti **szint interakció profilkészítés**, amely biztosítja, hogy további információt a végrehajtási időpontok szinkron ADO.NET-meghívja a többrétegű alkalmazások egy vagy több adatbázissal kommunikáló függvények. A profilkészítési módszerek bármelyikével is összegyűjtheti a csomag kapcsolati adatokat. Réteg interakció profilkészítés kapcsolatos további információkért lásd: [szint interakciók nézet](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Profilkészítési beállításainak konfigurálása
Az alábbi ábra bemutatja, hogyan konfigurálhatja a profilkészítési beállításokat az Azure-alkalmazások közzététele párbeszédpanelen.

![Nastavení Profilace konfigurálása](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Ahhoz, hogy a **engedélyezése a profilkészítés** jelölőnégyzet bejelölésével, rendelkeznie kell a profiler telepítve a helyi számítógépen, amely közzéteszi a felhőalapú szolgáltatást használ. Alapértelmezés szerint a profiler telepítve van a Visual Studio telepítésekor.
> 
> 

### <a name="to-configure-profiling-settings"></a>Profilkészítési beállításainak konfigurálása
1. A Megoldáskezelőben nyissa meg az Azure-projekt helyi menüjének, és válassza **közzététel**. Közzététele a felhőalapú szolgáltatásként kapcsolatos részletes lépéseiért lásd: [közzététele az Azure-eszközökkel egy felhőszolgáltatás](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. Az a **Azure-alkalmazások közzététele** párbeszédpanelen választotta a **speciális beállítások** fülre.
3. Profilkészítés engedélyezéséhez jelölje be a **engedélyezése a profilkészítés** jelölőnégyzetet.
4. A profilkészítési beállításokat, válassza ki a **beállítások** hivatkozás. A profilkészítés beállítások párbeszédpanel jelenik meg.
5. Az a **profilkészítés melyik módszert szeretné használni** gombok lehetőségnél válassza ki a profilkészítést, hogy nincs szüksége.
6. A profilaci interakce profilkészítési adatok gyűjtésére, válassza ki a **engedélyezése szint interakció profilkészítés** jelölőnégyzetet.
7. A beállítások mentéséhez válassza a **OK** gombra.
   
    Ez az alkalmazás közzétételekor ezeket a beállításokat az egyes szerepkörökhöz a profilkészítési munkamenet létrehozásához használt.

## <a name="viewing-profiling-reports"></a>Profilkészítés jelentések megtekintése
A profilkészítési munkamenet létrehozása egy szerepkört a cloud service-ben minden egyes példányánál. Minden munkamenet a Visual Studióból a profilkészítési jelentések megtekintéséhez megtekintése a Server Explorer ablak, és válassza ki az Azure számítási csomópontot a válasszon ki egy példányt egy szerepkör. Ezután megtekintheti a profilkészítési jelentést, az alábbi ábrán látható módon.

![Zobrazit Sestavu Profilace az Azure-ból](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Profilkészítési jelentések megtekintése
1. Megtekintheti a Server Explorer ablak a Visual Studióban, a menüsávon válassza a nézet, Server Explorerben.
2. Válassza ki az Azure számítási csomópont, és válassza a Azure-beli csomópont a felhőszolgáltatáshoz, amely a kijelölt profil mellett tette közzé a Visual studióból.
3. Egy példány profilkészítési jelentések megtekintéséhez válassza ki a szerepkört a szolgáltatásban, egy adott példány esetében a helyi menü megnyitásához, és válassza **profilkészítés jelentés megtekintése**.
   
    A jelentés egy .vsp fájlt már le lett töltve az Azure, és a letöltés állapotát az Azure-tevékenységnapló jelenik meg. A letöltés befejezésekor megjelenik-e a profilkészítési jelentés a nevű Visual Studio-szerkesztőben lapon <Role name> *<Instance Number>* <identifier>.vsp. A jelentés összefoglaló adatok jelennek meg.
4. A jelentés különböző nézeteket az aktuális nézet listán megjelenítendő, válassza ki a kívánt típusát. További információkért lásd: [profilkészítés eszközök jelentés nézetei](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>További lépések
[Felhőszolgáltatások hibakeresése](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Közzététel az Azure felhőszolgáltatás a Visual Studióból](https://msdn.microsoft.com/library/azure/ee460772.aspx)

