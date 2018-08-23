---
title: Mi az a feltételes hozzáférés az Azure Active Directoryban? | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan valósíthat meg az Azure Active Directory a feltételes hozzáférésével olyan automatizált döntési mechanizmusokat, amelyek nemcsak azt figyelik, hogy ki próbálja elérni az erőforrást, hanem azt is, hogy hogyan.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3e96e251e655c7c7ad862fbf875111e08c0dfbdf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42023576"
---
# <a name="what-is-conditional-access-in-azure-active-directory"></a>Mi az a feltételes hozzáférés az Azure Active Directoryban?

A biztonság elsődleges szempont a felhőt használó szervezetek számára. A felhő biztonságának egyik fontos eleme az identitás és a hozzáférés a felhőbeli erőforrások felügyelete során. Korunk mobileszközökre és felhőre építkező világában a felhasználók a legkülönfélébb eszközökről és alkalmazásokból érhetik el a szervezet erőforrásait. Ebből kifolyólag már nem elég csak azt figyelni, hogy ki próbálja elérni az adott erőforrást. A biztonság és a hatékonyság közötti megfelelő egyensúly eléréséhez a hozzáférés-vezérlési döntésekben azt is figyelembe kell venni, hogyan próbálják elérni az erőforrást. Az Azure AD feltételes hozzáférésével megfelelhet ennek a követelménynek. A feltételes hozzáférés az Azure Active Directory egyik képessége. A feltételes hozzáférés segítségével automatikus döntéshozatali képességeket valósíthat meg a felhőalkalmazásai eléréséhez különféle feltételek alapján. 

![Vezérlés](./media/overview/81.png)

Ez a cikk fogalmi áttekintést nyújt az Azure AD feltételes hozzáférés funkciójáról.



## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Korunk mobil- és felhőalapú világában az Azure Active Directory egyszeri bejelentkezés szolgáltatásával a felhasználók bárhonnan bejelentkezhetnek az eszközökre, alkalmazásokba és szolgáltatásokba. A különféle (esetenként saját tulajdonú) eszközök, a vállalati hálózatokon kívüli munkavégzés, valamint a külső SaaS-alkalmazások terjedésével két ellentétes cél kerülhet előtérbe:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A vállalati tulajdon védelme bármikor

A feltételes hozzáférési szabályzatokkal mindig a megfelelő hozzáférés-vezérlés alkalmazható a követelményeknek megfelelően. Az Azure AD feltételes hozzáférés fokozott védelmet biztosít, amikor szükséges, és nem akadályozza a felhasználót, ahol nem fontos. 

Íme néhány gyakoribb hozzáférési probléma, amelyek megoldásában a feltételes hozzáférés segíthet:



