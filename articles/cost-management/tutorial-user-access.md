---
title: "Azure költség felügyeleti hozzáférés hozzárendelése |} Microsoft Docs"
description: "Rendeljen hozzá felhasználói fiókokkal, amelyek a hozzáférési szintek meghatározásához entitások felügyeleti költségadatok elérésére."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a42f3b51bf6d888d0d5602887ed317c6164391ef
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="assign-access-to-cost-management-data"></a>Hozzáférés hozzárendelése a felügyeleti adatokat költség

Felhasználó vagy entitás felügyeleti költség felügyeleti adataihoz való hozzáférés biztosítja. Cloudyn felhasználói fiókok hozzáférés meghatározása *entitások* és felügyeleti funkciókat. Ott a két típusú hozzáférést: rendszergazdai és felhasználói. Csak a módosított felhasználónként, rendszergazdai hozzáférés engedélyezi, hogy egy felhasználó korlátozás összes funkciójának Cloudyn portálon többek között: felhasználók kezelése, a címzettek listák felügyeleti és a legfelső szintű entitás hozzáférés entitás összes adatra. Felhasználói hozzáférés célja a végfelhasználók számára, a jelentések megtekintéséhez és Entitásadatok kell a hozzáférés-jelentések létrehozása.

Entitások segítségével az üzleti szervezet hierarchikus struktúra tükrözik. Szervezeti egységek, osztályok és csoportok az Ön szervezetében Cloudyn azonosításának. Az entitáshierarchia segít pontosabban nyomon követheti a Költekezési személyek.

A fiók vagy Azure szerződés regisztrálásakor rendszergazdai engedéllyel rendelkező fiók készült Cloudyn, ebben az oktatóanyagban a lépések végrehajtása érdekében. Ez az oktatóanyag ismerteti felhasználókezelés és entitás felügyeletét beleértve költség felügyeleti adataihoz való hozzáférés. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy felhasználó rendszergazdai hozzáférés
> * A felhasználói hozzáférés egy felhasználó létrehozása
> * Entitások létrehozása



## <a name="create-a-user-with-admin-access"></a>Hozzon létre egy felhasználó rendszergazdai hozzáférés

Már rendelkezik rendszergazdai hozzáféréssel, de a szervezet munkatársai számára is módosítania kell rendszergazdai hozzáféréssel rendelkeznek. A Cloudyn portálon kattintson a felső, jobb és válassza ki a fogaskerék szimbólum **felhasználókezelés**. Kattintson a **új felhasználó hozzáadása** új felhasználó hozzáadásához.

Adja meg a felhasználó vonatkozó szükséges információkat. Képes a jelszó mező üresen hagyja, hogy a felhasználó első bejelentkezés beállíthatja az új jelszót. Egy hivatkozás a bejelentkezési adatok beolvasása a felhasználó e-mailben a küldött Cloudyn kiválasztásakor **e-mailben értesítse felhasználói**. Válassza ki a felhasználók kezelése, hogy a felhasználó létrehozása és módosítása a más felhasználók engedélyeket. Felsorolja Címzettkezelés a felhasználó szerkesztése címzettjeinek listája.

