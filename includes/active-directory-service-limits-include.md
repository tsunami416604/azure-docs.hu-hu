---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 067280cdad85f59106bce5ff214e2fa9eddf3b71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133179"
---
Az alábbiakban az Azure Active Directory (Azure AD) szolgáltatás használati és egyéb szolgáltatási korlátai olvashatóak.

| Category | Korlátok |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtárhoz tartozhat tagként vagy vendégként.<br/>Egy felhasználó legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartománynév felvételére van lehetőség. Ha minden tartományt szeretne összevonni a helyszíni Active Directoryval, akkor címtáranként legfeljebb 450 felügyelt tartománynév adható hozzá. |
| Objektumok |<ul><li>Az Azure Active Directory ingyenes kiadásának felhasználói alapértelmezés szerint egyetlen címtárban legfeljebb 50 000 objektumot hozhatnak létre. Ha van legalább egy ellenőrzött tartománya, az Azure AD alapértelmezett címtár-szolgáltatási kvótája kibővül 300 000 objektumra. </li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre. Ebbe a kvótába az aktív objektumok és a törölt, de visszaállítható objektumok is beleszámítanak. Visszaállítani csak a kevesebb mint 30 napja törölt objektumokat lehet. Azok a törölt objektumok, amelyeket már nem lehet visszaállítani, 30 napig negyed értékkel számítanak bele a kvótába. Érdemes [rendszergazdai szerepkört hozzárendelni](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) azokhoz a felhasználókhoz, akik munkájuk során valószínűleg rendszeresen túl fogják lépni a kvótát.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb 100 bővítményértéket lehet írni (az *összes* típust és az *összes* alkalmazást beleértve).</li><li>Csak a User, Group, TenantDetail, Device, Application és ServicePrincipal entitások bővíthetők String típusú vagy Binary típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API 1.21-es előzetes verziójában érhetők el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Egyetlen alkalmazásnak legfeljebb 100 felhasználó lehet a tulajdonosa. |
| Csoportok |<ul><li>Egyetlen csoportnak legfeljebb 100 felhasználó lehet a tulajdonosa.</li><li>Egyetlen csoportnak korlátlan számú objektum lehet a tagja.</li><li>Egy felhasználó egyszerre korlátlan számú csoport tagja lehet.</li><li>Az Azure AD Connecttel egy csoportnak legfeljebb 50 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li></ul> |
| Alkalmazásproxy | <ul><li>Legfeljebb 500 tranzakció másodpercenként App Proxy-alkalmazások</li><li>Legfeljebb 750 tranzakció / másodperc, a bérlő számára</li></ul><br/>Egy tranzakció egy egyetlen http-kérés és a egy egyedi erőforráshoz tartozó válasz meghatározása. Szabályozott, amikor az ügyfelek 429 (túl sok kérés) választ fog kapni. |
| Hozzáférési panel |<ul><li>A felhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja. Ez a Prémium szintű Azure AD- vagy Nagyvállalati mobilitási csomag-licenccel rendelkező felhasználókra vonatkozik.</li><li>A felhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe jeleníthető meg. Ez a korlátozás az Azure Active Directory ingyenes vagy alapszintű kiadásához tartozó licenccel rendelkező felhasználókra vonatkozik. Az alkalmazáscsempék közé tartozik például a Box, a Salesforce vagy a Dropbox. Ez a korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
| Rendszergazdai szerepkörök és engedélyek | <ul><li>A csoport nem lehet hozzáadni egy [tulajdonosa](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>A csoport nem lehet hozzárendelni egy [szerepkör](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A többi felhasználó címtáradatok olvasása, hogy a felhasználók nem korlátozhatók kívül minden adatát (nem javasolt) minden nem rendszergazdai felhasználók elérésének letiltása a bérlői szintű kapcsolót. További információ az alapértelmezett engedélyek [Itt](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Akár 15 percig is eltarthat, vagy aláírási out/bejelentkezés előtt rendszergazdai szerepkör tagsága hozzáadások és által okozott hibával találkozzanak érvénybe léptetéséhez.</li></ul> |
