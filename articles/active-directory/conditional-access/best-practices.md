---
title: Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, mit kell tudnia, és mit érdemes elkerülni a feltételes hozzáférési házirendek konfigurálásakor.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63cb1d7e2b0086a3d9ef6e3917ebefa11c7ccba
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253375"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directory

A [Azure Active Directory (Azure ad) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)segítségével szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá a felhőalapú alkalmazásokhoz. Ez a cikk a következő információkat tartalmazza:

- Tudni kívánt dolgok 
- Mit kell elkerülni a feltételes hozzáférési házirendek konfigurálásakor. 

Ez a cikk azt feltételezi, hogy már ismeri az alábbi fogalmakat és terminológiát: [Mi a feltételes hozzáférés a Azure Active Directoryban?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Mi szükséges a szabályzat működéséhez?

Új szabályzat létrehozásakor nincsenek kiválasztva felhasználók, csoportok, alkalmazások vagy hozzáférés-vezérlések.

![Felhőalapú alkalmazások](./media/best-practices/02.png)

A szabályzat működéséhez konfigurálnia kell a következőket:

| Mi           | Hogyan                                  | miért |
| :--            | :--                                  | :-- |
| **Felhőalapú alkalmazások** |Válasszon ki egy vagy több alkalmazást.  | A feltételes hozzáférési szabályzat célja, hogy lehetővé tegye annak szabályozását, hogy a jogosult felhasználók hozzáférjenek a felhőalapú alkalmazásokhoz.|
| **Felhasználók és csoportok** | Válasszon ki legalább egy olyan felhasználót vagy csoportot, amely jogosult a kiválasztott felhőalapú alkalmazások elérésére. | A rendszer soha nem indít olyan feltételes hozzáférési szabályzatot, amely nem rendelkezik hozzárendelt felhasználókkal és csoportokkal. |
| **Hozzáférés-vezérlés** | Válasszon ki legalább egy hozzáférés-vezérlést. | Ha a feltételek teljesülnek, a házirend-feldolgozónak tudnia kell, hogy mi a teendő. |

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="how-are-conditional-access-policies-applied"></a>Hogyan alkalmazza a feltételes hozzáférési szabályzatokat?

Egy felhőalapú alkalmazáshoz való hozzáféréskor több feltételes hozzáférési házirend is alkalmazható. Ebben az esetben minden érvényes szabályzatnak teljesülnie kell. Ha például egy házirendhez többtényezős hitelesítés (MFA) szükséges, és egy másiknak megfelelő eszközre van szüksége, akkor az MFA-t kell elvégeznie, és egy megfelelő eszközt kell használnia. 

Az összes házirend kikényszerítve két fázisban:

- 1. fázis: 
   - Részletek gyűjteménye: adatok összegyűjtése a már megelégedett házirendek azonosításához.
   - Ebben a fázisban a felhasználók akkor láthatják a tanúsítványt, ha az eszköz megfelelősége a feltételes hozzáférési szabályzatok részét képezi. Ez az üzenet akkor fordulhat elő, ha az eszköz operációs rendszere nem Windows 10.
   - A házirend kiértékelésének 1. fázisa az összes engedélyezett házirendre és házirendre vonatkozóan a [csak jelentési módban](concept-conditional-access-report-only.md)történik.
- 2. fázis:
   - Kényszerítés: az 1. fázisban összegyűjtött részletek beszerzése érdekében kérje a felhasználótól, hogy teljesítse a még nem teljesített további követelményeket.
   - Eredmények alkalmazása a munkamenetre. 
   - A házirend kiértékelésének 2. fázisa az összes engedélyezett házirend esetében bekövetkezik.

### <a name="how-are-assignments-evaluated"></a>Hogyan történik a hozzárendelések kiértékelése?

Minden hozzárendelés logikailag **ANDed**. Ha egynél több hozzárendelés van konfigurálva, minden hozzárendelésnek meg kell felelnie a szabályzat elindításának.  

Ha olyan hely feltételt kell konfigurálnia, amely a szervezet hálózatán kívülről végzett összes kapcsolatra vonatkozik:

- **Minden helyszín** belefoglalása
- **Az összes megbízható IP** -cím kizárása

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Mi a teendő, ha ki van zárva az Azure AD felügyeleti portálon?

Ha a feltételes hozzáférési szabályzat helytelen beállítása miatt kizárja az Azure AD-portált:

- Győződjön meg arról, hogy a szervezet más rendszergazdái még nincsenek letiltva. A Azure Portalhoz hozzáféréssel rendelkező rendszergazda letilthatja a bejelentkezést befolyásoló házirendet. 
- Ha a szervezet egyik rendszergazdája sem tudja frissíteni a szabályzatot, be kell küldenie egy támogatási kérést. A Microsoft támogatási szolgálata megtekintheti és frissítheti a hozzáférést megakadályozó feltételes hozzáférési szabályzatokat.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Mi történik, ha szabályzatok vannak a klasszikus Azure portálon, és Azure Portal konfigurálva?  

A Azure Active Directory mindkét szabályzatot kényszeríti, és a felhasználó csak akkor kap hozzáférést, ha minden követelmény teljesül.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Mi történik, ha szabályzatok vannak az Intune Silverlight Portálon és a Azure Portal?

A Azure Active Directory mindkét szabályzatot kényszeríti, és a felhasználó csak akkor kap hozzáférést, ha minden követelmény teljesül.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Mi történik, ha ugyanahhoz a felhasználóhoz több szabályzat is van konfigurálva?  

Minden bejelentkezéskor Azure Active Directory kiértékel minden házirendet, és biztosítja, hogy minden követelmény teljesül, mielőtt hozzáférést kap a felhasználóhoz. Letiltja a hozzáférést az összes többi konfigurációs beállításhoz. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Működik a feltételes hozzáférés az Exchange ActiveSync szolgáltatással?

Igen, az Exchange ActiveSync használatát feltételes hozzáférési szabályzatban használhatja.

Egyes felhőalapú alkalmazások, például a SharePoint Online és az Exchange Online is támogatják az örökölt hitelesítési protokollokat. Ha egy ügyfélalkalmazás örökölt hitelesítési protokollt használ egy felhőalapú alkalmazás eléréséhez, az Azure AD nem kényszerítheti ki a feltételes hozzáférési szabályzatot erre a hozzáférési kísérletre. Annak megakadályozása érdekében, hogy egy ügyfélalkalmazás megkerüljék a házirendek kényszerítését, ellenőriznie kell, hogy csak a modern hitelesítést lehet-e engedélyezni az érintett Felhőbeli alkalmazásokban.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hogyan konfigurálhatja a feltételes hozzáférést az Office 365-alkalmazásokkal?

Mivel az Office 365-alkalmazások egymással kapcsolódnak egymáshoz, javasoljuk, hogy a leggyakrabban használt alkalmazásokat a szabályzatok létrehozásakor együtt adja hozzá.

A gyakori összekapcsolt alkalmazások közé tartozik a Microsoft Flow, a Microsoft Planner, a Microsoft Teams, az Office 365 Exchange Online, az Office 365 SharePoint Online és az Office 365 Yammer.

Fontos olyan házirendek esetében, amelyek felhasználói interakciókat igényelnek, például a többtényezős hitelesítést, amikor a hozzáférés egy munkamenet vagy feladat elején van szabályozva. Ha nem, a felhasználók bizonyos feladatokat nem tudnak végrehajtani egy alkalmazáson belül. Ha például többtényezős hitelesítést igényel a nem felügyelt eszközökön a SharePoint eléréséhez, de nem az e-mailekhez, az e-mailben dolgozó felhasználók nem tudnak SharePoint-fájlokat csatolni egy üzenethez. További információt a cikk a [feltételes hozzáférés Azure Active Directory szolgáltatás függőségei](service-dependencies.md)című cikkben talál.

## <a name="what-you-should-avoid-doing"></a>Mit érdemes elkerülni

A feltételes hozzáférési keretrendszer nagyszerű konfigurációs rugalmasságot biztosít. A nagy rugalmasság azonban azt is jelenti, hogy az egyes konfigurációs házirendeket alaposan át kell tekintenie, mielőtt a nemkívánatos eredmények elkerülnek. Ebben a kontextusban különös figyelmet kell fordítani a teljes készleteket érintő hozzárendelésekre, például az **összes felhasználó/csoport/Felhőbeli alkalmazásra**.

A környezetében el kell kerülnie a következő konfigurációkat:

**Minden felhasználó, minden felhőalapú alkalmazás:**

- **Hozzáférés letiltása** – ez a konfiguráció blokkolja a teljes szervezetet, ami biztosan nem jó ötlet.
- **Megfelelő eszköz megkövetelése** – azoknál a felhasználóknál, akik még nem regisztrálták az eszközeiket, ez a szabályzat blokkolja az összes hozzáférést, beleértve az Intune-portálhoz való hozzáférést is. Ha a rendszergazda regisztrált eszköz nélkül van, akkor ez a szabályzat megakadályozza, hogy visszalépjen a Azure Portal a szabályzat módosításához.
- **Tartományhoz való csatlakozás megkövetelése** – ez a házirend-blokkolási hozzáférés a szervezet összes felhasználója számára is letilthatja a hozzáférést, ha még nem rendelkezik tartományhoz csatlakoztatott eszközzel.
- **Alkalmazás-védelmi házirend megkövetelése** – ez a házirend-blokkolási hozzáférés a szervezet összes felhasználója számára is letilthatja a hozzáférést, ha nem rendelkezik Intune-házirenddel. Ha Ön az Intune app Protection-szabályzattal nem rendelkező ügyfélalkalmazás, akkor ez a szabályzat megakadályozza, hogy visszajusson a portálokra, például az Intune-ba és az Azure-ba.

**Minden felhasználó, minden felhőalapú alkalmazás, minden eszköz platform:**

- **Hozzáférés letiltása** – ez a konfiguráció blokkolja a teljes szervezetet, ami biztosan nem jó ötlet.

## <a name="how-should-you-deploy-a-new-policy"></a>Hogyan érdemes új szabályzatot telepíteni?

Első lépésként ki kell értékelnie a szabályzatot a [Mi az IF eszköz](what-if-tool.md)használatával.

Ha az új szabályzatok készen állnak a környezetre, a következő fázisokban telepítse őket:

1. Alkalmazzon egy házirendet a felhasználók egy kis készletére, és ellenőrizze, hogy az a várt módon működik-e. 
1. Amikor kibővít egy házirendet, hogy több felhasználót is tartalmazzon. Továbbra is kizárhatja az összes rendszergazdát a szabályzatból, hogy azok továbbra is hozzáférhessenek, és ha módosításra van szükség, frissítse a szabályzatot.
1. Ha szükséges, alkalmazza a házirendet az összes felhasználóra. 

Ajánlott eljárásként hozzon létre egy felhasználói fiókot:

- Házirend-felügyeletre dedikált 
- Kizárva az összes házirendből

## <a name="policy-migration"></a>Házirend áttelepítése

Érdemes áttelepíteni azokat a házirendeket, amelyeket nem a Azure Portal hozott létre, mert:

- Mostantól olyan forgatókönyveket is megadhat, amelyeket nem tudott kezelni.
- Csökkentheti a felügyelni kívánt szabályzatok számát.   
- A feltételes hozzáférési szabályzatokat egyetlen központi helyen kezelheti.
- A klasszikus Azure portál ki lett vonva.   

További információért lásd: [Klasszikus szabályzatok áttelepítése az Azure Portalon](policy-migration.md).

## <a name="next-steps"></a>További lépések

Ha tudni szeretné:

- Feltételes hozzáférési szabályzat konfigurálása: a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md).
- A feltételes hozzáférési szabályzatok megtervezése: a [feltételes hozzáférési környezet megtervezése Azure Active Directoryban](plan-conditional-access.md).
