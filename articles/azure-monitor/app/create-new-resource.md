---
title: Hozzon létre egy új Azure Application Insights-erőforrást |} A Microsoft Docs
description: Manuálisan állítsa be az Application Insights új élő alkalmazások figyelését.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 32da4ad6f35d8fd5585135a71572e0ad4fa41499
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082910"
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az Azure Application Insights a Microsoft Azure-ban jeleníti meg az alkalmazással kapcsolatos adatok *erőforrás*. Ezért részét képezi egy új erőforrás létrehozását [Application Insights beállítása egy új alkalmazás figyelésére][start]. Sok esetben egy erőforrás létrehozását teheti meg automatikusan az ide. De bizonyos esetekben egy erőforrást manuálisan létrehozott – például számára elkülönített erőforrások fejlesztési és éles környezetben hoz létre az alkalmazás.

Miután létrehozta az erőforrást, a kialakítási kulcs lekérése, és használja, amely az alkalmazás az SDK konfigurálására. Az erőforrás-kulcsot a telemetria hivatkozik, az erőforrás.

## <a name="sign-up-to-microsoft-azure"></a>Iratkozzon fel a Microsoft Azure
Ha még nem kapott egy [Microsoft-fiók, most már igényelhet](https://live.com). (Ha használja a szolgáltatások, például az Outlook.com, onedrive vállalati verzió, Windows Phone vagy XBox Live, már Microsoft-fiókkal.)

Emellett az előfizetés [Microsoft Azure](https://azure.com). Ha a csapata vagy szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja, használja a Windows Live ID azonosítójával. Csak díjkötelesek kell fizetni. Az alapértelmezett alapszintű csomag lehetővé teszi bizonyos mennyiségű kísérleti használata díjmentes.

Amikor egy előfizetéshez konfiguráltunk, jelentkezzen be az Application Insights jelenleg [ https://portal.azure.com ](https://portal.azure.com), és használhatja a bejelentkezni a Live ID.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az a [portal.azure.com](https://portal.azure.com), Application Insights-erőforrás hozzáadása:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/create-new-resource/01-new.png)

* **Az alkalmazástípus** érinti, amit lát az áttekintési panelen és az elérhető tulajdonságok [metrika explorer][metrics]. Ha nem látja az alkalmazás típusát, válassza az általános.
* **Előfizetés** a fizetési fiók az Azure-ban.
* **Erőforráscsoport** van, a könnyebb kezeléséhez tulajdonságai, például hozzáférés-vezérlés. Ha már létrehozott egyéb Azure-erőforrások, válassza ki az új erőforrás helyezze ugyanabba a csoportba.
* **Hely** van, ahol azt tartsa adatait.
* **Rögzítés az irányítópulton** az erőforrás gyors elérést csempe az Azure kezdőlapjának helyezi. Ajánlott.

Az alkalmazás létrehozásakor egy új panel nyílik meg. Ezen a panelen, ahol látható teljesítmény- és használati adatokat az alkalmazása. 

Való visszatéréshez, amikor legközelebb belép az Azure, keresse meg az alkalmazás gyors üzembe helyezési csempe a kezdő táblán (kezdőképernyő). Vagy kattintson a Tallózás gombra kattintva keresse meg azt.

## <a name="copy-the-instrumentation-key"></a>A kialakítási kulcs másolása
A kialakítási kulcs azonosítja az erőforrást, Ön által létrehozott. Szüksége lesz rá, az SDK-t biztosíthat.

![Kattintson az Essentials, a kialakítási kulcsot, a CTRL + C](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Az SDK telepítése az alkalmazásban
Az Application Insights SDK telepítése az alkalmazásban. Ebben a lépésben a az alkalmazás erősen függ. 

Konfigurálja a rendszerállapotkulcsot használatával [az SDK az alkalmazás telepítése][start].

Az SDK-t a globális modulok anélkül kód írása a telemetriai adatokat küldő tartalmaz. Felhasználói műveletek nyomon követése, vagy további részleteket a problémák diagnosztizálása [az API-val] [ api] saját telemetriát küldhet.

## <a name="monitor"></a>Telemetriai adatok megtekintése
Zárja be a gyors üzembe helyezési panel térjen vissza alkalmazása paneljéhez az Azure Portalon.

Kattintson a keresés csempét megtekintéséhez [diagnosztikai keresés][diagnostic], ahol az első események megjelennek. 

Ha több adatot vár, kattintson a **frissítése** néhány másodperc múlva.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása
Írhat egy [PowerShell-parancsprogram](../../azure-monitor/app/powershell.md) erőforrás automatikus létrehozásához.

## <a name="next-steps"></a>További lépések
* [Irányítópult létrehozása](../../azure-monitor/app/app-insights-dashboards.md)
* [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)
* [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../application-insights/app-insights-overview.md

