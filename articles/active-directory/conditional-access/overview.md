---
title: Mi az az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, az Azure Active Directory feltételes hozzáférés segítségével miként automatikus hozzáférés döntéseket, amelyek nem csak alapuló akik próbál meg hozzáférni egy erőforrást, hanem egy erőforrás elérésekor hogyan megvalósításához.
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
ms.openlocfilehash: 370771d44e2fbbf0fca7af453f7425ebc1b1bdcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612610"
---
# <a name="what-is-conditional-access"></a>Mi az a feltételes hozzáférés?

A biztonság elsődleges szempont a felhőt használó szervezetek számára. A felhő biztonságának egyik fontos eleme az identitás és a hozzáférés a felhőbeli erőforrások felügyelete során. Korunk mobileszközökre és felhőre építkező világában a felhasználók a legkülönfélébb eszközökről és alkalmazásokból érhetik el a szervezet erőforrásait. Ebből kifolyólag már nem elég csak azt figyelni, hogy ki próbálja elérni az adott erőforrást. A biztonság és a hatékonyság közötti megfelelő egyensúly eléréséhez a hozzáférés-vezérlési döntésekben azt is figyelembe kell venni, hogyan próbálják elérni az erőforrást. Az Azure Active Directory (Azure AD) feltételes hozzáférés meg lehet oldani ennek a követelménynek. Feltételes hozzáférés az Azure Active Directory egy funkciója. A feltételes hozzáférés automatikus döntést hozhasson a hozzáférésről vonatkozó feltételek alapuló a felhőalkalmazásokhoz hozzáférő valósíthat meg.

Feltételes hozzáférési szabályzatai érvényben vannak, az első többtényezős hitelesítés befejezése után. Ezért a feltételes hozzáférés nem célja az első sor védelmi például-szolgáltatásmegtagadásos (DDoS-) támadások, de használhat fel ezeket az eseményeket (például a bejelentkezési kockázati szintet, helyét a kérelmet, és így tovább) érkező általi hozzáférés megállapításához.  

![Szabályozás](./media/overview/81.png)

Ez a cikk nyújt fogalmi áttekintést a feltételes hozzáférés az Azure ad-ben.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Korunk mobil- és felhőalapú világában az Azure Active Directory egyszeri bejelentkezés szolgáltatásával a felhasználók bárhonnan bejelentkezhetnek az eszközökre, alkalmazásokba és szolgáltatásokba. A különféle (esetenként saját tulajdonú) eszközök, a vállalati hálózatokon kívüli munkavégzés, valamint a külső SaaS-alkalmazások terjedésével két ellentétes cél kerülhet előtérbe:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A vállalati tulajdon védelme bármikor

Feltételes hozzáférési szabályzatok segítségével alkalmazhatja a megfelelő hozzáférés-vezérlés szükséges feltételek szerint. Az Azure ad-beli feltételes hozzáférés a fokozott biztonság, ha szükséges, és a felhasználó útból marad, abban az esetben, ha még nem biztosít.

Az alábbiakban néhány gyakori hozzáférési problémákat, amelyek a feltételes hozzáférés segítségével a:

