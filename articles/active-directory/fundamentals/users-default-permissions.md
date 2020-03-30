---
title: Alapértelmezett felhasználói engedélyek – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directoryban elérhető különböző felhasználói engedélyeket.
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
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366246"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Melyek az azure Active Directory alapértelmezett felhasználói engedélyei?
Az Azure Active Directoryban (Azure AD-ban) minden felhasználóhoz alapértelmezett engedélyek készlete tartozik. A felhasználó hozzáférése a felhasználó típusából, [szerepkör-hozzárendeléseiből](active-directory-users-assign-role-azure-portal.md)és az egyes objektumok tulajdonjogából áll. Ez a cikk az alapértelmezett engedélyeket ismerteti, valamint összehasonlítja a tag- és vendégfelhasználók alapértelmezett beállításait. Az alapértelmezett felhasználói engedélyek csak az Azure AD felhasználói beállításaiban módosíthatók.

## <a name="member-and-guest-users"></a>Tag- és vendégfelhasználók
A kapott alapértelmezett engedélyek készlete attól függ, hogy a felhasználó a bérlő natív tagja (tagfelhasználó), vagy a felhasználó tankönyvtárból térül át B2B együttműködési vendégként (vendégfelhasználó). Tekintse meg [az Azure AD B2B együttműködés?](../b2b/what-is-b2b.md) További információ a vendégfelhasználók hozzáadásáról.
* A tagfelhasználók alkalmazásokat regisztrálhatnak, kezelhetik saját profilképüket és mobiltelefonszámukat, módosíthatják jelszavukat, valamint meghívhatnak B2B-vendégfelhasználókat. Ezen kívül a felhasználók a címtár összes adatát olvashatják (néhány kivétellel). 
* A vendégfelhasználók korlátozott címtárengedélyekkel rendelkeznek. A vendégfelhasználók például csak a saját profiljuk adatait látják a bérlőn. A vendégfelhasználók azonban egy másik felhasználó egyszerű felhasználónevének vagy ObjectId azonosítójának megadásával adatokat kérdezhetnek le az adott felhasználóról. A vendégfelhasználó elolvashatja a csoportok tulajdonságait, amelyekhez tartoznak, beleértve a csoporttagságot, függetlenül attól, hogy a **Vendég felhasználók engedélyei korlátozottak.A** guest user can read properties of groups they belong of, including group membership, regardless of the Guest users permissions are limited setting. A vendég nem tekintheti meg a többi bérlőobjektum adatait.

A vendégekre vonatkozó alapértelmezett engedélyek korlátozásokat tartalmaznak. A vendégfelhasználókhoz rendszergazdai szerepkör rendelhető, amelynek birtokában az adott szerepkör teljes körű olvasási és írási engedélyeivel rendelkeznek. Még egy további korlátozás áll rendelkezésre a vendégfelhasználók számára, ez pedig a más felhasználók meghívásának lehetősége. Ha a **Vendégek meghívót küldhetnek** beállítás értéke **Nem**, akkor a vendégfelhasználók nem hívhatnak meg más vendégeket. További információ: [Meghívók delegálása B2B-együttműködéshez](../b2b/delegate-invitations.md). Ha azt szeretné, hogy a vendégfelhasználók a tagfelhasználók alapértelmezett engedélyeivel azonos engedélyeket kapjanak, akkor **A vendégfelhasználók engedélyei korlátozottak** beállítás számára adjon meg **Nem** értéket. E beállítás alkalmazása esetén a vendégfelhasználók megkapják a tagfelhasználók minden alapértelmezett engedélyét, továbbá a vendégfelhasználók felvehetők lesznek a rendszergazdai szerepkörökbe is.

## <a name="compare-member-and-guest-default-permissions"></a>Tagok és vendégek alapértelmezett engedélyeinek összehasonlítása

