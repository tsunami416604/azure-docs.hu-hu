---
title: Http 502- és HTTP 503-as hibák javítása
description: Az 502-es rossz átjáró és az 503-as szolgáltatás nem érhető el hibák elhárítása az Azure App Service-ben üzemeltetett alkalmazásban.
tags: top-support-issue
keywords: 502 rossz átjáró, 503 szolgáltatás nem érhető el, hiba 503, hiba 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688329"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Az "502 rossz átjáró" és az "503 szolgáltatás nem érhető el" HTTP-hibáinak elhárítása az Azure App Service szolgáltatásban
Az "502-es rossz átjáró" és az "503-as szolgáltatás nem érhető el" gyakori hibák az [Azure App Service-ben](https://go.microsoft.com/fwlink/?LinkId=529714)üzemeltetett alkalmazásban. Ez a cikk segítséget nyújt a hibák elhárításához.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és kattintson a **Támogatás beszerezni**lehetőségre.

## <a name="symptom"></a>Hibajelenség
Amikor az alkalmazást böngészi, http "502 Bad Gateway" hibaüzenetet vagy "503-as szolgáltatás nem érhető el" hibaüzenetet ad vissza.

## <a name="cause"></a>Ok
Ezt a problémát gyakran alkalmazásszintű problémák okozzák, például:

* hosszú időt vesz igénybe
* alkalmazás nagy memóriával / CPU
* egy kivétel miatt összeomlik.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Az "502 rossz átjáró" és az "503 szolgáltatás nem érhető el" hibák elhárításának lépései
A hibaelhárítás három különböző feladatra osztható, sorrendben:

1. [Az alkalmazások viselkedésének megfigyelése és figyelése](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma enyhítése](#mitigate)

[Az App Service](overview.md) különböző lehetőségeket kínál az egyes lépésein.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Az alkalmazások viselkedésének megfigyelése és figyelése
#### <a name="track-service-health"></a>Szolgáltatás állapotának nyomon követése
A Microsoft Azure minden alkalommal nyilvánosságra hozza a szolgáltatás megszakítását vagy a teljesítmény romlását. A szolgáltatás állapotát az Azure [Portalon](https://portal.azure.com/)követheti nyomon. További információ: [A szolgáltatás állapotának nyomon követése.](../monitoring-and-diagnostics/insights-service-health.md)

#### <a name="monitor-your-app"></a>Az alkalmazás figyelése
Ez a beállítás lehetővé teszi, hogy megtudja, ha az alkalmazás, amelyek bármilyen probléma. Az alkalmazás paneljén kattintson a **Kérések és hibák** csempére. A **Metrikus** panel megmutatja az összes hozzáadható mérőszámokat.

Az alkalmazáshoz figyelni kívánt mérőszámok némelyike

* Átlagos memóriamunkakészlet
* Átlagos válaszidő
* PROCESSZOR idő
* Memóriamunkakészlet
* Kérelmek

![monitor app felé megoldása HTTP hibák 502 rossz átjáró és 503 szolgáltatás nem érhető el](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

További információkért lásd:

* [Alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatgyűjtés
#### <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata
Az App Service intelligens és interaktív élményt nyújt az alkalmazás konfigurálás nélküli hibaelhárításához. Ha problémákba ütközik az alkalmazással kapcsolatban, a diagnosztikai eszköz rámutat arra, hogy mi a hiba, amely a megfelelő információkhoz vezeti a problémát, hogy könnyebben és gyorsabban elháríthassa és megoldhassa a problémát.

Az App Service-diagnosztika eléréséhez keresse meg az App Service-alkalmazást vagy az App Service-környezetet az [Azure Portalon.](https://portal.azure.com) A bal oldali navigációs, kattintson **a diagnosztizálása és a problémák megoldása**.

#### <a name="use-the-kudu-debug-console"></a>A Kudu Debug konzol használata
Az App Service tartalmaz egy hibakeresési konzolt, amely segítségével hibakeresés, feltárása, fájlok feltöltése, valamint a JSON-végpontok a környezettel kapcsolatos információk megszerzéséhez. Ezt nevezzük *kudu konzolnak* vagy az alkalmazás *SCM irányítópultjának.*

Ezt az irányítópultot az **&lt;>.scm.azurewebsites.net/** https:// linkre kattintva érheti el.

Néhány dolog, hogy Kudu nyújt a következők:

* az alkalmazás környezeti beállításai
* naplófolyam
* diagnosztikai memóriakép
* debug konzol, amelyben futtathatja a Powershell parancsmagjait és az alapvető DOS parancsokat.

Egy másik hasznos jellemzője Kudu, hogy abban az esetben, ha az alkalmazás dobott első esély kivételek, használhatja Kudu és a SysInternals eszköz Procdump létrehozni memóriaképek. Ezek a memóriaképek a folyamat pillanatképei, és gyakran segítenek az alkalmazással kapcsolatos bonyolultabb problémák elhárításában.

A Kudu ban elérhető funkciókról az [Azure Webhelyek online eszközeiről](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)további információt talál.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma enyhítése
#### <a name="scale-the-app"></a>Az alkalmazás méretezése
Az Azure App Service-ben a nagyobb teljesítmény és átviteli teljesítmény érdekében módosíthatja az alkalmazás futtatásának léptékét. Egy alkalmazás felskálázása két kapcsolódó műveletet foglal magában: az App Service-csomag magasabb tarifacsomagra történő módosítása, és bizonyos beállítások konfigurálása a magasabb tarifacsomagra való váltás után.

A méretezésről további információt az [Alkalmazás méretezése az Azure App Service-ben című témakörben talál.](manage-scale-up.md)

Ezenkívül dönthet úgy is, hogy az alkalmazást több példányon is futtatja. Ez nem csak több feldolgozási képességet biztosít, hanem némi hibatűrést is biztosít. Ha a folyamat leáll az egyik példányon, a másik példány továbbra is a kérelmek kiszolgálása.

Beállíthatja, hogy a méretezés manuális vagy automatikus legyen.

#### <a name="use-autoheal"></a>Az Automatikus javítás használata
Az AutoHeal a kiválasztott beállítások (például konfigurációs módosítások, kérések, memóriaalapú korlátok vagy a kérelem végrehajtásához szükséges idő) alapján újrahasznosítja az alkalmazás munkavégző folyamatát. Az idő nagy részében, újra a folyamat a leggyorsabb módja annak, hogy visszaszerezze a problémát. Bár az alkalmazást bármikor újraindíthatja közvetlenül az Azure Portalon belül, az AutoHeal automatikusan megteszi. Mindössze annyit kell tennie, hogy néhány eseményindítót ad hozzá az alkalmazás gyökérweb.config gyökérében. Ne feledje, hogy ezek a beállítások ugyanúgy működnek, még akkor is, ha az alkalmazás nem .NET.

További információt az [Azure-webhelyek automatikus meggyógyítása című témakörben talál.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)

#### <a name="restart-the-app"></a>Az alkalmazás újraindítása
Ez gyakran a legegyszerűbb módja annak, hogy visszaszerezze az egyszeri problémákat. Az [Azure Portalon](https://portal.azure.com/)az alkalmazás paneljén lehetőség van az alkalmazás leállítására vagy újraindítására.

 ![restart app to solve HTTP errors of 502 bad gateway and 503 service unavailable](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Az Azure Powershell használatával is kezelheti az alkalmazást. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

