<properties

    pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
    description="How to create and manage groups to manage Azure users using Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>


# Csoportkezelés az Azure Active Directoryban

Az Azure Active Directory (Azure AD) egyik felhasználó-kezelési szolgáltatása a felhasználói csoportok létrehozásának lehetősége. A létrehozott csoportokkal licenceket rendelhet egy felhasználói osztályhoz. A csoportokkal hozzáférési engedélyeket is hozzárendelhet a következőkhöz:

- erőforrásokhoz, például a címtárban található objektumokhoz;
- olyan címtáron kívül található erőforrásokhoz, mint például az SaaS-alkalmazások, az Azure-szolgáltatások, a SharePoint-webhelyek vagy a helyszíni erőforrások.

Ezen túlmenően az erőforrás tulajdonosa erőforrás-hozzáférési jogosultságot rendelhet az Azure AD-csoportokhoz. Ezzel az adott csoport tagjai számára hozzáférést biztosít az adott erőforráshoz. Innentől kezdve a csoporttagság kezelését a csoport tulajdonosa végzi. A gyakorlatban az erőforrás tulajdonosa ad jogosultságot a csoport tulajdonosának arra, hogy felhasználókat rendeljen a saját erőforrásához.

## Hogyan hozható létre csoport?

Ez a feladat az Office 365 fiókportálon, a Windows Intune fiókportálon vagy a klasszikus Azure portálon hajtható végre, attól függően, hogy a szervezet milyen szolgáltatásokra fizet elő. További információk az Azure Active Directory nem Azure portálon történő kezeléséről: [Administering your Azure AD directory](active-directory-administer.md) (Az Azure AD-címtár felügyelete).

1. A [klasszikus Azure portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd jelölje ki a szervezete címtárának nevét.

2. Válassza ki a **Csoportok** lapot.

3. Válassza ki a **Csoport hozzáadása** lehetőséget.

4. A **Csoport hozzáadása** ablakban adja meg a csoport nevét és leírását.


## Hogyan lehet adott felhasználókat felvenni egy biztonsági csoportba vagy eltávolítani onnan?

**Adott felhasználó felvétele egy csoportba**

1. A [klasszikus Azure portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd jelölje ki a szervezete címtárának nevét.

2. Válassza ki a **Csoportok** lapot.

3. Nyissa meg azt a csoportot, amelybe tagokat szeretne felvenni. Alapértelmezés szerint ekkor a kiválasztott csoporthoz tartozó **Tagok** lap nyílik meg.

4. Válassza ki a **Tagok hozzáadása** lehetőséget.

5. A **Tagok hozzáadása** oldalon válassza ki annak a felhasználónak vagy csoportnak a nevét, amelyet tagként fel kíván venni a csoportba, és győződjön meg arról, hogy ez a név a **Kiválasztva** ablaktáblán is szerepel.


**Adott felhasználó eltávolítása egy csoportból**

1. A [klasszikus Azure portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd jelölje ki a szervezete címtárának nevét.

2. Válassza ki a **Csoportok** lapot.

3. Nyissa meg azt a csoportot, amelyből tagokat szeretne eltávolítani.

4. Válassza ki a **Tagok** lapot, majd a csoportból eltávolítani kívánt tag nevét, végül kattintson az **Eltávolítás** gombra.

6. A megjelenő kérdésre válaszul erősítse meg, hogy az adott tagot el szeretné távolítani a csoportból.


## Hogyan történik a csoporttagság dinamikus kezelése?

Az Azure AD-ben könnyen megoldható egyszerű (csak egyetlen összehasonlítást használó) szabály beállítása annak megadásához, hogy melyek a csoporttagságra jelölt felhasználók. Ha például egy csoport egy SaaS-alkalmazáshoz van hozzárendelve, és olyan szabályt állít be, hogy a csoportba az „értékesítési képviselő” beosztású felhasználók legyenek felvéve, az Azure AD-címtárban található összes ilyen beosztású felhasználó hozzá fog férni az adott SaaS-alkalmazáshoz.

> [AZURE.NOTE] Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van. A beágyazott csoporttagság az alkalmazásokhoz történő csoportalapú hozzárendeléseknél egyelőre nem támogatott.
>
> Dinamikus csoporttagság hozzárendeléséhez Prémium szintű Azure AD licencet kell hozzárendelni
>
> - a csoportszabályt felügyelő rendszergazdához;
> - az összes, az adott szabály által csoporttagságra jelölt felhasználóhoz.

**Dinamikus csoporttagság engedélyezése**

1. A [klasszikus Azure portálon](https://manage.windowsazure.com) válassza az **Active Directory** lehetőséget, majd jelölje ki a szervezete címtárának nevét.

2. A **Csoportok** lapon nyissa meg a szerkeszteni kívánt csoportot.

3. A **Konfigurálás** lapon állítsa a **Dinamikus csoporttagságok engedélyezése** beállítást **Igen** értékre.

4. Állítson be egy egyszerű szabályt a csoporthoz, amely szabályozza a dinamikus csoporttagság működését. Győződjön meg arról, hogy az **Add users where** (Felhasználók hozzáadása, ahol) beállítás ki van jelölve, majd válasszon ki egy felhasználói tulajdonságot (például részleg, beosztás) a listából.

5. Ezt követően válasszon ki egy állapotot (nem egyenlő, egyenlő, nem ezzel kezdődik, ezzel kezdődik, nem tartalmazza, tartalmazza, nem egyezik, megegyezik), majd adjon értéket a kiválasztott felhasználói tulajdonságnak.

Információk a dinamikus csoporttagsághoz kapcsolódó *speciális* (akár többszörös összehasonlítást is tartalmazó) szabályok létrehozásáról: [Using attributes to create advanced rules](active-directory-accessmanagement-groups-with-advanced-rules.md) (Attribútumok használata speciális szabályok létrehozásához).

## További információ

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Managing access to resources with Azure Active Directory groups (Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal)](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)](active-directory-apps-index.md)

* [Mi az az Azure Active Directory?](active-directory-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


