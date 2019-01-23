---
title: 'Gyors útmutató: a munkahelyi csoportok és tagok – Azure Active Directory megtekintése |} A Microsoft Docs'
description: Keresse meg, és megtekintheti a szervezet és a hozzárendelt tagjaik kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.openlocfilehash: 7ccd42fa1593e420c3d95ac1b50e8ef5084e68a0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446408"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Gyors útmutató: A munkahelyi csoportok és tagok megtekintése az Azure Active Directoryban
Megtekintheti a vállalat meglévő csoportjait és csoporttagjait az Azure Portalon. A csoportok az olyan felhasználók (tagok) kezelésére szolgálnak, akiknek azonos hozzáférés és azonos engedélyek szükségesek potenciálisan korlátozott alkalmazásokhoz és szolgáltatásokhoz.

Ebben a rövid útmutatóban megtekinti a vállalat összes meglévő csoportját és az azokhoz rendelt tagokat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következőket kell tennie:

- Azure Active Directory-bérlő létrehozása. További információkat [az Azure Active Directory portálhoz való hozzáférést és új bérlő létrehozását ismertető](active-directory-access-create-new-tenant.md) cikkben olvashat.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
A címtár eléréséhez globális rendszergazdai fiókkal kell bejelentkezni az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-new-group"></a>Új csoport létrehozása 
Hozzon létre egy _MDM policy – West_ nevű új csoportot. Csoportok létrehozásával kapcsolatos további információkért lásd: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md).

1. Válassza az **Azure Active Directory** a **Csoportok**, majd az **Új csoport** lehetőséget.

2. Töltse ki a **Csoport** lapot:
    
    - **Csoport típusa:** Válassza ki **biztonsági**
    
    - **Csoport neve:** Típus _mobileszköz-kezelési szabályzat – Nyugat-India_
    
    - **Tagság típusa:** Válassza ki **hozzárendelt**.

3. Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-user"></a>Új felhasználó létrehozása
Hozzon létre egy _Alain Charon_ nevű új felhasználót. A felhasználóknak már létezniük kell, hogy hozzáadhatóak legyenek csoporttagként. Felhasználók létrehozásával kapcsolatos további információkért tekintse meg a [felhasználók hozzáadását és törlését](add-users-azure-active-directory.md) ismertető cikket.

1. Válassza az **Azure Active Directory**, a **Felhasználók**, majd az **Új felhasználó** lehetőséget.

2. Töltse ki a **Felhasználó** lapot:

    - **név:** Típus _Alain Charon_.

    - **Felhasználónév:** Típus *alain@contoso.com*.

3. Másolja a **Jelszó** mezőben szereplő, automatikusan előállított jelszót, majd válassza a **Létrehozás** lehetőséget.

## <a name="add-a-group-member"></a>Csoporttag hozzáadása
Most, hogy már rendelkezik egy csoporttal és egy felhasználóval, hozzáadhatja _Alain Charont_ az _MDM policy – West_ csoport tagjaként. Csoporttagok hozzáadásával kapcsolatos további információkért lásd: [Csoporttagok hozzáadása vagy eltávolítása](active-directory-groups-members-azure-portal.md).

1. Válassza az **Azure Active Directory** > **Csoportok** elemet.

2. A **Csoportok – Minden csoport** lapon keresse meg és válassza ki az **MDM policy – West** csoportot.

3. Az **MDM policy – West áttekintési** lapján válassza a **Tagok** lehetőséget a **Kezelés** területen.

4. Válassza a **Tagok hozzáadása** lehetőséget, majd keresse meg és válassza az **Alain Charon** elemet.

5. Válassza a **Kiválasztás** lehetőséget

## <a name="view-all-groups"></a>Az összes csoport megtekintése
A vállalat összes csoportját megtekintheti az Azure Portal **Csoportok – Minden csoport** lapján.

- Válassza az Azure **Active Directory** > **Csoportok** elemet.

    A **Csoportok – Minden csoport** lap megjelenik, és mutatja az összes aktív csoportot.

    ![A Csoportok – Minden csoport lap, amelyen az összes létező csoport látható](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Csoport keresése
A **Csoportok – Minden csoport** lapon keresse meg az **MDM policy – West** csoportot.

1. A **Csoportok – Minden csoport** lapon írja be az _MDM_ karakterláncot a **keresőmezőbe**.

    A keresési eredmények a **keresőmező** alatt jelennek meg, és tartalmazzák az _MDM policy – West_ csoportot.

    ![A Csoportok – Minden csoport lap, amelynek a keresőmezője ki van töltve](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. Válassza ki az **MDM policy – West** csoportot.

4. Az **MDM policy – West áttekintési** lapján megtekintheti a csoport információit, például azt, hogy hány tagja van az adott csoportnak.

    ![Az MDM policy – West áttekintési lapja a tagok információival](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Csoporttagok megtekintése
Most, hogy megtalálta a csoportot, megtekintheti a csoporthoz rendelt tagokat.

- Válassza a **Tagok** elemet a **Kezelés** területen, majd tekintse át az adott csoporthoz rendelt tagok neveinek teljes listáját, köztük _Alain Charont_.

    ![Az MDM policy – West csoporthoz hozzárendelt tagok listája](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ezt a csoportot több útmutatóban is használjuk, amelyek a dokumentáció **Útmutatók** szakaszában érhetőek el. Ha inkább mégsem használná ezt a csoportot, az alábbi lépésekkel törölheti a hozzárendelt tagjaival együtt:

1. A **Csoportok – Minden csoport** lapon keresse meg az **MDM policy – West** csoportot.

2.  Válassza ki az **MDM policy – West** csoportot.

    Megjelenik az **MDM policy – West áttekintési** oldala.

3. Válassza a **Törlés** elemet.

    Ekkor a csoport és a társított tagok törlődnek.

    ![Az MDM policy – West áttekintési lapja, amelyen a törlési hivatkozás ki van emelve](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Ez nem törli Alain Charon felhasználót, csak a tagságát a törölt csoportban.

## <a name="next-steps"></a>További lépések
A következő cikk azt ismerteti, hogyan társíthat egy előfizetést az Azure AD-címtárhoz.

> [!div class="nextstepaction"]
> [Azure-előfizetés társítása](active-directory-how-subscriptions-associated-directory.md)