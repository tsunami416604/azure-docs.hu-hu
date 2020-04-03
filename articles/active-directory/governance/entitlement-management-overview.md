---
title: Mi az a jogosultságkezelés? - Az Azure Hirdetés
description: Áttekintést kaphat az Azure Active Directory jogosultságkezeléséről, és arról, hogyan kezelheti a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez való hozzáférést belső és külső felhasználók számára.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65100e8584eba5c31edc2f9fd8c57ad8bd14c0d1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582544"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Mi az az Azure AD-jogosultságkezelés?

Az Azure Active Directory (Azure AD) jogosultságkezelési szolgáltatás egy [identitás-irányítási](identity-governance-overview.md) funkció, amely lehetővé teszi a szervezetek számára, hogy az identitásés a hozzáférési életciklus okán, a hozzáférés-kérelem munkafolyamatok automatizálásával, a hozzáférési hozzárendelések, az ellenőrzések és a lejárati rendszer rel.

A szervezetek alkalmazottainak különböző csoportokhoz, alkalmazásokhoz és webhelyekhez kell hozzáférniük a munkájuk elvégzéséhez. A hozzáférés kezelése kihívást jelent, mivel a követelmények változnak – új alkalmazások at adnak hozzá, vagy a felhasználóknak további hozzáférési jogokra van szükségük.  Ez a forgatókönyv bonyolultabbá válik, ha külső szervezetekkel dolgozik együtt – előfordulhat, hogy nem tudja, hogy a másik szervezetben kinek van szüksége a szervezet erőforrásaihoz való hozzáférésre, és nem fogják tudni, hogy a szervezet milyen alkalmazásokat, csoportokat vagy webhelyeket használ.

Az Azure AD jogosultságkezelés segítségével hatékonyabban kezelheti a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez való hozzáférést a belső felhasználók, valamint a szervezeten kívüli felhasználók számára, akiknek szükségük van ezekhez az erőforrásokhoz.

## <a name="why-use-entitlement-management"></a>Miért érdemes a jogosultságkezelést használni?

A vállalati szervezetek gyakran szembesülnek kihívásokkal az alkalmazottak erőforrásokhoz való hozzáférésének kezelése során, például:

- Előfordulhat, hogy a felhasználók nem tudják, milyen hozzáféréssel kell rendelkezniük, és még ha így is tesznek, nehézséget okozhat a megfelelő személyek felkutatása a hozzáférés jóváhagyásához
- Amint a felhasználók megtalálják és megkapják az erőforráshoz való hozzáférést, az üzleti célokhoz szükségesnél hosszabb ideig tarthatnak a hozzáféréshez.

Ezek a problémák csak azok a felhasználók számára merülnek fel, akiknek más szervezettől kell hozzáférniük, például az ellátási láncszervezeteitől vagy más üzleti partnerektől származó külső felhasználók számára. Példa:

- Senki sem ismerheti meg a szervezet könyvtárainak összes konkrét személyét, hogy meghívhatja őket
- Még ha meg is tudták hívni ezeket a felhasználókat, a szervezetben senki sem emlékszik arra, hogy következetesen kezelje a felhasználó összes hozzáférését.