- **[Bejelentkezési kockázati](conditions.md#sign-in-risk)** : Az Azure AD Identity Protection észleli a bejelentkezési kockázat. Hogyan korlátozhatja a hozzáférést, ha egy észlelt bejelentkezési kockázat egy kártékony elemet jelez? Mit tehet, ha erősebb bizonyítékra van szüksége, hogy a bejelentkezést egy legitim felhasználó hajtotta végre? Mi történik, ha a kételyei elég megalapozottak, hogy akár adott felhasználók hozzáférését is letiltsa egy alkalmazáshoz?  
- **[Hálózati hely](location-condition.md)** : Azure AD egy bárhonnan elérhető. Mi a teendő, ha a hozzáférési kísérlet egy olyan hálózati helyről történik, amely nem tartozik az informatikai részleg irányítása alá? A felhasználónév és a jelszó kombinációja megfelelő bizonyíték lehet az identitás igazolására a vállalati hálózatról megkísérelt hozzáférések esetén. Mi a teendő, ha erősebb identitásigazolást szeretne az olyan hozzáférési kísérleteknél, amelyeket a világ más, váratlan országaiból vagy régióiból kezdeményeznek? Mi a teendő, ha egyes helyekről akár blokkolni is szeretné a hozzáférést?  
- **[Eszközkezelés](conditions.md#device-platforms)** : Azure ad-felhasználók érhessék el a felhőalapú alkalmazások az széles körét, beleértve a mobil eszközök és személyes eszközök is. Mi a teendő, ha azt szeretné, hogy csak a vállalat informatikai részlege által felügyelt eszközökről lehessen megkísérelni a hozzáférést? Mi a teendő, ha meg szeretné tiltani, hogy egyes eszköztípusok hozzáférhessenek a környezetében lévő felhőalkalmazásokhoz?
- **[Ügyfélalkalmazás](conditions.md#client-apps)** : Ma számos olyan felhőalkalmazások, például a webes alkalmazások, mobilalkalmazások és asztali alkalmazások különböző alkalmazástípust használatával is elérheti. Mi a teendő, ha olyan típusú ügyfélalkalmazással történik hozzáférési kísérlet, amely ismert hibákat okoz? Mi a teendő, ha azt szeretné, hogy bizonyos típusú alkalmazásokat csak az informatikai részleg által felügyelt eszközökről lehessen elérni?

Ezeket a kérdéseket és a kapcsolódó válaszok jelentik a közös hozzáférési forgatókönyvek az Azure AD feltételes hozzáférés.
Feltételes hozzáférés egy funkció az Azure Active Directory, amely lehetővé teszi az adathozzáférési forgatókönyvek esetében egy csoportházirend-alapú módszer használata kezelheti.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

Feltételes hozzáférési szabályzat egy, a következő minta használatával hozzáférési forgatókönyv meghatározása:

![Szabályozás](./media/overview/10.png)


Az **Amikor ez történik** címszó alatt adhatja meg a szabályzatot aktiváló okokat. Az okokat az jellemzi, hogy több feltétel teljesül. Az Azure AD feltételes hozzáférése két hozzárendelés feltételek speciális szerepet játszanak:

- **[Felhasználók](conditions.md#users-and-groups)** : A felhasználó által végzett hozzáférési kísérlet (**akik**).
- **[Felhőalkalmazások](conditions.md#cloud-apps-and-actions)** : Egy hozzáférési kísérlet kereszthivatkozásoknak (**mi**).

E két feltételek kötelezőek a feltételes hozzáférési szabályzat. A két kötelező feltétel mellett további feltételeket is megadhat, amelyek a hozzáférési kísérlet körülményeit írják le. Gyakori példa a mobileszközök használatával vagy a vállalati hálózaton kívüli helyekről megkísérelt hozzáférés. További információkért lásd: [az Azure Active Directory feltételes hozzáférési feltételek](conditions.md).

A hozzáférés-vezérlést a feltételek kombinációja a feltételes hozzáférési szabályzatot jelöli.

![Szabályozás](./media/overview/51.png)

Az Azure AD feltételes hozzáférési szabályozhatja, hogy jogosult felhasználók hozzáférhetnek a felhőbeli alkalmazásokat. Feltételes hozzáférési szabályzat célja egy hozzáférési kísérlet, a cloud app alapján hogyan megy végbe egy hozzáférési kísérlet a további hozzáférés-vezérléssel kényszerítésére.

A felhőbeli alkalmazásokhoz való hozzáférések szabályzatalapú korlátozása lehetővé teszi, hogy a jelen cikkben ismertetett struktúra alapján elkezdje kidolgozni a szabályzatok követelményeit anélkül, hogy foglalkoznia kellene a műszaki megvalósítás részleteivel.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Az Azure AD feltételes hozzáférés és az összevont hitelesítés

Feltételes hozzáférési házirendek és zökkenőmentesen működjön [összevont hitelesítés](../../security/azure-ad-choose-authn.md#federated-authentication). Ez a támogatás magában foglalja a minden támogatott feltételek és a vezérlők és a házirend alkalmazása a történő aktív felhasználói bejelentkezéseket betekintést [az Azure AD-jelentéskészítés](../reports-monitoring/concept-sign-ins.md).

Az *Azure AD-vel történő összevont hitelesítés* azt jelenti, hogy egy megbízható hitelesíti szolgáltatás végzi a felhasználók hitelesítését az Azure AD felé. Megbízható hitelesíti szolgáltatásnak számítanak például az Active Directory összevonási szolgáltatások (AD FS) vagy bármilyen más összevonási szolgáltatás. Ebben a konfigurációban a felhasználók elsődleges hitelesítése a szolgáltatásban történik, majd az egyes alkalmazásokba való bejelentkezés az Azure AD segítségével zajlik. Az Azure AD feltételes hozzáférés a rendszer alkalmazza, hogy hozzáférést kapjanak az alkalmazáshoz, a felhasználó fér hozzá. 

Ha a beállított feltételes hozzáférési házirend többtényezős hitelesítést igényel, az Azure AD alapértelmezés szerint az Azure MFA használatával. Ha az MFA-hoz az összevonási szolgáltatást használja, az Azure AD konfigurálható az összevonási szolgáltatásra történő átirányításra, ha az MFA-ra van szükség. Ehhez állítsa a `-SupportsMFA` paramétert `$true` értékűre a [PowerShellben](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Ez a beállítás az olyan összevont hitelesítési szolgáltatások esetében működik, amelyek támogatják Azure AD által a `wauth= http://schemas.microsoft.com/claims/multipleauthn` használatával kiadott MFA-kérdést.

Miután a felhasználó bejelentkezett az összevont hitelesítés szolgáltatásba, az Azure AD kezeli a további szabályzatkövetelményeket, például az eszközmegfelelőséget vagy a jóváhagyott alkalmazásokat.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Az ügyfelek [Microsoft 365 vállalati licencek](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) is van hozzáférése a feltételes hozzáférési funkciókat. 

## <a name="next-steps"></a>További lépések

Feltételes hozzáférés alkalmazása a környezetben, lásd: [az Azure Active Directoryban a feltételes hozzáférés üzembe helyezésének megtervezése](plan-conditional-access.md).