A **felhasználó rendelkezik rendszergazdai hozzáférési**, a legfelső szintű entitás a szervezet van kiválasztva. Legfelső szintű kijelölt hagyja, és mentse a felhasználói adatokat. A legfelső szintű entitás kiválasztása a felhasználó nem csak a fa legfelső szintű entitás, hanem az alatta található entitások rendszergazdai jogosultsággal kell rendelkeznie.  
  ![rendszergazdai hozzáféréssel rendelkező új felhasználók hozzáadása](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>A felhasználói hozzáférés egy felhasználó létrehozása
Tipikus felhasználót kell költség felügyeleti adataihoz való hozzáférés, például az irányítópultok és jelentések felhasználói jogosultságra megtekintheti őket. Hozzon létre egy új felhasználót a felhasználói hozzáférés hasonló hozta létre a rendszergazdai hozzáférés a következő eltérésekkel:

- Törölje a jelet **felhasználókezelés engedélyezése**, **engedélyezése címzett sorolja fel a felügyeleti**, és minden a **felhasználónak rendszergazdai hozzáféréssel rendelkezik** listája.
- Jelölje ki az entitásokat, hogy a felhasználónak kell-e a hozzáférés a **felhasználó rendelkezik-e a felhasználói hozzáférés** listája.
- Akkor is a rendszergazda konkrét személyek által elérhető, igény szerint.

![felhasználói hozzáférés az új felhasználó hozzáadása](.\media\tutorial-user-access\new-user-access.png)

Útmutató videót felhasználók hozzáadásáról, lásd: [felhasználók hozzáadása az Azure költség Management Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Entitások létrehozása

A költségek entitáshierarchia határozza meg, az ajánlott eljárás esetén a szervezet felépítése azonosításához.

A fa létrehozása, vegye figyelembe, hogyan szeretné, vagy tekintse meg az üzleti egységek erőforrásokat, és az értékesítési részleg költség elkülönített költségek kell. Az entitás fában Cloudyn kvórummodellje rugalmas entitás öröklési miatt. A felhő fiókok egyéni előfizetések adott entitások van csatolva. Igen entitások olyan több-bérlős. Rendelhet bizonyos felhasználók hozzáférés csak az üzleti entitásokat használatával szegmensében. Így megtartja elkülönített, akár keresztül nagy részének leányvállalatai például egy üzleti adatok. És adatok elkülönítési elősegíti a cégirányítási.  

Amikor regisztrál az Azure szerződés vagy a fiók Cloudyn, az Azure-erőforrás adatokat, többek között a használat, a teljesítmény, a számlázási és a címke adatait az előfizetések Cloudyn fiókja át lett másolva. Azonban manuálisan kell létrehoznia az entitás fa. Ha kihagyja az Azure Resource Manager regisztrálása, majd csak számlázási adatai és néhány eszközintelligencia-jelentések érhetők el a Cloudyn portálon.

A Cloudyn portálon kattintson **beállítások** a felső, jobb és válassza ki a **felhő fiókok**. Egyetlen entitás (root) kezdődnie, és a legfelső szintű az entitás-fa létrehozása. Íme egy példa egy entitás hierarchiában, amely hasonló lehet a következőhöz számos informatikai szervezet a fa befejezése után:

![entitás fa](.\media\tutorial-user-access\entity-tree.png)

A **entitások**, kattintson a **entitás hozzáadása**. Adja meg a személy vagy a részleg, amelyhez hozzá szeretné adni. A **teljes nevét** és **E-mail** mezőket a meglévő felhasználók nem rendelkeznek. Ha meg szeretné tekinteni a hozzáférési szintek listája, keressen a súgóban *entitás hozzáadása*.

![Entitás hozzáadása](.\media\tutorial-user-access\add-entity.png)

Amikor elkészült, **mentése** az entitás.


Útmutató videót egy költség entitáshierarchia létrehozásával kapcsolatban, tekintse meg a [költség entitás hierarchia létrehozása az Azure költség Management Cloudyn a](https://youtu.be/dAd9G7u0FmU).

Ha egy Azure nagyvállalati szerződéssel felhasználók videót tekintené oktatóanyag előfizetési entitásokat, és a fiókok társításával kapcsolatos [csatlakozás az Azure Resource Manager és az Azure költség Management Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy felhasználó rendszergazdai hozzáférés
> * A felhasználói hozzáférés egy felhasználó létrehozása
> * Entitások létrehozása

A következő oktatóanyag felmérheti a kiadásokat előzményadatok használatával hogyan továbblépés.

> [!div class="nextstepaction"]
> [Jövőbeli kiadások előrejelzése](tutorial-forecast-spending.md)
