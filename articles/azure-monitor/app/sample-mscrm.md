---
title: A Microsoft Dynamics CRM és az Azure Application Insights |} A Microsoft Docs
description: 'A Microsoft Dynamics CRM Online Application Insights használatával lekérheti a telemetriai adatokat. Forgatókönyv: a telepítés első adatokat, megjelenítési és exportálása.'
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 6119f1116d255f7cd2a2bfc20e86eeca9e5dfe82
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121159"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Forgatókönyv: Telemetria engedélyezése a Microsoft Dynamics CRM Online Application Insights használatával
Ez a cikk bemutatja, hogyan küldött telemetriai adatok lekérése [Microsoft Dynamics CRM Online](https://www.dynamics.com/) használatával [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Végigvezetjük a teljes folyamat az Application insights eszközt ad hozzá az alkalmazás adatokat, és az adatvizualizációról rögzítése.

> [!NOTE]
> [Keresse meg a Mintamegoldás](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Az Application Insights hozzáadása új vagy meglévő CRM Online-példányra
Az alkalmazás figyelése, az alkalmazás vegyen fel egy Application Insights SDK-t. Az SDK telemetriát küld a [Application Insights portálon](https://portal.azure.com), ahol használhatja a hatékony elemzési és diagnosztikai eszközöket, vagy az adatok exportálása tárolóba.

### <a name="create-an-application-insights-resource-in-azure"></a>Application Insights-erőforrás létrehozása az Azure-ban
1. Első [egy fiókot a Microsoft Azure-ban](https://azure.com/pricing). 
2. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és adjon hozzá egy új Application Insights-erőforrást. Ez az, ahol az adatok feldolgozása és jelenik meg.

    ![Kattintson a +, fejlesztői szolgáltatások, Application Insights.](./media/sample-mscrm/01.png)

    Az alkalmazás típusának válassza az ASP.NET lehetőséget.
3. Kövesse az utasításokat [beolvasása a JavaScript SDK-t az alkalmazás](../../azure-monitor/app/javascript.md#set-up-application-insights-for-your-web-page), másolja a JavaScript-kódrészletet, és győződjön meg arról, hogy cserélje le a kialakítási kulcsot a megfelelő értéket, az Application Insights-erőforrás.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Hozzon létre egy Javascriptes webes erőforrásban a Microsoft Dynamics CRM-ben
1. Nyissa meg a példány CRM Online-hoz és a bejelentkezési rendszergazdai jogosultságokkal.
2. Nyissa meg a Microsoft Dynamics CRM beállítások, a testreszabások, a rendszer testreszabása

    ![A Microsoft Dynamics CRM-beállítások](./media/sample-mscrm/00001.png)

    ![Beállítások > testreszabások](./media/sample-mscrm/00002.png)

1. Hozzon létre egy JavaScript-erőforrást.

    ![Új webes erőforrás párbeszédpanel](./media/sample-mscrm/07.png)

    Adjon meg egy nevet, válassza ki **parancsfájl (JScript)** és a szöveges szerkesztő megnyitásához.

    ![A szöveges szerkesztő megnyitásához](./media/sample-mscrm/00004.png)
2. Másolja a kódot az Application Insights JavaScript SDK, amelyben a kialakítási kulcsot, mielőtt konfigurálva. A másolás, közben ügyeljen arra, hogy parancsfájl címkék figyelmen kívül. Az alábbi képernyőképen az itt található:

    ![Állítsa be a kialakítási kulcsot](./media/sample-mscrm/000005.png)

    A kód a kialakítási kulcsot, amely azonosítja az Application insights-erőforrást tartalmaz.
3. Mentse és tegye közzé.

    ![Mentés és közzététel](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Eszköz űrlapok
1. A Microsoft CRM Online-ban a fiók képernyő megnyitása

    ![Fiók űrlap](./media/sample-mscrm/00007.png)
2. Nyissa meg az űrlap tulajdonságai

    ![Űrlap tulajdonságai](./media/sample-mscrm/00008.png)
3. Adja hozzá a JavaScript webes erőforráshoz, amelyet Ön hozott létre

    ![Hozzáadásra szolgáló menü](./media/sample-mscrm/13.png)

4. Mentse, és tegye közzé az űrlapok testreszabásai.

## <a name="metrics-captured"></a>Rögzített metrikák
Most már beállította az űrlap a telemetria-rögzítést. Használatos, amikor adatokat küld az Application Insights-erőforrást.

Az alábbiakban a mintákat, látni fogja az adatokat.

#### <a name="application-health"></a>Alkalmazás állapota
![Példa lapmegtekintés betöltési ideje](./media/sample-mscrm/15.png)

![Példa oldal nézetek diagram](./media/sample-mscrm/16.png)

Böngészőbeli kivételekkel:

![Böngésző kiszolgálókivételek diagramján](./media/sample-mscrm/17.png)

Kattintson a diagram részletesebb megjelenítéséhez:

![Kivételek listájáról](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Használat
![Felhasználók, munkamenetek és Lapmegtekintések](./media/sample-mscrm/19.png)

![Munkamenet-diagramok](./media/sample-mscrm/20.png)

![Böngésző verziója](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Böngészők
![Lapbetöltési idő áttekintését](./media/sample-mscrm/22.png)

![A böngésző verziója munkamenetek száma](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Földrajzi hely
![Országonkénti munkamenetek száma](./media/sample-mscrm/24.png)

![A munkamenetek és a felhasználók ország szerint](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Kérelem belül lap megtekintése
![Oldal összegzésének megtekintése](./media/sample-mscrm/26.png)

![Keressen a lapmegtekintési események](./media/sample-mscrm/27.png)

![Hasonló lapmegtekintések](./media/sample-mscrm/28.png)

![Lapmegtekintési tulajdonságok](./media/sample-mscrm/29.png)

![Lapok munkamenetenként](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Mintakód
[Keresse meg a mintakód](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Lehetőség van még mélyebb elemzésre, ha Ön [exportálja az adatokat a Microsoft Power bi-bA](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>A Microsoft Dynamics CRM megoldást
[Itt látható a minta megoldás a Microsoft Dynamics CRM-ben megvalósított](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Részletek
* [Mi az Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights weblapokhoz](../../azure-monitor/app/javascript.md)
* [További minták és útmutatók](../../azure-monitor/app/app-insights-overview.md)
