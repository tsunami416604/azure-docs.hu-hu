---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 06/20/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 5529a4510b6d9f005dc46165e1be799585e5a153
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100844"
---
Az alábbiakban az Azure Active Directory szolgáltatás használati és egyéb szolgáltatási korlátai olvashatóak.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak |Egy felhasználó társítható legfeljebb 500 Azure Active Directory-könyvtárak.<br />Példák a lehetséges kombinációkra: <ul> <li>Egy felhasználó 500 könyvtárak hoz létre.</li><li>Egy felhasználó tagja 500 könyvtárak kerül.</li><li>Egy-egy felhasználóhoz 250 könyvtárak hoz létre, és később ad hozzá mások 250 más könyvtárban.</li></ul> |
| Tartományok | Legfeljebb 900 felügyelt tartományneveket adhat hozzá. Hoz létre minden a tartománynak a helyszíni Active Directory összevonási, ha minden directory legfeljebb 450 tartományneveket adhat hozzá. |
| Objektumok |<ul><li>Legfeljebb 500 000 objektumok a felhasználók az Azure Active Directory ingyenes kiadás egyetlen címtárban is létrehozható.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb 100 bővítményértéket lehet írni (az ÖSSZES típust és az ÖSSZES alkalmazást beleértve).</li><li>Csak a „User”, „Group”, „TenantDetail”, „Device”, „Application” és „ServicePrincipal” entitások bővíthetőek „String” típusú vagy „Binary” típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API-version 1.21-preview esetében érhetőek el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Legfeljebb 100 felhasználó tulajdonosai egyetlen alkalmazás lehet. |
| Csoportok |<ul><li>Legfeljebb 100 felhasználó lehet egy csoport tulajdonosainak.</li><li>Az Azure Active Directoryban egyetlen csoportnak tetszőleges számú objektum lehet a tagja.</li><li>Az Azure Active Directory címtár-szinkronizálással (DirSync) egy csoportnak legfeljebb 15 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li><li>Az Azure AD Connecttel egy csoportnak legfeljebb 50 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li></ul> |
| Hozzáférési panel |<ul><li>A Prémium szintű Azure AD vagy Nagyvállalati mobilitási csomag licenccel rendelkező végfelhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja.</li><li>Az Azure Active Directory ingyenes vagy alapszintű kiadásához tartozó licenccel rendelkező végfelhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe látható (például Box, Salesforce vagy Dropbox). A korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
