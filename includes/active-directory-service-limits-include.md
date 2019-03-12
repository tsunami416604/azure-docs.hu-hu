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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554615"
---
Az alábbiakban a használati korlátozásokat és egyéb szolgáltatási korlátai, az Azure Active Directory (Azure AD) szolgáltatás.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtár vagy egy Vendég is tartozhat.<br/>Egy-egy felhasználóhoz legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartományneveket adhat hozzá. Beállítása minden a tartománynak a helyszíni Active Directoryval az összevonáshoz, ha minden könyvtár nem több mint 450 tartományneveket adhat hozzá. |
| Objektumok |<ul><li>Egy legfeljebb 500 000 objektumot az Azure Active Directory ingyenes kiadásának felhasználói egyetlen címtárban hozható létre.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre. Aktív objektumok és a törölt objektumok visszaállítása a kvóta felszámítja számára elérhető. Csak törölni a törölt objektumok visszaállítása kevesebb mint 30 napja érhetők el. Törölt objektumok, amelyek már nem érhető el ez a kvóta értéken felszámítja visszaállítása negyedére 30 napig. Talán [rendszergazdai szerepkör hozzárendelése](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) valószínűleg többször meghaladják majd rendszeres munkájuk során a kvóta nem rendszergazdai felhasználók.</li></ul> |
| Sémabővítmények |<ul><li>Karakterlánc-típusú bővítmények legfeljebb 256 karakter lehet. </li><li>Bináris típusú bővítmények korlátozva, 256 bájt.</li><li>Csak 100 bővítmény keresztben értékek *összes* típusok és *összes* alkalmazások, csak írható egyetlen objektumhoz sem.</li><li>Egyetlen felhasználó, csoport, TenantDetail, eszköz, alkalmazás és ServicePrincipal entitások bővíthetőek típusú karakterlánc vagy bináris típusú egyértékű attribútumokkal.</li><li>Sémabővítmények csak a Graph API verzió 1.21 előzetes verzióban érhetők el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Legfeljebb 100 felhasználó lehet a tulajdonosa egy alkalmazás. |
| Csoportok |<ul><li>Legfeljebb 100 felhasználó lehet a tulajdonosa egyetlen csoportnak.</li><li>Tetszőleges számú objektum lehet egyetlen csoport tagjai.</li><li>Egy felhasználó lehet bármely számú csoport tagja.</li><li>Szinkronizálható a helyszíni Active Directoryból az Azure Active Directory az Azure AD Connect használatával egy csoport tagjainak száma legfeljebb 50 000 tagja.</li></ul> |
| Hozzáférési panel |<ul><li>Nincs felhasználónkénti a hozzáférési panelen látható alkalmazások számának nincs korlátja. Ez vonatkozik az Azure AD prémium vagy nagyvállalati mobilitási csomag licenccel rendelkező.</li><li>Legfeljebb 10 alkalmazáscsempék látható minden felhasználó számára a hozzáférési panelen. Ez a korlátozás ingyenes hozzárendelt licencek felhasználók vagy az Azure Active Directory alapszintű kiadásához vonatkozik. Alkalmazáscsempék például Box, Salesforce vagy Dropbox. Ez a korlátozás nem vonatkozik a rendszergazdai fiókokat.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
| Rendszergazdai szerepkörök és engedélyek | <li>A csoport tulajdonosai nem adhatók hozzá.<li>A csoport nem lehet hozzárendelni egy szerepkört.<li>Az alapértelmezett felhasználói engedélyek nem lehet módosítani a bérlői kapcsolók, amelyek az Azure AD-felhasználói beállítások kivételével. |
