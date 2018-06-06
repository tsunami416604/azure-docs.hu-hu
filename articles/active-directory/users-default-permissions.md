---
title: Az Azure Active Directory engedélyeinek összehasonlítása |} Microsoft Docs
description: Hasonlítsa össze a tag, Vendég, alkalmazás tulajdonosa és csoportengedélyek tulajdonosa
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 01/29/2018
ms.author: curtand
ms.reviewer: vincesm
ms.openlocfilehash: 41f69a1dd4cbd4c0f6be0764efdbf0fb61ac3cb7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724428"
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Alapértelmezett felhasználói engedélyek az Azure Active Directoryban

Az Azure Active Directory (Azure AD) az összes felhasználó megkapja alapértelmezett engedélyek egy készletét. A felhasználó hozzáférési áll a felhasználó, típusát a [szerepkörtagságok](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal), és az egyes objektumok tulajdonosa. Ez a cikk ismerteti az alapértelmezett engedélyeket, és a tag és a Vendég felhasználói alapértelmezései összehasonlítását tartalmazza.

## <a name="member-and-guest-users"></a>A tag, és a vendég felhasználók
A kapott alapértelmezett engedélyek csoportja függ, ha a felhasználó tagja natív a bérlő (felhasználói), vagy ha a felhasználó a B2B együttműködés Vendég (Vendég felhasználó). További információ a B2B együttműködés: [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md) vendégfelhasználók kapcsolatos további információk). 
* Tag felhasználók alkalmazások regisztrálására, kezelheti a saját profil fénykép és a mobiltelefon száma, módosítania kell a saját jelszavát, és B2B vendégek meghívásához. Emellett a felhasználók olvashatják összes címtár-információ (néhány kivételtől eltekintve). 
* Az Azure AD B2B vendégfelhasználók korlátozta a directory-engedélyek. Például a vendégfelhasználók nem tud tallózni információkat a bérlőtől túl a saját profil adatait. Azonban az a Vendég felhasználói tudják lekérni az adatokat egy másik felhasználóval kapcsolatos azáltal, hogy a felhasználó egyszerű felhasználónév vagy objektumazonosító. A vendég nem bármely más bérlői objektumok például a csoportok és alkalmazások adatainak megtekintése.

Alapértelmezett engedélyek a vendégek is korlátozó alapértelmezés szerint. A vendégek rendszergazdai szerepköröket, amelyek adja meg számukra olvasási és írási engedéllyel a szerepkör található lehet hozzáadni. Nincs egy további korlátozás érhető el, a vendégek a meghívott más vendégek azon képességét. Beállítás **vendégek kérhetnek** való **nem** megakadályozza, hogy a vendégek más vendégek fióknevet. Lásd: [B2B együttműködés meghívók delegálása](active-directory-b2b-delegate-invitations.md) megtudhatja, hogyan. Vendégfelhasználók ugyanazokkal az engedélyekkel tag felhasználóként alapértelmezés szerint engedélyezheti a, állítsa **vendég felhasználók engedélyek korlátozottak** való **nem**. Ez a beállítás engedélyt ad az összes tag felhasználói vendégfelhasználók alapértelmezés szerint, valamint lehetővé teszik a vendégek rendszergazdai szerepkörökhöz lehet hozzáadni.

## <a name="compare-member-and-guest-default-permissions"></a>Hasonlítsa össze a tagnak és a Vendég alapértelmezett engedélyek

