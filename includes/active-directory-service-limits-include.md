---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/30/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: a2055c3f00306fbfdad3028a16bb49d919e1e251
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361404"
---
Az alábbiakban a használati korlátozásokat és egyéb szolgáltatási korlátai, az Azure Active Directory (Azure AD) szolgáltatás.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtár vagy egy Vendég is tartozhat.<br/>Egy-egy felhasználóhoz legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartományneveket adhat hozzá. Beállítása minden a tartománynak a helyszíni Active Directoryval az összevonáshoz, ha minden könyvtár nem több mint 450 tartományneveket adhat hozzá. |
| Objektumok |<ul><li>Egy legfeljebb 500 000 objektumot az Azure Active Directory ingyenes kiadásának felhasználói egyetlen címtárban hozható létre.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb 100 bővítményértéket lehet írni (az ÖSSZES típust és az ÖSSZES alkalmazást beleértve).</li><li>Csak a „User”, „Group”, „TenantDetail”, „Device”, „Application” és „ServicePrincipal” entitások bővíthetőek „String” típusú vagy „Binary” típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API-version 1.21-preview esetében érhetőek el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Legfeljebb 100 felhasználó lehet a tulajdonosa egy alkalmazás. |
| Csoportok |<ul><li>Legfeljebb 100 felhasználó lehet a tulajdonosa egyetlen csoportnak.</li><li>Az Azure Active Directoryban egyetlen csoportnak tetszőleges számú objektum lehet a tagja.</li><li>Szinkronizálható a helyszíni Active Directoryból az Azure Active Directory, Azure AD connecttel egy csoportnak száma legfeljebb 50 ezer tagja.</li></ul> |
| Hozzáférési panel |<ul><li>A Prémium szintű Azure AD vagy Nagyvállalati mobilitási csomag licenccel rendelkező végfelhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja.</li><li>Az Azure Active Directory ingyenes vagy alapszintű kiadásához tartozó licenccel rendelkező végfelhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe látható (például Box, Salesforce vagy Dropbox). A korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
