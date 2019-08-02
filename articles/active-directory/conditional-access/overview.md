---
title: Mi a feltételes hozzáférés a Azure Active Directory? | Microsoft Docs
description: Ismerje meg, hogy a Azure Active Directory feltételes hozzáférése hogyan valósítható meg olyan automatizált hozzáférési döntések megvalósításában, amelyek nem csupán az erőforrásokhoz való hozzáférésre, hanem az erőforrások elérésének módjára épülnek.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608093"
---
# <a name="what-is-conditional-access"></a>Mi az a feltételes hozzáférés?

A biztonság elsődleges szempont a felhőt használó szervezetek számára. A felhő biztonságának egyik fontos eleme az identitás és a hozzáférés a felhőbeli erőforrások felügyelete során. Korunk mobileszközökre és felhőre építkező világában a felhasználók a legkülönfélébb eszközökről és alkalmazásokból érhetik el a szervezet erőforrásait. Ebből kifolyólag már nem elég csak azt figyelni, hogy ki próbálja elérni az adott erőforrást. A biztonság és a hatékonyság közötti megfelelő egyensúly eléréséhez a hozzáférés-vezérlési döntésekben azt is figyelembe kell venni, hogyan próbálják elérni az erőforrást. Ha Azure Active Directory (Azure AD) feltételes hozzáféréssel rendelkezik, ezt a követelményt kezelheti. A feltételes hozzáférés a Azure Active Directory egyik funkciója. A feltételes hozzáférés használatával automatizált hozzáférés-vezérlési döntéseket hozhat létre a feltételeken alapuló felhőalapú alkalmazások eléréséhez.

A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. Ezért a feltételes hozzáférés nem az első vonali védelem, mint például a szolgáltatásmegtagadási (DoS) támadások, de az ilyen eseményekből származó jeleket is használhat (például a bejelentkezési kockázati szint, a kérelem helye stb.) a hozzáférés meghatározásához.  

![Szabályozás](./media/overview/81.png)

Ez a cikk az Azure AD feltételes hozzáférésének fogalmi áttekintését tartalmazza.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Korunk mobil- és felhőalapú világában az Azure Active Directory egyszeri bejelentkezés szolgáltatásával a felhasználók bárhonnan bejelentkezhetnek az eszközökre, alkalmazásokba és szolgáltatásokba. A különféle (esetenként saját tulajdonú) eszközök, a vállalati hálózatokon kívüli munkavégzés, valamint a külső SaaS-alkalmazások terjedésével két ellentétes cél kerülhet előtérbe:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A vállalati tulajdon védelme bármikor

Feltételes hozzáférési szabályzatok használatával a megfelelő hozzáférés-vezérlést alkalmazhatja a szükséges feltételek szerint. Az Azure AD feltételes hozzáférés lehetővé teszi, hogy szükség esetén nagyobb biztonságot biztosítson, és a felhasználó nem tudja, ha nem.

Az alábbiakban néhány gyakori hozzáférési szempontot láthat, amelyekkel a feltételes hozzáférés segíthet a következőkben:

