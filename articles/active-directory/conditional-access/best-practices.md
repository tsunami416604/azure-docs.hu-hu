---
title: Gyakorlati tanácsok a feltételes hozzáféréshez az Azure Active Directoryban | Microsoft dokumentumok
description: Ismerje meg, hogy mi a szükséges tudnivaló, és mit kell elkerülnie a feltételes hozzáférési házirendek konfigurálásakor.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295358"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Gyakorlati tanácsok a feltételes hozzáféréshez az Azure Active Directoryban

Az [Azure Active Directory (Azure AD) feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)szabályozhatja, hogy a jogosult felhasználók hogyan férnek hozzá a felhőalapú alkalmazásokhoz. Ez a cikk a következőkről nyújt tájékoztatást:

- Dolgok, amiket tudnod kell 
- Mit kell kerülni e feltételes hozzáférési házirendek konfigurálásakor. 

Ez a cikk feltételezi, hogy ismeri a fogalmakat és a terminológia az Azure Active Directory feltételes [hozzáférésében?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Mi szükséges ahhoz, hogy a politika működjön?

Új házirend létrehozásakor nincs kijelölt felhasználó, csoport, alkalmazás vagy hozzáférés-vezérlő.

![Felhőalapú alkalmazások](./media/best-practices/02.png)

A házirend működéséhez konfigurálnia kell a következőket:

| Mi           | Hogyan                                  | miért |
| :--            | :--                                  | :-- |
| **Felhőalapú alkalmazások** |Jelöljön ki egy vagy több alkalmazást.  | A feltételes hozzáférési szabályzat célja, hogy lehetővé tegye annak szabályozását, hogy az engedéllyel rendelkező felhasználók hogyan férhetnek hozzá a felhőalapú alkalmazásokhoz.|
| **Felhasználók és csoportok** | Jelöljön ki legalább egy olyan felhasználót vagy csoportot, amely jogosult a kiválasztott felhőalapú alkalmazások elérésére. | A feltételes hozzáférési szabályzat, amely nem rendelkezik a felhasználók és csoportok hozzárendelt, soha nem aktiválódik. |
| **Hozzáférés-vezérlés** | Jelöljön ki legalább egy hozzáférés-vezérlést. | Ha a feltételek teljesülnek, a házirend-feldolgozónak tudnia kell, hogy mit kell tennie. |

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="how-are-conditional-access-policies-applied"></a>Hogyan történik a feltételes hozzáférési házirendek alkalmazása?

Egy felhőalapú alkalmazás elérésekor egynél több feltételes hozzáférési szabályzat is alkalmazható. Ebben az esetben minden vonatkozó szabályzatnak teljesülnie kell. Ha például az egyik házirend többtényezős hitelesítést (MFA) és egy másikhoz megfelelő eszköz szükséges, akkor ki kell töltenie az MFA-t, és megfelelő eszközt kell használnia. 

Minden házirend két fázisban van érvényesítve:

- 1. fázis: 
   - Részletgyűjtemény: Adatok összegyűjtése a már teljesült házirendek azonosításához.
   - Ebben a fázisban a felhasználók láthatják a tanúsítvány-kérdést, ha az eszköz megfelelősége része a feltételes hozzáférési szabályzatok. Ez a kérdés akkor fordulhat elő a böngészőalkalmazások esetében, ha az eszköz operációs rendszere nem Windows 10.
   - A házirend-értékelés [1.](concept-conditional-access-report-only.md)
- 2. fázis:
   - Végrehajtás: Figyelembe véve az 1.
   - Eredmények alkalmazása a munkamenetre. 
   - A házirend-értékelés 2.

### <a name="how-are-assignments-evaluated"></a>Hogyan történik a hozzárendelések kiértékelése?

Minden hozzárendelés logikailag **ANDed**. Ha egynél több hozzárendelés van konfigurálva, minden hozzárendelésnek meg kell felelnie a házirend aktiválásához.  

Ha olyan helyfeltételt kell konfigurálnia, amely a szervezet hálózatán kívülről származó összes kapcsolatra vonatkozik:

- **Az összes hely belefoglalása**
- **Az összes megbízható IP kizárása**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Mi a teendő, ha ki van zárva az Azure AD felügyeleti portálról?

Ha egy feltételes hozzáférési szabályzat helytelen beállítása miatt van kizárva az Azure AD-portálról:

- Ellenőrizze, hogy vannak-e más rendszergazdák a szervezetben, amelyek még nincsenek letiltva. Az Azure Portalhoz hozzáféréssel rendelkező rendszergazda letilthatja a bejelentkezést befolyásoló szabályzatot. 
- Ha a szervezet egyik rendszergazdája sem tudja frissíteni a házirendet, támogatási kérelmet kell benyújtania. A Microsoft támogatási szolgálata áttekintheti és frissítheti a hozzáférést akadályozó feltételes hozzáférési házirendeket.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Mi történik, ha szabályzatok vannak a klasszikus Azure-portálon és az Azure Portalon konfigurálva?  

Mindkét szabályzatot az Azure Active Directory kényszeríti, és a felhasználó csak akkor kap hozzáférést, ha minden követelmény teljesül.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Mi történik, ha szabályzatokkal rendelkezik az Intune Silverlight portálon és az Azure Portalon?

Mindkét szabályzatot az Azure Active Directory kényszeríti, és a felhasználó csak akkor kap hozzáférést, ha minden követelmény teljesül.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Mi történik, ha több házirend van ugyanahhoz a felhasználóhoz konfigurálva?  

Az Azure Active Directory minden bejelentkezéskor kiértékeli az összes szabályzatot, és biztosítja, hogy minden követelmény teljesüljön, mielőtt hozzáférést kapna a felhasználóhoz. A hozzáférés letiltása üti az összes többi konfigurációs beállítást. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Működik a feltételes hozzáférés az Exchange ActiveSync szolgáltatással?

Igen, az Exchange ActiveSync programot feltételes hozzáférési házirendben is használhatja.

Egyes felhőalapú alkalmazások, például a SharePoint Online és az Exchange Online is támogatják a régebbi hitelesítési protokollokat. Ha egy ügyfélalkalmazás egy örökölt hitelesítési protokoll használatával férhet hozzá egy felhőalapú alkalmazáshoz, az Azure AD nem kényszeríthet feltételes hozzáférési szabályzatot ezen a hozzáférési kísérleten. Annak érdekében, hogy egy ügyfélalkalmazás ne kerülhesse meg a szabályzatok végrehajtását, ellenőrizze, hogy csak az érintett felhőalapú alkalmazásokon engedélyezheti-e a modern hitelesítést.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hogyan konfigurálja a feltételes hozzáférést az Office 365-alkalmazásokkal?

Mivel az Office 365-alkalmazások össze vannak kapcsolva, javasoljuk, hogy a szabályzatok létrehozásakor a gyakran használt alkalmazásokat rendeljük össze.

A gyakori összekapcsolt alkalmazások közé tartozik a Microsoft Flow, a Microsoft Planner, a Microsoft Teams, az Office 365 Exchange Online, az Office 365 SharePoint Online és az Office 365 Yammer.

Fontos, hogy a felhasználói beavatkozást igénylő házirendek, például a többtényezős hitelesítés, ha a hozzáférés egy munkamenet vagy feladat elején szabályozott. Ha nem teszi, a felhasználók nem tudnak elvégezni bizonyos feladatokat az alkalmazáson belül. Ha például nem felügyelt eszközökön többtényezős hitelesítésre van szüksége a SharePoint eléréséhez, de az e-mailekhez nem, az e-mailben dolgozó felhasználók nem tudnak SharePoint-fájlokat csatolni egy üzenethez. További információ a cikkben található: [Mik a szolgáltatásfüggőségek az Azure Active Directory feltételes hozzáférésében?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Mit kell elkerülni etenni

A feltételes hozzáférési keretrendszer nagyszerű konfigurációs rugalmasságot biztosít. Azonban a nagy rugalmasság azt is jelenti, hogy alaposan tekintse át az egyes konfigurációs házirendek kiadása előtt, hogy elkerüljék a nemkívánatos eredményeket. Ebben az összefüggésben különös figyelmet kell fordítania a teljes készleteket érintő feladatokra, például **az összes felhasználóra / csoportra / felhőalkalmazásra.**

A környezetben kerülje el a következő konfigurációkat:

**Az összes felhasználó számára az összes felhőalapú alkalmazás:**

- **Hozzáférés letiltása** – Ez a konfiguráció blokkolja a teljes szervezetet, ami egyáltalán nem jó ötlet.
- **Megfelelő eszköz megkövetelése** – Azok a felhasználók, akik még nem regisztrálták az eszközeiket, ez a szabályzat letiltja az összes hozzáférést, beleértve az Intune-portálhoz való hozzáférést is. Ha regisztrált eszköz nélkül rendszergazda, ez a szabályzat megakadályozza, hogy visszakerüljön az Azure Portalra a szabályzat módosításához.
- **Tartományhoz való csatlakozás megkövetelése** – Ez a házirend-blokkhozzáférés letilthatja a hozzáférést a szervezet összes felhasználója számára, ha még nem rendelkezik tartományhoz csatlakozott eszközzel.
- **Alkalmazásvédelmi szabályzat megkövetelése** – Ez a házirend-blokk-hozzáférés is letilthatja a hozzáférést a szervezet összes felhasználója számára, ha nem rendelkezik Intune-szabályzattal. Ha olyan rendszergazda, akinek nincs intune-alkalmazásvédelmi szabályzata, és nem rendelkezik olyan ügyfélalkalmazással, amely rendelkezik egy Intune-alkalmazásvédelmi szabályzattal, ez a szabályzat megakadályozza, hogy visszatérjen az Intune-hoz, például az Intune-ba és az Azure-ba.

**Minden felhasználó, az összes felhőalapú alkalmazás, az összes eszközplatform esetében:**

- **Hozzáférés letiltása** – Ez a konfiguráció blokkolja a teljes szervezetet, ami egyáltalán nem jó ötlet.

## <a name="how-should-you-deploy-a-new-policy"></a>Hogyan kell telepíteni egy új szabályzatot?

Első lépésként a mi lenne, [ha eszköz](what-if-tool.md)segítségével értékeli a házirendet.

Ha az új házirendek készen állnak a környezetre, telepítse őket fázisokban:

1. Házirend alkalmazása a felhasználók egy kis készletére, és ellenőrizze, hogy a várt módon viselkedik-e. 
1. Ha kibont egy házirendet, hogy több felhasználót vegyen fel. Továbbra is zárja ki az összes rendszergazdát a házirendből, hogy továbbra is hozzáférhessenek, és szükség esetén frissíthetjük a házirendet.
1. Házirend alkalmazása minden felhasználóra csak szükség esetén. 

Ajánlott eljárásként hozzon létre egy felhasználói fiókot, amely:

- A házirend-felügyeletnek szentelve 
- Minden irányelvből kizárva

## <a name="policy-migration"></a>Politika áttelepítése

Fontolja meg az Azure Portalon nem létrehozott szabályzatok áttelepítését, mert:

- Most már foglalkozik forgatókönyvek nem tudta kezelni korábban.
- A kezelni kívánt házirendek számát a konszolidálásukkal csökkentheti.   
- Az összes feltételes hozzáférési szabályzatot egy központi helyen kezelheti.
- A klasszikus Azure-portál kilett vonva.   

További információért lásd: [Klasszikus szabályzatok áttelepítése az Azure Portalon](policy-migration.md).

## <a name="next-steps"></a>További lépések

Ha tudni szeretné:

- Feltételes hozzáférési szabályzat konfigurálása, [lásd: Többkori hitelesítés megkövetelése bizonyos alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel.](app-based-mfa.md)
- A feltételes hozzáférési szabályzatok megtervezése a [Feltételes hozzáférés központi telepítésének megtervezése az Azure Active Directoryban.](plan-conditional-access.md)
