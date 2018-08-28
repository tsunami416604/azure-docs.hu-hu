---
title: Dinamikus csoporttagság, valamint a felhasználók automatikus hozzáadása és törlése az Azure Active Directoryban – oktatóanyag
description: Az oktatóanyagban felhasználói tagsági szabályokkal rendelkező csoportokat használunk a felhasználók automatikus hozzáadásához és eltávolításához
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 2119bb60cbdc36f62623ce0db52885e17f3d3006
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209179"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Oktatóanyag: Csoporttagok automatikus hozzáadása és eltávolítása

Az Azure Active Directory (Azure AD) szolgáltatásban automatikusan adhat hozzá vagy távolíthat el felhasználókat a biztonsági vagy Office 365-csoportokban, így ezt nem kell minden alkalommal manuálisan elvégeznie. Ha egy felhasználó vagy eszköz bármely tulajdonsága megváltozik, az Azure AD kiértékeli a dinamikus csoportokra vonatkozó összes szabályt a bérlőn, és ezáltal ellenőrzi, hogy a változás magával vonja-e tagok hozzáadását vagy eltávolítását.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Egy adott partnervállalat vendégfelhasználóival automatikusan feltöltött csoport létrehozása
> * Licencek hozzárendelése a csoporthoz a partnerspecifikus funkciók számára, amelyekhez a vendégfelhasználók hozzáférhetnek
> * Bónusz: a **Minden felhasználó** csoport biztonságossá tétele a vendégfelhasználók eltávolításával, hogy például hozzáférést adhasson a tagfelhasználóknak a csak belső elérésű helyekhez

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A funkcióhoz egy prémium szintű Azure AD-licenccel kell rendelkeznie a bérlő globális rendszergazdájaként. Ha még nem rendelkezik ilyennel, válassza az Azure AD-ben a **Licencek** > **Termékek** > **Kipróbálás/vásárlás** lehetőséget.

A felhasználókhoz nem szükséges licenceket hozzárendelnie ahhoz, hogy dinamikus csoportok tagjai lehessenek. Csupán a bérlőn elérhető minimális számú prémium szintű Azure AD P1 licencre van szükség az összes ilyen felhasználó lefedéséhez. 

## <a name="create-a-group-of-guest-users"></a>Vendégfelhasználók csoportjának létrehozása

Először létrehozunk egy csoportot az egy partnervállalatból származó vendégfelhasználók számára. Esetükben speciális licencelésre van szükség, ezért érdemes erre a célra létrehozni egy csoportot.

1. Jelentkezzen be az Azure Portalra (https://portal.azure.com) egy olyan fiókkal, amely a bérlő globális rendszergazdája.
2. Válassza az **Azure Active Directory** > **Csoportok** > **Új csoport** lehetőséget.
  ![az Új csoport parancs kiválasztása](./media/groups-dynamic-tutorial/new-group.png)
3. A **Csoport** panelen:
  
  * Válassza a **Biztonsági** lehetőséget a csoport típusaként.
  * Adja meg a következőt a csoport nevénél és leírásánál: `Guest users Contoso`
  * Módosítsa a **Tagság típusát** a **Dinamikus felhasználó** értékre.
  * Válassza a **Dinamikus lekérdezés hozzáadása** lehetőséget.
  
4. Válassza a **Speciális szabály** lehetőséget, majd a **Speciális szabály** mezőbe írja be a következőt: `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`.
5. A panel bezárásához kattintson a **Lekérdezés hozzáadása** gombra.
6. A fiók létrehozásához kattintson a **Csoport** panel **Létrehozás** elemére.

## <a name="assign-licenses"></a>Licencek hozzárendelése

Most, hogy létrejött az új csoport, hozzárendelheti a partnerek számára szükséges licenceket.

1. Az Azure AD-ben kattintson a **Licencek** gombra, válasszon ki egy vagy több licencet, majd kattintson a **Hozzárendelés** gombra.
2. Válassza a **Felhasználók és csoportok** lehetőséget, majd a **Guest users Contoso** csoportot, és mentse a módosításokat.
3. A **Hozzárendelési beállítások** segítségével be- és kikapcsolhatja a kiválasztott licencekhez tartozó szolgáltatáscsomagokat is. A módosítások végeztével mindenképpen kattintson az **OK** gombra azok mentéséhez.
4. A hozzárendelés véglegesítéséhez kattintson a **Licenc hozzárendelése** panelen a lap alján található **Hozzárendelés** gombra.

## <a name="remove-guests-from-all-users-group"></a>Vendégek eltávolítása a Minden felhasználó csoportból

Tegyük fel, hogy a végső cél, hogy idővel az összes vendégfelhasználót saját csoportokba rendelje vállalat szerint. Most már módosíthatja a **Minden felhasználó** csoportot is, hogy csak a bérlőn lévő tagfelhasználók számára legyen fenntartva. Így aztán olyan alkalmazások és licencek hozzárendeléséhez használhatja, amelyek kimondottan a saját cégéhez tartoznak.

   ![A Minden felhasználó csoport módosítása, hogy csak a tagokat tartalmazza](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

**A vendégfelhasználók csoportjának eltávolítása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a bérlő globális rendszergazdai fiókjával.
2. Válassza az **Azure Active Directory** > **Csoportok** elemet. Válassza ki a **Guest users Contoso** csoportot, majd kattintson a három pontra (...), és végül a **Törlés** elemre. A csoport törlésekor minden hozzárendelt licenc el lesz távolítva.

**A Minden felhasználó csoport visszaállítása**
1. Válassza az **Azure Active Directory** > **Csoportok** elemet. Kattintson a **Minden felhasználó** csoport nevére a csoport megnyitásához.
1. Kattintson a **Dinamikus tagsági szabályok** elemre, törölje a szabály teljes szövegét, majd kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Vendégfelhasználók csoportjának létrehozása
> * Licencek hozzárendelése az új csoporthoz
> * A Minden felhasználó csoport módosítása, hogy csak a tagokat tartalmazza

Folytassa a következő cikkel, ha többet szeretne megtudni a csoportalapú licencelés alapjairól.
> [!div class="nextstepaction"]
> [A csoportalapú licencelés alapjai](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



