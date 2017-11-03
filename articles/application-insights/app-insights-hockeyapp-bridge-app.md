---
title: "Azure Application Insights Hockeyappra adatainak felfedezése |} Microsoft Docs"
description: "Elemezze a használati és teljesítményadatokat az Azure-alkalmazás az Application insights szolgáltatással."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: a925241d10b068e377fa9a11fc854db34c808343
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Az Application Insightsban Hockeyappra adatok

> [!NOTE]
> Visual Studio Mobile Center mostantól a Microsoft ajánlott szolgáltatás új mobilalkalmazások figyelésre. [Ismerje meg, hogyan állíthat be az alkalmazások mobilalkalmazás-központ és az Application Insights](app-insights-mobile-center-quickstart.md).
> 
> 

[Hockeyappra](https://azure.microsoft.com/services/hockeyapp/) szolgáltatás történő élő az asztali és mobil alkalmazások figyelését. A Hockeyappra egyéni küldhet és nyomkövetési telemetria megfigyeléséhez és annak elősegítéséhez diagnosztikai (mellett első összeomlási adatait). Ez az adatfolyam telemetriai adatot lekérdezhetők, használja a hatékony [Analytics](app-insights-analytics.md) szolgáltatása [Azure Application Insights](app-insights-overview.md). Emellett képes [exportálja az egyéni, és nyomkövetési telemetria](app-insights-export-telemetry.md). Ahhoz, hogy ezeket a szolgáltatásokat, akkor egy híd beállítása, amely a Hockeyappra egyéni adatok az Application Insights részére továbbítja.

## <a name="the-hockeyapp-bridge-app"></a>A Hockeyappra híd alkalmazás
A Hockeyappra híd az alkalmazás nem az alapvető funkciója, amely lehetővé teszi az elemzések és a folyamatos exportálás funkciók révén a Hockeyappra egyéni és az Application Insights – nyomkövetési telemetria elérésére. Egyéni és nyomkövetési eseményeket a Hockeyappra híd alkalmazás létrehozása után a Hockeyappra által gyűjtött fog érhető el ezeket a szolgáltatásokat. Nézzük meg, ezek híd alkalmazások beállításával.

A Hockeyappra, nyissa meg a fiók beállításait, [API jogkivonatok](https://rink.hockeyapp.net/manage/auth_tokens). Hozzon létre egy új jogkivonatot, vagy egy meglévő használja fel. A minimális jogosultságokkal szükséges, amelyek "csak olvasható". Az API-t egy példányát jogkivonat érvénybe.

![A Hockeyappra API-token beszerzése](./media/app-insights-hockeyapp-bridge-app/01.png)

A Microsoft Azure portál megnyitásához és [Application Insights-erőforrás létrehozása](app-insights-create-new-resource.md). Alkalmazás típusa "Hockeyappra híd alkalmazás" beállítani:

![Új Application Insights-erőforrás](./media/app-insights-hockeyapp-bridge-app/02.png)

Nem kell állítson be egy nevet, mert ez a program automatikusan állítja a Hockeyappra neve.

A Hockeyappra híd mezők jelennek meg. 

![Adja meg a híd mezők](./media/app-insights-hockeyapp-bridge-app/03.png)

Adja meg a Hockeyappra jogkivonatot, amelyet korábban feljegyzett. Ez a művelet feltölti a "Hockeyappra alkalmazás" legördülő menüjében a Hockeyappra alkalmazásokkal. Jelölje ki a használni kívánt, és fejezze be a telepítővarázslót a mezők. 

Nyissa meg az új erőforrást. 

Vegye figyelembe, hogy az adatok eltarthat áramló elindításához.

![Várakozás az adatok Application Insights-erőforrás](./media/app-insights-hockeyapp-bridge-app/04.png)

Készen is van. Ettől kezdve a Hockeyappra tagolva alkalmazásban gyűjtött egyéni és nyomkövetési adatok jelenleg is az Application Insights elemzés és a folyamatos exportálás funkcióit érhető el.

Most rövid időre tekintse át az összes ezek a szolgáltatások most már elérhető.

## <a name="analytics"></a>Elemzés
Analytics egy olyan erőteljes eszköz alkalmi lekérdezése az adatok, diagnosztizálása és a telemetriai adatok elemzése és gyorsan Fedezze fel az alapvető okok és a minták.

![Elemzés](./media/app-insights-hockeyapp-bridge-app/05.png)

* [További információ az elemzés](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>Folyamatos exportálás
A folyamatos exportálás lehetővé teszi az adatok exportálása az Azure Blob Storage tárolóban. Nagyon hasznos, ha az adatok hosszabb a megőrzési időtartam jelenleg az Application Insights által kínált kell azt. Az adatok megőrzéséhez a blob Storage tárolóban, SQL-adatbázis, vagy az elsődleges adatok tekinthetünk feldolgozni azt.

[További információ a folyamatos exportálás](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Következő lépések
* [Az adatok Analytics alkalmazása](app-insights-analytics-tour.md)