**Terület** | **Tagfelhasználók engedélyei** | **Vendégfelhasználók engedélyei**
------------ | --------- | ----------
Felhasználók és kapcsolatok | Felhasználók és kapcsolatok összes nyilvános tulajdonságának olvasása<br>Vendégek meghívása<br>Saját jelszó módosítása<br>Saját mobiltelefonszám kezelése<br>Saját fénykép kezelése<br>Saját frissítési jogkivonatok érvénytelenítése | Saját tulajdonságok olvasása<br>Más felhasználók és névjegyek megjelenítendő nevének, e-mail-címének, bejelentkezési nevének, fényképének, egyszerű felhasználónevének és felhasználótípus-tulajdonságainak olvasása<br>Saját jelszó módosítása
Csoportok | Biztonsági csoportok létrehozása<br>Office 365-csoportok létrehozása<br>Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Felvett csoport rejtett Office 365-csoporttagságainak olvasása<br>A felhasználó tulajdonában lévő csoportok tulajdonságainak, tulajdonjogának és tagságának kezelése<br>Vendégek hozzáadása saját csoportokhoz<br>Dinamikus tagsági beállítások kezelése<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása | Minden csoporttulajdonság olvasása<br>Nem rejtett csoporttagságok olvasása<br>Csatlakozott csoport rejtett Office 365-csoporttagságainak olvasása<br>Saját csoportok kezelése<br>Vendégek hozzáadása saját csoportokhoz (ha engedélyezett)<br>Saját csoportok törlése<br>Saját Office 365-csoportok visszaállítása<br>Olvassa el azoknak a csoportoknak a tulajdonságait, amelyekhez tartoznak, beleértve a tagságot is.
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
A felhasználók regisztrálhatják az alkalmazást | Ha a beállítás tikulya beállítással megakadályozza, hogy a felhasználók alkalmazásregisztrációkat hozzanak létre. Ezután adott személyeknek visszaadható, ha hozzáadja őket az Alkalmazásfejlesztői szerepkörhöz.
A felhasználók kapcsolhassák össze a munkahelyi vagy iskolai fiókot a LinkedIn-nel | Ha ezt a beállítást Nem-re állítja, a felhasználók nem kapcsolják össze munkahelyi vagy iskolai fiókjukat a LinkedIn-fiókjukkal. További információ: [LinkedIn-fiókkapcsolatok adatmegosztása és hozzájárulása.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)
Biztonsági csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre biztonsági csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Office 365-csoportok létrehozásának képessége | Ha e beállítás értéke Nem, akkor a felhasználók nem hozhatnak létre Office 365-csoportokat. Ha e beállítás értéke Néhány, akkor egyes kiválasztott felhasználók létrehozhatnak Office 365-csoportokat. A globális rendszergazdák és a felhasználói rendszergazdák továbbra is létrehozhatnak Office 365-csoportokat. További tudnivalók: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ha ezt a beállítást nem reked, a nem rendszergazdák az Azure AD felügyeleti portál on-és Azure AD-erőforrások olvasása és kezelése. Igen, korlátozza az összes nem rendszergazda i access bármilyen Azure AD-adatok a felügyeleti portálon. Fontos megjegyezni: ez a beállítás nem korlátozza az Azure AD-adatokhoz való hozzáférést a PowerShell vagy más ügyfelek, például a Visual Studio használatával. Ha az Igen, egy adott nem rendszergazdai felhasználó számára lehetővé teszi az Azure AD felügyeleti portál használatát, rendeljen hozzá bármilyen felügyeleti szerepkört, például a címtárolvasók szerepkört. Ez a szerepkör lehetővé teszi az alapvető címtáradatok olvasását, amelyekkel a tagfelhasználók alapértelmezés szerint rendelkeznek (a vendégek és a szolgáltatástagok nem).
Egyéb felhasználók olvasásának képessége | Ez a beállítás csak a PowerShellben érhető el. Ha ezt a jelzőt $false, akkor a nem rendszergazdák nem olvassák a felhasználói adatokat a címtárból. Ez a jelző nem akadályozza meg a felhasználói adatok olvasását más Microsoft-szolgáltatásokban, például az Exchange Online-ban. Ez a beállítás különleges körülmények esetén van, és a jelző beállítása $false nem ajánlott.

## <a name="object-ownership"></a>Objektumok tulajdonjoga

