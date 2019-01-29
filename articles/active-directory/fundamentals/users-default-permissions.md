---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory |} A Microsoft Docs
description: További információ a különböző felhasználói engedélyek elérhető az Azure Active Directoryban.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.openlocfilehash: c737ca9f2214df4fe06780604261995540c9aaaf
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103292"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Mik azok az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználók hozzáférése a felhasználó típusát áll a [szerepkörtagságai](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal), és az egyes objektumok tulajdonjogának. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
A kiosztott alapértelmezett engedélykészlet tartalma attól függ, hogy az adott felhasználó natív tagja-e a bérlőnek (tagfelhasználó) vagy egy B2B-együttműködés révén megjelenő vendég (vendégfelhasználó). További információ a B2B-együttműködés: [Mi az Azure AD B2B együttműködés?](../b2b/what-is-b2b.md) vendégfelhasználók további információt. 
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* Az Azure AD B2B-vendégfelhasználói korlátozott címtárengedélyekkel rendelkeznek. A vendégfelhasználók például csak a saját profiljuk adatait látják a bérlőn. A vendégfelhasználók azonban egy másik felhasználó egyszerű felhasználónevének vagy ObjectId azonosítójának megadásával adatokat kérdezhetnek le az adott felhasználóról. A vendégfelhasználók nem láthatják a többi bérlőobjektum (például csoportok és alkalmazások) adatait.

A vendégekre vonatkozó alapértelmezett engedélyek korlátozásokat tartalmaznak. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. Még egy további korlátozás áll rendelkezésre a vendégfelhasználók számára, ez pedig a más felhasználók meghívásának lehetősége. Ha a **Vendégek meghívót küldhetnek** beállítás értéke **Nem**, akkor a vendégfelhasználók nem hívhatnak meg más vendégeket. További információ: [Meghívók delegálása B2B-együttműködéshez](../b2b/delegate-invitations.md). Ha azt szeretné, hogy a vendégfelhasználók a tagfelhasználók alapértelmezett engedélyeivel azonos engedélyeket kapjanak, akkor **A vendégfelhasználók engedélyei korlátozottak** beállítás számára adjon meg **Nem** értéket. E beállítás alkalmazása esetén a vendégfelhasználók megkapják a tagfelhasználók minden alapértelmezett engedélyét, továbbá a vendégfelhasználók felvehetők lesznek a rendszergazdai szerepkörökbe is.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Vendégfelhasználók engedélyei**
------------ | --------- | ----------
Felhasználók és kapcsolatok | Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása<br>Vendégek meghívása<br>Saját jelszó módosítása<br>Saját mobiltelefonszám kezelése<br>Saját fénykép kezelése<br>Saját frissítési jogkivonatok érvénytelenítése | Saját tulajdonságok olvasása<br>Más felhasználók és kapcsolatok megjelenített nevének, e-mail-címének, bejelentkezési nevének, fényképének, egyszerű felhasználónevének, valamint felhasználótípus-tulajdonságainak olvasása<br>Saját jelszó módosítása
Csoportok | Biztonsági csoportok létrehozása<br>Office 365-csoportok létrehozása<br>Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Felvett csoport rejtett Office 365-csoporttagságainak olvasása<br>Saját csoportok tulajdonságainak, tulajdonosi jogainak és tagságának kezelése<br>Vendégek hozzáadása saját csoportokhoz<br>Dinamikus tagsági beállítások kezelése<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása | Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Csatlakozott csoport rejtett Office 365-csoporttagságainak olvasása<br>Saját csoportok kezelése<br>Vendégek hozzáadása saját csoportokhoz (ha engedélyezett)<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása 
Alkalmazások | Új alkalmazás regisztrálása (létrehozása)<br>Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<br>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<br>Felhasználó alkalmazásjelszavának létrehozása vagy törlése<br>Saját alkalmazások törlése<br>Saját alkalmazások visszaállítása | Regisztrált és vállalati alkalmazások tulajdonságainak olvasása<br>Saját alkalmazások tulajdonságainak, hozzárendeléseinek és hitelesítő adatainak kezelése<br>Saját alkalmazások törlése<br>Saját alkalmazások visszaállítása
Eszközök | Minden eszköztulajdonság olvasása<br>Saját eszközök minden tulajdonságának olvasása<br> | Nincs engedély<br>Saját eszközök törlése<br>
Címtár | Minden vállalati adat olvasása<br>Minden tartomány olvasása<br>Minden partnerszerződés olvasása | Megjelenített név és ellenőrzött tartományok olvasása
Szerepkörök és hatókörök | Minden rendszergazdai szerepkör és tagság olvasása<br>Felügyeleti egységek minden tulajdonságának és tagságának olvasása | Nincs engedély 
Előfizetések | Minden előfizetés olvasása<br>Szolgáltatáscsomag-tag engedélyezése | Nincs engedély
Házirendek | Szabályzatok minden tulajdonságának olvasása<br>Saját szabályzat minden tulajdonságának kezelése | Nincs engedély

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Tagfelhasználók alapértelmezett engedélyeinek korlátozása

A tagfelhasználók alapértelmezett engedélyeinek korlátozására az alábbi lehetőségek állnak rendelkezésre.

Engedély | Magyarázat beállítása
---------- | ------------
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói fiókok rendszergazdái továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Office 365-csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre Office 365-csoportokat. Ha e beállítás értéke Néhány, akkor egyes kiválasztott felhasználók létrehozhatnak Office 365-csoportokat. A globális rendszergazdák és a felhasználói fiókok rendszergazdái továbbra is létrehozhatnak majd Office 365-csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ezt a beállítást nem megakadályozza, hogy a felhasználók hozzáférését az Azure Active Directoryban.
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha e beállítás értéke $false, akkor a nem rendszergazda felhasználók nem olvashatják a címtárban található felhasználói adatokat. Ez nem jelenti azt, hogy ne olvashatnák egyéb Microsoft-szolgáltatások (például az Exchange Online) felhasználói adatait. Ez a beállítás speciális esetekben alkalmazandó, ajánlott értéke: $false.

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek

A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként kezelheti a csoport tulajdonságait, például annak nevét és tagságát. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól és a felhasználói fiókok rendszergazdáitól eltérően a tulajdonosok csak saját csoportjaikat kezelhetik. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>További lépések

* Az Azure AD-rendszergazdai szerepkörök hozzárendelése kapcsolatos további információkért lásd: [felhasználó az Azure Active Directory rendszergazdai szerepkörök hozzárendelése](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)
