---
title: Értesítések küldése az Azure Application Insightsból |} A Microsoft Docs
description: Az oktatóanyag válaszként az Azure Application Insights használatával alkalmazásban szereplő hibák a riasztások küldéséhez.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 05285a177827cd0dd1e0e39e779a395ccfdfc0cd
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578764"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>A figyelő és az Azure Application Insights alkalmazásállapot-riasztás

Az Azure Application Insights lehetővé teszi az alkalmazás figyelése és küld, riasztást, ha vagy nem érhető el, tapasztalt hibák vagy teljesítményproblémák szenvedő.  Ez az oktatóanyag végigvezeti a folyamatos, az alkalmazás rendelkezésre állásának ellenőrzéséhez tesztek létrehozásának folyamatán.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A válasz az alkalmazás folyamatos ellenőrzéséhez rendelkezésre állási teszt létrehozása
> * A rendszergazdák üzenetet küldeni, ha probléma merül fel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

Hozzon létre egy [Application Insights-erőforrást](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-availability-test"></a>Rendelkezésre állási teszt létrehozása

Az Application Insights rendelkezésre állási tesztek automatikusan tesztelheti a világszerte különböző helyekről alkalmazását teszi lehetővé.   Ebben az oktatóanyagban végre fogja hajtani, győződjön meg arról, hogy elérhető legyen-e a webalkalmazás url-teszt.  Emellett létrehozhat egy teljes forgatókönyv részletes működésének teszteléséhez. 

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  

2. Válassza ki **rendelkezésre állási** alatt a **vizsgálat** menüben, majd kattintson **létrehozás teszt**.

    ![A rendelkezésre állási teszt hozzáadása](media/tutorial-alert/add-test-001.png)

3. Írja be a teszthez nevét, és hagyja a többi alapértelmezett értéket.  Ezzel a választással elindítja a kérelmeket az alkalmazás URL-5 percenként öt különböző földrajzi helyeken lévő.

4. Válassza ki **riasztások** megnyitásához a **riasztások** legördülő lista részletei hogyan reagáljon, ha a teszt meghiúsul határozhatja. Válasszon **közel valós idejű** , és állítsa az állapot **engedélyezve.**

    Írja be az e-mail-címet, küldhet, amikor a riasztási feltételek teljesülésére.  Szükség esetén beírhatja a cím, egy webhook meghívására, ha a riasztási feltételek teljesülésére.

    ![Teszt létrehozása](media/tutorial-alert/create-test-001.png)

5. A teszt panelre való visszatéréshez, kattintson a három pontra, és adja meg a konfigurációs, a közel valós idejű riasztás a riasztás szerkesztése.

    ![Riasztás szerkesztése](media/tutorial-alert/edit-alert-001.png)

6. Sikertelen műveletek helyeinek állítsa nagyobb vagy egyenlő a 3. Hozzon létre egy [műveletcsoport](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) ki kap értesítést kapjon, ha a riasztási küszöbértékének konfigurálása.

    ![Mentse a riasztási felhasználói felület](media/tutorial-alert/save-alert-001.png)

7. Miután konfigurálta a riasztást, kattintson a teszt neve, az egyes helyek a részletek megtekintéséhez. Formátumban is sor graph- és pontdiagramokhoz diagram megjelenítése egy adott időtartományt a sikeres/sikertelen tesztek tekinthet meg.

    ![Teszt részletei](media/tutorial-alert/test-details-001.png)

8. Részletezhet minden teszt adatait, a pontdiagram a pontjára kattintva. Ekkor megnyílik a végpontok közötti tranzakció részletei nézet. Az alábbi példa egy sikertelen kérelmek részleteit jeleníti meg.

    ![Teszteredmény](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan lehet a problémák riasztást, folytassa a következő oktatóanyaggal, elemezhetők a felhasználók hogyan használja az alkalmazását.

> [!div class="nextstepaction"]
> [A felhasználók megismerése](../../azure-monitor/learn/tutorial-users.md)