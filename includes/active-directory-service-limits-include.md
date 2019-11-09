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
ms.openlocfilehash: 3f358dedea37eb33c2d2bb26a823d3633560d3a0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850378"
---
Az alábbiakban az Azure Active Directory (Azure AD) szolgáltatás használati és egyéb szolgáltatási korlátai olvashatóak.

| Kategória | Korlátozások |
| --- | --- |
| Könyvtárak | Egy felhasználó legfeljebb 500 Azure AD-címtárhoz tartozhat tagként vagy vendégként.<br/>Egy felhasználó legfeljebb 20 címtárat hozhat létre. |
| Tartományok | Legfeljebb 900 felügyelt tartománynév felvételére van lehetőség. Ha minden tartományt szeretne összevonni a helyszíni Active Directoryval, akkor címtáranként legfeljebb 450 felügyelt tartománynév adható hozzá. |
|Segédanyagok és eszközök |<ul><li>Alapértelmezés szerint legfeljebb 50 000 Azure AD-erőforrás hozható létre egyetlen címtárban a Azure Active Directory ingyenes kiadásának felhasználói számára. Ha legalább egy ellenőrzött tartománya van, az Azure AD-ben az alapértelmezett címtárszolgáltatás-kvóta 300 000 Azure AD-erőforrásra van kiterjesztve. </li><li>A nem rendszergazda felhasználók legfeljebb 250 Azure AD-erőforrást hozhatnak létre. Az aktív erőforrások és a törölt erőforrások is visszaállíthatók a kvóta felé. Csak a 30 napnál rövidebb idő alatt törölt Azure AD-erőforrások állnak rendelkezésre a visszaállításhoz. Törölte azokat az Azure AD-erőforrásokat, amelyek már nem állnak rendelkezésre a kvótához a kvóta felé a 30 napos értéknél. Ha olyan fejlesztőket használ, akik valószínűleg többször is túllépik ezt a kvótát a szokásos feladataik során, létrehozhat [és hozzárendelhet egy egyéni szerepkört](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) , amely korlátlan számú alkalmazás regisztrációját hozza létre.</li></ul> |
| Sémabővítmények |<ul><li>A sztring típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen Azure AD-erőforráshoz csak a 100-es kiterjesztési értékek írhatók, *minden* típus és *minden* alkalmazás esetében.</li><li>Csak a User, Group, TenantDetail, Device, Application és ServicePrincipal entitások bővíthetők String típusú vagy Binary típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API 1.21-es előzetes verziójában érhetők el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Egyetlen alkalmazásnak legfeljebb 100 felhasználó lehet a tulajdonosa. |
|Alkalmazás jegyzékfájlja |Az alkalmazás jegyzékfájljában legfeljebb 1200 bejegyzést lehet hozzáadni. |
| Csoportok |<ul><li>Egyetlen csoportnak legfeljebb 100 felhasználó lehet a tulajdonosa.</li><li>Tetszőleges számú Azure AD-erőforrás lehet egyetlen csoport tagja.</li><li>Egy felhasználó egyszerre korlátlan számú csoport tagja lehet.</li><li>Az Azure AD Connecttel egy csoportnak legfeljebb 50 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li></ul> |
| Application Proxy | <ul><li>Az App proxy-alkalmazásokban legfeljebb 500 tranzakció/másodperc</li><li>Legfeljebb 750 tranzakció másodpercenként a bérlőhöz</li></ul><br/>Egy tranzakció egyetlen http-kérelemként és egy egyedi erőforrásra adott válaszként van definiálva. A szabályozást követően az ügyfelek 429 választ kapnak (túl sok kérés). |
| Hozzáférési panel |<ul><li>A felhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja. Ez a Prémium szintű Azure AD- vagy Nagyvállalati mobilitási csomag-licenccel rendelkező felhasználókra vonatkozik.</li><li>A felhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe jeleníthető meg. Ez a korlát azokra a felhasználókra vonatkozik, akik ingyenes Azure AD licenccel rendelkező licenceket rendelnek hozzá. Az alkalmazáscsempék közé tartozik például a Box, a Salesforce vagy a Dropbox. Ez a korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy Azure AD-erőforrás legfeljebb 30 felügyeleti egység tagja lehet. |
| Rendszergazdai szerepkörök és engedélyek | <ul><li>Egy csoport nem adható hozzá [tulajdonosként](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Egy csoport nem rendelhető hozzá [szerepkörhöz](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A felhasználók más felhasználói címtáradatokat nem lehet korlátozni a teljes bérlőre kiterjedő kapcsolón kívül, ha le szeretné tiltani az összes nem rendszergazda felhasználó hozzáférését az összes címtár-információhoz (nem ajánlott). További információ az alapértelmezett engedélyekről [itt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Akár 15 percet is igénybe vehet, vagy ki-és bejelentkezhet, mielőtt a rendszergazdai szerepkör tagságának kiegészítése és visszavonása érvénybe lép.</li></ul> |
