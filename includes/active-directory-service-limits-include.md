---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443317"
---
Az alábbiakban a használati korlátozásokat és egyéb szolgáltatási korlátai, az Azure Active Directory (Azure AD) szolgáltatás.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtár vagy egy Vendég is tartozhat.<br/>Egy-egy felhasználóhoz legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartományneveket adhat hozzá. Beállítása minden a tartománynak a helyszíni Active Directoryval az összevonáshoz, ha minden könyvtár nem több mint 450 tartományneveket adhat hozzá. |
| Objektumok |<ul><li>Egy legfeljebb 500 000 objektumot az Azure Active Directory ingyenes kiadásának felhasználói egyetlen címtárban hozható létre.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb 100 bővítményértéket lehet írni (az ÖSSZES típust és az ÖSSZES alkalmazást beleértve).</li><li>Csak a „User”, „Group”, „TenantDetail”, „Device”, „Application” és „ServicePrincipal” entitások bővíthetőek „String” típusú vagy „Binary” típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API-version 1.21-preview esetében érhetőek el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Legfeljebb 100 felhasználó lehet a tulajdonosa egy alkalmazás. |
| Csoportok |<ul><li>Legfeljebb 100 felhasználó lehet a tulajdonosa egyetlen csoportnak.</li><li>Tetszőleges számú objektum lehet egyetlen csoport tagjai.</li><li>Egy felhasználó lehet bármely számú csoport tagja.</li><li>Szinkronizálható a helyszíni Active Directoryból az Azure Active Directory, Azure AD connecttel egy csoportnak száma legfeljebb 50 ezer tagja.</li></ul> |
| Hozzáférési panel |<ul><li>A Prémium szintű Azure AD vagy Nagyvállalati mobilitási csomag licenccel rendelkező végfelhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja.</li><li>Legfeljebb 10 alkalmazáscsempék (példák: Box, Salesforce vagy Dropbox) látható a hozzáférési panelen licenccel rendelkező ingyenes vagy alapszintű kiadásához, az Azure Active Directory a végfelhasználók számára. A korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |
| Rendszergazdai szerepkörök és engedélyek | <li>A csoport nem adható hozzá tulajdonosként<li>A csoport nem rendelhető hozzá egy szerepkörhöz<li>Alapértelmezett felhasználói engedélyek túli (felhasználói beállítások az Azure AD) bérlő kapcsolók nem módosítható. |
