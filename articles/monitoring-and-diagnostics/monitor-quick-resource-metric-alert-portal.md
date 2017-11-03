---
title: "Értesítést kapnak, ha egy metrika értékét feltétel |} Microsoft Docs"
description: "A gyors üzembe helyezési útmutató segítenek a metrika a logikai alkalmazás létrehozása"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 08d63d47a99bdf9480299a74634bc0e9a09e691e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="receive-a-notification-when-a-metric-value-meets-a-condition"></a>Értesítést kapnak, ha egy metrika értékét feltétel

Az Azure figyelő elérhetővé metrikák számos Azure-erőforrások. A metrikák átadja a teljesítmény- és állapot erőforrások. A sok esetben metrika értékek mutathat valamilyen erőforrás hibás alatt. Figyelje a rendellenes viselkedés, és értesítést küld akkor, ha metrika riasztásokat hozhat létre. A gyors üzembe helyezés lépéseit logikai alkalmazás létrehozása, feladat létrehozása, és a logikai alkalmazást a használatmérés megjelenítése. Majd végighalad létre riasztást, és egy értesítés a metrika a logikai alkalmazást az erőforráshoz.

A metrikák és metrika riasztások további információkért lásd: [Azure metrikák – áttekintés](./monitoring-overview-metrics.md) és [Azure figyelő riasztások áttekintése](./monitoring-overview-alerts.md). 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Keresse meg és jelölje ki **logikai alkalmazás**. Hozzon létre egy új erőforráscsoportot nevű **myResourceGroup** alapértelmezett helyet használja. Kattintson a **Létrehozás** gombra.

3. Adja meg a logic app adatokat, és ellenőrizze a **rögzítés az irányítópulton** lehetőséget. Amikor végzett, kattintson a **Létrehozás** gombra.

    ![Adja meg a Logic Apps alkalmazást alapvető adatait a portálon](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-portal.png)  


4. A logikai alkalmazást kell rögzítve az irányítópulton való rögzítéséhez. Keresse meg azt a logikai alkalmazást.

5. A logikai alkalmazás panelen válassza ki a **Logic App Designer**

     ![Ismétlődés eseményindító létrehozása a logic app tervezőben, a portál panel](./media/monitoring-quick-resource-metric-alert-portal/logic-app-designer.png)  

6. Készen áll, értékeket az alábbi ábrán látható módon.

    ![A logic app eseményindító konfigurálása a portál panel](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-triggers.png). 

7. A tervezőben, válassza ki a **ismétlődési** eseményindító.

8. Megadni a 20, és annak biztosítása érdekében a Logic Apps alkalmazást második gyakorisággal akkor váltódik ki, minden 20 másodperc.

9. Kattintson a **új lépés** gombra, és válassza ki **művelet hozzáadása**.

10. Válassza ki a **HTTP** lehetőséget, majd válassza ki **HTTP-HTTP**.

11. Állítsa be a **metódus** , POST és a **Uri** az Ön által választott webes címre.

12. Kattintson a **Save** (Mentés) gombra.

## <a name="view-metrics-for-your-logic-app"></a>A Logic Apps alkalmazást metrikáját megtekintése

1. Kattintson a **figyelő** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza ki a **metrikák** lapján töltse ki a **előfizetés**, **erőforráscsoport**, **erőforrástípus** és **erőforrás** adatait a Logic Apps alkalmazást.

3. A metrikák listából válassza ki a **futtatása megkezdődött**.

4. Módosítsa a **időtartománynak** a diagram megjelenítése az adatok az elmúlt egy óra.

5. Ekkor megjelenik egy diagram keresztül az elmúlt egy órában elindult a Logic Apps alkalmazást futtat száma ábrázolásához.

    ![A metrika a logikai alkalmazás erőforrás-diagram megrajzolásához](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metric-chart.png)

## <a name="create-a-metric-alert-for-your-logic-app"></a>Riasztás létrehozása, a metrika a logikai alkalmazásnak

1.  A metrikák panel jobb felső részén kattintson a **metrika riasztás hozzáadása** gombra.

2. A metrika riasztás "myLogicAppAlert" nevet, és adjon meg egy rövid leírást a riasztás.

3. Állítsa be a **feltétel** "Nagyobb, mint", állítsa be a metrika riasztás a **küszöbérték** , "10", és állítsa be a **időszak** , mivel a(z) "keresztül az elmúlt 5 percben".

4. Végezetül a **további rendszergazda email(s)** meg e-mail címét. Ez a riasztás biztosítja, egy e-mailt kap, abban az esetben, ha a Logic Apps alkalmazást több mint 10 sikertelen Futtatás rendelkezik 5 percen belül.

    ![A logic app riasztás konfigurálása a portál panel](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metrics-alert-portal.png)

## <a name="receive-metric-alert-notifications-for-your-logic-app"></a>A logikai alkalmazásnak metrika riasztási értesítéseket
1. Kis idő elteltével a "Microsoft Azure riasztásokat" arról tájékoztatják, a riasztás "aktiválása" az e-mailt kell kapnia.

2. Lépjen vissza a Logic Apps alkalmazást, és az ismétlődési eseményindító 1 időközt és óra gyakoriságának módosítása.

3. Néhány percen belül kap egy e-mailt a "Microsoft Azure riasztások" tájékoztat a riasztás "megoldódott".

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a gyűjteményben más gyors üzembe helyezések a gyors üzembe helyezés épül. Ha azt tervezi, a későbbi gyors üzembe helyezések vagy az oktatóanyagok munka folytatásához, üríti a gyors üzembe helyezés létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure-portálon a bal oldali menüben kattintson a **figyelő**.

2. Válassza ki a **riasztások** lapon, a riasztás létrehozott gyors üzembe helyezési útmutatóban található, és kattintson rá.

3. Kattintson a metrika riasztási panel **törlése**.

4. Az Azure-portálon a bal oldali menüben keresse meg **logikai alkalmazás** majd **a Logic apps**.

5. A panelen, kattintson a logikai alkalmazást a gyors üzembe helyezés a szövegmezőben létrehozott, majd **törlése**.

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés hogyan erőforrások metrika riasztás létrehozása, hogy megismerte. Metrika riasztásról további információkért kattintson a riasztások – áttekintés.

> [!div class="nextstepaction"]
> [Az Azure előfizetés művelet riasztások](./monitor-quick-audit-notify-action-in-subscription.md )
