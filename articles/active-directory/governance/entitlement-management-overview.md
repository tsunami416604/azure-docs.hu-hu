---
title: Mi az az Azure AD-jogosultságkezelés? (Előzetes verzió) – Azure Active Directory
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
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 136a9994415b42c456ebdb0caa8ed6edcc7b4534
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934382"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Mi az az Azure AD-jogosultságkezelés? (Előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szervezet alkalmazottainak a feladatuk elvégzéséhez különböző csoportokhoz, alkalmazásokhoz és webhelyekhez kell hozzáférnie. A hozzáférés kezelése kihívást jelent. A legtöbb esetben nem létezik a projekthez felhasználói igényeknek megfelelő összes erőforrást tartalmazó rendezett lista. A projektmenedzser jól ismeri a szükséges erőforrásokat, az érintett személyeket, és azt, hogy mennyi ideig tart a projekt. A projektmenedzsernek azonban általában nincs engedélye a mások számára való hozzáférés engedélyezésére vagy megadására. Ez a forgatókönyv bonyolultabb lehet, ha külső személyeket vagy vállalatokat próbál meg dolgozni.

A Azure Active Directory (Azure AD) jogosultságok kezelése segítséget nyújt a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez való hozzáférés kezeléséhez a belső felhasználók és a szervezeten kívüli felhasználók számára egyaránt.

Ez a videó áttekintést nyújt a jogosultságok kezeléséről és az üzleti értékéről:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>Miért érdemes használni a jogosultság-kezelést?

A nagyvállalati szervezetek gyakran szembesülnek kihívásokkal az erőforrásokhoz való hozzáférés kezelése során, például:

- Előfordulhat, hogy a felhasználók nem tudják, milyen hozzáférésre van szükségük
- Előfordulhat, hogy a felhasználók nehézségekbe ütközik a megfelelő személyek vagy a megfelelő erőforrások megkeresése során
- Miután a felhasználók megkeresik és megkapják a hozzáférést egy erőforráshoz, előfordulhat, hogy a hozzáférésük hosszabb ideig tart, mint az üzleti célra szükséges

Ezek a problémák olyan felhasználók számára vannak összekapcsolva, akiknek más címtárból kell elérniük, például az ellátási lánc szervezeteitől vagy más üzleti partnereinktől származó külső felhasználóknak. Példa:

- Előfordulhat, hogy a szervezetek nem tudják, hogy a többi könyvtárban lévő egyes személyek meghívhatják őket
- Még ha a szervezetek is meghívhatják ezeket a felhasználókat, előfordulhat, hogy a szervezetek nem emlékeznek arra, hogy az összes felhasználó hozzáférését következetesen kezeljék

Az Azure AD-jogosultságok kezelése segíthet a problémák megoldásában.

## <a name="what-can-i-do-with-entitlement-management"></a>Mire használhatom a jogosultságok felügyeletét?

Íme néhány lehetőség a jogosultságok kezeléséhez:

- A felhasználók által igényelhető kapcsolódó erőforrások csomagjainak létrehozása
- Szabályok definiálása az erőforrások igényléséhez és a hozzáférés lejáratakor
- A belső és külső felhasználókhoz való hozzáférés életciklusának szabályozása
- Erőforrások kezelésének delegálása
- Jóváhagyók kijelölése a kérelmek jóváhagyásához
- Jelentések létrehozása az előzmények követéséhez

Az Identitáskezelés és a jogosultságok kezelésének áttekintéséhez tekintse meg az alábbi videót az Ignite 2018 konferenciáról:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Milyen erőforrásokat kezelhetek?

A jogosultság-kezeléssel a következő típusú erőforrásokat kezelheti:

- Azure AD-beli biztonsági csoportok
- Office 365-csoportok
- Azure AD Enterprise-alkalmazások, beleértve az SaaS-alkalmazásokat és az összevonást vagy üzembe helyezést támogató egyedi integrált alkalmazásokat
- SharePoint Online-webhelyek gyűjteményei és webhelyei

Az Azure AD biztonsági csoportjaira vagy az Office 365-csoportokra támaszkodó egyéb erőforrásokhoz való hozzáférést is szabályozhatja.  Példa:

- A Microsoft Office 365-es verzióra vonatkozó licenccel rendelkező felhasználók számára engedélyezheti az Azure AD biztonsági csoportját egy hozzáférési csomagban, és konfigurálhatja az adott csoportra vonatkozó [csoport alapú licencelést](../users-groups-roles/licensing-groups-assign.md) .
- Hozzáférést biztosíthat a felhasználóknak az Azure-erőforrások kezeléséhez egy hozzáférési csomagban található Azure AD biztonsági csoport használatával és az adott csoport [Azure-beli szerepkör-hozzárendelésének](../../role-based-access-control/role-assignments-portal.md) létrehozásával

## <a name="what-are-access-packages-and-policies"></a>Mik azok a hozzáférési csomagok és szabályzatok?

A jogosultságok kezelése bevezeti a *hozzáférési csomag*fogalmát. A hozzáférési csomag az összes olyan erőforrás kötegét képezi, amelyet a felhasználónak egy projekten kell dolgoznia, vagy feladatait kell végrehajtania. Az erőforrások közé tartoznak a csoportok, az alkalmazások és a webhelyek hozzáférése. A hozzáférési csomagok a belső alkalmazottak és a szervezeten kívüli felhasználók hozzáférésének szabályozására szolgálnak. A hozzáférési csomagok a *katalógusok*nevű tárolókban vannak meghatározva.