- **[Bejelentkezési kockázat](conditions.md#sign-in-risk)** : Azure AD Identity Protection észleli a bejelentkezési kockázatokat. Hogyan korlátozhatja a hozzáférést, ha egy észlelt bejelentkezési kockázat egy kártékony elemet jelez? Mit tehet, ha erősebb bizonyítékra van szüksége, hogy a bejelentkezést egy legitim felhasználó hajtotta végre? Mi történik, ha a kételyei elég megalapozottak, hogy akár adott felhasználók hozzáférését is letiltsa egy alkalmazáshoz?  
- **[Hálózati hely](location-condition.md)** : Az Azure AD bárhonnan elérhető. Mi a teendő, ha a hozzáférési kísérlet egy olyan hálózati helyről történik, amely nem tartozik az informatikai részleg irányítása alá? A felhasználónév és a jelszó kombinációja megfelelő bizonyíték lehet az identitás igazolására a vállalati hálózatról megkísérelt hozzáférések esetén. Mi a teendő, ha erősebb identitásigazolást szeretne az olyan hozzáférési kísérleteknél, amelyeket a világ más, váratlan országaiból vagy régióiból kezdeményeznek? Mi a teendő, ha egyes helyekről akár blokkolni is szeretné a hozzáférést?  
- **[Eszközkezelés](conditions.md#device-platforms)** : Az Azure AD-ben a felhasználók számos eszközről férhetnek hozzá a felhőalapú alkalmazásokhoz, beleértve a mobil-és a személyes eszközöket is. Mi a teendő, ha azt szeretné, hogy csak a vállalat informatikai részlege által felügyelt eszközökről lehessen megkísérelni a hozzáférést? Mi a teendő, ha meg szeretné tiltani, hogy egyes eszköztípusok hozzáférhessenek a környezetében lévő felhőalkalmazásokhoz?
- **[Ügyfélalkalmazás](conditions.md#client-apps)** : Jelenleg számos felhőalapú alkalmazáshoz férhet hozzá, különböző típusú alkalmazásokkal, például webalapú alkalmazásokkal, mobil alkalmazásokkal vagy asztali alkalmazásokkal. Mi a teendő, ha olyan típusú ügyfélalkalmazással történik hozzáférési kísérlet, amely ismert hibákat okoz? Mi a teendő, ha azt szeretné, hogy bizonyos típusú alkalmazásokat csak az informatikai részleg által felügyelt eszközökről lehessen elérni?

Ezek a kérdések és a kapcsolódó válaszok az Azure AD feltételes hozzáférés gyakori hozzáférési forgatókönyveit jelölik.
A feltételes hozzáférés olyan Azure Active Directory képességgel rendelkezik, amely lehetővé teszi a hozzáférési forgatókönyvek kezelését házirend-alapú megközelítéssel.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

A feltételes hozzáférési szabályzat egy hozzáférési forgatókönyv definíciója a következő minta használatával:

![Szabályozás](./media/overview/10.png)


Az **Amikor ez történik** címszó alatt adhatja meg a szabályzatot aktiváló okokat. Az okokat az jellemzi, hogy több feltétel teljesül. Az Azure AD feltételes hozzáférés szolgáltatásban a két hozzárendelési feltétel speciális szerepet játszik:

- **[Felhasználók](conditions.md#users-and-groups)** : Azok a felhasználók, akik hozzáférési kísérletet (**WHO**) futtatnak.
- **[Felhőalapú alkalmazások](conditions.md#cloud-apps-and-actions)** : Egy hozzáférési kísérlet célja (**mi**).

Ez a két feltétel kötelező a feltételes hozzáférési házirendben. A két kötelező feltétel mellett további feltételeket is megadhat, amelyek a hozzáférési kísérlet körülményeit írják le. Gyakori példa a mobileszközök használatával vagy a vállalati hálózaton kívüli helyekről megkísérelt hozzáférés. További információ: [Azure Active Directory feltételes hozzáférés feltételei](conditions.md).

A feltételek és a hozzáférés-vezérlések kombinációja feltételes hozzáférési szabályzatot jelöl.

![Szabályozás](./media/overview/51.png)

Az Azure AD feltételes hozzáférés segítségével szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá a felhőalapú alkalmazásokhoz. A feltételes hozzáférési szabályzat célja, hogy további hozzáférés-vezérlést alkalmazzon a felhőalapú alkalmazásokhoz való hozzáférési kísérleteken a hozzáférési kísérletek elvégzése alapján.

A felhőbeli alkalmazásokhoz való hozzáférések szabályzatalapú korlátozása lehetővé teszi, hogy a jelen cikkben ismertetett struktúra alapján elkezdje kidolgozni a szabályzatok követelményeit anélkül, hogy foglalkoznia kellene a műszaki megvalósítás részleteivel.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Azure AD feltételes hozzáférés és összevont hitelesítés

A feltételes hozzáférési szabályzatok zökkenőmentesen [](../../security/fundamentals/choose-ad-authn.md#federated-authentication)működnek összevont hitelesítéssel. Ez a támogatás magában foglalja az összes támogatott feltételt és ellenőrzést, valamint annak láthatóságát, hogyan alkalmazza a szabályzatot az aktív felhasználói bejelentkezésekre az [Azure ad jelentéskészítés](../reports-monitoring/concept-sign-ins.md)használatával.

Az *Azure AD-vel történő összevont hitelesítés* azt jelenti, hogy egy megbízható hitelesíti szolgáltatás végzi a felhasználók hitelesítését az Azure AD felé. Megbízható hitelesíti szolgáltatásnak számítanak például az Active Directory összevonási szolgáltatások (AD FS) vagy bármilyen más összevonási szolgáltatás. Ebben a konfigurációban a felhasználók elsődleges hitelesítése a szolgáltatásban történik, majd az egyes alkalmazásokba való bejelentkezés az Azure AD segítségével zajlik. Az Azure AD feltételes hozzáférését a rendszer a hozzáférés engedélyezése előtt alkalmazza a felhasználó által elérni kívánt alkalmazáshoz. 

Ha a konfigurált feltételes hozzáférési szabályzat többtényezős hitelesítést igényel, az Azure AD alapértelmezés szerint az Azure MFA-t használja. Ha az MFA-hoz az összevonási szolgáltatást használja, az Azure AD konfigurálható az összevonási szolgáltatásra történő átirányításra, ha az MFA-ra van szükség. Ehhez állítsa a `-SupportsMFA` paramétert `$true` értékűre a [PowerShellben](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Ez a beállítás az olyan összevont hitelesítési szolgáltatások esetében működik, amelyek támogatják Azure AD által a `wauth= http://schemas.microsoft.com/claims/multipleauthn` használatával kiadott MFA-kérdést.

Miután a felhasználó bejelentkezett az összevont hitelesítés szolgáltatásba, az Azure AD kezeli a további szabályzatkövetelményeket, például az eszközmegfelelőséget vagy a jóváhagyott alkalmazásokat.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

A [Microsoft 365 vállalati verzió licenccel](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is. 

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan valósítható meg a feltételes hozzáférés a környezetben, tekintse meg [a feltételes hozzáférés](plan-conditional-access.md)megtervezése Azure Active Directoryban című témakört.
