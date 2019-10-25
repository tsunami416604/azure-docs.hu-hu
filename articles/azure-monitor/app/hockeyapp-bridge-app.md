---
title: HockeyApp-adatelemzések feltárása az Azure Application Insightsban | Microsoft Docs
description: Az Azure-alkalmazás használatának és teljesítményének elemzése a Application Insights használatával.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819579"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>HockeyApp-adatApplication Insightsek feltárása

> [!NOTE]
> A HockeyApp már nem érhető el az új alkalmazásokhoz. A meglévő HockeyApp-telepítések továbbra is működni fognak. Visual Studio App Center mostantól a Microsoft ajánlott szolgáltatása az új Mobile apps figyelésére. [Ismerje meg, hogyan állíthatja be az alkalmazásait app Center és Application Insights használatával](../../azure-monitor/learn/mobile-center-quickstart.md).

A [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) az élő asztali és mobil alkalmazások figyelésére szolgál. A HockeyApp segítségével egyéni és nyomkövetési telemetria küldhet a használat figyeléséhez, és segít a diagnosztizálásban (az összeomlási adatok beszerzése mellett). Ezt a telemetria-streamet az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)hatékony [elemzési](../../azure-monitor/app/analytics.md) funkciójának használatával kérdezheti le. Emellett [exportálhatja az egyéni és a nyomkövetési telemetria](export-telemetry.md)is. Ezeknek a funkcióknak az engedélyezéséhez állítson be egy hidat, amely az egyéni HockeyApp továbbít Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>A HockeyApp-összekötő alkalmazás
A HockeyApp-összekötő alkalmazás a legfontosabb funkciója, amely lehetővé teszi a HockeyApp egyéni és nyomkövetési telemetria elérését Application Insights az elemzési és a folyamatos exportálási funkciók segítségével. A HockeyApp által gyűjtött egyéni és nyomkövetési események a HockeyApp-összekötő alkalmazás létrehozása után elérhetők lesznek ezekből a funkciókból. Lássuk, hogyan állíthatja be az egyik híd-alkalmazást.

A HockeyApp-ben nyissa meg a Fiókbeállítások, [API-tokenek elemet](https://rink.hockeyapp.net/manage/auth_tokens). Hozzon létre egy új jogkivonatot, vagy használja a meglévőt. A minimálisan szükséges jogosultságok csak olvashatók. Készítsen másolatot az API-tokenről.

![HockeyApp API-Token beszerzése](./media/hockeyapp-bridge-app/01.png)

Nyissa meg a Microsoft Azure Portal, és [hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ). Az alkalmazás típusának beállítása a "HockeyApp Bridge alkalmazáshoz":

![Új Application Insights erőforrás](./media/hockeyapp-bridge-app/02.png)

Nem kell megadnia a nevet – a rendszer automatikusan beállítja a HockeyApp nevét.

Megjelenik a HockeyApp híd mezői. 

![Adja meg a Bridge-mezőket](./media/hockeyapp-bridge-app/03.png)

Adja meg a korábban feljegyzett HockeyApp tokent. Ez a művelet feltölti a "HockeyApp alkalmazás" legördülő menüt az összes HockeyApp-alkalmazással. Válassza ki a használni kívánt elemet, és fejezze be a többi mezőt. 

Nyissa meg az új erőforrást. 

Vegye figyelembe, hogy az adatforgalom elindításához szükséges az adatmennyiség.

![Application Insights az erőforrás-várakozási idő](./media/hockeyapp-bridge-app/04.png)

Ennyi az egész! A HockeyApp-alapú alkalmazásban gyűjtött egyéni és nyomkövetési adatok mostantól elérhetők a Application Insights elemzési és folyamatos exportálási szolgáltatásaiban is.

Röviden vizsgáljuk meg, hogy az összes funkció elérhető-e mostantól.

## <a name="analytics"></a>Elemzés
Az elemzés egy hatékony eszköz az adatai alkalmi lekérdezéséhez, amely lehetővé teszi a telemetria diagnosztizálását és elemzését, valamint a kiváltó okok és minták gyors felderítését.

![Elemzés](./media/hockeyapp-bridge-app/05.png)

* [További információ az elemzésekről](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Folyamatos exportálás
A folyamatos exportálás lehetővé teszi az adatexportálást egy Azure Blob Storage-tárolóba. Ez akkor hasznos, ha a Application Insights által jelenleg kínált megőrzési időtartamnál hosszabb ideig kell megőriznie az adatait. Megtarthatja az adatait a blob Storage-ban, feldolgozhatja egy SQL Databaseba vagy az Ön által preferált adattárház-megoldásba.

[További információ a folyamatos exportálásról](export-telemetry.md)

## <a name="next-steps"></a>Következő lépések
* [Elemzések alkalmazása az adataira](../../azure-monitor/log-query/get-started-portal.md)

