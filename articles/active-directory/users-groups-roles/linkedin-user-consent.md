---
title: LinkedIn-adatmegosztás és hozzájárulás – Azure Active Directory | Microsoft dokumentumok
description: Bemutatja, hogy a LinkedIn-integráció hogyan osztja meg az adatokat a Microsoft-alkalmazásokon keresztül az Azure Active Directoryban
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025395"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn-fiókkapcsolatok adatainak megosztása és hozzájárulása

Engedélyezheti, hogy az Active Directory (Azure AD) szervezet felhasználói hozzájáruljanak a Microsoft munkahelyi vagy iskolai fiókjának összekapcsolásához a LinkedIn-fiókjukkal. Miután a felhasználó csatlakoztatta a fiókjait, a LinkedIn adatai és kiemelései egyes Microsoft-alkalmazásokban és -szolgáltatásokban elérhetők. A felhasználók arra is számíthatnak, hogy a LinkedIn-en kapcsolatos hálózati élményük javulni fog, és a Microsoft tól származó információkkal gazdagodnak.

A LinkedIn-adatok Microsoft-alkalmazásokban és -szolgáltatásokban való megtekintéséhez a felhasználóknak hozzá kell járulniuk a saját Microsoft- és LinkedIn-fiókjuk csatlakoztatásához. A rendszer arra kéri a felhasználókat, hogy amikor először kattintanak a fiókjukhoz, az Outlook, a OneDrive vagy a SharePoint Online profilkártyáján lévő linkedin-adatok megtekintéséhez kapcsolódjanak. A LinkedIn-fiókkapcsolatok nem teljesen engedélyezettek a felhasználók számára, amíg nem járulnak hozzá a felhasználói élményhez és a fiókjuk összekapcsolásához.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>A LinkedIn-információk megosztásának előnyei

A Microsoft-alkalmazásokon és -szolgáltatásokon belüli LinkedIn-adatokhoz való hozzáférés megkönnyíti a felhasználók számára, hogy a szervezeten belül és kívül is kapcsolatba lépjenek, vegyenek részt és szakmai kapcsolatokat építsenek ki munkatársaival, ügyfeleivel és partnereivel. Az új felhasználók gyorsabban felgyorsíthatják a folyamatot, ha kapcsolatba lépnek a kollégákkal, többet tudnak meg róluk, és egyszerűen hozzáférhetnek a további információkhoz. Íme egy példa arra, hogyan jelennek meg a LinkedIn-adatok a Profilkártyán a Microsoft-alkalmazásokban:

