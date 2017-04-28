---
title: "Csoportkezelés az Azure Active Directoryban | Microsoft Docs"
description: "Csoportok létrehozása és kezelése az Azure Active Directoryban az Azure-felhasználók kezeléséhez."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d1f5451c-3807-423c-8bac-2822d27b893f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8f171456f74925eba16ebd6f56ced558a11f9d99
ms.openlocfilehash: 7d2cc99925e01f8135f04f5863f798e13d7413e3
ms.lasthandoff: 02/11/2017


---
# <a name="managing-groups-in-azure-active-directory"></a>Csoportkezelés az Azure Active Directoryban
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [klasszikus Azure portál](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Az Azure Active Directory (Azure AD) egyik felhasználó-kezelési szolgáltatása a felhasználói csoportok létrehozásának lehetősége. A csoportokat kezelési feladatok, például licencek hozzárendelése vagy egyszerre több felhasználónak adott engedélyek elvégzésére használhatja. A csoportokkal hozzáférési engedélyeket is hozzárendelhet a következőkhöz:

* erőforrásokhoz, például a címtárban található objektumokhoz;
* olyan címtáron kívül található erőforrásokhoz, mint például az SaaS-alkalmazások, az Azure-szolgáltatások, a SharePoint-webhelyek vagy a helyszíni erőforrások

Ezenkívül az erőforrás tulajdonosa erőforrás-hozzáférési jogosultságot rendelhet egy más tulajdonában álló Azure AD-csoporthoz. Ezzel az adott csoport tagjai számára hozzáférést biztosít az erőforráshoz. Innentől kezdve a csoporttagság kezelését a csoport tulajdonosa végzi. A gyakorlatban az erőforrás tulajdonosa ad jogosultságot a csoport tulajdonosának arra, hogy felhasználókat rendeljen a saját erőforrásához.

## <a name="how-do-i-create-a-group"></a>Hogyan hozható létre csoport?
Attól függően, hogy a szervezet melyik szolgáltatásokra fizetett elő, a következők használatával hozhat létre csoportot:

* klasszikus Azure-portál
* Office 365-fiókportál
* Windows Intune-fiókportál

A feladatokat a klasszikus Azure-portál alapján ismertetjük. További információk az Azure Active Directory nem Azure Portalon történő kezeléséről: [Az Azure AD-címtár felügyelete](active-directory-administer.md).

1. A [klasszikus Azure-portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
2. Válassza ki a **Csoportok** lapot.
3. Válassza ki a **Csoport hozzáadása** lehetőséget.
4. A **Csoport hozzáadása** ablakban adja meg a csoport nevét és leírását.

## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Hogyan lehet adott felhasználókat felvenni egy biztonsági csoportba vagy eltávolítani onnan?
**Adott felhasználó felvétele egy csoportba**

1. A [klasszikus Azure-portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
2. Válassza ki a **Csoportok** lapot.
3. Nyissa meg azt a csoportot, amelybe tagokat szeretne felvenni. Nyissa meg a kiválasztott csoporthoz tartozó **Tagok** lapot, ha még nem jelent meg.
4. Válassza ki a **Tagok hozzáadása** lehetőséget.
5. A **Tagok hozzáadása** oldalon válassza ki annak a felhasználónak vagy csoportnak a nevét, amelyet tagként fel kíván venni a csoportba. Győződjön meg arról, hogy ez a név a **Kiválasztva** ablaktáblán is szerepel.

**Adott felhasználó eltávolítása egy csoportból**

1. A [klasszikus Azure-portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
2. Válassza ki a **Csoportok** lapot.
3. Nyissa meg azt a csoportot, amelyből tagokat szeretne eltávolítani.
4. Válassza ki a **Tagok** lapot, majd a csoportból eltávolítani kívánt tag nevét, végül kattintson az **Eltávolítás** gombra.
5. A megjelenő kérdésre válaszul erősítse meg, hogy az adott tagot el szeretné távolítani a csoportból.

## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Hogyan történik a csoporttagság dinamikus kezelése?
Az Azure AD-ben könnyen beállíthat egy egyszerű szabályt annak megadásához, hogy melyek a csoporttagságra kijelölt felhasználók. Az egyszerű szabály egy olyan szabály, amely csak egyszeres összehasonlítást végez. Ha például egy csoport egy SaaS-alkalmazáshoz van hozzárendelve, beállíthat egy olyan szabályt, hogy a csoportba az „értékesítési képviselő” beosztású felhasználók legyenek felvéve. Ez a szabály minden ilyen beosztású felhasználó számára hozzáférést biztosít az adott SaaS-alkalmazáshoz.

Ha egy felhasználó bármely attribútuma megváltozik, a rendszer kiértékeli a dinamikus csoportokra vonatkozó összes szabályt egy címtárban, és ezáltal ellenőrzi, hogy a felhasználó attribútumának módosulása magával vonja-e bármilyen csoport hozzáadását vagy eltávolítását. Ha egy felhasználó megfelel egy csoportra vonatkozó szabálynak, automatikusan tagként lesz hozzáadva az adott csoporthoz. Amennyiben már nem felel meg az arra a csoportra vonatkozó szabálynak, amelynek tagja, el lesz távolítva az adott csoportból, és a tagsága megszűnik.

> [!NOTE]
> Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van. A beágyazott csoporttagság az alkalmazásokhoz történő csoportalapú hozzárendeléseknél egyelőre nem támogatott.
>
> Dinamikus csoporttagság hozzárendeléséhez Prémium szintű Azure AD licencet kell hozzárendelni
>
> * a csoportszabályt felügyelő rendszergazdához;
> * A csoport minden tagja
>
>

**Dinamikus csoporttagság engedélyezése**

1. A [klasszikus Azure-portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
2. A **Csoportok** lapon nyissa meg a szerkeszteni kívánt csoportot.
3. A **Konfigurálás** lapon állítsa a **Dinamikus csoporttagságok engedélyezése** beállítást **Igen** értékre.
4. Állítson be egy egyszerű szabályt a csoporthoz a dinamikus csoporttagság működésének szabályozása érdekében. Győződjön meg arról, hogy az **Add users where** (Felhasználók hozzáadása, ahol) beállítás ki van jelölve, majd válasszon ki egy felhasználói tulajdonságot (például részleg, beosztás) a listából.
5. Ezt követően válasszon ki egy állapotot (nem egyenlő, egyenlő, nem ezzel kezdődik, ezzel kezdődik, nem tartalmazza, tartalmazza, nem egyezik, megegyezik).
6. Adjon meg egy összehasonlító értéket a kiválasztott felhasználói tulajdonságnál.

Információk a dinamikus csoporttagsághoz kapcsolódó *speciális* (akár többszörös összehasonlítást is tartalmazó) szabályok létrehozásáról: [Using attributes to create advanced rules](active-directory-accessmanagement-groups-with-advanced-rules.md) (Attribútumok használata speciális szabályok létrehozásához).

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

