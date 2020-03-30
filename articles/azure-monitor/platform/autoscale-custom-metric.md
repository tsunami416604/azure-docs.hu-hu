---
title: Automatikus skálázás az Azure-ban egyéni metrika használatával
description: Ismerje meg, hogyan skálázhatja az erőforrást egyéni metrika szerint az Azure-ban.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425119"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Első lépések az egyéni mérőszámokkal az Azure-ban
Ez a cikk ismerteti, hogyan skálázhatja az erőforrást egy egyéni metrika az Azure Portalon.

Az Azure Monitor automatikus skálázása csak [a virtuális gépméretezési készletekre,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra](https://azure.microsoft.com/services/cloud-services/), [az alkalmazásszolgáltatásokra - webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/), [az Azure Data Explorer fürtre](https://azure.microsoft.com/services/data-explorer/) ,   
Integrációs szolgáltatáskörnyezet és [API-kezelési szolgáltatások.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="lets-get-started"></a>Lehetővé teszi, hogy az induláshoz
Ez a cikk feltételezi, hogy rendelkezik egy webalkalmazás alkalmazáselemzési konfigurálva. Ha még nem rendelkezik ilyen, [beállíthatja az Application Insights][1] ot a ASP.NET webhelyéhez

- [Az Azure-portál megnyitása][2]
- Kattintson az Azure Monitor ikonra a bal oldali navigációs ablakban.
  ![Az Azure Monitor elindítása][3]
- Kattintson az Automatikus skálázás beállításra az összes olyan erőforrás megtekintéséhez, ![amelyre az automatikus skálázás alkalmazható, valamint a jelenlegi automatikus skálázási állapot Az Automatikus skálázás felderítése az Azure-figyelőben][4]
- Nyissa meg az "Automatikus skálázás" panelt az Azure Monitorban, és válassza ki a méretezéshez kívánt erőforrást
  > Megjegyzés: Az alábbi lépések egy olyan webalkalmazáshoz társított alkalmazásszolgáltatási csomagot használnak, amelyhez konfigurált alkalmazáselemzési adatok vannak konfigurálva.
- Az erőforrás méretezési beállítási paneljén figyelje meg, hogy az aktuális példányszáma 1. Kattintson az "Automatikus skálázás engedélyezése" gombra.
  ![Új webalkalmazás méretezési beállítása][5]
- Adja meg a skála beállításnevét, és kattintson a "Szabály hozzáadása" gombra. Figyelje meg a méretezési szabály beállításait, amelyek a jobb oldalon környezetablaktáblaként nyílnak meg. Alapértelmezés szerint beállítja a példányszám 1-es skálázását, ha az erőforrás CPU-százaléka meghaladja a 70%-ot. Módosítsa a metrikaforrást felül "Application Insights" (Alkalmazáselemzési adatok" – válassza ki az alkalmazáselemzési erőforrást az "Erőforrás" legördülő menüben, majd válassza ki azt az egyéni metrikát, amely alapján skálázni szeretne.
  ![Méretezés egyéni metrika szerint][6]
- A fenti lépéshez hasonlóan adjon hozzá egy léptékszabályt, amely 1-re méretezi és csökkenti a léptékszámot, ha az egyéni metrika egy küszöbérték alatt van.
  ![Méretezés a cpu alapján][7]
- Állítsa be a példánykorlátokat. Ha például az egyéni metrika-ingadozásoktól függően 2–5 példány között szeretne skálázni, állítsa a "minimum" értéket "2", a "maximum" és az "5" és az "alapértelmezett" értéket "2"-re.
  > Megjegyzés: Abban az esetben, ha probléma merül fel az erőforrás-metrikák olvasásakor, és az aktuális kapacitás az alapértelmezett kapacitás alatt van, majd az erőforrás rendelkezésre állásának biztosítása érdekében az automatikus skálázás az alapértelmezett értékre lesz skálázva. Ha az aktuális kapacitás már nagyobb, mint az alapértelmezett kapacitás, az automatikus skálázás nem fog beskálázni.
- Kattintson a "Mentés"

Gratulálunk! Most már sikeresen létrehozta a méretezési beállítást a webalkalmazás egyéni mérőszám alapján automatikus méretezéséhez.

> Megjegyzés: Ugyanazok a lépések alkalmazhatók a VMSS- vagy felhőszolgáltatási szerepkör első lépésekhez.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

