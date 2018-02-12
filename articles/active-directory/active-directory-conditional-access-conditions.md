---
title: "Az Azure Active Directory feltételes hozzáférési feltételek |} Microsoft Docs"
description: "Tudnivalók a hozzárendelések használata a Azure Active Directory feltételes hozzáférési házirend indításához."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: bb2124613ccc467f3c560e92bdf760420410267c
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Az Azure Active Directory feltételes hozzáférési feltételek 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja a hogyan engedéllyel rendelkező felhasználók hozzáférést a felhőalapú alkalmazásokat. A feltételes hozzáférési szabályzatot a válasz ("Ehhez") okának indítására, a házirend ("Amikor ez történik,") megadása. 

![vezérlő](./media/active-directory-conditional-access-conditions/10.png)


A feltételes hozzáférés a környezetben:

- "**Ha ez történik**" nevezik **feltételek**. 
- "**Majd ehhez**" nevezik **hozzáférés-szabályozási**.

A feltételek és a hozzáférés-vezérlést kombinációja a feltételes hozzáférési házirend jelöli.

![vezérlő](./media/active-directory-conditional-access-conditions/61.png)

Ez a cikk áttekintést nyújt a feltételeket és azok hogyan használhatók a feltételes hozzáférési házirendben. 


## <a name="users-and-groups"></a>Felhasználók és csoportok

A felhasználók és csoportok feltétele egy feltételes hozzáférési házirendben kötelező. A házirend, vagy válassza ki is **minden felhasználó** , vagy válasszon adott felhasználókat és csoportokat.

![vezérlő](./media/active-directory-conditional-access-conditions/02.png)

Ha bejelöli:

- **Minden felhasználó**, a házirend vonatkozik, a címtárban szereplő összes felhasználó. Ez magában foglalja a vendégfelhasználók számára.

- **Válassza ki a felhasználók és csoportok**, egy célcsoport kijelölésével az adott felhasználócsoportokhoz, például minden tagját a HR részleg, amikor bejelentkeznek az HR-alkalmazásba. 

- Egy csoport lehet bármilyen típusú csoport az Azure AD, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportok.

Is kizárhat egyes felhasználók vagy csoportok egy házirend. Egy gyakori használati eset szolgáltatás fiók is, ha a házirend érvénybe lépteti a multi-factor Authentication hitelesítést. 

Felhasználók adott csoportja célzó akkor hasznos, új központi telepítésére vonatkozóan. Egy új házirend céljaként meghatározott érvényesíti a házirendet viselkedés csak egy kezdeti készleteinek. 



## <a name="cloud-apps"></a>Felhőalkalmazások 

