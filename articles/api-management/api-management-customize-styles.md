---
title: Az oldalstílus testreszabása az Azure API Management fejlesztői portálon | Microsoft Docs
description: Ennek a rövid útmutatónak a lépéseit követve szabhatja testre az elemek stílusát az Azure API Management fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 00e6f2ed866afd13343df37de625e2913f613dfa
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092403"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>A fejlesztői portál oldalstílusának testreszabása

A fejlesztői portál testreszabásának három leggyakoribb módja az Azure API Managementben:
 
* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése](api-management-modify-content-layout.md)
* A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése (részletesen ebben az útmutatóban)
* [A portál által létrehozott oldalakhoz használt sablonok módosítása](api-management-developer-portal-templates.md) (például API-dokumentáció, termékek vagy felhasználói hitelesítés)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A **fejlesztői** portál oldalain lévő elemek stílusának testreszabása
> * A módosítások megtekintése

![stílus testreszabása](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>A fejlesztői portál testreszabása

1. Válassza az **Áttekintés** lehetőséget.
2. Kattintson a **Fejlesztői portál** gombra az **Áttekintés** ablak tetején. Másik megoldásként a **Fejlesztői portál URL-címe** hivatkozásra is kattinthat.
3. A képernyő bal felső részén egy két ecsetet ábrázoló ikon látható. Vigye a mutatót az ikon fölé a portál testreszabási menüjének megnyitásához.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Válassza a menü **Stílusok** elemét a stílus testreszabási paneljének megnyitásához.

    A **Stílusok** panellel testre szabható összes elem megjelenik az oldalon.
5. Írja be a „headings-color” (fejléc színe) kifejezést a **Változó értékeinek módosítása a fejlesztői portál megjelenésének testreszabásához:** mezőbe.

    A **@headings-color** elem megjelenik az oldalon. Ez a változó szabályozza a szöveg színét.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kattintson a **@headings-color** változó mezőjére. 
    
    Megjelenik a színválasztó legördülő menü.
7. A színválasztó legördülő menüben válassza ki az új színt.

    > [!TIP]
    > Minden módosítás előnézete megtekinthető valós időben. Egy folyamatjelző jelenik meg a testreszabási panel tetején. Néhány másodperc elteltével a fejléc szövegének színe átvált az újonnan kijelölt színre.

8. Válassza a **Közzététel** lehetőséget a testreszabási panel menüjének bal alsó részén.
9. Válassza a **Testreszabások közzététele** lehetőséget a módosítások nyilvánosan elérhetővé tételéhez.

## <a name="view-your-change"></a>A módosítások megtekintése

1. Lépjen a fejlesztői portálra.
2. Itt megtekintheti a végrehajtott módosításokat.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A **fejlesztői** portál oldalain lévő elemek stílusának testreszabása
> * A módosítások megtekintése

További érdekes tudnivalók [az Azure API Management fejlesztői portál sablonok használatával történő testreszabásáról](api-management-developer-portal-templates.md).