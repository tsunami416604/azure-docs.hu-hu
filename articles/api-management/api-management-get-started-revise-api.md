---
title: "Frissítése az Azure API Management az API-verziók használatával |} Microsoft Docs"
description: "Ez az oktatóanyag áttekintésével megismerheti, hogyan változatok az API Management használata nem törhető módosításokat kövesse."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>Biztonságos használatával változatok nem törhető módosításokat
Ez az oktatóanyag ismerteti, hogyan módosíthatja az API-biztonságosan, és a módosítás kommunikálni a fejlesztők.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége API Management szolgáltatás létrehozása, és egy meglévő API-t (helyett konferencia-API) módosíthatja.

## <a name="about-revisions"></a>Információk felülvizsgálata
Az API-készen áll, és elindítja a fejlesztők által használható, amikor általában az adott API - úgy, hogy ne zavarja a API hívóknak módosítása gondoskodunk kell. Akkor célszerű is, hogy a fejlesztők a végrehajtott változtatásokat ismernie. A Microsoft ehhez az Azure API Management használata **változatok**.

## <a name="walkthrough"></a>Útmutatás
A forgatókönyv azt adja hozzá az új verziókra, egy műveletet hozzá, majd rájuk aktuális - módosítás naplóbejegyzés létrehozása esetén ellenőrizze.

## <a name="add-a-new-revision"></a>A rendszer új változatot hozzáadása
1. Keresse meg a **API-k** lap az Azure-portálon az API Management szolgáltatáson belül.
2. Válassza ki **konferencia-API** API, majd válassza ki a listáról a **változatok** fülre az oldal tetején a menüből.
3. Válassza ki **+ újabb változatot**

    > [!TIP]
    > Másik lehetőségként **hozzáadása változat** a helyi menü (**...** ) API.
![Képernyő tetején változatok menü](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Adjon meg egy leírást az új verziókra, mi azt használandó szöveget.
5. Válassza ki **létrehozása**
6. A rendszer új változatot megtörtént.

    > [!NOTE]
    > Eredeti API marad **változat 1**. Ez az a verzió, a felhasználók továbbra is hívható, amíg a felhasználó annak másik változatát aktuális.

## <a name="make-non-breaking-changes-to-your-revision"></a>A verzió nem törhető módosításokat
1. Válassza ki a **tervezési** lapon, a képernyő felső részén.
2. Figyelje meg, hogy a **változat választó** (közvetlenül felett a tervezés lapról) jeleníti meg az aktuális verzióra **változat 2**.

    > [!TIP]
    > Használja a változat választó, amely a használni kívánt változatok közötti váltáshoz.

3. Válassza ki **+ Hozzáadás művelet**.
4. Állítsa be az új művelet **POST**, és a nevét, és a megjelenített neve a műveletet, mert **visszajelzés**
5. **Mentés** az új művelet.
6. Most hajtottunk módosítva **változat 2**. Használja a **változat választó** váltson vissza a lap tetején **változat 1**.
7. Figyelje meg, hogy az új művelet nem jelenik meg **változat 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Ellenőrizze a változat aktuális és a módosítási napló bejegyzés hozzáadása
1. Válassza ki a **változatok** fülre az oldal tetején a menüből.
![A verzió menü változat képernyőn.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Nyissa meg a helyi menüt (**...** ) a **2. módosítása**.
3. Válassza ki **ellenőrizze aktuális**.
![Változat aktuális ellenőrizze, és küldje el a napló módosítása](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Válassza ki **történő nyilvános Változásnapló ehhez az API-közzététellel**
5. Adjon meg egy leírást a módosítást, amelyet a fejlesztők számára, például **"Hozzáadott új visszajelzési műveletet."**
6. **2. módosítása** jelenleg aktuális.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Keresse meg a fejlesztői portálján módosítások megtekintéséhez és módosításához napló
1. Válassza ki **fejlesztői portálján** a felső menüben.
2. Válassza ki **API-k**, majd válassza ki **konferencia-API**.
3. Figyelje meg, az új **visszajelzés** művelet már elérhető.
4. Válassza ki **API-változások nyomon követése** alábbi API-név.
5. Figyelje meg, hogy a módosítási napló tétel megjelenik-e a listában.
![A fejlesztői portálján a módosítási napló](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Következő lépések
[Az Azure API Management API verziójának közzététele](#api-management-getstarted-publish-versions.md)