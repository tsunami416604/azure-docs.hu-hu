---
title: Az Azure Active Directory feltételes hozzáférési feltételek |} Microsoft Docs
description: Tudnivalók a hozzárendelések használata a Azure Active Directory feltételes hozzáférési házirend indításához.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3cb8e598864bccfbea24a2aec5d9387ff903e51c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Az Azure Active Directory feltételes hozzáférési feltételek 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja a hogyan engedéllyel rendelkező felhasználók hozzáférést a felhőalapú alkalmazásokat. A feltételes hozzáférési szabályzatot a válasz ("Ehhez") okának indítására, a házirend ("Amikor ez történik,") megadása. 

![Vezérlés](./media/active-directory-conditional-access-conditions/10.png)


A feltételes hozzáférés a környezetben:

- "**Ha ez történik**" nevezik **feltételek**. 
- "**Majd ehhez**" nevezik **hozzáférés-szabályozási**.

A feltételek és a hozzáférés-vezérlést kombinációja a feltételes hozzáférési házirend jelöli.

![Vezérlés](./media/active-directory-conditional-access-conditions/61.png)


Nincs beállítva a feltételes hozzáférési házirend feltételek nem érvényesek. Egyes feltételek [kötelező](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) feltételes hozzáférési házirendet alkalmazhat a környezetben. 

Ez a cikk áttekintést nyújt a feltételeket és azok hogyan használhatók a feltételes hozzáférési házirendben. 

## <a name="users-and-groups"></a>Felhasználók és csoportok

A felhasználók és csoportok feltétele egy feltételes hozzáférési házirendben kötelező. A házirend, vagy válassza ki is **minden felhasználó** , vagy válasszon adott felhasználókat és csoportokat.

![Vezérlés](./media/active-directory-conditional-access-conditions/111.png)

Ha bejelöli:

- **Minden felhasználó**, a házirend vonatkozik, hogy a címtárban szereplő összes felhasználó. Ez magában foglalja a vendégfelhasználók számára.

- **Válassza ki a felhasználók és csoportok**, beállíthatja a következő beállításokat:

    - **Minden vendégfelhasználók** -lehetővé teszi a B2B vendégfelhasználók számára a szabályzatok célzásához. Ez a feltétel rendelkező felhasználói fiók megfelel a *userType* attribútum értékének beállítása *vendég*. Ez a beállítás olyan esetekben, ahol egy házirendet kell alkalmazni, amint az a fiók létrejön egy összehíváshoz folyamatában, az Azure AD is használhatja.

    - **Directory szerepkörök** -lehetővé teszi, amelyekre a házirend a felhasználói szerepkör-hozzárendelés alapján. Ez a feltétel támogatja directory szerepkörök például *globális rendszergazda* vagy *jelszókezelő*.

    - **Felhasználók és csoportok** -lehetővé teszi, hogy a felhasználók adott csoportja cél. Például kiválaszthatja egy csoportot, amely tartalmazza a HR osztály összes tagja, ha egy felhő alkalmazásként HR alkalmazást.

Egy csoport lehet bármilyen típusú csoport az Azure AD, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportok

Is kizárhat egyes felhasználók vagy csoportok egy házirend. Egy gyakori használati eset szolgáltatás fiók is, ha a házirend érvénybe lépteti a többtényezős hitelesítés (MFA). 

Felhasználók adott csoportja célzó akkor hasznos, új központi telepítésére vonatkozóan. Egy új házirend céljaként meghatározott érvényesíti a házirendet viselkedés csak egy kezdeti készleteinek. 



## <a name="cloud-apps"></a>Felhőalkalmazások 