### <a name="application-registration-owner-permissions"></a>Alkalmazásregisztrációs tulajdonosi engedélyek
Az alkalmazást regisztráló felhasználó automatikusan az adott alkalmazás tulajdonosává válik. Tulajdonosként kezelheti az alkalmazás metaadatait, például annak nevét és az előírt engedélyeket. Ezen kívül kezelheti az alkalmazás nem bérlőspecifikus konfigurációját, például az egyszeri bejelentkezést és a felhasználó-hozzárendelést. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdáktól eltérően a tulajdonosok csak saját alkalmazásaikat kezelhetik.

### <a name="enterprise-application-owner-permissions"></a>Vállalati alkalmazástulajdonosi engedélyek
Amikor egy felhasználó új vállalati alkalmazást ad hozzá, a rendszer automatikusan hozzáadja tulajdonosként. Tulajdonosként kezelhetik az alkalmazás bérlő-specifikus konfigurációját, például az egyszeri szolgáltatás konfigurációját, a kiépítést és a felhasználói hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdákkal ellentétben a tulajdonosok csak a saját alkalmazásaikat kezelhetik.

### <a name="group-owner-permissions"></a>Csoporttulajdonosi engedélyek
A csoportot létrehozó felhasználó automatikusan az adott csoport tulajdonosává válik. Tulajdonosként kezelhetik a csoport tulajdonságait, például a nevet, valamint kezelhetik a csoporttagságot. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális és a felhasználói rendszergazdákkal ellentétben a tulajdonosok csak a saját csoportjaikat kezelhetik. A csoporttulajdonosok hozzárendelésével kapcsolatban lásd: [Csoporttulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Tulajdonosi engedélyek
Az alábbi táblázatok ismertetik az Azure Active Directory-tagok felhasználóinak adott engedélyeket a tulajdonában lévő objektumok felett. A felhasználó csak a saját objektumaihoz rendelkezik ilyen engedélyekkel.

#### <a name="owned-application-registrations"></a>Saját alkalmazásregisztrációk
A felhasználók a következő műveleteket hajthatják végre a saját alkalmazásregisztrációkon.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/audience/update | Frissítse az applications.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Frissítse a applications.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Az Azure Active Directory ban lévő alkalmazások alapvető tulajdonságainak frissítése. |
| microsoft.directory/applications/credentials/update | Frissítse az applications.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/delete | Alkalmazások törlése az Azure Active Directoryban. |
| microsoft.directory/applications/owners/update | Frissítse az applications.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Frissítse az applications.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | Frissítse az applications.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/restore | Alkalmazások visszaállítása az Azure Active Directoryban. |

#### <a name="owned-enterprise-applications"></a>Saját vállalati alkalmazások
A felhasználók a következő műveleteket hajthatják végre a saját vállalati alkalmazásokon. A vállalati alkalmazás egyszerű szolgáltatásból, egy vagy több alkalmazásházirendből és néha egy alkalmazásobjektumból áll, amely ugyanabban a bérlőben található, mint a szolgáltatásnév.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/policies/basic/update | Az Azure Active Directory szabályzatai alapvető tulajdonságainak frissítése. |
| microsoft.directory/policies/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/owners/update | Frissítse a policies.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | Frissítse a servicePrincipals.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a ServicePrincipals.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | Az Azure Active Directory ban lévő szolgáltatásfővéi törlése. |
| microsoft.directory/servicePrincipals/owners/update | Frissítse a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Frissítse a ServicePrincipals.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |

#### <a name="owned-devices"></a>Saját eszközök
A felhasználók a következő műveleteket hajthatják végre a tulajdonában lévő eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/devices/disable | Letilthatja az eszközöket az Azure Active Directoryban. |

#### <a name="owned-groups"></a>Saját tulajdonú csoportok
A felhasználók a következő műveleteket hajthatják végre a saját tulajdonú csoportokon.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Frissítse a groups.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/basic/update | Az Azure Active Directory csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/delete | Csoportok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/dynamicMembershipRule/update | Frissítse a groups.dynamicMembershipRule tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot az Azure Active Directoryban. |

## <a name="next-steps"></a>További lépések

* Ha többet szeretne tudni arról, hogyan rendelhet hozzá Azure AD-rendszergazdai szerepköröket, olvassa el [a Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directoryban című témakört.](active-directory-users-assign-role-azure-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](add-users-azure-active-directory.md)
