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
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073303"
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Az Azure Application Insights a Microsoft Azure-ban jeleníti meg az alkalmazással kapcsolatos adatok *erőforrás*. Ezért részét képezi egy új erőforrás létrehozását [Application Insights beállítása egy új alkalmazás figyelésére][start]. Miután létrehozta az új erőforrást, a kialakítási kulcs lekérése, és használja, amely az Application Insights SDK konfigurálása. A kialakítási kulcsot a telemetria az erőforrás hivatkozik.

## <a name="sign-in-to-microsoft-azure"></a>Jelentkezzen be a Microsoft Azure

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be a [az Azure portal](https://portal.azure.com), és hozzon létre egy Application Insights-erőforrást:

![Kattintson a bal felső sarokban található "+" jelre. Válassza ki a fejlesztői eszközökkel kiegészítve az Application Insights](./media/create-new-resource/new-app-insights.png)

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | myResourceGroup      | A gazdagép App Insights-adatok új vagy létező erőforráscsoport neve. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz közeli helyet, vagy közel, ahol az alkalmazás üzemel. |

Adja meg a megfelelő értékeket a kötelező mezőkbe, majd a **felülvizsgálat + létrehozása**.

![Értékek megadása kötelező, és válassza ki a "felülvizsgálat + létrehozás" gombra.](./media/create-new-resource/review-create.png)

Ha az alkalmazás létrehozása után egy új panel nyílik meg. Ezen a panelen, ahol látható teljesítmény- és használati adatok a figyelt alkalmazásról. 

## <a name="copy-the-instrumentation-key"></a>A kialakítási kulcs másolása

A kialakítási kulcs azonosítja az erőforrást, amelyhez hozzá szeretne rendelni a telemetriai adatok. Másolás a kialakítási kulcs hozzáadása az alkalmazás kódjában kell.

![Kattintson a gombra, és a kialakítási kulcs másolása](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Az SDK telepítése az alkalmazásban

Az Application Insights SDK telepítése az alkalmazásban. Ebben a lépésben a az alkalmazás erősen függ.

Konfigurálja a rendszerállapotkulcsot használatával [az SDK az alkalmazás telepítése][start].

Az SDK-t a globális modulok anélkül további kód írása a telemetriai adatokat küldő tartalmaz. Felhasználói műveletek nyomon követése, vagy további részleteket a problémák diagnosztizálása [az API-val] [ api] saját telemetriát küldhet.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása
Írhat egy [PowerShell-parancsprogram](../../azure-monitor/app/powershell.md) erőforrás automatikus létrehozásához.

## <a name="next-steps"></a>További lépések
* [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)
* [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md)
* [Analytics-lekérdezések](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md