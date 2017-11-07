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
ms.openlocfilehash: 09542c0e7f628ca4fea00a6562c0b9525432c213
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
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

Lásd: [OMS Naplóelemzési beállítása](service-fabric-diagnostics-oms-setup.md) használatába Ez a fürt számára.

## <a name="using-the-oms-agent"></a>Az OMS-ügynök használatával

Ez nem ajánlott használandó EventFlow és ÜVEGVATTA összesítési megoldások mert lehetővé teszik a több moduláris megközelítést diagnosztikát és figyelési. Például ha szeretné módosítani a kimenetek EventFlow, igényel a tényleges instrumentation, a konfigurációs fájl csak egy egyszerű módosítása nem változik. Ha azonban úgy dönt, hogy beruházásának OMS Naplóelemzési használ, be kell állítania a [OMS-ügynököt](../log-analytics/log-analytics-windows-agents.md). Is használjon az OMS-ügynököt a fürthöz, a tárolók telepítésekor lásd alább. 

Központi keresztül történő [adja hozzá az OMS-ügynököt a fürthöz](service-fabric-diagnostics-oms-agent.md) a lépéseket.

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

[Az OMS szolgáltatáshoz tárolók figyelése](service-fabric-diagnostics-oms-containers.md) tároló figyelését a fürt beállításához szükséges lépéseket ismerteti. Az OMS Szolgáltatáshoz tartozó tárolók megoldás kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Következő lépések

Ismerheti meg a következő OMS-eszközök és a beállítások munkaterület az igényeinek megfelelően testre:

* A helyi fürthöz OMS szeretnék adatokat küldeni a OMS használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [internetkapcsolattal nem rendelkező számítógépek kapcsolódásához az OMS-be az OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési