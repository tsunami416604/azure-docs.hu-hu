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
ms.openlocfilehash: 4ea64b16a9a581683d3b7a44b4b331af435db22c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762721"
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

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Hajtsa végre a következő rövid útmutatót: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
+ Ezenkívül hajtsa végre a következő oktatóanyaggal: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>A fejlesztői portál testreszabása

1. Válassza az **Áttekintés** lehetőséget.
2. Kattintson a **Fejlesztői portál** gombra az **Áttekintés** ablak tetején. Másik megoldásként a **Fejlesztői portál URL-címe** hivatkozásra is kattinthat.
3. A képernyő bal felső részén egy két ecsetet ábrázoló ikon látható. Vigye a mutatót az ikon fölé a portál testreszabási menüjének megnyitásához.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Válassza a menü **Stílusok** elemét a stílus testreszabási paneljének megnyitásához.

    A **Stílusok** panellel testre szabható összes elem megjelenik az oldalon.
5. Írja be a „headings-color” (fejléc színe) kifejezést a **Változó értékeinek módosítása a fejlesztői portál megjelenésének testreszabásához:** mezőbe.

    A  **\@headings-color** elemet oldalon jelenik meg. Ez a változó szabályozza a szöveg színét.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kattintson a mező a  **\@headings-color** változó. 
    
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