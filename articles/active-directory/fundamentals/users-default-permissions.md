---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory |} A Microsoft Docs
description: További információ a különböző felhasználói engedélyek elérhető az Azure Active Directoryban.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561607"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Mik azok az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználók hozzáférése a felhasználó típusától, a [szerepkörük hozzárendeléseitől](active-directory-users-assign-role-azure-portal.md), valamint az egyes objektumok tulajdonosainak. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait. Az alapértelmezett felhasználói engedélyeket csak az Azure AD felhasználói beállításaiban lehet módosítani.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
Az alapértelmezett engedélyek készlete attól függ, hogy a felhasználó a bérlő (tag) natív tagja-e, vagy ha a felhasználót VÁLLALATKÖZI együttműködési vendégként (vendég felhasználóként) átveszik egy másik címtárból. Lásd: [Mi az az Azure ad B2B Collaboration?](../b2b/what-is-b2b.md) további információ a vendég felhasználók hozzáadásáról.
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* A vendég felhasználók korlátozott címtárbeli engedélyekkel rendelkeznek. A vendégfelhasználók például csak a saját profiljuk adatait látják a bérlőn. A vendégfelhasználók azonban egy másik felhasználó egyszerű felhasználónevének vagy ObjectId azonosítójának megadásával adatokat kérdezhetnek le az adott felhasználóról. A vendég felhasználók beolvashatják a csoportokhoz tartozó csoportok tulajdonságait, beleértve a csoporttagság is, függetlenül attól, hogy a **vendég felhasználói engedélyei korlátozottak** -e. A vendég nem tekintheti meg a többi bérlői objektum információit.