- **[Bejelentkezési kockázatok](conditions.md#sign-in-risk)**: Az Azure AD Identity Protection észleli a bejelentkezési kockázatokat. Hogyan korlátozhatja a hozzáférést, ha egy észlelt bejelentkezési kockázat egy kártékony elemet jelez? Mit tehet, ha erősebb bizonyítékra van szüksége, hogy a bejelentkezést egy legitim felhasználó hajtotta végre? Mi történik, ha a kételyei elég megalapozottak, hogy akár adott felhasználók hozzáférését is letiltsa egy alkalmazáshoz?  

- **[Hálózati hely](location-condition.md)**: Az Azure AD bárhonnan elérhető. Mi a teendő, ha a hozzáférési kísérlet egy olyan hálózati helyről történik, amely nem tartozik az informatikai részleg irányítása alá? A felhasználónév és a jelszó kombinációja megfelelő bizonyíték lehet az identitás igazolására, amikor a vállalati hálózatról próbálják elérni az erőforrásokat. Mi a teendő, ha erősebb identitásigazolást szeretne az olyan hozzáférési kísérleteknél, amelyeket a világ más, váratlan országaiból vagy régióiból kezdeményeznek? Mi a teendő, ha egyes helyekről akár blokkolni is szeretné a hozzáférést?  

- **[Eszközkezelés](conditions.md#device-platforms)**: Az Azure AD-ben a felhasználók az eszközök széles választékáról próbálhatják elérni a felhőalkalmazásokat – mobileszközökről, sőt akár saját tulajdonú eszközökről is. Mi a teendő, ha azt szeretné, csak a vállalat informatikai részlege által felügyelt eszközökről lehessen megkísérelni a hozzáférést? Mi a teendő, ha meg szeretné tiltani, hogy egyes eszköztípusok hozzáférhessenek a környezetében lévő felhőalkalmazásokhoz? 

- **[Ügyfélalkalmazás](conditions.md#client-apps)**: Napjainkban számos felhőalkalmazás különböző alkalmazástípusokon, például webes, mobil- vagy asztali alkalmazásokon keresztül is elérhető. Mi a teendő, ha olyan típusú ügyfélalkalmazással történik hozzáférési kísérlet, amely ismert hibákat okoz? Mi a teendő, ha azt szeretné, hogy bizonyos típusú alkalmazásokat csak az informatikai részleg által felügyelt eszközökről lehessen elérni? 

Ezek a kérdések és a kapcsolódó válaszok gyakori hozzáférési forgatókönyveket vázolnak fel az Azure AD feltételes hozzáféréshez. A feltételes hozzáférés az Azure Active Directory egyik képessége, amellyel szabályzatalapú módon kezelheti a hozzáférés-kezelési forgatókönyveket.


## <a name="conditional-access-policies"></a>Feltételes hozzáférési szabályzatok

A feltételes hozzáférési szabályzat egy olyan hozzáférési forgatókönyv, amely az alábbi mintát követi:

![Vezérlés](./media/overview/10.png)

Az **Akkor ezt tegye** címszó alatt határozhatja meg a szabályzat válaszát. Fontos megjegyezni, hogy a feltételes hozzáférési szabályzatok célja nem az, hogy hozzáférést adjanak egy felhőalkalmazáshoz. Az Azure AD-ben a felhőalkalmazások hozzáférésének engedélyezése a felhasználó-hozzárendelések függvénye. A feltételes hozzáférési szabályzatokkal vezérelheti, hogy az engedéllyel rendelkező felhasználók (azok a felhasználók, akik hozzáférést kaptak egy adott felhőalkalmazáshoz) hogyan férhetnek hozzá bizonyos feltételek mellett a felhőalapú alkalmazásokhoz. A válaszban további követelményeket határozhat meg, például többtényezős hitelesítést, felügyelt eszközök használatát stb. Az Azure AD feltételes hozzáférés kontextusában a szabályzatok által előírt követelményeket hozzáférés-vezérlésnek nevezzük. A legszigorúbb formájukban a szabályzatokkal blokkolható a hozzáférés. További információ: [Hozzáférés-vezérlés az Azure Active Directory feltételes hozzáféréssel](controls.md).
     

Az **Amikor ez történik** címszó alatt adhatja meg a szabályzatot aktiváló okokat. Az okokat az jellemzi, hogy több feltétel teljesül. Az Azure AD feltételes hozzáférésekben a két hozzárendelési feltétel speciális szerepet játszik:

- **[Felhasználók](conditions.md#users-and-groups)**: A hozzáférési kísérletet végrehajtó felhasználók (**Ki**). 

- **[Felhőalkalmazások](conditions.md#cloud-apps)**: A hozzáférési kísérlet célja (**Mit**).    

Ennek a két feltételnek a megadása kötelező a feltételes hozzáférési szabályzatokban. A két kötelező feltétel mellett további feltételeket is megadhat, amelyek a hozzáférési kísérlet körülményeit írják le. Gyakori példa a mobileszközök használatával vagy a vállalati hálózaton kívüli helyekről megkísérelt hozzáférés. További információ: [Az Azure Active Directory feltételes hozzáférésének feltételei](conditions.md).   

A hozzáférés-vezérlés feltételekhez való kötésével hozhat létre feltételes hozzáférési szabályzatot. 

![Vezérlés](./media/overview/51.png)

Az Azure AD feltételes hozzáférésével szabályozhatja, hogy az engedéllyel rendelkező felhasználók hogyan érhetik el a felhőalkalmazásokat. A feltételes hozzáférési szabályzatok célja, hogy a felhőalkalmazások elérésekor további hozzáférés-vezérléseket kényszerítsenek ki, amelyek a hozzáférési kísérlet körülményeit szabályozzák.

A felhőbeli alkalmazásokhoz való hozzáférések szabályzatalapú korlátozásának egyik előnye, hogy a jelen cikkben ismertetett struktúra alapján elkezdheti kidolgozni a szabályzatok követelményeit anélkül, hogy foglalkoznia kellene a műszaki megvalósítás részleteivel. 


## <a name="license-requirements-for-using-conditional-access"></a>A feltételes hozzáférések használatához szükséges licencek

A feltételes hozzáférés használatához Azure AD Prémium szintű licenc szükséges. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni:
    - a feltételekről, olvassa el [az Azure Active Directory feltételes hozzáférések feltételeit ismertető cikket](conditions.md).

    - a hozzáférés-vezérlésről, olvassa el [az Azure Active Directory feltételes hozzáférés-vezérlését ismertető cikket](controls.md).

- Ha szeretne gyakorlatot szerezni a feltételes hozzáférési szabályzatok konfigurálásában, lásd: [Többtényezős hitelesítés megkövetelése adott alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md).

- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md). 

- Ha át szeretne tekinteni egy részletes üzembehelyezési tervet a javasolt szabályzatokkal, tekintse meg a [feltételes hozzáférés üzembe helyezésére vonatkozó tervet](http://aka.ms/conditionalaccessdeploymentplan).
