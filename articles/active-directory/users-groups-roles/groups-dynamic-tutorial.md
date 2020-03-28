---
title: Felhasználók hozzáadása dinamikus csoporthoz - oktatóanyag - Azure AD | Microsoft dokumentumok
description: Az oktatóanyagban felhasználói tagsági szabályokkal rendelkező csoportokat használunk a felhasználók automatikus hozzáadásához és eltávolításához
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382f3b59142aee7ddfbec4aceb153a174874ac1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74027112"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Oktatóanyag: Csoporttagok automatikus hozzáadása és eltávolítása

Az Azure Active Directory (Azure AD) szolgáltatásban automatikusan adhat hozzá vagy távolíthat el felhasználókat a biztonsági vagy Office 365-csoportokban, így ezt nem kell minden alkalommal manuálisan elvégeznie. Ha egy felhasználó vagy eszköz bármely tulajdonsága megváltozik, az Azure AD kiértékeli a dinamikus csoportokra vonatkozó összes szabályt a bérlőn, és ezáltal ellenőrzi, hogy a változás magával vonja-e tagok hozzáadását vagy eltávolítását.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Vendégfelhasználók automatikusan kitöltött csoportjának létrehozása partnervállalatból
> * Licencek hozzárendelése a csoporthoz a partnerspecifikus funkciók számára, amelyekhez a vendégfelhasználók hozzáférhetnek
> * Bónusz: a **Minden felhasználó** csoport biztonságossá tétele a vendégfelhasználók eltávolításával, hogy például hozzáférést adhasson a tagfelhasználóknak a csak belső elérésű helyekhez

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció a bérlő globális rendszergazdájaként egy Azure AD Premium-licencet igényel. Ha nem rendelkezik ilyen, az Azure AD-ben válassza **a Licencek** > **termékek** > **kipróbálása/megvásárlása**lehetőséget.

A felhasználókhoz nem szükséges licenceket hozzárendelnie ahhoz, hogy dinamikus csoportok tagjai lehessenek. Csupán a bérlőn elérhető minimális számú prémium szintű Azure AD P1 licencre van szükség az összes ilyen felhasználó lefedéséhez. 

## <a name="create-a-group-of-guest-users"></a>Vendégfelhasználók csoportjának létrehozása

Először létrehozunk egy csoportot az egy partnervállalatból származó vendégfelhasználók számára. Esetükben speciális licencelésre van szükség, ezért érdemes erre a célra létrehozni egy csoportot.

1. Jelentkezzen be az Azurehttps://portal.azure.com) Portalra (a bérlő globális rendszergazdájával.
2. Válassza az **Azure Active Directory** > **csoportok** > **új csoportját.**
   ![válassza ki a parancsot új csoport indításához](./media/groups-dynamic-tutorial/new-group.png)
3. A **Csoport** panelen:
  
   * Csoporttípusként válassza a **Biztonság** lehetőséget.
   * Írja `Guest users Contoso` be a csoport nevét és leírását.
   * Módosítsa **a tagság típusát** dinamikus **felhasználóra.**
   
4. Válassza a **Tulajdonosok** **lehetőséget,** és a Tulajdonosok hozzáadása panelen keresse meg a kívánt tulajdonosokat. Kattintson a kívánt tulajdonosok hozzá a kiválasztáshoz.
5. A **Kijelölés gombra** kattintva zárja be a **Tulajdonosok hozzáadása** panelt.  
6. A Dinamikus felhasználói tagok mezőben jelölje be a **Dinamikus lekérdezés szerkesztése** jelölőnégyzetet. **Dynamic user members**
7. A **Dinamikus tagsági szabályok** panelen tegye a következőket:

   * A **Tulajdonság** mezőben kattintson a meglévő értékre, és válassza a **userType**lehetőséget. 
   * Ellenőrizze, hogy az **Operátor** mezőben **az Egyenlők beállítás** van-e kijelölve.  
   * Jelölje ki az **Érték** mezőt, és írja be a **Vendég lehetőséget.** 
   * Másik **sor** hozzáadásához kattintson a Kifejezés hozzáadása hivatkozásra.
   * Az **És/vagy** mezőben válassza az **És**lehetőséget.
   * A **Tulajdonság** mezőben válassza a **cégnév lehetőséget.**
   * Ellenőrizze, hogy az **Operátor** mezőben **az Egyenlők beállítás** van-e kijelölve.
   * Az **Érték** mezőbe írja be a **Contoso**értéket.
   * A **Dinamikus tagsági szabályok** panel bezárásához kattintson a **Mentés** gombra.
   
8. A fiók létrehozásához kattintson a **Csoport** panel **Létrehozás** elemére.

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
2. Válassza az **Azure Active Directory-csoportok** > **lehetőséget.** Válassza ki a **Guest users Contoso** csoportot, majd kattintson a három pontra (...), és végül a **Törlés** elemre. A csoport törlésekor minden hozzárendelt licenc el lesz távolítva.

**A Minden felhasználó csoport visszaállítása**
1. Válassza az **Azure Active Directory-csoportok** > **lehetőséget.** Kattintson a **Minden felhasználó** csoport nevére a csoport megnyitásához.
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