A hozzáférési csomagok egy vagy több *szabályzatot*is tartalmaznak. A szabályzatok határozzák meg a hozzáférési csomag elérésére vonatkozó szabályokat vagy guardrails. A szabályzat engedélyezése kényszeríti, hogy csak a megfelelő felhasználók férhessenek hozzá a megfelelő erőforrásokhoz, és a megfelelő időtartamra.

![Hozzáférési csomag és szabályzatok](./media/entitlement-management-overview/elm-overview-access-package.png)

A hozzáférési csomag és a hozzá tartozó házirendek esetében a hozzáférés-csomagkezelő a következőket határozza meg:

- Segédanyagok és eszközök
- A felhasználók számára szükséges szerepkörök az erőforrásokhoz
- A hozzáférést kérő külső felhasználók belső felhasználói és partnerszervezetek
- Jóváhagyási folyamat és a hozzáférést engedélyező vagy megtagadó felhasználók
- A felhasználó hozzáférésének időtartama

Az alábbi ábrán egy példa látható a jogosultságok kezelésének különböző elemeire. Két példa hozzáférési csomagot mutat be.

- Az **1. csomag elérési csomagja** egyetlen csoportot tartalmaz erőforrásként. A hozzáférés egy olyan házirenddel van meghatározva, amely lehetővé teszi, hogy a címtárban lévő felhasználók egy csoportja hozzáférjen a hozzáféréshez.
- A **2-es hozzáférési csomag** egy csoportot, egy alkalmazást és egy SharePoint Online-webhelyet tartalmaz erőforrásként. A hozzáférés két különböző házirenddel van definiálva. Az első házirend lehetővé teszi, hogy a címtárban a felhasználók egy csoportja hozzáférjen a hozzáféréshez. A második házirend lehetővé teszi a külső címtárban lévő felhasználók számára a hozzáférés kérését.

![A jogosultságok kezelésének áttekintése](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>Szakkifejezések

A jogosultságok kezelésének és dokumentációjának jobb megismeréséhez tekintse át a következő feltételeket.

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| jogosultságok kezelése | Hozzáférési csomagokat hozzárendelő, visszavonó és felügyelő szolgáltatás. |
| hozzáférési csomag | Egy csoport vagy projekt által igényelt erőforrás-csomag, amely szabályzatokkal van szabályozva. Egy hozzáférési csomag mindig szerepel a katalógusban. |
| hozzáférési kérelem | Egy hozzáférési csomag erőforrásaihoz való hozzáférésre vonatkozó kérelem. A kérések általában egy munkafolyamaton keresztül haladnak át. |
| szabályzat | Olyan szabályok összessége, amelyek meghatározzák a hozzáférési életciklust, például azt, hogy a felhasználók hogyan férhetnek hozzá, ki hagyhatják jóvá, és hogy mennyi ideig férhetnek hozzá a felhasználók. Például a szabályzatok az alkalmazottak hozzáférését és a külső hozzáférést is tartalmazzák. |
| Katalógus | Kapcsolódó erőforrások és hozzáférési csomagok tárolója. |
| Általános katalógus | Olyan beépített katalógus, amely mindig elérhető. Ahhoz, hogy erőforrásokat vegyen fel az általános katalógusba, bizonyos engedélyekre van szükség. |
| Erőforrás | Egy eszköz vagy szolgáltatás (például egy Office-csoport, egy biztonsági csoport, egy alkalmazás vagy egy SharePoint Online-webhely) számára, amelyhez engedélyeket adhat a felhasználóknak. |
| Erőforrás típusa | Az erőforrás típusa, amely magában foglalja a csoportokat, az alkalmazásokat és a SharePoint Online-webhelyeket. |
| erőforrás-szerepkör | Egy erőforráshoz társított engedélyek gyűjteménye. |
| erőforrás könyvtára | Egy vagy több megosztani kívánt erőforrással rendelkező könyvtár. |
| csatlakoztatott szervezet | Egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van. |
| hozzárendelt felhasználók | Hozzáférési csomag hozzárendelése egy felhasználóhoz, így a felhasználó rendelkezik az adott hozzáférési csomag összes erőforrás-szerepkörével. |
| Engedélyezése | Hozzáférési csomag elérhetővé tétele a felhasználók számára a kéréshez. |

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

A speciális felhők, például az Azure Government, az Azure Germany és az Azure China 21Vianet jelenleg nem használhatók ebben az előzetes verzióban.

### <a name="which-users-must-have-licenses"></a>Mely felhasználóknak kell licenccel rendelkezniük?

A bérlőnek legalább annyi prémium szintű Azure AD P2-licenccel kell rendelkeznie, mint az aktív tag felhasználóinak. Az aktív tag felhasználói a jogosultságok kezelésében a következők:

- Az a felhasználó, aki egy hozzáférési csomagra vonatkozó kérelmet indít vagy fogad el.
- Olyan felhasználó, akinek hozzáférési csomagja van hozzárendelve. 
- A hozzáférési csomagokat kezelő felhasználó.

A tagok felhasználói számára a licencek részeként több vendég is engedélyezheti a jogosultságok felügyeletét. További információ a felvehető vendég felhasználók számának kiszámításáról: [Azure Active Directory B2B együttműködés licencelési útmutatója](../b2b/licensing-guidance.md).

További információ a licenceknek a felhasználókhoz való hozzárendeléséről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: az első hozzáférési csomag létrehozása](entitlement-management-access-package-first.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
