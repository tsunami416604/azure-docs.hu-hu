---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory | Microsoft Docs
description: További információ a Azure Active Directoryban elérhető különböző felhasználói engedélyekről.
services: active-directory
author: ajburnle
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
ms.openlocfilehash: bae802d8aa9378155bcca0713992a8cc041ea1a9
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799021"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Melyek a Azure Active Directory alapértelmezett felhasználói engedélyei?
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
Munkahelyi vagy iskolai fióknak a LinkedIn használatával való összekapcsolásának engedélyezése a felhasználók számára | Ha ezt a beállítást választja, a nem akadályozza meg, hogy a felhasználók a saját LinkedIn-fiókjával csatlakozzanak a munkahelyi vagy iskolai fiókjához. További információ: a [LinkedIn-fiókok kapcsolatainak megosztása és beleegyezett](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Office 365-csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre Office 365-csoportokat. Ha e beállítás értéke Néhány, akkor egyes kiválasztott felhasználók létrehozhatnak Office 365-csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak Office 365-csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ha ezt a beállítást nem teszi lehetővé, a nem rendszergazdák az Azure ad felügyeleti portálon keresztül olvashatják és kezelhetik az Azure AD-erőforrásokat. Igen – korlátozza, hogy az összes nem rendszergazda hozzáférjen az összes Azure AD-adatokhoz a felügyeleti portálon. Fontos megjegyezni: Ez a beállítás nem korlátozza az Azure AD-szolgáltatásokhoz való hozzáférést a PowerShell vagy más ügyfelek, például a Visual Studio használatával. Ha az Igen értékre van állítva, akkor egy adott nem rendszergazda felhasználó számára az Azure AD felügyeleti portál használatával bármilyen rendszergazdai szerepkört hozzárendelhet, például a Directory-olvasók szerepkört. Ez a szerepkör lehetővé teszi az alapszintű címtáradatok olvasását, amelyekhez a felhasználók alapértelmezés szerint rendelkeznek (a vendégek és az egyszerű szolgáltatások nem).
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
| Microsoft. Directory/alkalmazások/célközönség/frissítés | Az Applications. célközönség tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/hitelesítés/frissítés | Az Applications. Authentication tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/alapszintű/frissítés | Azure Active Directory alkalmazásokban lévő alkalmazások alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés | Az Applications. hitelesítőadatok tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/törlés | Alkalmazások törlése Azure Active Directory. |
| Microsoft. Directory/alkalmazások/tulajdonosok/frissítés | Az Applications. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/engedélyek/frissítés | Az Applications. permissions tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/házirendek/frissítés | Az Applications. policies tulajdonság frissítése Azure Active Directory. |
| Microsoft. Directory/alkalmazások/visszaállítás | Alkalmazások visszaállítása a Azure Active Directoryban. |

#### <a name="owned-enterprise-applications"></a>Birtokolt vállalati alkalmazások
A felhasználók a következő műveleteket hajthatják végre a tulajdonában lévő vállalati alkalmazásokban. A vállalati alkalmazások egyszerű szolgáltatásnév, egy vagy több alkalmazás-házirend, valamint esetenként egy, az egyszerű szolgáltatással azonos bérlőn lévő alkalmazás-objektum.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/házirendek/alapszintű/frissítés | Azure Active Directory szabályzatok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/házirendek/törlés | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/tulajdonosok/frissítés | A policies. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Frissítse a servicePrincipals. appRoleAssignedTo tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A servicePrincipals. célközönség tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Frissítse a servicePrincipals. Authentication tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/Basic/Update | A servicePrincipals alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | Frissítse a servicePrincipals. hitelesítőadatok tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/delete | Azure Active Directory servicePrincipals törlése. |
| Microsoft. Directory/servicePrincipals/owners/Update | A servicePrincipals. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/engedélyek/frissítés | Azure Active Directory servicePrincipals. permissions tulajdonságának frissítése. |
| Microsoft. Directory/servicePrincipals/házirendek/frissítés | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |

#### <a name="owned-devices"></a>Tulajdonban lévő eszközök
A felhasználók a következő műveleteket hajthatják végre a tulajdonban lévő eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/eszközök/bitLockerRecoveryKeys/olvasás | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/letiltás | Azure Active Directory eszközök letiltása. |

#### <a name="owned-groups"></a>Birtokolt csoportok
A felhasználók a következő műveleteket hajthatják végre a tulajdonos csoportoknál.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/appRoleAssignments/Update | A groups. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | Azure Active Directoryban lévő csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/törlés | Csoportok törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/dynamicMembershipRule/Update | A groups. dynamicMembershipRule tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | Csoportok visszaállítása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/beállítások/frissítés | A groups. Settings tulajdonság frissítése Azure Active Directoryban. |

## <a name="next-steps"></a>További lépések

* További információ az Azure AD-rendszergazdai szerepkörök hozzárendeléséről: [felhasználó társítása rendszergazdai szerepkörökhöz Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)