![LinkedIn-integráció engedélyezése a szervezetben](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn-integráció engedélyezése és bejelentése

A szervezet beállításának kezeléséhez Azure Active Directory-rendszergazdai jogosultsággal kell rendelkeznie. Engedélyezheti az összes felhasználó, vagy egy adott felhasználói csoportok számára.

1. Az integráció engedélyezéséhez vagy letiltásához kövesse a Hozzájárulás a [LinkedIn-integrációhoz az Azure AD-szervezethez című lépéseit.](linkedin-integration.md)
2. Amikor bejelenti a LinkedIn-integrációt a szervezetben, irányítsa a felhasználókat a [Microsoft-alkalmazásokban és -szolgáltatásokban található LinkedIn-adatokkal](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)kapcsolatos gyakori kérdésekre. A cikk tájékoztatást nyújt arról, hogy hol jelennek meg a LinkedIn-információk, hogyan lehet fiókokat csatlakoztatni stb.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>A felhasználó hozzájárulása a Microsoft és a LinkedIn adataihoz való hozzáféréshez

A LinkedIn-ről elért adatokat nem tárolják véglegesen a Microsoft-szolgáltatásokban. A Microsofttól elért adatokat a Kapcsolatok kivételével nem tároljuk véglegesen a LinkedIn-nel. A Microsoft-névjegyek a LinkedInen tárolódnak mindaddig, amíg a felhasználók el nem távolítják őket, ahogy azt az [importált névjegyek LinkedIn-ből való törlése](https://www.linkedin.com/help/linkedin/answer/43377)ismerteti.

Amikor a felhasználók csatlakoztatják a fiókjukat, a LinkedIn-től származó információk és elemzések egyes Microsoft-alkalmazásokban, például a profilkártyán érhetők el. A felhasználók arra is számíthatnak, hogy a LinkedIn-en kapcsolatos hálózati élményük javulni fog, és a Microsoft tól származó információkkal gazdagodnak.
Amikor a szervezet felhasználói összekapcsolják a LinkedIn- és Microsoft-munkahelyi vagy iskolai fiókjukat, két lehetőség közül választhatnak:

* Adjon engedélyt arra, hogy az adatok mindkét fiókból elérhetők legyenek. Ez azt jelenti, hogy engedélyt adnak a Microsoft- vagy munkahelyi fiókjuknak a LinkedIn-fiókjukból származó adatok elérésére, a [LinkedIn-fiókjukhoz pedig a Microsoft munkahelyi vagy iskolai fiókjából származó adatok eléréséhez.](https://www.linkedin.com/help/linkedin/answer/84077)
* Engedélyt adhat arra, hogy csak a LinkedIn-adatokat férjen hozzá a Microsoft munkahelyi és iskolai fiókjához.

A felhasználók bármikor leválaszthatják a fiókokat, és eltávolíthatják az adatelérési engedélyeket, a [felhasználók pedig szabályozhatják saját LinkedIn-profiljuk megtekintésének módját,](https://www.linkedin.com/help/linkedin/answer/83)beleértve azt is, hogy profiljuk megtekinthető-e a Microsoft-alkalmazásokban.

### <a name="linkedin-account-data"></a>LinkedIn-fiókadatok

Microsoft- és LinkedIn-fiókjainak csatlakoztatásakor engedélyezi, hogy a LinkedIn a következő adatokat adja meg a Microsoftnak:

* Profiladatok – tartalmazza a LinkedIn-identitást, a kapcsolattartási adatokat és a [LinkedIn-profilján](https://www.linkedin.com/help/linkedin/answer/15493)másokkal megosztott adatokat.
* Érdeklődési körök adatai – beleértve a LinkedIn-en lévő érdeklődési köröket, például az Ön által követett személyeket és témákat, a tanfolyamok csoportjait, valamint az Ön által kedvelt és megosztott tartalmakat.
* Előfizetések adatai – a LinkedIn-alkalmazásokra és -szolgáltatásokra való előfizetéseket, valamint a kapcsolódó adatokat tartalmazza. 
* Kapcsolatok adatai - tartalmazza a [LinkedIn hálózatot,](https://www.linkedin.com/help/linkedin/answer/110) beleértve az első fokos kapcsolatok profiljait és kapcsolattartási adatait.

A LinkedIn-ről elért adatokat nem tárolják véglegesen a Microsoft-szolgáltatásokban. A személyes adatok Microsoft általi felhasználásáról a [Microsoft adatvédelmi nyilatkozatában](https://privacy.microsoft.com/privacystatement/)talál további információt.

### <a name="microsoft-work-or-school-account-data"></a>Microsoft munkahelyi vagy iskolai fiókadatai

Microsoft- és LinkedIn-fiókjának csatlakoztatásakor engedélyezi a Microsoft számára, hogy a következő adatokat adja meg a LinkedIn-nek:

* Profiladatok – olyan adatokat tartalmaznak, mint a keresztnév, a vezetéknév, a profilfotó, az e-mail-cím, a kezelő és az Ön által kezelt személyek.
* Naptáradatok – tartalmazza az értekezleteket a naptárakban, azok idejét, helyét és a résztvevők kapcsolattartási adatait. Az értekezletre vonatkozó információk, például a napirend, a tartalom vagy az értekezlet címe nem szerepelnek a naptáradatokban.
* Érdeklődési körök adatai – a Microsoft-szolgáltatások, például a Cortana és a Bing for Business használata alapján a fiókjához kapcsolódó érdeklődési köröket is magukban foglalja.
* Előfizetési adatok – tartalmazza a szervezet által a Microsoft-alkalmazásokhoz és -szolgáltatásokhoz, például az Office 365-höz biztosított előfizetéseket.
* Névjegyadatok – névjegylistákat tartalmaz az Outlookban, a Skype-ban és más Microsoft-fiókszolgáltatásokban, beleértve azon személyek kapcsolattartási adatait is, akikkel gyakran kommunikál vagy együttműködik. A LinkedIn rendszeres időközönként importálja, tárolja és használja a névjegyeket, például kapcsolatokat javasol, segít a névjegyek rendszerezésében és a névjegyek frissítéseimegjelenítésében.

A Microsofttól elért adatokat a Kapcsolatok kivételével nem tároljuk véglegesen a LinkedIn-nel. A Microsoft-névjegyek a LinkedInen tárolódnak, amíg a felhasználók el nem távolítják őket. További információ az [importált névjegyek LinkedIn-ből való törléséről.](https://www.linkedin.com/help/linkedin/answer/43377)

A LinkedIn személyes adatok felhasználásáról a [LinkedIn adatvédelmi szabályzatában](https://www.linkedin.com/legal/privacy-policy)talál további információt. A LinkedIn-szolgáltatások, az adatátvitel és a tárolás esetében az adatok az Európai Unióból az Egyesült Államokba és vissza is befolyhatnak, és az Ön adatainak védelme az [Európai Unió adatátviteli](https://www.linkedin.com/help/linkedin/answer/62533)irányelveiben leírtak szerint történik.

## <a name="next-steps"></a>További lépések

* [LinkedIn a Microsoft-alkalmazásokban munkahelyi vagy iskolai fiókkal](https://www.linkedin.com/help/linkedin/answer/84077)
