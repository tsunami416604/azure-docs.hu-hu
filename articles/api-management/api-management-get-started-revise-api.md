---
title: "Változatok a módosítások nem törhető biztonságosan az Azure API Management használata |} Microsoft Docs"
description: "Ez az oktatóanyag áttekintésével megismerheti, hogyan változatok az API Management használata nem törhető módosításokat kövesse."
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
ms.openlocfilehash: 50d7ac17faebb34f1a1f9a3259aa0196950391d9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Nem törhető módosítások biztonságosan kövesse felülvizsgálata
Az API-készen áll, és elindítja a fejlesztők által használható, amikor általában a módosítások elvégzése az adott API-hoz, és egyszerre a nem zavarja a API hívóknak gondoskodunk kell. Akkor célszerű is, hogy a fejlesztők a végrehajtott változtatásokat ismernie. A Microsoft ehhez az Azure API Management használata **változatok**. További információkért lásd: [változatok & verzió](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) és [az Azure API Management API Versioning](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A rendszer új változatot hozzáadása
> * A verzió nem törhető módosításokat
> * Ellenőrizze a változat aktuális és a módosítási napló bejegyzés hozzáadása
> * Keresse meg a fejlesztői portálján módosítások megtekintéséhez és módosításához napló

![A fejlesztői portálján a módosítási napló](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-revision"></a>A rendszer új változatot hozzáadása

1. Válassza ki **API-k** lap.
2. Válassza ki **konferencia-API** az API-lista (vagy egyéb változatok hozzáadni kívánt API).
3. Kattintson a **változatok** fülre az oldal tetején a menüből.
4. Válassza ki **+ újabb változatot**

    > [!TIP]
    > Másik lehetőségként **hozzáadása változat** a helyi menü (**...** ) API.
    
    ![Képernyő tetején változatok menü](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Adjon meg egy leírást az új verziókra, mi azt használandó szöveget.
6. Válassza ki **létrehozása**
7. A rendszer új változatot megtörtént.

    > [!NOTE]
    > Marad az eredeti API **változat 1**. Ez az a verzió, a felhasználók továbbra is hívható, amíg a felhasználó annak másik változatát aktuális.

## <a name="make-non-breaking-changes-to-your-revision"></a>A verzió nem törhető módosításokat

1. Válassza ki **konferencia-API** API listájából.
2. Válassza ki a **tervezési** lapon, a képernyő felső részén.
3. Figyelje meg, hogy a **változat választó** (közvetlenül felett a tervezés lapról) jeleníti meg az aktuális verzióra **változat 2**.

    > [!TIP]
    > Használja a változat választó, amely a használni kívánt változatok közötti váltáshoz.

4. Válassza ki **+ Hozzáadás művelet**.
5. Állítsa be az új művelet **POST**, és a nevét, és a megjelenített neve a műveletet, mert **tesztelése**
6. **Mentés** az új művelet.
7. Most hajtottunk módosítva **változat 2**. Használja a **változat választó** váltson vissza a lap tetején **változat 1**.
8. Figyelje meg, hogy az új művelet nem jelenik meg **változat 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Ellenőrizze a változat aktuális és a módosítási napló bejegyzés hozzáadása
1. Válassza ki a **változatok** fülre az oldal tetején a menüből.

    ![A verzió menü változat képernyőn.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Nyissa meg a helyi menüt (**...** ) a **2. módosítása**.
2. Válassza ki **ellenőrizze aktuális**. Ellenőrizze **küldje el nyilvános Változásnapló ehhez az API**, ha a használni kívánt megjegyzések az ezt a módosítást.
3. Válassza ki **történő nyilvános Változásnapló ehhez az API-közzététellel**
4. Adjon meg egy leírást a módosítást, amelyet a fejlesztők számára, például **változatok tesztelése. A hozzáadott új "teszt" műveletet.**
5. **2. módosítása** jelenleg aktuális.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Keresse meg a fejlesztői portálján módosítások megtekintéséhez és módosításához napló
1. Válassza ki az Azure-portálon **API-k**
2. Válassza ki **fejlesztői portálján** a felső menüben.
3. Válassza ki **API-k**, majd válassza ki **konferencia-API**.
4. Figyelje meg, az új **tesztelése** művelet már elérhető.
5. Válassza ki **API-változások nyomon követése** alábbi API-név.
6. Figyelje meg, hogy a módosítási napló tétel megjelenik-e a listában.

    ![Fejlesztői portál](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A rendszer új változatot hozzáadása
> * A verzió nem törhető módosításokat
> * Ellenőrizze a változat aktuális és a módosítási napló bejegyzés hozzáadása
> * Keresse meg a fejlesztői portálján módosítások megtekintéséhez és módosításához napló

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Az API-t több verziójának közzététele](api-management-get-started-publish-versions.md)