**Terület** | **Tag felhasználói engedélyek** | **A Vendég felhasználói jogok**
------------ | --------- | ----------
Felhasználók és névjegyek | Az összes nyilvános tulajdonság a felhasználók és névjegyek olvasása<br>A vendégek meghívása<br>Saját jelszó módosítása<br>Saját mobiltelefonszám kezelése<br>Saját fénykép kezelése<br>Saját frissítési jogkivonatokat érvénytelenné válnak | Saját tulajdonság olvasása<br>Megjelenített név, e-mail, bejelentkezési név, fénykép, egyszerű felhasználónevet és felhasználói típus tulajdonságainak más felhasználók és névjegyek olvasása<br>Saját jelszó módosítása
Csoportok | Biztonsági csoportok létrehozása<br>Office 365-csoportok létrehozása<br>A csoportok az összes tulajdonság olvasása<br>Olvassa el a nem rejtett csoporttagságok<br>Rejtett csatlakoztatott csoport Office 365-csoporttagságot olvasása<br>Tulajdonságait, a tulajdonosi és a saját tulajdonú csoportok tagságának kezelése<br>A vendégek tulajdonában lévő csoportok hozzáadása<br>Dinamikus csoporttagság beállításainak kezelése<br>Saját tulajdonú csoportok törlése<br>Állítsa vissza a tulajdonában lévő Office 365-csoportokat | A csoportok az összes tulajdonság olvasása<br>Olvassa el a nem rejtett csoporttagságok<br>A csatlakoztatott csoportok rejtett Office 365-csoporttagságot olvasása<br>Saját tulajdonú csoportok kezelése<br>Vegye fel saját tulajdonú csoportok vendégek (Ha engedélyezett)<br>Saját tulajdonú csoportok törlése<br>Állítsa vissza a tulajdonában lévő Office 365-csoportokat 
Alkalmazások | Regisztrálni (létrehozása) az új alkalmazás<br>Olvasási tulajdonságainak regisztrált és a vállalati alkalmazások<br>Alkalmazás tulajdonságai, a hozzárendelések és a hitelesítő adatokat a tulajdonában lévő alkalmazások kezelése<br>Hozzon létre vagy felhasználó jelszava alkalmazás törlése<br>Tulajdonában lévő alkalmazások törlése<br>Állítsa vissza a tulajdonában lévő alkalmazások | Olvasási tulajdonságainak regisztrált és a vállalati alkalmazások<br>Alkalmazás tulajdonságai, a hozzárendelések és a hitelesítő adatokat a tulajdonában lévő alkalmazások kezelése<br>Tulajdonában lévő alkalmazások törlése<br>Állítsa vissza a tulajdonában lévő alkalmazások
Eszközök | Az eszközök az összes tulajdonság olvasása<br>Összes tulajdonságának értéke tulajdonában lévő eszközök kezelése<br> | Nincs engedély<br>Saját tulajdonú eszközök törlése<br>
Címtár | Olvassa el az összes vállalati információk<br>Olvassa el az összes tartomány<br>Olvassa el a partner minden szerződés | Olvassa el a megjelenített név, és ellenőrzött tartományok
Szerepkörök és hatókörök | Rendszergazdai szerepkörök és a csoporttagságokat olvasása<br>Olvassa el az összes tulajdonságát és adminisztratív egységei tagsága | Nincs engedély 
Előfizetések | Olvassa el az összes előfizetés<br>Szolgáltatás terv-tag engedélyezése | Nincs engedély
Házirendek | A házirendek az összes tulajdonság olvasása<br>Saját tulajdonú házirend minden tulajdonságainak kezelése | Nincs engedély

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Az alapértelmezett engedélyek tag felhasználók korlátozása


Az alapértelmezett tag felhasználók korlátozhatja a következőképpen engedélyeit. További információkért lásd: [alkalmazások, engedélyek és az Azure Active Directoryban hozzájárulási](active-directory-apps-permissions-consent.md).

