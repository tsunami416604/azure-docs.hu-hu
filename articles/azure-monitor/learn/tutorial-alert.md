---
title: Értesítések küldése az Azure Application Insightsból | Microsoft dokumentumok
description: Oktatóanyag az Azure Application Insights használatával az alkalmazás hibáira adott válaszként riasztások küldéséhez.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77656262"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Az Azure Application Insights segítségével figyelheti és riasztást ad az alkalmazások állapotáról

Az Azure Application Insights lehetővé teszi, hogy figyelje az alkalmazást, és küldjön riasztásokat, ha az nem érhető el, hibákat tapasztal, vagy teljesítményproblémákkal küzd.  Ez az oktatóanyag végigvezeti a tesztek létrehozásának folyamatán, amelyek folyamatosan ellenőrzik az alkalmazás rendelkezésre állását.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Rendelkezésre állási teszt létrehozása az alkalmazás válaszának folyamatos ellenőrzéséhez
> * E-mail küldése a rendszergazdáknak, ha probléma merül fel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

Hozzon létre egy [Application Insights-erőforrást.](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-availability-test"></a>Rendelkezésre állási teszt létrehozása

Az Application Insights rendelkezésre állási tesztjei lehetővé teszik, hogy automatikusan tesztelje az alkalmazást a világ különböző pontjairól.   Ebben az oktatóanyagban url-tesztet hajt végre annak érdekében, hogy a webalkalmazás elérhető legyen.  Létrehozhat egy teljes forgatókönyvet is a részletes művelet teszteléséhez. 

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  

2. Válassza az **Elérhetőség parancsot** a **Vizsgálat** menüben, majd kattintson a **Teszt létrehozása parancsra.**

    ![Rendelkezésre állási teszt hozzáadása](media/tutorial-alert/add-test-001.png)

3. Írja be a teszt nevét, és hagyja meg a többi alapértelmezést.  Ez a választás öt különböző földrajzi helyről 5 percenként elindítja az alkalmazás URL-címére vonatkozó kérelmeket.

4. **Válassza a Riasztások lehetőséget** a **Riasztások** legördülő menü megnyitásához, ahol megadhatja, hogyan reagáljon, ha a teszt sikertelen. Válassza **a Közel valós idejű** lehetőséget, és állítsa az **állapotot Engedélyezve értékre.**

    Írja be a riasztási feltételek teljesülése esetén elküldandó e-mail címet.  A riasztási feltételek teljesülése esetén meghívandó webhook címét is beírhatja.

    ![Teszt létrehozása](media/tutorial-alert/create-test-001.png)

5. Térjen vissza a tesztpanelre, jelölje ki a három pontot, és szerkesztheti a riasztást a közel valós idejű riasztás konfigurációjának megadásához.

    ![Riasztás szerkesztése](media/tutorial-alert/edit-alert-001.png)

6. A sikertelen helyek beállítása 3-nál nagyobb ra. Hozzon létre egy [műveletcsoportot](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) annak konfigurálásához, hogy ki kap értesítést a riasztási küszöbérték megszegéséről.

    ![Riasztási felhasználói felület mentése](media/tutorial-alert/save-alert-001.png)

7. Miután beállította a riasztást, kattintson a teszt nevére az egyes helyeken lévő részletek megtekintéséhez. A tesztek megtekinthetők mind a vonaldiagram, mind a scatter nyomtatási formátumban, hogy megjelenítsék egy adott időtartomány sikerét/hibáit.

    ![A vizsgálat részletei](media/tutorial-alert/test-details-001.png)

8. A pontdiagramon található pontra kattintva részletezheti bármely teszt részleteit. Ezzel elindítja a végpontok között a tranzakciók részletei nézetet. Az alábbi példa egy sikertelen kérelem részleteit mutatja be.

    ![A vizsgálat eredménye](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kell riasztást adni a problémákról, lépjen a következő oktatóanyagra, és ismerje meg, hogyan elemezheti, hogyan kommunikálnak a felhasználók az alkalmazással.

> [!div class="nextstepaction"]
> [A felhasználók megismerése](../../azure-monitor/learn/tutorial-users.md)
