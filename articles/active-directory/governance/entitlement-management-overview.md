---
title: Mi az a jogosultságkezelés? – Azure AD
description: Áttekintés a Azure Active Directory jogosultságok kezeléséről, valamint arról, hogy miként kezelheti a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez való hozzáférést a belső és külső felhasználók számára.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a99b9089e568351cf736310e778ba477441407
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422576"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Mi az az Azure AD-jogosultságkezelés?

A Azure Active Directory (Azure AD) jogosultságok kezelése egy olyan [identitás-irányítási](identity-governance-overview.md) szolgáltatás, amely lehetővé teszi a szervezetek számára, hogy a hozzáférési kérelmek munkafolyamatainak automatizálásával, a hozzáférési hozzárendelésekkel, a felülvizsgálatokkal és a lejárattal együtt kezeljék az identitásokat és a hozzáférés életciklusát

A szervezet alkalmazottainak a feladatuk elvégzéséhez különböző csoportokhoz, alkalmazásokhoz és webhelyekhez kell hozzáférnie. A hozzáférés kezelése kihívást jelent, mivel a követelmények változnak – új alkalmazások hozzáadása vagy a felhasználóknak további hozzáférési jogosultságok szükségesek.  Ez a forgatókönyv bonyolultabb lehet a külső szervezetekkel való együttműködés során – előfordulhat, hogy nem tudja, hogy a másik szervezetnél kik is hozzáférjenek a szervezet erőforrásaihoz, és nem tudják, hogy a szervezet által használt alkalmazások, csoportok vagy webhelyek milyen alkalmazásokat használnak.

Az Azure AD-jogosultságok kezelésével hatékonyabban kezelheti a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez való hozzáférést a belső felhasználók számára, valamint a szervezeten kívüli felhasználók számára is, akiknek szükségük van az erőforrásokhoz való hozzáférésre.

## <a name="why-use-entitlement-management"></a>Miért érdemes használni a jogosultság-kezelést?

A nagyvállalati szervezetek gyakran szembesülnek kihívásokkal az alkalmazottaknak az erőforrásokhoz való hozzáférésének kezelésekor, például:

- Előfordulhat, hogy a felhasználók nem tudják, hogy milyen hozzáférésre van szükségük, és még ha igen, nehéz lehet megtalálni a megfelelő személyeket a hozzáférésük jóváhagyására
- Miután a felhasználók megkeresik és megkapják a hozzáférést egy erőforráshoz, előfordulhat, hogy a hozzáférésük hosszabb ideig tart, mint az üzleti célra szükséges

Ezek a problémák olyan felhasználók számára készültek, akiknek más szervezettől, például az ellátási lánc szervezeteitől vagy más üzleti partnereinktől származó külső felhasználókhoz kell hozzáférni. Példa:

- Előfordulhat, hogy egyetlen személy sem tudja, hogy a többi szervezet könyvtáraiban lévő összes adott személy meghívja őket
- Még ha ezek a felhasználók is meghívhatják ezeket a felhasználókat, a szervezetben senki sem emlékszik arra, hogy az összes felhasználó hozzáférését következetesen kezelje

