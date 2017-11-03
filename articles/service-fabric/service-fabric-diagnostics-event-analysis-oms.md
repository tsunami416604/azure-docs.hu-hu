---
title: "Az Azure Service Fabric esemény elemzésekről az OMS |} Microsoft Docs"
description: "Információ megjelenítése és eseményeket OMS figyelési és az Azure Service Fabric-fürtök diagnosztika elemzése."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Esemény elemzése és az OMS képi megjelenítés

Az Operations Management Suite (OMS) felhőalapú szolgáltatások és szolgáltatások figyelése és az alkalmazások diagnosztika segítenek gyűjteménye. Az OMS Szolgáltatáshoz, és mi kínál részletesebb áttekintése, olvassa el [Mi az az OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>A Naplóelemzési és az OMS-munkaterület

Naplóelemzési kezelt erőforrások, például egy Azure storage tábla vagy az ügynök gyűjti az adatokat, és egy központi tárházban megőrzi azt. Az elemzés, a riasztás és a képi megjelenítés, vagy a használt további exportáló majd lehet az az adatokat. A Naplóelemzési támogatja az eseményeket, teljesítményadatokat vagy egyéb egyéni adatokat.

Ha OMS van konfigurálva, hogy hozzáférést egy adott *OMS-munkaterület*, ahol lekérdezése vagy az irányítópultokon ábrázolt a adatokat.

Naplóelemzési fogadja a adatokat, miután a OMS rendelkezik, több *megoldások* , amelyek a figyelheti a bejövő adatok, több forgatókönyv szerint testre szabott előre csomagolt megoldások. Ezek közé tartozik egy *Service Fabric Analytics* megoldás és a *tárolók* megoldást, amely a két legfontosabb ők diagnosztika és a Service Fabric-fürtök használata esetén figyelését. Léteznek számos egyéb is, amelyek érdemes felfedezése, és az OMS egyéni megoldások, amelyek olvashat további tudnivalókat a létrehozását is lehetővé teszi [Itt](../operations-management-suite/operations-management-suite-solutions.md). Az azonos OMS-munkaterület Naplóelemzési mellett egy fürtben való használat érdekében minden megoldásnak konfigurálható. Munkaterületek lehetővé teszi egyéni irányítópultok és a képi megjelenítés, az adatok és az adatok gyűjtése, feldolgozása, és elemezni szeretné a módosításokat.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>A Service Fabric Analytics megoldás az OMS-munkaterület beállítása
Javasoljuk, hogy a Service Fabric megoldás szerepeljenek az OMS-munkaterület - tartalmaz egy irányítópultot, amely megjeleníti a platformot, és az alkalmazás szintről a különböző bejövő naplócsatornák, és lehetővé teszi a lekérdezést a Service Fabric meghatározott naplókban. Íme egy viszonylag egyszerű Service Fabric megoldás néz, a fürtön telepítve egyetlen alkalmazással:

![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Két módon lehet felkészítse és konfigurálja az OMS-munkaterület, a Resource Manager-sablon vagy közvetlenül az Azure piactérről. Használja a korábbi egy fürtöt telepít, és ez utóbbi Ha már rendelkezik egy fürt üzembe helyezéséhez diagnosztika engedélyezése után.

### <a name="deploying-oms-using-a-resource-management-template"></a>Erőforrás-kezelés sablon használatával OMS telepítése

A Resource Manager-sablon a fürt telepítésekor a sablon is létrehozhat egy új OMS-munkaterület felvétele a Service Fabric megoldás, és konfigurálja úgy, hogy a megfelelő tárolási táblákból származó adatokat olvasni.

>[!NOTE]
>Ennek működéséhez diagnosztika engedélyezni kell ahhoz, hogy az Azure storage táblákat OMS léteznek rendelkezik / Log Analyticshez adatait a következőre.

[Itt](https://azure.microsoft.com/resources/templates/service-fabric-oms/) egy minta sablon használ, és módosítsa megfelelően követelmény, amely a fenti műveleteket hajt végre. Abban az esetben, hogy azt szeretné, hogy több lehetőség, néhány további sablonok, amelyek különböző beállítások attól függően, hogy ha a folyamat során is, hogyan kell beállítani az OMS-munkaterület - helyen találhatók [Service Fabric-és OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Központi telepítés OMS segítségével az Azure piactéren keresztül

Ha egy OMS-munkaterület hozzáadása a fürt telepítése után szeretné végrehajtani, látogasson el az Azure piactéren, és keressen *"Service Fabric Analytics"*.

![A piactéren OMS ú elemzés](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* Kattintson a **létrehozása**
* A Service Fabric Analytics létrehozása ablakában kattintson **munkaterület kiválasztása** a a *OMS-munkaterület* mezőben, majd **hozzon létre egy új munkaterületet**. Töltse ki a szükséges bejegyzések – a egyetlen követelménye, hogy az előfizetés, a Service Fabric-fürt és az OMS-munkaterület azonosnak kell lennie. Ha a bejegyzések érvényesítése az OMS-munkaterület központi telepítéséhez indul el. Ez csak kell eltarthat néhány percig.
* Ha befejezte, kattintson a **létrehozása** újra a Service Fabric Analytics létrehozása ablak alján. Győződjön meg arról, hogy alatt megjelenik az új munkaterületet *OMS-munkaterület*. Ez hozzáadja a megoldás az imént létrehozott munkaterületet.


Bár a megoldás hozzáadása a munkaterületen, a munkaterület továbbra is meg kell kapcsolódnia kell a fürt érkező diagnosztikai adatok. Nyissa meg a Service Fabric elemzési megoldások a létrehozott erőforráscsoportot. Megjelenik egy *ServiceFabric (\<nameOfOMSWorkspace\>)*.

* Kattintson a – áttekintés oldalra, ahol megoldás beállításokat, a beállítások munkaterületet, és keresse meg az OMS-portálon keresse meg a megoldást.
* A bal oldali navigációs menüben kattintson a **tárfiókok naplók**a *munkaterület adatforrások*.

    ![Service Fabric elemzési megoldások a portálon](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* A a *a tárolási fiók naplófájljai* kattintson **hozzáadása** hozzáadása a fürt naplók a munkaterület tetején.
* Kattintson a be **tárfiók** vegye fel a fürt létrehozása a megfelelő fiókot. Ha az alapértelmezett nevet, a tárfiók neve *sfdg\<resourceGroupName\>*. Azt is ellenőrizheti az Azure Resource Manager-sablon központi telepítéséhez a fürt által használt érték ellenőrzésével a `applicationDiagnosticsStorageAccountName`. Is lehet, hogy görgessen lefelé, és kattintson a **betöltése több** Ha a fiók neve nem jelenik meg. Kattintson a jobb oldali tárolási fiók nevére, amikor azt mutatja rá kattintva jelölje ki.
* A következő együtt kell megadni a *adattípus*, kell lennie, amely **Service Fabric események**.
* A *forrás* automatikusan meg *WADServiceFabric\*címke*.
* Kattintson a **OK** a munkaterület kapcsolódni a fürt naplókat.

    ![Adja hozzá a tárolási fiók naplófájljai az OMS-be](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* A fiók kell megjelennek-e részeként a *a tárolási fiók naplófájljai* a munkaterület adatforrások.

A most hozzáadott a Service Fabric elemzési megoldás az OMS Naplóelemzési munkaterület, most már megfelelően csatlakozik-e a fürt platform és Alkalmazásnapló-tábla. Azonos módon adhat hozzá további források a munkaterületre.

## <a name="using-the-oms-agent"></a>Az OMS-ügynök használatával

Ez nem ajánlott használandó EventFlow és ÜVEGVATTA összesítési megoldások mert lehetővé teszik a több moduláris megközelítést diagnosztikát és figyelési. Például ha szeretné módosítani a kimenetek EventFlow, igényel a tényleges instrumentation, a konfigurációs fájl csak egy egyszerű módosítása nem változik. Ha azonban úgy dönt, hogy beruházásának OMS használatával, és a rendszer továbbra is használja az esemény elemzéshez (kell lennie az egyetlen platform használhatja, de ahelyett, hogy, hogy legalább az egyik platformot), azt javasoljuk, hogy a beállítás mentése megismerkedhet a [OMS rendelkezésre állási csoport félellenőrzés](../log-analytics/log-analytics-windows-agents.md). Is használjon az OMS-ügynököt a fürthöz, a tárolók telepítésekor lásd alább.

Mindezt a folyamat alkalmazása viszonylag könnyű, mivel csak kell hozzáadni az ügynök, mert a virtuálisgép-méretezési bővítmény beállítva a Resource Manager-sablon, győződjön meg arról, hogy megkapja-e telepítve minden egyes csomópontján. Rendszert futtató fürtöket található, amely telepíti az OMS-munkaterület a Service Fabric-megoldás (lásd fent), és az ügynök felvétele a csomópontok minta Resource Manager sablon [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Ez előnyei a következők:

* Részletesebb adatok a teljesítmény-számlálókból és a metrikák oldalon
* Könnyen konfigurálható metrikák gyűjtenek a fürtből, és anélkül, hogy a fürt konfiguráció frissítése. Az ügynök beállításai az OMS-portálon végezhető, és az ügynök automatikusan újraindul a szükséges konfigurációs kereséséhez. Az egyes teljesítményszámlálókra átvételéhez OMS-ügynököt megadásához nyissa meg a munkaterületet **Kezdőlap > Beállítások > adatok > Windows-teljesítményszámlálók** , és válassza ki, szívesen látna az összegyűjtött adatok
* Adatok gyorsabb, mint az azt nem kell előtt OMS alatt észlelnie kell tárolni mutatja / Log Analyticshez
* Sokkal egyszerűbb, tárolók figyelése azért, mert azt átvételéhez docker-naplók (stdout, stderr) és a statisztikák (teljesítménymutatók tároló-és csomópont)

Itt a fő szempont, hogy az ügynököt a rendszer telepíti a fürt összes alkalmazás mellett, mivel lehet néhány hatással van a fürtön található alkalmazásainak teljesítményét.

## <a name="monitoring-containers"></a>Tárolók figyelése

A Service Fabric-fürt tárolók üzembe, esetén ajánlott, hogy a fürt van beállítva az OMS-ügynököt, és, hogy a tárolók megoldás az OMS-munkaterület számára engedélyezze a megfigyelést és diagnosztikai bővült. Ez a tárolók megoldás néz munkaterület:

![Alapszintű OMS irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi, hogy több OMS kérdezhetők le, vagy feladatkonfigurációkat teljesítménymutatók használt tároló-specifikus-naplók gyűjtésére. A napló típusok tartoznak a következők:

* ContainerInventory: tároló helye, a nevét, és a képeket információkat jeleníti meg.
* ContainerImageInventory: információ a központilag telepített lemezképek, többek között az azonosítók vagy mérete
* ContainerLog: más bejegyzések, adott hibanaplókat és a docker-naplók (stdout, stb.)
* ContainerServiceLog: docker démon parancsok futtatása
* Teljesítmény: teljesítményszámlálókat, beleértve a tároló processzor, memória, a hálózati forgalom, a lemez i/o, és a gazdagép gépekről egyéni metrikák

Ez a cikk tároló figyelését a fürt beállításához szükséges lépéseket ismerteti. Az OMS Szolgáltatáshoz tartozó tárolók megoldás kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](../log-analytics/log-analytics-containers.md).

A tároló megoldás beállítása a munkaterületen található, győződjön meg arról, hogy a fenti lépések a fürtcsomópontokon telepített OMS-ügynököt. Ha készen áll a fürt, telepítse azt egy tárolót. Figyelembe kell vennie, hogy az első egy tároló lemezképfájlt egy fürtbe, vesz letölti a lemezképet a méretétől függően néhány percig.

A Azure piactérről, keresse meg a *tároló figyelésére szolgáló megoldás* , és hozzon létre a **tároló figyelésére szolgáló megoldás** eredményre kell tartozniuk, a figyelés és a felügyeleti kategória.

![Tárolók megoldás hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

Az létrehozását lépést, a kérelmek az OMS-munkaterület. Jelölje ki a fenti telepítési hoztak létre. Ezt a lépést hozzáadja az OMS-munkaterület belül tárolók megoldást, és automatikusan felismeri a sablon által telepített OMS-ügynököt. Az ügynök fog adatokat gyűjt a fürtöt és kevesebb mint 15 percet tárolók folyamatainak vagy Igen, megtekintheti az adatokkal, mint a lemezképet a fenti irányítópult mentése könnyű megoldást.


## <a name="next-steps"></a>Következő lépések

Ismerheti meg a következő OMS-eszközök és a beállítások munkaterület az igényeinek megfelelően testre:

* A helyi fürthöz OMS szeretnék adatokat küldeni a OMS használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [internetkapcsolattal nem rendelkező számítógépek kapcsolódásához az OMS-be az OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési