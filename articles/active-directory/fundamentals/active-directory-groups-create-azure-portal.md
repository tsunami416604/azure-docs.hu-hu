---
title: Alapcsoport létrehozása és tagok hozzáadása - Azure Active Directory | Microsoft dokumentumok
description: Útmutató az Azure Active Directory használatával egy alapcsoport létrehozásához.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73473416"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat az Azure Active Directory használatával
Az Azure Active Directory (Azure AD) portálon létrehozhat alapszintű csoportot. A cikk céljaira az erőforrás tulajdonosa (rendszergazda) egy alapszintű csoportot ad az erőforráshoz, amely azokat a tagokat tartalmazza (alkalmazottak), akiknek az adott erőforrást el kell érniük. Az összetettebb eseteknél, beleértve a dinamikus csoporttagságot és szabályok létrehozását is, lásd az [Azure Active Directory felhasználókezelés dokumentációt](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Alapszintű csoport létrehozása és tagok hozzáadása
Az alapszintű csoport létrehozása és a tagok felvétele történhet egyszerre.

### <a name="to-create-a-basic-group-and-add-members"></a>Alapszintű csoport létrehozása és tagok hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

    ![Azure AD lap, a csoportok megjelenítése](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. Az **Active Directory** lapon válassza a **Csoportok,** majd az **Új csoport**lehetőséget.

    ![Azure AD lap, a csoportok megjelenítése](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Az **Új csoport** lapon adja meg a szükséges adatokat.

    ![Új csoport oldal, példa adatokkal kitöltve](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Csoporttípus (kötelező).** Válasszon egy előre definiált csoportnevet. Ez a következőket foglalja magában: - **Biztonság**. Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni. Erőforrásokhoz való hozzáférés kezelésével kapcsolatos további információkért lásd: [Erőforrásokhoz való hozzáférés kezelése Azure Active Directory-csoportokkal](active-directory-manage-groups.md).
               - **Office 365.** Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a beállítás lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz. Az Office 365-csoportokról bővebben lásd: [A Office 365-csoportok ismertetése](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Csoport név (kötelező).** Olyan nevet adjon a csoportnak, aminek értelme van és nem felejti el. A végrehajtás ellenőrzése azt állapítja meg, hogy a név már használatban van-e egy másik csoportnál. Ha a név már használatban van, az ismétlődő elnevezés elkerülése érdekében a rendszer megkéri, hogy módosítsa a csoport nevét.

   - **Csoport e-mail cím (kötelező).** Adjon meg egy e-mail címet a csoportnak, vagy tartsa meg automatikusan a kitöltött e-mail címet.

   - **A csoport ismertetése.** Ha szeretne kiegészítő leírást adni a csoporthoz.

   - **Tagság típusa (kötelező).** Válasszon egy előre definiált tagsági típust. Ez a következőket foglalja magában: - **Hozzárendelve.** Lehetővé teszi, hogy konkrét felhasználók a csoport tagjai lehessenek és egyedi engedélyekkel rendelkezzenek. A cikk céljaira ezt a lehetőséget használjuk.
          - **Dinamikus felhasználó.** Lehetővé teszi a dinamikus tagsági szabályok használatát a tagok automatikus hozzáadásához és eltávolításához. Ha a tag attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy a tag megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).
          - **Dinamikus eszköz.** Lehetővé teszi a dinamikus csoport szabályok segítségével a távoli eszközök automatikus felvételét és eltávolítását. Ha az eszköz attribútumai változnak, a rendszer megvizsgálja a címtárra vonatkozó dinamikus csoportszabályokat, hogy az eszköz megfelel-e a szabály követelményeinek (hozzáadja), vagy már nem felel meg a szabályok szerinti követelményeknek (eltávolítja).

        >[!Important]
        >Dinamikus csoportot létre lehet hozni eszközök és tagok számára is, de egyszerre mindkettőhöz nem. Nem lehet eszköz csoportot létrehozni az eszköz tulajdonosok attribútumai alapján sem. Eszköz tagsági szabályok csak eszköz attribútumokra hivatkozhatnak. A dinamikus csoport felhasználók és eszközök számára való létrehozásáról a [Dinamikus csoport létrehozása és az állapot ellenőrzése című](../users-groups-roles/groups-create-rule.md)témakörben talál további információt.

1. Kattintson a **Létrehozás** gombra.

    A csoport létrehozása megtörtént és készen áll a tagok hozzáadására.

1. Válassza ki a **Tagok** területet a **Csoport** lapon, és utána kezdje el megkeresni a csoportba felvenni kívánt tagokat a **Tagok kiválasztása** lapon.

    ![Csoport tagjainak kiválasztása a csoport létrehozása során](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Amikor elkészült a tagok hozzáadásával, válassza a **kiválasztást**.

    A **csoport – áttekintés** lap frissül és mutatja a csoportba most felvett tagok számát.

    ![Csoport áttekintés oldal a tagok számával kiemelve](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Az üdvözlő e-mailek be- és kikapcsolása

Új Office 365-csoport létrehozásakor – akár dinamikus, akár statikus tagsággal – üdvözlő értesítést küld a rendszer a csoporthoz hozzáadott összes felhasználónak. Ha egy felhasználó vagy eszköz bármely attribútuma megváltozik, a szervezet összes dinamikus csoportszabályát feldolgozza a rendszer a tagság esetleges módosításához. A hozzáadott felhasználók is megkapják az üdvözlő értesítést. Ezt a viselkedést kikapcsolhatja az [Exchange PowerShell ben.](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps) 

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