Engedély | MAGYARÁZAT beállítása
---------- | ------------
Biztonsági csoportok létrehozásának lehetősége | Ezt a beállítást nem nem engedélyezi a felhasználóknak a biztonsági csoportok létrehozása. Globális rendszergazdák és a felhasználó a rendszergazdák továbbra is létrehozhatnak biztonsági csoportokat. Lásd: [csoport beállításainak konfigurálása az Azure Active Directory parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md) megtudhatja, hogyan.
Office 365-csoportok létrehozásának lehetősége | Ezt a beállítást nem megakadályozza, hogy a felhasználók az Office 365-csoportok létrehozása. Ezt a beállítást egyes lehetővé teszi, hogy egy Office 365-csoportok létrehozása felhasználók kiválasztott csoportja. Globális rendszergazdák és a felhasználó a rendszergazdák is Office 365-csoportok létrehozásához. Lásd: [csoport beállításainak konfigurálása az Azure Active Directory parancsmagok](active-directory-accessmanagement-groups-settings-cmdlets.md) megtudhatja, hogyan.
Alkalmazások engedélyezése lehetőséget | Ezt a beállítást nem megakadályozza, hogy felhasználók hozzájárul ahhoz, hogy az alkalmazások. Globális rendszergazdák továbbra is tudnak járul hozzá az alkalmazásokhoz. További információkért lásd: [alkalmazások, engedélyek és az Azure Active Directoryban hozzájárulási](active-directory-apps-permissions-consent.md).
Gyűjteményelem alkalmazások hozzáadása a hozzáférési panel képessége | Ezt a beállítást nem megakadályozza, hogy felhasználók gyűjteménye alkalmazások felvétele a hozzáférési Panel.
Regisztrálása (létrehozás) alkalmazások | Ezt a beállítást nem megakadályozza, hogy nem rendszergazda-alkalmazások létrehozása. Globális rendszergazdák is képesek alkalmazásokat hozhat létre. További információkért lásd: [alkalmazások, engedélyek és az Azure Active Directoryban hozzájárulási](active-directory-apps-permissions-consent.md).
Rendszergazdák és a Vendég meghívó a szerepkörben levő felhasználók kérhetnek a vendégek | Ezt a beállítást nem a minden felhasználó hívja fel a vendégek nem. Tekintse meg az alapértelmezett tag felhasználók engedélyeinek konfigurálásához. További információkért lásd: [alkalmazások, engedélyek és az Azure Active Directoryban hozzájárulási](active-directory-apps-permissions-consent.md).
Tagok kérhetnek a vendégek | Beállítás a nem a felhasználó hívja fel a vendégek nem. Globális rendszergazdák, a felhasználó a rendszergazdák és a Vendég Inviters is a vendégek meghívott. További információkért lásd: [alkalmazások, engedélyek és az Azure Active Directoryban hozzájárulási](active-directory-apps-permissions-consent.md).
Az Azure AD felügyeleti portálhoz való hozzáférés korlátozása | Ezt a beállítást nem felhasználók nem tudnak hozzáférni az Azure Active Directory portálon.
Olvasás a más felhasználók | A beállítás csak akkor érhető el a PowerShell. $False értékre állítja megakadályozza, hogy az összes nem rendszergazda felhasználói információkért olvasva a könyvtárból. Ez nem akadályozza meg a olvasási felhasználói információkért az egyéb Microsoft-szolgáltatásokat, például az Exchange online-hoz. Célja, hogy ez a beállítás a különleges körülmények között, és a $false értékre ez nem ajánlott beállítás.

## <a name="object-ownership"></a>Objektum tulajdonjogának

### <a name="application-registration-owner-permissions"></a>Alkalmazásengedélyek regisztrációs tulajdonosa
A felhasználó regisztrál egy alkalmazás, automatikusan hozzáadja az alkalmazáshoz tulajdonos. Tulajdonos az alkalmazás metaadatai kezelésére, például a nevét, és az engedélyek kér az alkalmazás. A bérlői konfigurációs az alkalmazás, például az SSO konfigurációs és a felhasználó hozzárendelések is kezelhetik. Egy olyan tulajdonost is hozzáadhat és eltávolíthat más tulajdonosai. Globális rendszergazdák eltérően tulajdonosok csak kezelhetik saját alkalmazások. Alkalmazás regisztrálása tulajdonos rendeléséhez lásd: [Azure Active Directory-alkalmazás regisztrációja](active-directory-app-registration.md).

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Tulajdonos csoportengedélyek

Amikor egy felhasználó létrehoz egy csoportot, azok automatikusan hozzá lesznek adva a csoport tulajdonosa. Tulajdonos akkor lehet például a nevet a csoport tulajdonságainak kezelése, valamint tagságának kezelésére. Egy olyan tulajdonost is hozzáadhat és eltávolíthat más tulajdonosai. Ellentétben a globális rendszergazdák és a felhasználó a rendszergazdák a tulajdonosok csak kezelése saját csoportok. A csoport tulajdonosa rendeléséhez lásd: [egy csoport tulajdonosainak kezelése](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>További lépések

* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../billing-add-change-azure-subscription-administrator.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Hogyan Azure Active Directory vonatkozik-e az Azure-előfizetéshez további információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directoryval](active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](active-directory-create-users.md)
