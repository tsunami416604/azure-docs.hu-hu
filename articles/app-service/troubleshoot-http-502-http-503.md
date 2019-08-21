---
title: 502-es hibás átjáró javítása, 503 szolgáltatás nem érhető el. hibák – Azure App Service | Microsoft Docs
description: Az 502-es hibás átjáró és a 503 szolgáltatás nem érhető el a Azure App Service-ben üzemeltetett alkalmazásban.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 hibás átjáró, 503 szolgáltatás nem érhető el, hiba 503, Hiba 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d40b11538d5aee20b54ddd6d3ca112f30238b512
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636545"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>A "502 hibás átjáró" és "503 szolgáltatás nem érhető el" HTTP-hibák elhárítása Azure App Service
a "502 hibás átjáró" és a "503 szolgáltatás nem érhető el" gyakori hibák a Azure App Serviceban [](https://go.microsoft.com/fwlink/?LinkId=529714)üzemeltetett alkalmazásban. Ez a cikk segít a hibák elhárításában.

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és kattintson a **támogatás kérése**lehetőségre.

## <a name="symptom"></a>Jelenség
Amikor megkeresi az alkalmazást, egy HTTP "502 hibás átjáró" hibaüzenetet ad vissza, vagy egy HTTP "503 szolgáltatás nem érhető el" hibaüzenet jelenik meg.

## <a name="cause"></a>Ok
Ezt a problémát gyakran az alkalmazási szintű problémák okozzák, például:

* a kérelmek hosszú időt vesznek igénybe
* nagy memóriát/CPU-t használó alkalmazás
* az alkalmazás egy kivétel miatt összeomlik.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Hibaelhárítási lépések a "502 hibás átjáró" és "503 szolgáltatás nem érhető el" hibáinak megoldásához
A hibaelhárítás három különálló feladatra osztható, szekvenciális sorrendben:

1. [Az alkalmazások viselkedésének megfigyelése és figyelése](#observe)
2. [Adatgyűjtés](#collect)
3. [A probléma enyhítése](#mitigate)

[App Service](overview.md) az egyes lépésekben különböző lehetőségeket biztosít.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Az alkalmazások viselkedésének megfigyelése és figyelése
#### <a name="track-service-health"></a>Szolgáltatás állapotának nyomon követése
Microsoft Azure minden alkalommal nyilvánosságra kerül, amikor a szolgáltatás megszakad vagy a teljesítmény romlása. A szolgáltatás állapotát az [Azure Portalon](https://portal.azure.com/)követheti nyomon. További információ: a [szolgáltatás állapotának nyomon követése](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Az alkalmazás figyelése
Ezzel a beállítással megtudhatja, hogy az alkalmazás problémákba ütközik-e. Az alkalmazás paneljén kattintson a **kérelmek és hibák** csempére. A **metrika** panel megjeleníti az összes felvehető mérőszámot.

Előfordulhat, hogy az alkalmazáshoz figyelni kívánt metrikák némelyike

* Átlagos memória-munkakészlet
* Átlagos válaszidő
* CPU-idő
* Memória-munkakészlet
* Kérelmek

![az alkalmazás figyelése a 502-es hibás átjáró HTTP-hibáinak megoldásához, és a 503 szolgáltatás nem érhető el](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

További információkért lásd:

* [Alkalmazások figyelése Azure App Service](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
#### <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata
A App Service egy intelligens és interaktív élményt nyújt, amely segít a szükséges konfigurációval kapcsolatos hibák megoldásában. Ha az alkalmazással kapcsolatos problémákba ütközik, a diagnosztikai eszköz kimutatja, hogy mi a baj, hogy a probléma megoldásához könnyebben és gyorsan javítsa a megfelelő információkat.

App Service diagnosztika eléréséhez nyissa meg a App Service alkalmazást vagy App Service Environment a [Azure Portalban](https://portal.azure.com). A bal oldali navigációs sávon kattintson a **problémák diagnosztizálása és megoldása**elemre.

#### <a name="use-the-kudu-debug-console"></a>A kudu hibakeresési konzoljának használata
A App Service egy hibakeresési konzolt tartalmaz, amellyel hibakeresést végezhet, megvizsgálhatja, feltöltheti és feltölthet fájlokat, valamint JSON-végpontokat a környezettel kapcsolatos információk beszerzéséhez. Ezt nevezzük az alkalmazás *kudu* -konzoljának vagy *SCM* -irányítópultjának.

Ehhez az irányítópulthoz az **alkalmazás&lt;https://>. SCM. azurewebsites. net/** .

A kudu által biztosított néhány dolog:

* az alkalmazás környezeti beállításai
* napló Stream
* diagnosztikai memóriakép
* hibakeresési konzol, amelyen PowerShell-parancsmagokat és alapszintű DOS-parancsokat futtathat.

A kudu egy másik hasznos funkciója, hogy ha az alkalmazás első alkalommal kivételeket vált ki, akkor a kudu és a SysInternals eszköz Procdump használatával hozhat létre memóriaképeket. Ezek a memóriaképek a folyamat pillanatképei, és gyakran segítenek az alkalmazással kapcsolatos bonyolultabb problémák megoldásában.

A kudu-ben elérhető szolgáltatásokkal kapcsolatos további információkért lásd: az [Azure websites online eszközei, amelyekről érdemes tudni](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma enyhítése
#### <a name="scale-the-app"></a>Az alkalmazás méretezése
Azure App Service a teljesítmény és az átviteli sebesség növelése érdekében módosíthatja azt a méretezést, amelyen az alkalmazást futtatja. Az alkalmazások horizontális felskálázása két kapcsolódó művelettel jár: a App Service terv magasabb árképzési szintre való módosításával, valamint bizonyos beállítások konfigurálásával, miután átváltotta a magasabb díjszabási szintet.

További információ a skálázásról: [alkalmazások méretezése Azure app Serviceban](manage-scale-up.md).

Emellett dönthet úgy is, hogy az alkalmazást egynél több példányon futtatja. Ez nem csupán nagyobb feldolgozási képességet biztosít, hanem némi hibatűrést is biztosít. Ha a folyamat egy példányon leáll, a másik példány továbbra is kéri a kérelmek kiszolgálását.

Beállíthatja, hogy a méretezés manuális vagy automatikus legyen.

#### <a name="use-autoheal"></a>Az autoheal használata
Az automatikus gyógyulás a választott beállítások alapján újrahasznosítja az alkalmazás munkavégző folyamatát (például a konfiguráció módosításait, a kérelmeket, a memória alapú korlátokat vagy a kérelem végrehajtásához szükséges időt). A legtöbb esetben a folyamat újrahasznosítása a leggyorsabb módszer a probléma megoldására. Bár az alkalmazást bármikor újraindíthatja közvetlenül az Azure Portalon, az automatikus gyógyítás automatikusan elvégzi Önt. Mindössze annyit kell tennie, hogy a root web. config fájlban ad hozzá néhány eseményindítót az alkalmazásához. Vegye figyelembe, hogy ezek a beállítások ugyanúgy működnek, még akkor is, ha az alkalmazás nem .NET-alapú.

További információ: az [Azure webhelyek automatikus](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)javítása.

#### <a name="restart-the-app"></a>Az alkalmazás újraindítása
Ez gyakran az egyszeri problémákból való helyreállítás legegyszerűbb módja. Az [Azure Portalon](https://portal.azure.com/)az alkalmazás paneljén lehetősége van az alkalmazás leállítására vagy újraindítására.

 ![az alkalmazás újraindítása a 502-es hibás átjáró HTTP-hibáinak megoldásához, és a 503 szolgáltatás nem érhető el](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Az alkalmazást az Azure PowerShell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

