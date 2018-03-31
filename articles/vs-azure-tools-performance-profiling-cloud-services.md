---
title: Egy felhőalapú szolgáltatás teljesítményének tesztelése |} Microsoft Docs
description: Egy felhőalapú szolgáltatás, a Visual Studio profiler használatával teljesítményének tesztelése
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
ms.openlocfilehash: 74ffd70bdb204cee75b86a1e922ac67873a88758
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Egy felhőalapú szolgáltatás teljesítményének tesztelése
## <a name="overview"></a>Áttekintés
Egy felhőalapú szolgáltatás teljesítményének tesztelheti a következőképpen:

* Használja az Azure diagnosztikai, kérelmek és kapcsolatok adatokat gyűjthet, és tekintse át a hely statisztikai adatokat tartalmaz a megjelenítése, hogy a szolgáltatás hajtja végre, az ügyfél szempontjából. Megkezdéséhez, lásd: [diagnosztika konfigurálása az Azure felhőalapú szolgáltatásairól és a virtuális gépek](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Használja a Visual Studio Profilkészítő hogyan a szolgáltatás fut-e a számítási aspektusait részletes elemzését. Mivel ez a témakör ismerteti, használhatja a Profilkészítő teljesítmény mérésére, mivel a szolgáltatás fut az Azure-ban. A Profilkészítő teljesítményének méréséhez, egy szolgáltatás helyben fut a compute emulator használatával kapcsolatos információkért lásd: [tesztelése teljesítményét egy Azure Cloud Service helyi a Compute Emulator használatával a Visual Studio Profilkészítő](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Egy teljesítménytesztelési módszer kiválasztása
### <a name="use-azure-diagnostics-to-collect"></a>Azure Diagnostics összegyűjtéséhez használja:
* A weblapok vagy szolgáltatások, például a kérelmek és kapcsolatok statisztikája.
* A szerepköröket, például egy szerepkör újraindítása milyen gyakran statisztika.
* A teljes memória használata, például az, hogy a szemétgyűjtő mennyi idő vagy a memória százalékát kapcsolatos információkat a futó szerepkörök beállítása.

### <a name="use-the-visual-studio-profiler-to"></a>A Visual Studio Profilkészítő használja:
* Határozza meg, mely függvények a legtöbb időt vehet igénybe.
* Mennyi számításilag intenzív program részét képező összes szükséges idő mérését.
* Hasonlítsa össze a két verziója szolgáltatás részletes teljesítmény a jelentésekre.
* Egyes memória kiosztásokat szintnél részletesebben memóriafoglalás elemzése.
* Többszálas kódban párhuzamossági problémák elemzéséhez.

A Profilkészítő használatakor is összegyűjtötte az adatokat egy felhőalapú szolgáltatás futtatásakor helyileg vagy az Azure-ban.

### <a name="collect-profiling-data-locally-to"></a>Gyűjtse össze a profilkészítési adatokat helyileg:
* Egy felhőalapú szolgáltatás, például az adott feldolgozói szerepkör, nem igényel szimulált valósághű terhelést végrehajtása részét teljesítményének a tesztelésére.
* Egy felhőalapú szolgáltatás teljesítményének a tesztelésére elkülönítve, ellenőrzött feltételek.
* Csak az Azure-bA telepítheti azt egy felhőalapú szolgáltatás teljesítményének a tesztelésére.
* Tesztelje a felhőszolgáltatás közvetlenül a Microsoftnak, a meglévő telepítések megzavarása nélkül.
* A szolgáltatás teljesítményének tesztelése az Azure-beli költségek nélkül.

### <a name="collect-profiling-data-in-azure-to"></a>Profilkészítési adatgyűjtést az Azure szolgáltatásban:
* Egy felhőalapú szolgáltatás szimulált vagy valós terhelésnek teljesítményének a tesztelésére.
* Ez a témakör későbbi használja a profilkészítési adatokat gyűjt instrumentation metódusában.
* A szolgáltatás teljesítményének a tesztelésére ugyanabban a környezetben, ha a szolgáltatás fut-e éles környezetben.

Általában szimulálása a betöltés teszt felhőszolgáltatások szokásos vagy terhelés esetén.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilkészítési egy felhőalapú szolgáltatás, az Azure-ban
A Visual Studio felhőszolgáltatás közzétételekor profilhoz, a szolgáltatás, és adja meg a profilkészítési beállításokat, amelyek a kívánt információkat biztosítanak. A profilkészítési munkamenetét szerepkör minden egyes példányánál. A Visual Studio szolgáltatás közzétételével kapcsolatos további információkért lásd: [közzététele a Visual Studio Azure Cloud Service](https://msdn.microsoft.com/library/azure/ee460772.aspx).

További információ a Visual Studio profilkészítési teljesítmény ismertetése: [kezdők útmutatóban teljesítmény profilkészítési](https://msdn.microsoft.com/library/azure/ms182372.aspx) és [alkalmazásteljesítmény elemzése profilkészítési eszközök használatával](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> IntelliTrace vagy a felhőszolgáltatás közzétételekor profilkészítési engedélyezheti. Nem engedélyezhető a is.
> 
> 

### <a name="profiler-collection-methods"></a>Profilkészítő adatgyűjtési módszerek
Különböző adatgyűjtési módszerek használható profilkészítési, a teljesítménnyel kapcsolatos problémákat alapján:

* **CPU mintavételi** -ezt a módszert, amelyek hasznosak a CPU-kihasználtság problémák kezdeti elemzésének alkalmazásstatisztika gyűjti. CPU mintavételi a javasolt lehetőség a legtöbb teljesítmény vizsgálatok elindítása. Nincs alacsonyabb hatással az alkalmazásra, amely CPU mintavételi adatok összegyűjtésében adatainak összegyűjtése.
* **Instrumentation** – Ez a módszer részletes adatait gyűjti, amelyeket akkor hasznos, a részletesebb elemzéshez és a bemeneti/kimeneti teljesítménnyel kapcsolatos problémák elemzéséhez. A instrumentation metódus rögzít minden bejegyzés, kilépési és függvény hívásához szükséges a funkciók modulban a profilkészítési futtatása során. Ez a módszer akkor hasznos, a kód egy szakaszban részletes információkat gyűjt, és a bemeneti és kimeneti műveletek alkalmazás teljesítményére gyakorolt hatása ismertetése. Ez a módszer nem végezhető el egy 32 bites operációs rendszert futtató számítógépre. Ez a beállítás csak akkor, ha futtatja a felhőalapú szolgáltatás, az Azure-ban helyileg nem a compute emulator érhető el.
* **.NET-memóriafoglalás** – Ez a módszer .NET-keretrendszer memória foglalási adatokat gyűjt a mintavételi profilkészítési módszer használatával. Az összegyűjtött adatok száma és mérete lefoglalt objektumok tartalmazza.
* **Párhuzamossági** – Ez a módszer összegyűjti az erőforrás versengés adatait és a folyamat és a szál végrehajtási adatok, amelyek hasznos többszálas és több folyamatból alkalmazások elemzése. A feldolgozási metódus számára gyűjti az adatokat, hogy a kód, például ha a szál megvárja-e a blokkok végrehajtását zárolt-e a hozzáférés az alkalmazás-erőforrás felszabadítását eseményekhez. Ez a módszer akkor hasznos, többszálas alkalmazások elemzéséhez.
* Is engedélyezheti **réteg interakció profilkészítési**, további információt a végrehajtási idő a szinkron ADO.NET meghívja a többrétegű alkalmazások, amelyek egy vagy több adatbázis kommunikálnak funkciókkal. Réteg interakció adatokat gyűjthet a profilkészítési módszerek bármelyikével. Réteg interakció profilkészítési kapcsolatos további információkért lásd: [réteg kapcsolati nézet](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Profilkészítési beállításainak konfigurálása
A következő ábrán az Azure-alkalmazás közzététele párbeszédpanelen a profilkészítési beállításainak konfigurálása.

![Profilkészítési beállításainak konfigurálása](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Ahhoz, hogy a **adatgyűjtés engedélyezése** jelölőnégyzetet, rendelkeznie kell a Profilkészítő telepítve a helyi számítógépen, Ön által használt közzététele a felhőalapú szolgáltatás. Alapértelmezés szerint a profilkészítőt a Visual Studio telepítése során települ.
> 
> 

### <a name="to-configure-profiling-settings"></a>Profilkészítési beállítások konfigurálása
1. A Solution Explorerben nyissa meg az Azure-projekt helyi menüjének, és válassza **közzététel**. Egy felhőalapú szolgáltatás közzététele kapcsolatos részletes lépéseiért lásd: [közzétételi egy felhőalapú szolgáltatás Azure eszközökkel](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. Az a **Azure-alkalmazás közzététele** párbeszédpanelen döntött, hogy a **speciális beállítások** fülre.
3. Profilkészítési engedélyezéséhez jelölje be a **adatgyűjtés engedélyezése** jelölőnégyzetet.
4. A profilkészítési beállítások konfigurálásához, válassza ki a **beállítások** hivatkozás. A profilkészítési beállítások párbeszédpanel jelenik meg.
5. Az a **profilkészítési, melyik módszert szeretné használni** választókapcsolók, válassza ki a profilkészítési, hogy szüksége van.
6. A réteg interakció profilkészítési adatokat gyűjthet, válassza ki a **engedélyezése réteg interakció profilkészítési** jelölőnégyzetet.
7. A beállítások mentéséhez válassza ki a **OK** gombra.
   
    Ha az alkalmazás közzétételéhez, ezeket a beállításokat használ az egyes szerepkörökhöz profilkészítési munkamenet létrehozásához.

## <a name="viewing-profiling-reports"></a>Profilkészítési jelentések megtekintése
A profilkészítési munkamenet létrehozása a felhőszolgáltatásban található egy szerepkör minden egyes példányánál. Minden munkamenet a Visual Studio eszközből a profilkészítési jelentések megtekintéséhez a Server Explorer ablakban megtekintheti, és válassza ki az Azure számítási csomópont válasszon ki egy példányt a szerepkör. Majd megtekintheti a profilkészítési jelentést, az alábbi ábrán látható módon.

![Az Azure-ból profilkészítési jelentés megtekintése](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Profilkészítési jelentések megtekintése
1. A Server Explorer ablak a Visual Studio megtekintéséhez menüsávban válassza a nézet, Server Explorer.
2. Válassza ki az Azure számítási csomópont, és válassza ki az Azure-telepítés csomópont a felhőalapú szolgáltatás, a kijelölt profil közzé a Visual Studio.
3. Egy példány profilkészítési jelentések megtekintéséhez válassza ki a szerepkört a szolgáltatásban, egy adott művelethez a helyi menü megnyitásához, és válassza **profilkészítési jelentés megtekintése**.
   
    A jelentés egy .vsp fájlt most le az Azure-ból, és a letöltési állapotát az Azure tevékenységnapló jelenik meg. A letöltés befejezése után megjelenik-e a profilkészítési jelentés nevű Visual Studio-szerkesztőben lapon <Role name> *<Instance Number>* <identifier>.vsp. A jelentés összefoglaló adatai jelennek meg.
4. Az aktuális nézet listán megjelenítendő különböző nézeteket a jelentést, válassza ki a kívánt nézetet. További információkért lásd: [profilkészítési eszközök jelentés nézetek](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>További lépések
[Hibakeresési Cloud Services csomag](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[A Visual Studio eszközből közzétételéhez Azure-Felhőszolgáltatás](https://msdn.microsoft.com/library/azure/ee460772.aspx)