A vendégekre vonatkozó alapértelmezett engedélyek korlátozásokat tartalmaznak. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. Még egy további korlátozás áll rendelkezésre a vendégfelhasználók számára, ez pedig a más felhasználók meghívásának lehetősége. Ha a **Vendégek meghívót küldhetnek** beállítás értéke **Nem**, akkor a vendégfelhasználók nem hívhatnak meg más vendégeket. További információ: [Meghívók delegálása B2B-együttműködéshez](../b2b/delegate-invitations.md). Ha azt szeretné, hogy a vendégfelhasználók a tagfelhasználók alapértelmezett engedélyeivel azonos engedélyeket kapjanak, akkor **A vendégfelhasználók engedélyei korlátozottak** beállítás számára adjon meg **Nem** értéket. E beállítás alkalmazása esetén a vendégfelhasználók megkapják a tagfelhasználók minden alapértelmezett engedélyét, továbbá a vendégfelhasználók felvehetők lesznek a rendszergazdai szerepkörökbe is.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Vendégfelhasználók engedélyei**
------------ | --------- | ----------
Felhasználók és kapcsolatok | Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása<br>Vendégek meghívása<br>Saját jelszó módosítása<br>Saját mobiltelefonszám kezelése<br>Saját fénykép kezelése<br>Saját frissítési jogkivonatok érvénytelenítése | Saját tulajdonságok olvasása<br>A megjelenítendő név, az e-mail cím, a bejelentkezési név, a fénykép, a felhasználó egyszerű neve és a felhasználói típus tulajdonságainak olvasása más felhasználók és névjegyek számára<br>Saját jelszó módosítása
Csoportok | Biztonsági csoportok létrehozása<br>Office 365-csoportok létrehozása<br>Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Felvett csoport rejtett Office 365-csoporttagságainak olvasása<br>A felhasználó tulajdonában lévő csoportok tulajdonságainak, tulajdonosának és tagságának kezelése<br>Vendégek hozzáadása saját csoportokhoz<br>Dinamikus tagsági beállítások kezelése<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása | Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Csatlakozott csoport rejtett Office 365-csoporttagságainak olvasása<br>Saját csoportok kezelése<br>Vendégek hozzáadása saját csoportokhoz (ha engedélyezett)<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása<br>A csoporthoz tartozó csoportok tulajdonságainak olvasása, beleértve a tagságot is.
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
A felhasználók regisztrálhatják az alkalmazást | Ha ezt a beállítást állítja be, a nem megakadályozza, hogy a felhasználók alkalmazás-regisztrációt hozzanak létre. Az alkalmazás fejlesztői szerepkörhöz való hozzáadásával lehetősége lesz visszaadni az adott személyeknek.
Munkahelyi vagy iskolai fiók és a LinkedIn összekapcsolásának engedélyezése a felhasználóknak | Ha ezt a beállítást választja, a nem akadályozza meg, hogy a felhasználók a saját LinkedIn-fiókjával csatlakozzanak a munkahelyi vagy iskolai fiókjához. További információ: a [LinkedIn-fiókok kapcsolatainak megosztása és beleegyezett](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Office 365-csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre Office 365-csoportokat. Ha e beállítás értéke Néhány, akkor egyes kiválasztott felhasználók létrehozhatnak Office 365-csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak Office 365-csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ha ezt a beállítást választja, az Igen beállítás megadásával megakadályozható, hogy a felhasználók csak Azure Portalon férhessenek hozzá Azure Active Directory
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha ezt a jelzőt úgy állítja be, hogy $false megakadályozza, hogy minden nem rendszergazda beolvassa a felhasználói adatokat a címtárból. Ez a jelző nem akadályozza meg a felhasználói adatok olvasását más Microsoft-szolgáltatásokban, például az Exchange Online-ban. Ez a beállítás különleges körülményeket jelent, és a jelzőt nem ajánlott $falsere beállítani.

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

### <a name="enterprise-application-owner-permissions"></a>Vállalati alkalmazás tulajdonosának engedélyei
Amikor egy felhasználó új vállalati alkalmazást ad hozzá, a rendszer automatikusan tulajdonosként adja hozzá őket. Tulajdonosként kezelheti az alkalmazás bérlő-specifikus konfigurációját, például az egyszeri bejelentkezés konfigurációját, a létesítést és a felhasználói hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak a saját maguk által felügyelt alkalmazásokat kezelhetik.

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek
A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként kezelheti a csoport tulajdonságait, például a nevet, valamint a csoporttagság kezelését. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdákkal és a felhasználói rendszergazdákkal ellentétben a tulajdonosok csak a saját maguk által kezelt csoportokat kezelhetik. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Tulajdonosi engedélyek
A következő táblázatok ismertetik, hogy Azure Active Directory tag felhasználói többek között rendelkeznek-e a tulajdonos objektumaival. A felhasználó csak az Ön tulajdonában lévő objektumokra vonatkozó engedélyekkel rendelkezik.

#### <a name="owned-application-registrations"></a>Tulajdonban lévő alkalmazások regisztrációi
A felhasználók a következő műveleteket hajthatják végre a tulajdonosi alkalmazások regisztrációján.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/audience/update | Az applications.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Az applications.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Az Application objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/credentials/update | Az applications.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/delete | Az Application objektumok törlése az Azure Active Directoryban. |
| microsoft.directory/applications/owners/update | Az applications.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Az applications.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/applications/restore | Az Application objektumok visszaállítása az Azure Active Directoryban. |

#### <a name="owned-enterprise-applications"></a>Birtokolt vállalati alkalmazások
A felhasználók a következő műveleteket hajthatják végre a tulajdonában lévő vállalati alkalmazásokban. A vállalati alkalmazások egyszerű szolgáltatásnév, egy vagy több alkalmazás-házirend, valamint esetenként egy, az egyszerű szolgáltatással azonos bérlőn lévő alkalmazás-objektum.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| Microsoft. Directory/házirendek/alapszintű/frissítés | A Policy objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. Directory/házirendek/törlés | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. Directory/házirendek/tulajdonosok/frissítés | A policies.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | A servicePrincipals törlése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |

#### <a name="owned-devices"></a>Tulajdonban lévő eszközök
A felhasználók a következő műveleteket hajthatják végre a tulajdonban lévő eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. Directory/eszközök/letiltás | Device objektumok letiltása az Azure Active Directoryban. |

#### <a name="owned-groups"></a>Birtokolt csoportok
A felhasználók a következő műveleteket hajthatják végre a tulajdonos csoportoknál.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.directory/groups/delete | A Group objektumok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/dynamicMembershipRule/update | A groups.dynamicMembershipRule tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |

## <a name="next-steps"></a>További lépések

* Az Azure AD-rendszergazdai szerepkörök hozzárendelése kapcsolatos további információkért lásd: [felhasználó az Azure Active Directory rendszergazdai szerepkörök hozzárendelése](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)