A cloud app a webhelyek vagy szolgáltatást. Ez magában foglalja az Azure-Proxy által védett webhelyeken. A támogatott felhőalkalmazások részletes ismertetését lásd: [cloud apps hozzárendelés](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

A felhőalapú alkalmazások feltétele egy feltételes hozzáférési házirendben kötelező. A házirend, vagy válassza ki is **összes felhőalapú alkalmazások** , vagy válasszon adott alkalmazásokra.

![Vezérlés](./media/active-directory-conditional-access-conditions/03.png)

Választhat:

- **Az összes felhőalapú alkalmazások** alapterv házirendeket, hogy a teljes szervezet alkalmazható. A kijelölés gyakori használati eset egy olyan házirend, többtényezős hitelesítést igényel bejelentkezési kockázat észlelésekor bármely felhőalapú alkalmazás. Egy házirend is tartozik **összes felhőalapú alkalmazások** hozzáférés vonatkozik minden webhelyen és szolgáltatásra. Ez a beállítás nem korlátozódik a felhőalapú alkalmazásokat, amelyek a **válasszon felhőalkalmazások** listája.

- Az egyes felhőalapú alkalmazások célként megadott szolgáltatások házirend. Például megkövetelheti a felhasználókat, hogy egy [megfelelő eszköz](active-directory-conditional-access-policy-connected-applications.md) SharePoint Online eléréséhez. A házirend más szolgáltatások is érvényben van, a SharePoint-tartalom, például a Microsoft Teams elérésekor. 

Bizonyos alkalmazások zárja ki a házirend; Ezek az alkalmazások azonban továbbra is a szolgáltatások hozzáféréskor alkalmazott házirendek legyenek. 



## <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázata azt jelzi, hogy egy bejelentkezési kísérlet után nem lett végrehajtva egy felhasználói fiókot jogos tulajdonosa valószínűsége (magas, közepes vagy alacsony) a rendszer. Az Azure AD-bejelentkezés kockázati szintjének bejelentkezéskor a felhasználó számítja ki. A számított bejelentkezési kockázati szint egy feltételes hozzáférési házirendben feltételként használható. 

![Feltételek](./media/active-directory-conditional-access-conditions/22.png)

Ez a feltétel használatához rendelkeznie kell [Azure Active Directory Identity Protection](active-directory-identityprotection.md) engedélyezve van.
 
Ez a feltétel gyakori alkalmazási esetei házirendek, amelyek:

- A felhasználók bejelentkezési nagy kockázattal potenciálisan feldolgozásától a felhőalkalmazások hozzáférését blokkolja. 
- Többtényezős hitelesítés megkövetelése a közepes bejelentkezési kockázata a felhasználók számára. Többtényezős hitelesítés kényszerítése biztosíthat további abban, hogy, hogy a bejelentkezés végzi el egy olyan fiók jogos tulajdonosa.

További információkért lásd a [kockázatos bejelentkezésekkel](active-directory-identityprotection.md#risky-sign-ins) foglalkozó részt.  

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformjától jellemzőek, az eszközön futó operációs rendszer. Az Azure AD az eszköz, például a felhasználói ügynök által biztosított információk alapján azonosítja a platform. Mivel ezek az információk nem ellenőrzött, javasoljuk, hogy minden platform látták el, blokkolja a hozzáférést, igénylő Intune szabályzatoknak való megfelelőségét, vagy az eszköz tartományhoz csatlakoztatott lehet igénylő házirenddel rendelkezhetnek a. Alapértelmezés szerint a rendszer összes eszközplatformra házirend vonatkozik. 


![Feltételek](./media/active-directory-conditional-access-conditions/24.png)

A támogatott eszközplatformok teljes listáját lásd: [eszköz platform feltétel](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Egy gyakori használati eset, ez az állapot értéke egy házirendet, amely korlátozza a hozzáférést a felhőalapú alkalmazások [által felügyelt eszközök](active-directory-conditional-access-policy-connected-applications.md#managed-devices). További helyzeteket is, beleértve az eszköz platformja feltétel, lásd: [Azure Active Directory alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Az eszköz állapotát

Az eszköz állapota feltétel lehetővé teszi, hogy az Azure AD hibrid tartományhoz csatlakoztatott és eszközök megjelölve megfelelőnek feltételes hozzáférési házirend alól. Ez akkor hasznos, ha a házirend csak további munkamenet biztonsága nem felügyelt eszközön kell alkalmazni. Például csak kényszerítése a Microsoft Cloud App Security munkamenet vezérlő a nem felügyelt eszközök esetén. 


![Feltételek](./media/active-directory-conditional-access-conditions/112.png)

Ha azt szeretné, a nem felügyelt eszközök hozzáférésének letiltására, akkor meg kell valósítania [eszközalapú feltételes hozzáférési](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Helyek

A helyek lehetősége van a ahol kapcsolódási kísérlet kezdeményezték alapuló feltételeinek megadása. 
     
![Feltételek](./media/active-directory-conditional-access-conditions/25.png)

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