A cloud app a webhelyek vagy szolgáltatást. Ez magában foglalja az Azure-Proxy által védett webhelyeken. A támogatott felhőalkalmazások részletes ismertetését lásd: [cloud apps hozzárendelés](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

A felhőalapú alkalmazások feltétele egy feltételes hozzáférési házirendben kötelező. A házirend, vagy válassza ki is **összes felhőalapú alkalmazások** , vagy válasszon adott alkalmazásokra.

![vezérlő](./media/active-directory-conditional-access-conditions/03.png)

Választhat:

- **Az összes felhőalapú alkalmazások** alapterv házirendeket, hogy a teljes szervezet alkalmazható. A kijelölés gyakori használati eset egy olyan házirend, többtényezős hitelesítést igényel bejelentkezési kockázat észlelésekor bármely felhőalapú alkalmazás.

- Az egyes felhőalapú alkalmazások célként megadott szolgáltatások házirend. Például megkövetelheti a felhasználókat, hogy egy [megfelelő eszköz](active-directory-conditional-access-policy-connected-applications.md) SharePoint Online eléréséhez. A házirend más szolgáltatások is érvényben van, a SharePoint-tartalom, például a Microsoft Teams elérésekor. 

Bizonyos alkalmazások zárja ki a házirend; Ezek az alkalmazások azonban továbbra is a szolgáltatások hozzáféréskor alkalmazott házirendek legyenek. 



## <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázata azt jelzi, hogy egy bejelentkezési kísérlet után nem lett végrehajtva egy felhasználói fiókot jogos tulajdonosa valószínűsége (magas, közepes vagy alacsony) a rendszer. Az Azure AD-bejelentkezés kockázati szintjének bejelentkezéskor a felhasználó számítja ki. A számított bejelentkezési kockázati szint egy feltételes hozzáférési házirendben feltételként használható. 

![Feltételek](./media/active-directory-conditional-access-conditions/22.png)

Ez a feltétel használatához rendelkeznie kell [Azure Active Directory Identity Protection](active-directory-identityprotection.md) engedélyezve van.
 
Ez a feltétel gyakori alkalmazási esetei házirendek, amelyek:

- A a felhasználók bejelentkezési nagy kockázattal potenciálisan feldolgozásától a felhőalkalmazások hozzáférését blokkolja. 
- Egy közepes bejelentkezési kockázat rendelkező felhasználók a többtényezős hitelesítést igényel. Többtényezős hitelesítés kényszerítése biztosíthat további abban, hogy, hogy a bejelentkezés végzi el egy olyan fiók jogos tulajdonosa.

További információkért lásd a [kockázatos bejelentkezésekkel](active-directory-identityprotection.md#risky-sign-ins) foglalkozó részt.  

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformjától jellemzőek, az eszközön futó operációs rendszer. Az Azure AD az eszköz, például a felhasználói ügynök által biztosított információk alapján azonosítja a platform. Mivel ezek az információk nem ellenőrzött, javasoljuk, hogy minden platform látták el, blokkolja a hozzáférést, igénylő Intune szabályzatoknak való megfelelőségét, vagy az eszköz tartományhoz csatlakoztatott lehet igénylő házirenddel rendelkezhetnek a. Alapértelmezés szerint a rendszer összes eszközplatformra házirend vonatkozik. 


![Feltételek](./media/active-directory-conditional-access-conditions/02.png)

A támogatott eszközplatformok teljes listáját lásd: [eszköz platform feltétel](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Egy gyakori használati eset, ez az állapot értéke egy házirendet, amely korlátozza a hozzáférést a felhőalapú alkalmazások [megbízható eszköz](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). További helyzeteket is, beleértve az eszköz platformja feltétel, lásd: [Azure Active Directory alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Helyek

A helyek lehetősége van a ahol kapcsolódási kísérlet kezdeményezték alapuló feltételeinek megadása. 
     
![Feltételek](./media/active-directory-conditional-access-conditions/03.png)

Ez a feltétel gyakori alkalmazási esetei házirendek, amelyek:

- Többtényezős hitelesítés megkövetelése a felhasználók számára a szolgáltatások elérésére, ha a vállalati hálózat ki vannak.  

- A szolgáltatás elérésével adott országokból vagy régiókból felhasználók hozzáférésének blokkolása. 

További információkért lásd: [Azure Active Directory feltételes hozzáférési feltételek hely](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Ügyfélalkalmazások

Az ügyfél alkalmazások feltétel lehetővé teszi, hogy egy házirendet alkalmazhat különböző típusú alkalmazás, például:

- Webhelyek és szolgáltatások
- Mobilalkalmazások és asztali alkalmazások. 

![Feltételek](./media/active-directory-conditional-access-conditions/04.png)

Az alkalmazás vonatkozik:

- Egy webhely vagy a szolgáltatást, ha azt a webes egyszeri bejelentkezés protokollokat használ, SAML, WS-Fed vagy az OpenID Connect bizalmas ügyfél számára.

- A mobilalkalmazással vagy asztali alkalmazás, ha a mobilalkalmazás OpenID Connect natív ügyfél használ.

A feltételes hozzáférési házirend használható ügyfél-alkalmazások teljes listáját lásd: a [Azure Active Directory feltételes hozzáférési technikai útmutató](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Ez a feltétel gyakori alkalmazási esetei házirendek, amelyek:

- Szükséges egy [megfelelő eszköz](active-directory-conditional-access-policy-connected-applications.md) hordozható és asztali alkalmazásokhoz, amelyek nagy mennyiségű adatok letöltése az eszközre, miközben lehetővé teszi a böngészőalapú hozzáférést, bármilyen eszközről.

- Letiltja a hozzáférést a webalkalmazásokból, de engedélyezi a hozzáférést a mobil- és asztali alkalmazások.

Mellett a webes egyszeri bejelentkezés és a modern hitelesítési protokollok megvalósítását végzi, ez a feltétel alkalmazhat e-mail alkalmazást, amely az Exchange ActiveSync protokollon, például a natív e-mail alkalmazások a legtöbb okostelefonok. Örökölt protokollok használatával ügyfélalkalmazások jelenleg, AD FS segítségével védeni kell.

 További információkért lásd:

- [Az Azure Active Directory feltételes hozzáférés SharePoint Online és Exchange Online beállítása](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory, alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>További lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 