Az Azure AD jogosultságkezelés segíthet kezelni ezeket a kihívásokat.  Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használják az Azure AD jogosultságkezelést, olvassa el az [Avanade esettanulmányát](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) és a [Centrica esettanulmányt.](https://customers.microsoft.com/story/757467-centrica-energy-azure)  Ez a videó áttekintést nyújt a jogosultságok kezeléséről és értékéről:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Mit tehetek a jogosultságkezeléssel?

Íme néhány jogosultságkezelési képesség:

- Nem rendszergazdáknak ruházhatja át a hozzáférési csomagok létrehozásának lehetőségét. Ezek a hozzáférési csomagok olyan erőforrásokat tartalmaznak, amelyeket a felhasználók kérhetnek, és a delegált hozzáférési csomagkezelők olyan szabályokat határozhatnak meg, amelyekhez a felhasználók kérhetik, akiknek jóvá kell hagyniuk a hozzáférésüket, és amikor a hozzáférés lejár.
- Válassza ki azokat a csatlakoztatott szervezeteket, amelyek felhasználói hozzáférést kérhetnek.  Ha egy felhasználó, aki még nem szerepel a címtárban, hozzáférést kér, és jóváhagyásra kerül, a rendszer automatikusan meghívást kap a címtárba, és hozzáférést kap.  Ha a hozzáférésük lejár, ha nincs más hozzáférési csomag-hozzárendelésük, a címtárban lévő B2B-fiókjuk automatikusan eltávolítható.

Az első hozzáférési csomag létrehozásához az első [hozzáférési csomag létrehozásához.](entitlement-management-access-package-first.md) Elolvashatja a [gyakori forgatókönyveket](entitlement-management-scenarios.md)is, vagy videókat nézhet, beleértve a

- [Az Azure AD-jogosultságok kezelésének telepítése a szervezetben](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Az Azure AD-jogosultságok kezelésének figyelése és méretezése](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [A jogosultságkezelés ben való delegálása](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Mik azok a hozzáférési csomagok, és milyen erőforrásokat kezelhetek velük?

A jogosultságkezelés bemutatja az Azure AD-nek a *hozzáférési csomag*fogalmát. A hozzáférési csomag az összes erőforrás csomagja, amelyhez a felhasználónak szüksége van a projekten való munkához vagy a feladat végrehajtásához szükséges hozzáféréssel. A hozzáférési csomagok a belső alkalmazottak, valamint a szervezeten kívüli felhasználók hozzáférésének szabályozására szolgálnak.

 A jogosultságkezeléssel az alábbiakban láthatja azokat az erőforrásokat, amelyekhez a felhasználók hozzáférését kezelheti:

- Az Azure AD biztonsági csoportjainak tagsága
- Tagság az Office 365 Csoportok és csoportok tagságában
- Hozzárendelés az Azure AD nagyvállalati alkalmazásokhoz, beleértve az SaaS-alkalmazásokat és az egyéni integrált alkalmazásokat, amelyek támogatják az összevonást/egyszeri bejelentkezést és/vagy kiépítést
- SharePoint Online-webhelyek tagsága

Az Azure AD biztonsági csoportjaitól vagy az Office 365-csoportoktól függő egyéb erőforrásokhoz való hozzáférést is szabályozhatja.  Példa:

- A Microsoft Office 365 felhasználói licenceket úgy adhat a felhasználóknak, hogy egy hozzáférési csomagban egy Azure AD biztonsági csoportot használ, és [csoportalapú licencelést](../users-groups-roles/licensing-groups-assign.md) konfigurál az adott csoporthoz.
- Hozzáférést biztosíthat a felhasználóknak az Azure-erőforrások kezeléséhez egy hozzáférési csomagban lévő Azure AD biztonsági csoport használatával, és létrehozhat egy [Azure-szerepkör-hozzárendelést](../../role-based-access-control/role-assignments-portal.md) az adott csoporthoz.

## <a name="how-do-i-control-who-gets-access"></a>Hogyan szabályozhatom, hogy ki férhet hozzá?

Egy hozzáférési csomag esetén a rendszergazda vagy a delegált hozzáféréscsomag-kezelő felsorolja az erőforrásokat (csoportokat, alkalmazásokat és webhelyeket), valamint azokat a szerepköröket, amelyekre a felhasználóknak szükségük van ezekhez az erőforrásokhoz.

Az Access csomagok egy vagy több *házirendet*is tartalmaznak. A házirend határozza meg a szabályokat vagy a korlátokat a csomaghoz való hozzárendeléshez. Minden házirend használható annak biztosítására, hogy csak a megfelelő felhasználók kérhetnek hozzáférést, hogy vannak jóváhagyók a kéréshez, és hogy az erőforrásokhoz való hozzáférésük időben korlátozott, és lejár, ha nem újítják meg.

![Hozzáférési csomag és házirendek](./media/entitlement-management-overview/elm-overview-access-package.png)

A rendszergazda vagy a hozzáférés-csomagkezelő minden házirenden belül

- Vagy a már meglévő felhasználók (jellemzően alkalmazottak vagy már meghívott vendégek), vagy a külső felhasználók partnerszervezetei, akik jogosultak a hozzáférés kérelmezésére
- A jóváhagyási folyamat és a hozzáférést jóváhagyó vagy megtagadó felhasználók
- A felhasználó hozzáférési hozzárendelésének időtartama a jóváhagyást követően, a hozzárendelés lejárta előtt

Az alábbi ábra egy példát mutat be a jogosultságkezelés különböző elemeire. Ez azt mutatja, egy katalógus két példa hozzáférési csomagok.

- **Az 1-es hozzáférési csomag** egyetlen csoportot tartalmaz erőforrásként. Az Access olyan házirenddel van definiálva, amely lehetővé teszi, hogy a címtárban lévő felhasználók egy része hozzáférést kérjen.
- **A 2-es access csomag** egy csoportot, egy alkalmazást és egy SharePoint Online-webhelyet tartalmaz erőforrásként. Az Access két különböző házirenddel van definiálva. Az első házirend lehetővé teszi, hogy a címtárban lévő felhasználók egy készlete hozzáférést kérjen. A második házirend lehetővé teszi a külső címtárfelhasználói számára a hozzáférést.

![Jogosultságkezelés – áttekintés](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Mikor kell használni a hozzáférési csomagokat?

A hozzáférési csomagok nem helyettesítik a hozzáférés-hozzárendelés más mechanizmusait.  Ezek a legmegfelelőbb helyzetekben, mint például a következő:

- Az alkalmazottaknak korlátozott időre van szükségük egy adott feladathoz.  Használhat például csoportalapú licencelést és egy dinamikus csoportot annak biztosítására, hogy minden alkalmazott rendelkezzen Exchange Online-postaládával, majd hozzáférési csomagokat használhat olyan helyzetekben, amikor az alkalmazottaknak további hozzáférésre van szükségük, például egy másik részleg részleg erőforrásainak olvasásához.
- A hozzáférést az alkalmazott vezetőjének vagy más kijelölt személynek jóvá kell hagynia.
- A részlegek informatikai közreműködés nélkül kívánják kezelni saját hozzáférési házirendjeiket az erőforrásaikhoz.  
- Két vagy több szervezet dolgozik együtt egy projekten, és ennek eredményeképpen egy szervezet több felhasználóját kell behozni az Azure AD B2B-n keresztül egy másik szervezet erőforrásainak eléréséhez.

## <a name="how-do-i-delegate-access"></a>Hogyan delegálhatok hozzáférést?

 Az access csomagok *katalógusok*nevű tárolókban vannak definiálva.  Az összes hozzáférési csomaghoz egyetlen katalógust is létrehozhat, vagy kijelölhet személyeket saját katalógusok létrehozására és saját tulajdonába. A rendszergazda erőforrásokat adhat bármely katalógushoz, de a nem rendszergazda csak a saját erőforrásait veheti fel a katalógusba. A katalógus tulajdonosa más felhasználókat is hozzáadhat katalógustárstulajdonosként vagy hozzáférés-csomagkezelőként.  Ezeket a forgatókönyveket a cikk [delegálása és szerepkörei ismertetik az Azure AD-jogosultságok kezelésében.](entitlement-management-delegate.md)

## <a name="summary-of-terminology"></a>A terminológia összefoglalása

A jogosultságkezelés és a dokumentáció jobb megértéséhez tekintse vissza a kifejezések alábbi listáját.

| Időtartam | Leírás |
| --- | --- |
| hozzáférési csomag | Egy csapatnak vagy projektnek szüksége van egy erőforrás-kötegre, amelyet házirendek szabályoznak. A hozzáférési csomag mindig katalógusban található. Hozzon létre egy új hozzáférési csomagot egy olyan forgatókönyvhöz, amelyben a felhasználóknak hozzáférést kell kérni.  |
| hozzáférési kérelem | Hozzáférési csomag erőforrásainak elérésére irányuló kérelem. A kérelem általában jóváhagyási munkafolyamaton megy keresztül.  Ha jóváhagyják, a kérelmező felhasználó hozzáférési csomag-hozzárendelést kap. |
| Hozzárendelés | Egy hozzáférési csomag felhasználóhoz rendelése biztosítja, hogy a felhasználó rendelkezik a hozzáférési csomag összes erőforrásszerepkörével.  Az Access csomag-hozzárendelések általában rendelkeznek időkorláttal a lejáratuk előtt. |
| Katalógus | Kapcsolódó erőforrások és hozzáférési csomagok tárolója.  A katalógusok delegálásra szolgálnak, így a nem rendszergazdák létrehozhatják saját hozzáférési csomagjaikat. A katalógustulajdonosok hozzáadhatják a tulajdonukban álló erőforrásokat a katalógushoz. |
| katalógus létrehozója | Új katalógusok létrehozására jogosult felhasználók gyűjteménye.  Amikor egy katalóguslétrehozóként jogosult nem rendszergazdai felhasználó új katalógust hoz létre, automatikusan a katalógus tulajdonosává válik. |
| összekapcsolt szervezet | Egy külső Azure AD-címtár vagy tartomány, amely kapcsolatban áll. A csatlakoztatott szervezet felhasználói megadhatók egy házirendben, hogy hozzáférést kérhetnek. |
| szabályzat | A hozzáférési életciklust meghatározó szabálykészlet, például az, hogy a felhasználók hogyan férhetnek hozzá, ki hagyhatja jóvá, és mennyi ideig férhetnek hozzá a felhasználók egy hozzárendelésen keresztül. A házirend egy hozzáférési csomaghoz van csatolva. Egy hozzáférési csomag például két házirendet használhat: az egyik az alkalmazottak számára hozzáférést kérhet, a másik pedig a külső felhasználók számára a hozzáférést. |
| Erőforrás | Olyan eszköz, például office-csoport, biztonsági csoport, alkalmazás vagy SharePoint Online-webhely, amelynek szerepköre a felhasználószámára engedélyeket kaphat. |
| erőforráskönyvtár | Egy vagy több megosztásra képes erőforrással rendelkező könyvtár. |
| erőforrás szerepkör | Egy erőforráshoz társított és definiált engedélyek gyűjteménye. Egy csoportnak két szerepköre van : tag és tulajdonos. A SharePoint-webhelyek általában 3 szerepkörrel rendelkeznek, de további egyéni szerepköröket is tartalmazhatnak. Az alkalmazások egyéni szerepköröket is használhatnak. |


## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Speciális felhők, például az Azure Germany és az Azure China 21Vianet, jelenleg nem érhetők el.

### <a name="how-many-licenses-must-you-have"></a>Hány licenccel kell rendelkeznie?

Győződjön meg arról, hogy a címtár legalább annyi Azure AD Premium P2 licenccel rendelkezik, mint ön:

- Azok a tagfelhasználók, akik hozzáférési csomagot **kérhetnek.**
- Hozzáférési csomagot kérő tag- és vendégfelhasználók.
- A hozzáférési csomagra vonatkozó kérelmeket jóváhagyó tag- és vendégfelhasználók.
- Azok a tag- és vendégfelhasználók, akik közvetlen hozzárendeléssel rendelkeznek egy hozzáférési csomaghoz.

Az Azure AD Premium P2 licencek **nem** szükségesek a következő feladatokhoz:

- Nincs szükség licencre a globális rendszergazdai szerepkörrel rendelkező felhasználók számára, akik beállítják a kezdeti katalógusokat, a hozzáférési csomagokat és a házirendeket, és felügyeleti feladatokat delegálnak más felhasználóknak.
- Nincs szükség licencre olyan felhasználók számára, akik delegáltak felügyeleti feladatokat, például a katalógus létrehozóját, a katalógus tulajdonosát és a hozzáférés-csomagkezelőt.
- Azoknak a vendégeknek, akik hozzáférést **kérhetnek,** nem kérnek licencet, de **nem** kérnek hozzáférési csomagot.

A tagfelhasználók (alkalmazottak) számára vásárolt minden egyes fizetett Azure AD Premium P2 licenchez az Azure AD B2B segítségével legfeljebb 5 vendégfelhasználót hívhat meg. Ezek a vendégfelhasználók azure AD Premium P2 funkciókat is használhatnak. További információ: [Azure AD B2B együttműködési licencelési útmutató.](../b2b/licensing-guidance.md)

A licencekről további információt [a Licencek hozzárendelése vagy eltávolítása az Azure Active Directory portálon című témakörben talál.](../fundamentals/license-users-groups.md)

### <a name="example-license-scenarios"></a>Példa licencforgatókönyvekre

Íme néhány példa licencforgatókönyv, amely segít meghatározni a licencek számát.

| Forgatókönyv | Számítás | Licencek száma |
| --- | --- | --- |
| A Woodgrove Bank globális rendszergazdája kezdeti katalógusokat hoz létre, és felügyeleti feladatokat delegál hat másik felhasználónak. Az egyik házirend azt határozza meg, hogy **minden alkalmazott** (2000 alkalmazott) hozzáférési csomagok egy adott készletét kérheti. 150 alkalmazott kéri a hozzáférési csomagokat. | 2000 alkalmazott, akik **kérhetik** a hozzáférési csomagokat | 2000 |
| A Woodgrove Bank globális rendszergazdája kezdeti katalógusokat hoz létre, és felügyeleti feladatokat delegál hat másik felhasználónak. Az egyik házirend azt határozza meg, hogy **minden alkalmazott** (2000 alkalmazott) hozzáférési csomagok egy adott készletét kérheti. Egy másik szabályzat azt határozza meg, hogy a **Contoso partner (vendégek) felhasználók egyes** felhasználói ugyanazokat a hozzáférési csomagokat kérhetik jóváhagyáshoz. A Contoso 30 000 felhasználóval rendelkezik. 150 alkalmazott kéri a hozzáférési csomagokat, és 10 500 felhasználó a Contoso-tól hozzáférést kér. | 2000 alkalmazott + 500 vendégfelhasználó a Contoso-tól, akik meghaladják az 1:5 arányt (10 500 - (2000 * 5)) | 2500 |

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
