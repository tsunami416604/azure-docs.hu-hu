---
title: Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat – Azure Active Directory | Microsoft Docs
description: Útmutató egy alapszintű csoport létrehozásához a Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73473416"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat Azure Active Directory használatával
Az Azure Active Directory (Azure AD) portálon létrehozhat alapszintű csoportot. A cikk céljaira az erőforrás tulajdonosa (rendszergazda) egy alapszintű csoportot ad az erőforráshoz, amely azokat a tagokat tartalmazza (alkalmazottak), akiknek az adott erőforrást el kell érniük. Az összetettebb eseteknél, beleértve a dinamikus csoporttagságot és szabályok létrehozását is, lásd az [Azure Active Directory felhasználókezelés dokumentációt](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Alapszintű csoport létrehozása és tagok hozzáadása
Az alapszintű csoport létrehozása és a tagok felvétele történhet egyszerre.

### <a name="to-create-a-basic-group-and-add-members"></a>Alapszintű csoport létrehozása és tagok hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

    ![Azure AD-oldal, amelyben a csoportok láthatók](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. A **Active Directory** lapon válassza a **csoportok** lehetőséget, majd válassza az **új csoport**lehetőséget.

    ![Azure AD-oldal, amelyben a csoportok láthatók](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Az **új csoport** lapon adja meg a szükséges információkat.

    ![Új csoport oldal, példa adatokkal kitöltve](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Csoporttípus (kötelező).** Válasszon egy előre definiált csoportnevet. Ez a következőkre terjed ki:- **Security**. Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni. Erőforrásokhoz való hozzáférés kezelésével kapcsolatos további információkért lásd: [Erőforrásokhoz való hozzáférés kezelése Azure Active Directory-csoportokkal](active-directory-manage-groups.md).
               - **Office 365**. Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a beállítás lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz. Az Office 365-csoportokról bővebben lásd: [A Office 365-csoportok ismertetése](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Csoport név (kötelező).** Olyan nevet adjon a csoportnak, aminek értelme van és nem felejti el. A rendszer egy ellenőrzés végrehajtásával megállapítja, hogy a név már használatban van-e egy másik csoport számára. Ha a név már használatban van, a duplikált nevek elkerülése érdekében a rendszer megkéri, hogy módosítsa a csoport nevét.

   - **Csoport e-mail-címe (kötelező).** Adjon meg egy e-mail-címet a csoport számára, vagy tartsa meg az automatikusan kitöltött e-mail-címet.

   - **A csoport ismertetése.** Ha szeretne kiegészítő leírást adni a csoporthoz.

   - **Tagság típusa (kötelező).** Válasszon egy előre definiált tagsági típust. Ez a következőkből áll:- **Assigned.** Lehetővé teszi, hogy konkrét felhasználók a csoport tagjai lehessenek és egyedi engedélyekkel rendelkezzenek. A cikk céljaira ezt a lehetőséget használjuk.
          - **Dinamikus felhasználó.** Lehetővé teszi a dinamikus tagsági szabályok használatát a tagok automatikus hozzáadásához és eltávolításához. Ha a tag attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy a tag megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).
          - **Dinamikus eszköz.** Lehetővé teszi a dinamikus csoport szabályok segítségével a távoli eszközök automatikus felvételét és eltávolítását. Ha az eszköz attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy az eszköz megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).

        >[!Important]
        >Dinamikus csoportot létre lehet hozni eszközök és tagok számára is, de egyszerre mindkettőhöz nem. Nem lehet eszköz csoportot létrehozni az eszköz tulajdonosok attribútumai alapján sem. Eszköz tagsági szabályok csak eszköz attribútumokra hivatkozhatnak. A dinamikus csoportok felhasználókhoz és eszközökhöz való létrehozásával kapcsolatos további információkért lásd: [dinamikus csoport létrehozása és állapotának ellenõrzése](../users-groups-roles/groups-create-rule.md).

1. Kattintson a **Létrehozás** gombra.

    A csoport létrehozása megtörtént és készen áll a tagok hozzáadására.

1. Válassza ki a **Tagok** területet a **Csoport** lapon, és utána kezdje el megkeresni a csoportba felvenni kívánt tagokat a **Tagok kiválasztása** lapon.

    ![Csoport tagjainak kiválasztása a csoport létrehozása során](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Amikor elkészült a tagok hozzáadásával, válassza a **kiválasztást**.

    A **csoport – áttekintés** lap frissül és mutatja a csoportba most felvett tagok számát.

    ![Csoport áttekintés oldal a tagok számával kiemelve](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Üdvözlő e-mailek be-és kikapcsolása

Ha új Office 365-csoportot hoz létre, akár dinamikus, akár statikus tagsággal rendelkezik, a rendszer a csoporthoz hozzáadott összes felhasználóhoz üdvözlő értesítést küld. Ha a felhasználó vagy az eszköz bármely attribútuma megváltozik, a rendszer a szervezet összes dinamikus csoportjának szabályait feldolgozza a lehetséges tagsági változások miatt. A hozzáadott felhasználók megkapják az üdvözlő értesítést is. Ezt a viselkedést kikapcsolhatja az [Exchange PowerShellben](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>További lépések
Most, hogy hozzáadott egy csoportot és legalább egy felhasználót, a következőket teheti:

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Csoporttagság kezelése](active-directory-groups-membership-azure-portal.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [Az SaaS-alkalmazásokhoz való hozzáférés kezelése csoportokkal](../users-groups-roles/groups-saasapps.md)

- [Csoportok kezelése PowerShell-parancsokkal](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
