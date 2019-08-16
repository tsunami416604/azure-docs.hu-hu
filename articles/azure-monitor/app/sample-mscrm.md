---
title: Microsoft Dynamics CRM és Azure Application Insights | Microsoft Docs
description: Telemetria beszerzése a Microsoft Dynamics CRM Online-ból a Application Insights használatával. A telepítő bemutatója, az adatolvasások, a vizualizációk és az exportálás.
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
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534293"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Forgatókönyv: A telemetria engedélyezése a Microsoft Dynamics CRM Online-hoz a Application Insights használatával
Ez a cikk bemutatja, hogyan kérhet le telemetria-adatait a [Microsoft Dynamics CRM Online](https://www.dynamics.com/) -ból az [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)használatával. Részletesen ismertetjük az Application Insights szkriptek alkalmazáshoz, az adatrögzítéshez és az adatvizualizációhoz való hozzáadásának teljes folyamatát.

> [!NOTE]
> [Böngésszen a minta megoldásban](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Application Insights hozzáadása új vagy meglévő CRM Online-példányhoz
Az alkalmazás figyeléséhez vegyen fel egy Application Insights SDK-t az alkalmazáshoz. Az SDK telemetria küld a [Application Insights portálra](https://portal.azure.com), ahol hatékony elemzési és diagnosztikai eszközöket használhat, vagy exportálhatja az adatokat a tárolóba.

### <a name="create-an-application-insights-resource-in-azure"></a>Application Insights-erőforrás létrehozása az Azure-ban
1. Fiók beszerzése [Microsoft Azure](https://azure.com/pricing). 
2. Jelentkezzen be [](https://portal.azure.com) a Azure Portalba, és adjon hozzá egy új Application Insights-erőforrást. Itt lesznek feldolgozva és megjelenítve az adatai.

    ![Kattintson a +, fejlesztői szolgáltatások, Application Insights lehetőségre.](./media/sample-mscrm/01.png)

    Az alkalmazás típusának válassza az ASP.NET lehetőséget.
3. Az alkalmazáshoz tartozó [JavaScript SDK-szkript](../../azure-monitor/app/javascript.md)beszerzéséhez kövesse az utasításokat, másolja a JavaScript-kódrészletet, és ügyeljen rá, hogy a rendszerállapot-kulcsot a Application Insights erőforrásának megfelelő értékkel cserélje le.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>JavaScript webes erőforrás létrehozása a Microsoft Dynamics CRM-ben
1. Nyissa meg a CRM Online-példányát, és jelentkezzen be rendszergazdai jogosultságokkal.
2. Nyissa meg a Microsoft Dynamics CRM beállításait, a testreszabásokat, és szabja testre a rendszert

    ![Microsoft Dynamics CRM-beállítások](./media/sample-mscrm/00001.png)

    ![Beállítások > testreszabások](./media/sample-mscrm/00002.png)

1. Hozzon létre egy JavaScript-erőforrást.

    ![Új webes erőforrás párbeszédpanel](./media/sample-mscrm/07.png)

    Adja meg a nevét, válassza a **script (JScript)** lehetőséget, majd nyissa meg a szövegszerkesztőt.

    ![A szövegszerkesztő megnyitása](./media/sample-mscrm/00004.png)
2. Másolja a kódot a Application Insights JavaScript SDK-ból, amelyben korábban konfigurálta a kialakítási kulcsot. A másolás során ügyeljen arra, hogy figyelmen kívül hagyja a parancsfájlok címkéit. Tekintse meg az alábbi képernyőképet:

    ![A kialakítási kulcs beállítása](./media/sample-mscrm/000005.png)

    A kód tartalmazza a kialakítási kulcsot, amely azonosítja az Application Insight-erőforrást.
3. Mentés és közzététel.

    ![Mentés és közzététel](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Eszközök űrlapjai
1. A Microsoft CRM Online-ban nyissa meg a fiók űrlapot.

    ![Fiók űrlapja](./media/sample-mscrm/00007.png)
2. Az űrlap tulajdonságainak megnyitása

    ![Űrlap tulajdonságai](./media/sample-mscrm/00008.png)
3. A létrehozott JavaScript webes erőforrás hozzáadása

    ![Menü hozzáadása](./media/sample-mscrm/13.png)

4. Az űrlap testreszabásainak mentése és közzététele.

## <a name="metrics-captured"></a>Rögzített metrikák
Most beállította a telemetria rögzítését az űrlaphoz. Minden alkalommal, amikor használatban van, a rendszer elküldi az adatait a Application Insights-erőforrásnak.

Itt láthatók a megjelenő információk mintái.

#### <a name="application-health"></a>Alkalmazás állapota
![Példa oldal betöltési ideje](./media/sample-mscrm/15.png)

![Példa az oldal nézetek diagramra](./media/sample-mscrm/16.png)

Böngészőbeli kivételek:

![Böngészőalapú kivételek diagramja](./media/sample-mscrm/17.png)

További részletekért kattintson a diagramra:

![Kivételek listája](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Használat
![Felhasználók, munkamenetek és lapok nézetei](./media/sample-mscrm/19.png)

![Munkamenet-diagramok](./media/sample-mscrm/20.png)

![Böngésző verziója](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Böngészők
![Az oldal betöltési idejének részletezése](./media/sample-mscrm/22.png)

![Munkamenetek száma a böngésző verziószáma szerint](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Földrajzi hely
![Munkamenetek száma országonként](./media/sample-mscrm/24.png)

![Munkamenetek és felhasználók országonként](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Oldalon belüli nézet kérése
![Oldal nézet összegzése](./media/sample-mscrm/26.png)

![Keresés az oldal nézet eseményein](./media/sample-mscrm/27.png)

![Hasonló lapmegtekintések](./media/sample-mscrm/28.png)

![Lapmegtekintési tulajdonságok](./media/sample-mscrm/29.png)

![Lapok száma munkamenetenként](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Mintakód
[Böngésszen a mintakódban](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
[A Microsoft Power BIba való exportáláskor](../../azure-monitor/app/export-power-bi.md )még mélyebb elemzést végezhet.

## <a name="sample-microsoft-dynamics-crm-solution"></a>Példa Microsoft Dynamics CRM-megoldásra
[Itt látható a Microsoft Dynamics CRM-ben megvalósított minta megoldás](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
* [Mi az Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Weblapok Application Insights](../../azure-monitor/app/javascript.md)
* [További minták és forgatókönyvek](../../azure-monitor/app/app-insights-overview.md)
