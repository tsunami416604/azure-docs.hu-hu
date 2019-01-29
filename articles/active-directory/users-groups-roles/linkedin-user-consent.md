---
title: LinkedIn integrációt az adatok megosztása és jóváhagyás az Azure Active Directory |} A Microsoft Docs
description: Azt ismerteti, hogyan LinkedIn-integráció oszt meg adatokat a Microsoft-alkalmazások az Azure Active Directory-n keresztül
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-jan
ms.openlocfilehash: 9a4f46f992072eafc82794999b493862217608fe
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180568"
---
# <a name="linkedin-integration-data-sharing-and-consent"></a>LinkedIn-integráció az adatok megosztása és jóváhagyás

Azure Active Directory (Azure AD) a rendszergazdák a szervezetben, hogy engedélyt adjanak az csatlakoztatása a Microsoft munkahelyi vagy iskolai és a LinkedIn-fiók engedélyezheti a felhasználóknak. Amikor a felhasználóknak a fiókjaik csatlakoztatására, információt és a LinkedIn pillanatai érhetők el egyes Microsoft-alkalmazások és szolgáltatások. Felhasználók saját kapcsolatépítési élményt a Linkedinen fejlesztjük és a Microsoft information renderelésre számíthatnak.

LinkedIn-adatokat a Microsoft-alkalmazások és szolgáltatások megtekintéséhez felhasználók bele kell egyeznie a saját Microsoft- és LinkedIn-fiók kapcsolódni. Csatlakoztathassák a fiókjukat, az Outlook, OneDrive vagy SharePoint online-hoz egy profil kártya valakinek a LinkedIn-információinak megtekintéséhez kattintson az első alkalommal a rendszer kéri. LinkedIn-fiókkapcsolatok nem teljes mértékben engedélyezettek a felhasználók mindaddig, amíg azok hozzájárul a felhasználói élményt és csatlakoztathassák a fiókjukat.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>LinkedIn-adatok megosztása előnyei

Microsoft-alkalmazások és szolgáltatások LinkedIn információkhoz való hozzáférés megkönnyíti a csatlakozás, és szakmai kapcsolatok létrehozása munkatársakkal, ügyfelek és partnerek belüli és a szervezeten kívüli felhasználók számára. Új felhasználók is gyorsan elsajátíthatja gyorsabban a munkatársakkal való csatlakozás, azokat többet és könnyen fér hozzá a további információkat. Íme egy példa LinkedIn-adatokat hogyan jelenik meg a profil kártya a Microsoft-alkalmazások:

