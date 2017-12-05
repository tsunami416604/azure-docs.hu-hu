---
title: "A lap testreszabásához az Azure API Management developer portálon |} Microsoft Docs"
description: "A gyors üzembe helyezés az Azure API Management fejlesztői portálján elemek stílusbeállításokat testreszabásához kövesse."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f427663ba1c437785c8c521925d9f733c45cb40d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>A fejlesztői portál lapjai stílusát testreszabása

A fejlesztői portálhoz, az Azure API Management testreszabása három leggyakoribb módja van:
 
* [Szerkessze a tartalmat statikus és elrendezés elemei](api-management-modify-content-layout.md)
* Frissítse a stílusok használt elemei között a fejlesztői portálján (Ez az útmutató alapján)
* [Módosíthatja a sablonokat a portál által létrehozott lapok](api-management-developer-portal-templates.md) (például API docs, termékek, felhasználói hitelesítés)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Elemek oldalának testreszabásához a **fejlesztői** portál
> * A módosítás megtekintése

![stílus testreszabása](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="customize-the-developer-portal"></a>A fejlesztői portál testreszabása

1. Válassza ki **áttekintése**.
2. Kattintson a **fejlesztői portálján** gombra kattint, a a a **áttekintése** ablak. Másik lehetőségként kattinthat a **fejlesztői portálján URL-cím** hivatkozásra.
3. A képernyő felső bal oldalán megjelenik egy ikon, két ecsetként áll. Ez az ikon a portál testreszabásának menü megnyitásához mutasson.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Válassza ki **stílusok** lehetőséget a menüből a stílusbeállításokat testreszabási ablaktábla megnyitása.

    Testre szabható használó összes elem **stílusok** az oldalon
5. A "fejlécére kattintva rendezhető színű" adja meg a **fejlesztői portál megjelenését testreszabásához változók értékeinek módosítása:** mező.

    A  **@headings-color**  elem lapján jelenik meg. Ez a változó határozza meg a szöveg színét.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kattintson a mező a  **@headings-color**  változó. 
    
    Szín kiválasztása legördülő lista megnyitása.
7. A szín dátumválasztók legördülő listán válassza ki egy új színt.

    > [!TIP]
    > Valós idejű preview összes módosítás érhető el. A testreszabási ablaktábla tetején egy folyamatjelző jelenik meg. Néhány másodpercen belül a fejléc szövege módosításait a szín az újonnan kijelölt.

8. Válassza ki **közzététel** a testreszabási menüjének a bal alsó.
9. Válassza ki **testreszabások közzététele** a kívánt módosítások nyilvánosan elérhető.

## <a name="view-your-change"></a>A módosítás megtekintése

1. Keresse meg a fejlesztői portálján.
2. A módosítást végzett tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Elemek oldalának testreszabásához a **fejlesztői** portál
> * A módosítás megtekintése

> [!div class="nextstepaction"]
> [Az Azure API Management developer portálon sablonok testreszabása](api-management-developer-portal-templates.md)