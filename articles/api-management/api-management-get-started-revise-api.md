---
title: Változatok használata a nem kompatibilitástörő változás biztonságos végrehajtásához az Azure API Managementben | Microsoft Docs
description: Az oktatóanyag lépéseiből megtudhatja, hogyan hajthat végre nem kompatibilitástörő változásokat változatok használatával az API Managementben.
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 864c269da61bcea885249021a44fe0259ca83e90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Változatok használata a nem kompatibilitástörő változások biztonságos elvégzéséhez
Amikor az API készen áll, és a fejlesztők elkezdik a használatát, általában ügyelni kell arra, hogy amikor módosításokat végez az API-n, ne akadályozza az API hívóit. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. Az Azure API Managementben ez a **változatok** használatával valósítható meg. További információ: [Verziók és változatok](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) és [API-k verziókezelése az Azure API Managementtel](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új változat hozzáadása
> * Nem kompatibilitástörő változások végrehajtása a változaton
> * Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása
> * A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

![Módosítási napló a fejlesztői portálon](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="add-a-new-revision"></a>Új változat hozzáadása

1. Kattintson az **API-k** fülre.
2. Válassza a **Conference API** lehetőséget az API-k listájából (vagy bármely egyéb API-t, amelyhez változatokat kíván hozzáadni).
3. Kattintson a **Változatok** fülre az oldal felső részén található menüben.
4. Válassza a **+ Változat hozzáadása** lehetőséget.

    > [!TIP]
    > Az API helyi menüjében (**...**) található **Változat hozzáadása** lehetőséget is választhatja.
    
    ![A képernyő felső részén található Változatok menü](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Adja meg az új változat leírását, amely alapján emlékezni fog a változat funkciójára.
6. Kattintson a **Létrehozás** elemre.
7. Az új változat létrehozása megtörtént.

    > [!NOTE]
    > Az eredeti API az **1. változatban** marad. A felhasználók ezt a változatot hívják egészen addig, amíg Ön nem tesz aktuálissá egy másik változatot.

## <a name="make-non-breaking-changes-to-your-revision"></a>Nem kompatibilitástörő változások végrehajtása a változaton

1. Válassza a **Conference API** elemet az API-k listájából.
2. Kattintson a képernyő felső részén található **Tervezés** fülre.
3. Figyelje meg, hogy a (közvetlenül a Tervezés lap fölött található) **változatválasztóban** a **2. változat** jelenik meg aktuális változatként.

    > [!TIP]
    > A változatválasztóval váltson arra a változatra, amelyen dolgozni kíván.

4. Válassza a **+ Művelet hozzáadása** lehetőséget.
5. Az új művelet legyen **POST**, a művelet Neve és Megjelenítendő neve pedig **test**.
6. **Mentse** az új műveletet.
7. Most módosítottuk a **2. változatot**. Az oldal felső részén található **Változatválasztóval** váltson vissza az **1. változatra**.
8. Figyelje meg, hogy az új művelet nem jelenik meg az **1. változatban**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása
1. Kattintson a **Változatok** fülre az oldal felső részén található menüben.

    ![A változatok képernyőn található változatok menü.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Nyissa meg a **2. változat** helyi menüjét (**...**).
2. Kattintson a **Legyen ez az aktuális** elemre. Jelölje be a **Közzététel az API nyilvános módosításnaplójában** beállítást, ha megjegyzéseket kíván közzétenni erről a módosításról.
3. Jelölje be a **Közzététel az API nyilvános módosításnaplójában** jelölőnégyzetet
4. Adja meg a módosítás leírását a fejlesztőknek, például **Változatok tesztelése. Új „test” művelet hozzáadva.**
5. Most már a **2. változat** az aktuális.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez
1. Az Azure Portalon válassza az **API-k** lehetőséget
2. A felső menüben kattintson a **Fejlesztői portál** elemre.
3. Kattintson az **API-k** elemre, majd válassza a **Conference API** lehetőséget.
4. Figyelje meg, hogy az új **test** művelet már elérhető.
5. Kattintson az **API-változások nyomon követése** elemre az API neve alatt.
6. Figyelje meg, hogy a módosításinapló-bejegyzés megjelenik a listában.

    ![Fejlesztői portál](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új változat hozzáadása
> * Nem kompatibilitástörő változások végrehajtása a változaton
> * Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása
> * A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az API több verziójának közzététele](api-management-get-started-publish-versions.md)