![LinkedIn-integráció engedélyezése](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Engedélyezze és jelentjük be a LinkedIn-integráció

Az Azure Active Directory-rendszergazda kezelheti a beállítást a szervezet számára kell lennie. Az összes felhasználó számára, vagy a felhasználók adott csoportja számára engedélyezheti.

1. Engedélyezze vagy tiltsa le az integrációt, kövesse a lépéseket a [LinkedIn-integráció](linkedin-integration.md).
2. LinkedIn integrálása a szervezet jelentjük be, amikor mutasson a felhasználók számára a gyakori Kérdéseket kapcsolatos [LinkedIn-adatokat a Microsoft-alkalmazások és szolgáltatások](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). A cikk ismerteti, hol LinkedIn-adatokat mutatja be, hogyan csatlakozhat a fiókok és egyéb.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Felhasználói beleegyezés az adatok eléréséhez a Microsoft és a LinkedIn

A LinkedIn elérhető adatok nincsenek maradandóan tárolva a Microsoft services. A Microsoft elérhető adatok nincsenek maradandóan tárolva a Linkedinnel, kivéve a névjegyeket. A Microsoft Contacts tárolódnak a Linkedinen felhasználók távolítsa el őket, amíg leírtak szerint [importált névjegyek törlése a LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Amikor a felhasználóknak a fiókjaik csatlakoztatására, információkat és elemzéseket a LinkedIn érhetők el az egyes Microsoft-alkalmazások, például a profil kártyák. Felhasználók saját kapcsolatépítési élményt a Linkedinen fejlesztjük és a Microsoft information renderelésre számíthatnak.
Ha a szervezet felhasználói csatlakozzon a LinkedIn és a Microsoft munkahelyi vagy iskolai fiókokat, akkor két lehetősége van:

* Engedélyezze a fiókot is az elérhető adatok. Ez azt jelenti, hogy engedélyt a Microsoft számára, vagy a munkahelyi LinkedIn-fiókjából, valamint a fiók adatokhoz való hozzáférésének [érheti el adatait a Microsoft munkahelyi vagy iskolai fiókjával, a LinkedIn-fiók](https://www.linkedin.com/help/linkedin/answer/84077).
* Engedélyezze a csak a LinkedIn adatra vonatkozó érhető el a Microsoft által munkahelyi és iskolai fiókot.

A felhasználók leválasztása a fiókok és adatokhoz való hozzáférési jogosultságok eltávolítása a tetszőleges időpontban, és [felhasználók szabályozhatják, hogyan a saját LinkedIn-profilok megtekintett](https://www.linkedin.com/help/linkedin/answer/83), például hogy a profil tekinthet meg a Microsoft-alkalmazások.

### <a name="linkedin-account-data"></a>LinkedIn-fiókja adatait

Ha csatlakozik a Microsoft és a LinkedIn-fiók, engedélyezi a Linkedinnek adja meg a következő adatokat a Microsoftnak:

* Profiladatok – tartalmazza a LinkedIn-identitást, kapcsolattartási adatokat és az adatokat a megosztja másokkal a [LinkedIn-profilok](https://www.linkedin.com/help/linkedin/answer/15493).
* Adatok megnézzem – érdekében a Linkedinen, tartalmaz, például a személyek és a témaköröket, hajtsa végre a tanfolyamok csoportokat, és a tartalom, például a, és megoszthatja.
* Előfizetések - szerepel az előfizetések LinkedIn-alkalmazásokhoz és szolgáltatásokhoz kapcsolódó adatokkal együtt. 
* Kapcsolatok – szerepel a [LinkedIn-hálózatot](https://www.linkedin.com/help/linkedin/answer/110) profilok és a kapcsolattartási adatokat. 1-szintű kapcsolat.

A LinkedIn elérhető adatok nincsenek maradandóan tárolva a Microsoft services. A személyes adatok használata a Microsoft kapcsolatos további információkért lásd: a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>A Microsoft munkahelyi vagy iskolai fiók adatok

Ha csatlakozik a Microsoft és a LinkedIn-fiók, engedélyezi a Microsoftnak adja meg a következő adatokat a LinkedIn webhelyre:

* Profiladatok – például az Utónév, utolsó nevét, profilkép, e-mail cím, kezelő és személyek, Ön által kezelt információkat tartalmaz.
* Naptár-adatok – értekezletek naptárak, időponthoz képest, helyek és résztvevők kapcsolattartási adatait tartalmazza. Információ az értekezlet napirend, a tartalmat vagy az értekezlet-cím nem szerepel a naptári adatok.
* Adatok megnézzem - magában foglalja a Microsoft-szolgáltatások, például a Cortana és a vállalati Bing alapján, a fiókjához társított érdekében.
* Előfizetések-adatok – Microsoft-alkalmazások és szolgáltatások, például az Office 365, a szervezet biztosítja nekik előfizetését tartalmazza.
* Kapcsolatba lép a data - partnerlistájukat az Outlook, a Skype és más Microsoft fiók-szolgáltatások, többek között az emberek gyakran kommunikációhoz vagy együttműködhet kapcsolattartási adatait tartalmazza. Névjegyek importálható, rendszeres időközönként, tárolása, és a LinkedIn, által használt kapcsolatok javasolja például biztosíthatja a névjegyek rendszerezheti és ügyfeleivel kapcsolatos frissítések megjelenítése.

A Microsoft elérhető adatok nincsenek maradandóan tárolva a Linkedinnel, kivéve a névjegyeket. A Microsoft Contacts tárolják a Linkedinen, amíg a felhasználók távolítsa el őket. Tudjon meg többet [importált névjegyek törlése a LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

A személyes adatok LinkedIn-a használja a további információkért lásd: a [LinkedIn adatvédelmi szabályzatát](https://www.linkedin.com/legal/privacy-policy). LinkedIn szolgáltatások, az adatátviteli és tárolási adatok áramolhasson az Európai Unióból, az Egyesült Államokban és a biztonsági és oldhatja meg, az adatvédelem védelemmel [Európai Unió adatforgalom](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>További lépések

* [A Microsoft-alkalmazások munkahelyi vagy iskolai fiókjával LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)
