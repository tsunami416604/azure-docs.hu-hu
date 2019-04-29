---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472220"
---
Az alábbiakban az Azure Active Directory (Azure AD) szolgáltatás használati és egyéb szolgáltatási korlátai olvashatóak.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtárhoz tartozhat tagként vagy vendégként.<br/>Egy felhasználó legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartománynév felvételére van lehetőség. Ha minden tartományt szeretne összevonni a helyszíni Active Directoryval, akkor címtáranként legfeljebb 450 felügyelt tartománynév adható hozzá. |
| Objektumok |<ul><li>Az Azure Active Directory ingyenes kiadásának felhasználói egyetlen címtárban legfeljebb 500 000 objektumot hozhatnak létre.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre. Ebbe a kvótába az aktív objektumok és a törölt, de visszaállítható objektumok is beleszámítanak. Visszaállítani csak a kevesebb mint 30 napja törölt objektumokat lehet. Azok a törölt objektumok, amelyeket már nem lehet visszaállítani, 30 napig negyed értékkel számítanak bele a kvótába. Érdemes [rendszergazdai szerepkört hozzárendelni](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) azokhoz a felhasználókhoz, akik munkájuk során valószínűleg rendszeresen túl fogják lépni a kvótát.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb 100 bővítményértéket lehet írni (az *összes* típust és az *összes* alkalmazást beleértve).</li><li>Csak a User, Group, TenantDetail, Device, Application és ServicePrincipal entitások bővíthetők String típusú vagy Binary típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API 1.21-es előzetes verziójában érhetők el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Egyetlen alkalmazásnak legfeljebb 100 felhasználó lehet a tulajdonosa. |
| Csoportok |<ul><li>Egyetlen csoportnak legfeljebb 100 felhasználó lehet a tulajdonosa.</li><li>Egyetlen csoportnak korlátlan számú objektum lehet a tagja.</li><li>Egy felhasználó egyszerre korlátlan számú csoport tagja lehet.</li><li>Az Azure AD Connecttel egy csoportnak legfeljebb 50 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li></ul> |
| Hozzáférési panel |<ul><li>A felhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja. Ez a Prémium szintű Azure AD- vagy Nagyvállalati mobilitási csomag-licenccel rendelkező felhasználókra vonatkozik.</li><li>A felhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe jeleníthető meg. Ez a korlátozás az Azure Active Directory ingyenes vagy alapszintű kiadásához tartozó licenccel rendelkező felhasználókra vonatkozik. Az alkalmazáscsempék közé tartozik például a Box, a Salesforce vagy a Dropbox. Ez a korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
| Rendszergazdai szerepkörök és engedélyek | <li>Csoportokat nem lehet tulajdonosként hozzáadni.<li>Csoportokat nem lehet szerepkörhöz rendelni.<li>Az alapértelmezett felhasználói engedélyek nem módosíthatók, kivéve a bérlők váltását, amely az Azure AD-ben érhető el felhasználói beállításként. |