Az Azure AD-jogosultságok kezelése segíthet a problémák megoldásában.  Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használják az Azure AD-jogosultságok kezelését, olvassa el a [Avanade-esettanulmányt](https://aka.ms/AvanadeELMCase) és a [központú esettanulmányt](https://aka.ms/CentricaELMCase).  Ez a videó áttekintést nyújt a jogosultságok kezeléséről és annak értékéről:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Mire használhatom a jogosultságok felügyeletét?

Íme néhány lehetőség a jogosultságok kezeléséhez:

- A nem rendszergazdák számára delegált hozzáférési csomagok létrehozásának lehetősége. Ezek a hozzáférési csomagok tartalmazzák azokat az erőforrásokat, amelyeket a felhasználók igényelhetnek, és a delegált hozzáférési csomag kezelői olyan szabályzatokat határoznak meg, amelyekkel a felhasználók igényelhetnek, és akiknek jóvá kell hagyniuk a hozzáférést, és amikor a hozzáférés lejár.
- Válassza ki azokat a csatlakoztatott szervezeteket, amelyekhez a felhasználók igényelhetnek hozzáférést.  Ha egy olyan felhasználó, aki még nem szerepel a címtárban, a hozzáférését jóváhagyja, a rendszer automatikusan meghívja őket a címtárba, és hozzárendeli a hozzáférést.  Ha a hozzáférésük lejár, ha nincs más hozzáférési csomag-hozzárendelésük, a címtárban lévő B2B-fiókja automatikusan eltávolítható.

Az [első hozzáférési csomag létrehozásához](entitlement-management-access-package-first.md)megkezdheti az oktatóanyagot. Elolvashatja a [gyakori forgatókönyveket](entitlement-management-scenarios.md), vagy megtekintheti a videókat, beleértve a következőket is

- [Az Azure AD-jogosultságok felügyeletének üzembe helyezése a szervezetben](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Az Azure AD-jogosultságok felügyeletének monitorozása és méretezése](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Delegálás a jogosultságok kezelésében](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Mik azok a hozzáférési csomagok, és milyen erőforrásokat kezelhetek velük?

A jogosultságok kezelése az Azure AD-be egy *hozzáférési csomag*fogalmát mutatja be. A hozzáférési csomag az összes olyan erőforrás kötegét képezi, amelynek hozzáférését a felhasználónak egy projekten kell dolgoznia, vagy a feladat elvégzéséhez. A hozzáférési csomagok a belső alkalmazottak és a szervezeten kívüli felhasználók hozzáférésének szabályozására szolgálnak.

 A felhasználók a jogosultsági felügyelethez való hozzáférését a következő típusú erőforrásokkal kezelheti:

- Azure AD-beli biztonsági csoportok tagsága
- Office 365-csoportok és-csapatok tagsága
- Hozzárendelés az Azure AD vállalati alkalmazásokhoz, beleértve az SaaS-alkalmazásokat, valamint az összevonás/egyszeri bejelentkezést és/vagy üzembe helyezést támogató egyéni integrált alkalmazásokat
- SharePoint Online-webhelyek tagsága

Az Azure AD biztonsági csoportjaira vagy az Office 365-csoportokra támaszkodó egyéb erőforrásokhoz való hozzáférést is szabályozhatja.  Példa:

- A Microsoft Office 365-es verzióra vonatkozó licenccel rendelkező felhasználók számára engedélyezheti az Azure AD biztonsági csoportját egy hozzáférési csomagban, és konfigurálhatja az adott csoportra vonatkozó [csoport alapú licencelést](../users-groups-roles/licensing-groups-assign.md) .
- Hozzáférést biztosíthat a felhasználóknak az Azure-erőforrások kezeléséhez egy hozzáférési csomagban található Azure AD biztonsági csoport használatával és az adott csoport [Azure-beli szerepkör-hozzárendelésének](../../role-based-access-control/role-assignments-portal.md) létrehozásával

## <a name="how-do-i-control-who-gets-access"></a>Hogyan a hozzáférés-vezérlést?

Egy hozzáférési csomaggal a rendszergazda vagy a delegált hozzáférés-kezelő csomag felsorolja az erőforrásokat (csoportokat, alkalmazásokat és helyeket), valamint azokat a szerepköröket, amelyekre a felhasználóknak szüksége van az adott erőforrásokhoz.

A hozzáférési csomagok egy vagy több *szabályzatot*is tartalmaznak. A szabályzatok a hozzáférési csomaghoz való hozzárendelés szabályait vagy guardrails határozzák meg. Az egyes szabályzatok segítségével biztosítható, hogy csak a megfelelő felhasználók férhessenek hozzá a hozzáféréshez, hogy a kérelmük jóváhagyók legyenek, valamint hogy az erőforrásokhoz való hozzáférésük időkorlátos legyen, és ha nincs megújítva, lejár.

![Hozzáférési csomag és szabályzatok](./media/entitlement-management-overview/elm-overview-access-package.png)

Az egyes házirendeken belül a rendszergazda vagy a hozzáférési csomag kezelője határozza meg

- Vagy a már meglévő felhasználók (jellemzően alkalmazottak vagy már meghívott vendégek) vagy a külső felhasználók partner szervezetei, amelyek jogosultak a hozzáférés kérésére
- A jóváhagyási folyamat és a hozzáférést engedélyező vagy megtagadó felhasználók
- A felhasználó hozzáférési hozzárendelésének időtartama a jóváhagyás után, a hozzárendelés lejárta előtt

Az alábbi ábrán egy példa látható a jogosultságok kezelésének különböző elemeire. Egy katalógust mutat be két példa hozzáférési csomaggal.

- Az **1. csomag elérési csomagja** egyetlen csoportot tartalmaz erőforrásként. A hozzáférés egy olyan házirenddel van meghatározva, amely lehetővé teszi, hogy a címtárban lévő felhasználók egy csoportja hozzáférjen a hozzáféréshez.
- A **2-es hozzáférési csomag** egy csoportot, egy alkalmazást és egy SharePoint Online-webhelyet tartalmaz erőforrásként. A hozzáférés két különböző házirenddel van definiálva. Az első házirend lehetővé teszi, hogy a címtárban a felhasználók egy csoportja hozzáférjen a hozzáféréshez. A második házirend lehetővé teszi a külső címtárban lévő felhasználók számára a hozzáférés kérését.

![A jogosultságok kezelésének áttekintése](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Mikor érdemes használni a hozzáférési csomagokat?

A hozzáférési csomagok nem helyettesítik más mechanizmusokat a hozzáférési hozzárendeléshez.  Ezek a legmegfelelőbbek olyan helyzetekben, mint például a következők:

- Az alkalmazottaknak időre korlátozott hozzáférésre van szükségük egy adott feladathoz.  Használhat például egy csoportos licencelést és egy dinamikus csoportot annak biztosítására, hogy az alkalmazottak rendelkezzenek Exchange Online-postaládával, majd a hozzáférési csomagok használatával olyan helyzetekben, amikor az alkalmazottaknak további hozzáférésre van szükségük, például a részleg erőforrásainak olvasására egy másikból részleg.
- A hozzáférést egy alkalmazott felettesének vagy más kijelölt személynek kell jóváhagynia.
- A részlegek saját hozzáférési szabályzatokat kívánnak kezelni az erőforrásaik bevonása nélkül.  
- Két vagy több szervezet együttműködik egy projekttel, és ennek eredményeképpen az egyik szervezet több felhasználóját az Azure AD B2B-be kell vinni egy másik szervezet erőforrásaihoz való hozzáféréshez.

## <a name="how-do-i-delegate-access"></a>Hogyan delegálja a hozzáférést?

 A hozzáférési csomagok a *katalógusok*nevű tárolókban vannak meghatározva.  Egyetlen katalógussal is rendelkezhet az összes hozzáférési csomaghoz, vagy kijelölhet egyéni személyeket saját katalógusok létrehozásához. A rendszergazdák bármilyen katalógusba hozzáadhatnak erőforrásokat, de a nem rendszergazdai jogosultsággal rendelkezők csak a saját erőforrásaikat használhatják katalógusba. A katalógus tulajdonosa más felhasználókat is hozzáadhat a katalógus-tulajdonosként, vagy a hozzáférési csomag kezelői.  Ezeket a forgatókönyveket az [Azure ad-jogosultságok felügyeletének delegálása és szerepkörei](entitlement-management-delegate.md)című cikk ismerteti.

## <a name="summary-of-terminology"></a>Terminológia összefoglalása

A jogosultságok kezelésének és dokumentációjának jobb megismeréséhez tekintse át a következő feltételek listáját.

| Időtartam | Leírás |
| --- | --- |
| hozzáférési csomag | Egy csoport vagy projekt által igényelt erőforrás-csomag, amely szabályzatokkal van szabályozva. Egy hozzáférési csomag mindig szerepel a katalógusban. Hozzon létre egy új hozzáférési csomagot egy olyan forgatókönyvhöz, amelyben a felhasználóknak hozzáférést kell kérniük.  |
| hozzáférési kérelem | Egy hozzáférési csomag erőforrásaihoz való hozzáférésre vonatkozó kérelem. A kérések általában egy jóváhagyási munkafolyamaton keresztül mennek keresztül.  Ha jóváhagyják, a kérelmező felhasználó hozzáférési csomagbeli hozzárendelést kap. |
| hozzárendelés | Egy hozzáférési csomag felhasználónak való hozzárendelése biztosítja, hogy a felhasználó rendelkezik az adott hozzáférési csomag összes erőforrás-szerepkörével.  Az Access Package-hozzárendelések esetében általában időkorlát van a lejárat előtt. |
| Katalógus | Kapcsolódó erőforrások és hozzáférési csomagok tárolója.  A rendszer katalógusokat használ a delegáláshoz, így a nem rendszergazdák létrehozhatják saját hozzáférési csomagjaikat. A katalógus tulajdonosai erőforrásokat adhatnak hozzá a katalógushoz. |
| Katalógus létrehozója | Olyan felhasználók gyűjteménye, akik jogosultak új katalógusok létrehozására.  Ha egy olyan nem rendszergazda felhasználó, aki jogosult katalógus létrehozására, új katalógust hoz létre, automatikusan a katalógus tulajdonosa lesz. |
| csatlakoztatott szervezet | Egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van. Egy csatlakoztatott szervezet felhasználóit megadhatja egy szabályzatban, mivel a hozzáférés kérése engedélyezett. |
| szabályzat | Olyan szabályok összessége, amelyek meghatározzák a hozzáférési életciklust, például azt, hogy a felhasználók hogyan férhetnek hozzá, ki hagyhatják jóvá, és hogy mennyi ideig férhetnek hozzá a felhasználók a hozzárendeléseken keresztül. Egy házirend egy hozzáférési csomaghoz van csatolva. Egy hozzáférési csomag például rendelkezhet két házirenddel – az egyik az alkalmazottak számára, hogy hozzáférést igényeljen, és egy másikat a külső felhasználók számára a hozzáférés kéréséhez. |
| erőforrás | Egy eszköz, például egy Office-csoport, egy biztonsági csoport, egy alkalmazás vagy egy SharePoint Online-webhely, egy olyan szerepkörrel, amellyel a felhasználók engedélyt kaphatnak. |
| erőforrás könyvtára | Egy vagy több megosztani kívánt erőforrással rendelkező könyvtár. |
| erőforrás-szerepkör | Egy erőforrás által társított és meghatározott engedélyek gyűjteménye. Egy csoportnak két szerepköre van – a tag és a tulajdonos. A SharePoint-webhelyek általában 3 szerepkörrel rendelkeznek, de lehetnek további egyéni szerepkörök. Az alkalmazásokhoz egyéni szerepkörök tartozhatnak. |


## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

A speciális felhők, például a Azure Government, az Azure Germany és az Azure China 21Vianet jelenleg nem használhatók.

### <a name="which-users-must-have-licenses"></a>Mely felhasználóknak kell licenccel rendelkezniük?

A bérlőnek legalább annyi prémium szintű Azure AD P2-licenccel kell rendelkeznie, hogy a jogosultságok kezelésében a felhasználók aktívak legyenek. Az aktív tag felhasználói a jogosultságok kezelésében a következők:

- Az a felhasználó, aki egy hozzáférési csomagra vonatkozó kérelmet indít vagy fogad el.
- Olyan felhasználó, akinek hozzáférési csomagja van hozzárendelve.
- A hozzáférési csomagokat kezelő felhasználó.

A tagok felhasználói számára a licencek részeként több vendég is engedélyezheti a jogosultságok felügyeletét. További információ a felvehető vendég felhasználók számának kiszámításáról: [Azure Active Directory B2B együttműködés licencelési útmutatója](../b2b/licensing-guidance.md).

További információ a licenceknek a felhasználókhoz való hozzárendeléséről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md). Vegye figyelembe, hogy a jogosultságok kezelése jelenleg nem kényszeríti ki a felhasználók licenc-hozzárendelését.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
