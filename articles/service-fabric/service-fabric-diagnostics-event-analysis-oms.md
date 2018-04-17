---
title: Az Azure Service Fabric esemény elemzése a Log Analyticshez |} Microsoft Docs
description: Információ megjelenítése és események Log Analyticshez használják a figyelési és az Azure Service Fabric-fürtök diagnosztika elemzése.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 290b1d594cc1f874bcfdd0cef728fc78af96f702
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Esemény elemzése és Naplóelemzési a képi megjelenítés

Az Azure felügyeleti megoldás, melyek felhőalapú szolgáltatások és szolgáltatások figyelése és az alkalmazások diagnosztika segítenek. A Naplóelemzési, és mi kínál részletes áttekintése, olvassa el [Naplóelemzési újdonságai?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület

Naplóelemzési kezelt erőforrások, például egy Azure storage tábla vagy az ügynök gyűjti az adatokat, és egy központi tárházban megőrzi azt. Az elemzés, a riasztás és a képi megjelenítés, vagy a használt további exportáló majd lehet az az adatokat. A Naplóelemzési támogatja az eseményeket, teljesítményadatokat vagy egyéb egyéni adatokat.

Ha konfigurálva van a Naplóelemzési, hogy hozzáférést egy adott *Naplóelemzési munkaterület*, ahol lekérdezése vagy az irányítópultokon ábrázolt a adatokat.

Naplóelemzési fogadja a adatokat, miután a Azure rendelkezik, több *megoldások* , amelyek a figyelheti a bejövő adatok, több forgatókönyv szerint testre szabott előre csomagolt megoldások. Ezek közé tartozik egy *Service Fabric Analytics* megoldás és a *tárolók* megoldást, amely a két legfontosabb ők diagnosztika és a Service Fabric-fürtök használata esetén figyelését. Nincs több mások is, amelyek érdemes felfedezése, illetve Naplóelemzési is lehetővé teszi, hogy egyéni megoldások, amelyek olvashat további tudnivalókat létrehozásához [Itt](../operations-management-suite/operations-management-suite-solutions.md). Egy fürtben való használat érdekében minden megoldásnak azonos Naplóelemzési munkaterület mellett Naplóelemzési konfigurálható. Munkaterületek lehetővé teszi egyéni irányítópultok és a képi megjelenítés, az adatok és az adatok gyűjtése, feldolgozása, és elemezni szeretné a módosításokat.

## <a name="setting-up-a-log-analytics-workspace-with-the-service-fabric-analytics-solution"></a>A Service Fabric elemzési megoldások a Naplóelemzési munkaterület beállítása
Javasoljuk, hogy a Service Fabric megoldás szerepeljenek a Naplóelemzési munkaterület - tartalmaz egy irányítópultot, amely megjeleníti a platformot, és az alkalmazás szintről a különböző bejövő naplócsatornák, és lehetővé teszi a Service Fabric adott lekérdezés naplózza. Íme egy viszonylag egyszerű Service Fabric megoldás néz, a fürtön telepítve egyetlen alkalmazással:

![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Lásd: [Naplóelemzési beállítása](service-fabric-diagnostics-oms-setup.md) használatába Ez a fürt számára.

## <a name="using-the-oms-agent"></a>Az OMS-ügynök használatával

Ez nem ajánlott használandó EventFlow és ÜVEGVATTA összesítési megoldások mert lehetővé teszik a több moduláris megközelítést diagnosztikát és figyelési. Például ha szeretné módosítani a kimenetek EventFlow, igényel a tényleges instrumentation, a konfigurációs fájl csak egy egyszerű módosítása nem változik. Ha azonban úgy dönt, hogy beruházásának Naplóelemzési használ, be kell állítania a [OMS-ügynököt](../log-analytics/log-analytics-windows-agent.md). Is használjon az OMS-ügynököt a fürthöz, a tárolók telepítésekor lásd alább. 

Központi keresztül történő [adja hozzá az OMS-ügynököt a fürthöz](service-fabric-diagnostics-oms-agent.md) a lépéseket.

Ez előnyei a következők:

* Részletesebb adatok a teljesítmény-számlálókból és a metrikák oldalon
* Könnyen konfigurálható metrikák gyűjtenek a fürtből, és anélkül, hogy a fürt konfiguráció frissítése. Az ügynök beállításai az OMS-portálon végezhető, és az ügynök automatikusan újraindul a szükséges konfigurációs kereséséhez. Az egyes teljesítményszámlálókra átvételéhez OMS-ügynököt megadásához nyissa meg a munkaterületet **Kezdőlap > Beállítások > adatok > Windows-teljesítményszámlálók** , és válassza ki, szívesen látna az összegyűjtött adatok
* Adatok megjelenik gyorsabb, mint az azt nem kell előtt Naplóelemzési alatt észlelnie kell tárolni
* Sokkal egyszerűbb, tárolók figyelése azért, mert azt átvételéhez docker-naplók (stdout, stderr) és a statisztikák (teljesítménymutatók tároló-és csomópont)

Itt a fő szempont, hogy az ügynököt a rendszer telepíti a fürt összes alkalmazás mellett, mivel lehet néhány hatással van a fürtön található alkalmazásainak teljesítményét.

## <a name="monitoring-containers"></a>Tárolók figyelése

A Service Fabric-fürt tárolók üzembe, esetén ajánlott, hogy a fürt van beállítva az OMS-ügynököt, és, hogy a tárolók megoldás hozzáadta a Naplóelemzési munkaterület számára engedélyezze a megfigyelést és a diagnosztika. Ez a tárolók megoldás néz munkaterület:

![Alapszintű OMS irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi, hogy több Naplóelemzési kérdezhetők le, vagy feladatkonfigurációkat teljesítménymutatók használt tároló-specifikus-naplók gyűjtésére. A napló típusok tartoznak a következők:

* ContainerInventory: tároló helye, a nevét, és a képeket információkat jeleníti meg.
* ContainerImageInventory: információ a központilag telepített lemezképek, többek között az azonosítók vagy mérete
* ContainerLog: más bejegyzések, adott hibanaplókat és a docker-naplók (stdout, stb.)
* ContainerServiceLog: docker démon parancsok futtatása
* Teljesítmény: teljesítményszámlálókat, beleértve a tároló processzor, memória, a hálózati forgalom, a lemez i/o, és a gazdagép gépekről egyéni metrikák

[A Naplóelemzési tárolók figyelése](service-fabric-diagnostics-oms-containers.md) tároló figyelését a fürt beállításához szükséges lépéseket ismerteti. Log Analytics-tárolók megoldás kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>További lépések

Ismerheti meg a következő Naplóelemzési eszközöket és a beállítások munkaterület az igényeinek megfelelően testre:

* A helyi fürthöz Naplóelemzési szeretnék adatokat küldeni a Naplóelemzési használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [Internet-hozzáférés nélküli számítógép csatlakozik az OMS-átjáró Naplóelemzési](../log-analytics/log-analytics-oms-gateway.md)
* Log Analytics beállítása konfigurálása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési