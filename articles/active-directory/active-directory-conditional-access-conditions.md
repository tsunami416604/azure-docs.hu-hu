---
title: Mik azok a feltételek az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, hogyan feltételek használatával az Azure Active Directory feltételes hozzáférési szabályzat aktiválása.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 42792170593dbd94d0eae9b408c70f326891508a
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "36232379"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Mik azok a feltételek az Azure Active Directory feltételes hozzáférés? 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférésének a felhőalapú alkalmazások. Feltételes hozzáférési szabályzatot adja meg a válasz ("Ehhez") való indítására, a házirend ("Amikor ez megtörténik") az az oka. 

![Vezérlés](./media/active-directory-conditional-access-conditions/10.png)


A feltételes hozzáférés a környezetben:

- "**Ebben az esetben**" nevezzük **feltételek**. 
- "**Majd ehhez**" nevezzük **hozzáférés-vezérlés**.

A hozzáférés-vezérlést a feltételek kombinációja feltételes hozzáférési szabályzatot jelöli.

![Vezérlés](./media/active-directory-conditional-access-conditions/61.png)


A feltételes hozzáférési szabályzat nem konfigurált feltételek nem érvényesek. Bizonyos feltételek [kötelező](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) környezet feltételes hozzáférési szabályzat vonatkozik. 

Ez a cikk áttekintést, a feltételek és azok hogyan használhatók a feltételes hozzáférési szabályzat. 

## <a name="users-and-groups"></a>Felhasználók és csoportok

A felhasználók és csoportok feltétel megadása kötelező a feltételes hozzáférési szabályzat. A házirendben válassza ki is **minden felhasználó** , vagy válasszon konkrét felhasználókat és csoportokat.

![Vezérlés](./media/active-directory-conditional-access-conditions/111.png)

Amikor kiválaszt:

- **Minden felhasználó**, a házirend vonatkozik a címtárban lévő összes felhasználó. Ez magában foglalja a vendégfelhasználókat.

- **Válassza ki a felhasználók és csoportok**, beállíthatja a következő beállításokat:

    - **Minden vendégfelhasználó** – lehetővé teszi a B2B vendégfelhasználók a szabályzatok célzásához. Ez a feltétel rendelkező összes felhasználói fiók megfelel a *userType* attribútum beállítása *vendég*. Ezzel a beállítással használhatja azokban az esetekben, ahol egy szabályzatot kell alkalmazni, amint az a fiók létrejön a meghívó folyamatban, az Azure ad-ben.

    - **Címtárbeli szerepkörök** – lehetővé teszi, hogy a felhasználó szerepkör-hozzárendelés alapján a szabályzatok célzásához. Ez a feltétel például támogatja a címtárbeli szerepkörök *globális rendszergazdai* vagy *jelszókezelő*.

    - **Felhasználók és csoportok** – lehetővé teszi a felhasználók adott részhalmazához cél. Például kiválaszthatja a felhőalkalmazás kiválasztva HR alkalmazás, ha a HR-osztály minden tagját tartalmazó csoport.

Egy csoport is lehet bármilyen típusú csoportja az Azure Active Directory, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportok

A szabályzat alól is konkrét felhasználókkal vagy csoportokkal is kizárhat. Egy gyakori használati eset, a service-fiókoknak, ha a szabályzat kötelezővé teszi a többtényezős hitelesítés (MFA). 

Felhasználók adott részhalmazához célzó hasznos a központi telepítés egy új szabályzat. Egy új szabályzat érvényesítéséhez a csoportházirend-viselkedés felhasználók csak egy kezdeti készleteinek célozhat meg kell. 



## <a name="cloud-apps"></a>Felhőalkalmazások 

A cloud app egy webhely vagy szolgáltatás. Ez magában foglalja az Azure Application Proxy által védett webhelyeken. A támogatott felhőbeli alkalmazások részletes ismertetését lásd: [felhőalapú alkalmazások hozzárendelési](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

A felhőalapú alkalmazások feltétel megadása kötelező a feltételes hozzáférési szabályzat. A házirendben válassza ki is **az összes felhőalapú alkalmazások** vagy konkrét alkalmazások kiválasztása.

![Vezérlés](./media/active-directory-conditional-access-conditions/03.png)

Választhat:

- **Az összes felhőalapú alkalmazások** alapkonfiguráció szabályzatokat kell alkalmazni a teljes szervezet számára. A kijelölés egy gyakori alkalmazási helyzet egy szabályzatot, amely a bejelentkezési kockázat észlelése esetén van szükség a multi-factor authentication szolgáltatás bármely felhőalapú alkalmazás esetében. Alkalmazott házirend **az összes felhőalapú alkalmazások** hozzáférés vonatkozik az összes webhely és szolgáltatásokat. Ez a beállítás már nem korlátozódik a felhőalapú alkalmazások, amelyek szerepelnek a **válassza ki a felhőalapú alkalmazások** listája.

- Az egyes felhőalapú alkalmazások a cél a házirend által meghatározott szolgáltatások. Ha például szükség lehet a felhasználókat, hogy egy [szabályzatnak megfelelő eszköz](active-directory-conditional-access-policy-connected-applications.md) a SharePoint Online-hoz. A házirend más szolgáltatások is érvényben van, a SharePoint-tartalom, például a Microsoft Teams elérésekor. 

Konkrét alkalmazások zárja ki a szabályzatból; Ezek az alkalmazások azonban továbbra is a alkalmazni szabályzatokat az elért szolgáltatások is. 



## <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat egy mutató a annak a valószínűségét (magas, közepes vagy alacsony), hogy egy bejelentkezési kísérlet után nem hajtottak végre egy felhasználói fiók jogos tulajdonosa. Azure ad-ben a bejelentkezési kockázati szint alatt a bejelentkezési felhasználói számítja ki. A számított bejelentkezési kockázati szint használhatja feltételként egy feltételes hozzáférési házirendben. 

![Feltételek](./media/active-directory-conditional-access-conditions/22.png)

Ez a feltétel használatához rendelkeznie kell [Azure Active Directory Identity Protection](active-directory-identityprotection.md) engedélyezve van.
 
Ez a feltétel gyakori alkalmazási helyzetei azok szabályzatok, amelyek:

- A magas bejelentkezési kockázat megakadályozza, hogy a felhasználók vélhetően feldolgozásától a felhőalkalmazásokhoz hozzáférő felhasználók letiltása. 
- Többtényezős hitelesítés megkövetelése a közepes bejelentkezési kockázat rendelkező felhasználók számára. A többtényezős hitelesítés kényszerítése, megadhat további magabiztosan, hogy a bejelentkezési végzi a fiók jogos tulajdonosa.

További információkért lásd a [kockázatos bejelentkezésekkel](active-directory-identityprotection.md#risky-sign-ins) foglalkozó részt.  

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformjától jellemzi az operációs rendszer, amelyen fut az eszközön. Azure ad-ben a platform az eszköz, például a felhasználói ügynök által biztosított információk alapján azonosítja. Mivel ezek az információk nem ellenőrzött, javasoljuk, hogy minden platform őket, blokkolja a hozzáférést, az Intune-szabályzatoknak való megfelelőségét, hogy vagy az eszköz tartományhoz legyen csatlakoztatva igénylő alkalmazott házirend van-e. Alapértelmezés szerint a rendszer alkalmazza a szabályzatot minden eszközplatformokat. 


![Feltételek](./media/active-directory-conditional-access-conditions/24.png)

A támogatott eszközplatformok teljes listáját lásd: [eszköz platform feltétel](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Egy közös használati eset, ez a feltétel van egy szabályzatot, amely korlátozza a hozzáférést, a felhőalapú alkalmazások [felügyelt eszközök](active-directory-conditional-access-policy-connected-applications.md#managed-devices). További forgatókönyvek, beleértve az eszköz platform feltétel, lásd: [Azure Active Directory alapján az alkalmazásalapú feltételes hozzáférési](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Eszköz állapota

Az eszköz Eszközállapot-feltételt lehetővé teszi a hibrid Azure AD-csatlakoztatott, és az eszköz megjelölve megfelelőként ki lesznek zárva a feltételes hozzáférési szabályzat. Ez akkor hasznos, ha a szabályzat csak a további biztonság érdekében a nem felügyelt eszközön kell alkalmazni. Ha például csak a Microsoft Cloud App Security munkamenet-vezérlő érvényesítési Ha egy eszköz nem felügyelt. 


![Feltételek](./media/active-directory-conditional-access-conditions/112.png)

Ha azt szeretné, a nem felügyelt eszközök hozzáférésének letiltására, is meg kell valósítania [eszközalapú feltételes hozzáférési](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Helyek

A helyek lehetősége van, ahol kapcsolódási kísérlet kezdeményezték alapuló feltételek meghatározása. 
     
![Feltételek](./media/active-directory-conditional-access-conditions/25.png)

Ez a feltétel gyakori alkalmazási helyzetei azok szabályzatok, amelyek:

- Többtényezős hitelesítés kötelezővé tétele a felhasználók a szolgáltatás elérésére, amelyek a vállalati hálózatról.  

- Hozzáférés letiltása a felhasználók bizonyos országokban vagy régiókban a szolgáltatás elérésére. 

További információkért lásd: [a helyfeltétel Mi az Azure Active Directory feltételes hozzáférés?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Ügyfélalkalmazások

Az ügyfél alkalmazások feltétel lehetővé teszi a különböző típusú alkalmazások, például a szabályzat alkalmazásához:

- Weblapok és szolgáltatások
- Mobilalkalmazások és asztali alkalmazásokhoz. 



Egy alkalmazás akkor minősül:

- Egy webhely vagy a szolgáltatás használ a webes egyszeri bejelentkezés protokollok, SAML, WS-Fed vagy az OpenID Connect bizalmas ügyfél számára.

- A mobilalkalmazással vagy asztali alkalmazás, ha a mobilalkalmazás, OpenID Connect egy natív ügyfél használ.

Az ügyfélalkalmazások, használhatja a feltételes hozzáférési szabályzat a teljes listáját lásd: [ügyfél alkalmazások feltétel](active-directory-conditional-access-technical-reference.md#client-apps-condition) az Azure Active Directory feltételes hozzáférés műszaki útmutatóban.

Ez a feltétel gyakori alkalmazási helyzetei azok szabályzatok, amelyek:

- Szükséges egy [szabályzatnak megfelelő eszköz](active-directory-conditional-access-policy-connected-applications.md) mobil- és asztali alkalmazásokhoz, amelyek nagy mennyiségű adat letöltése az eszközre, miközben bármilyen eszközről böngészőalapú hozzáférés lehetővé teszi.

- Webes alkalmazásokhoz való hozzáférés letiltása, de a mobil és asztali alkalmazásokhoz való hozzáférés.

Webes egyszeri bejelentkezés és a modern hitelesítési protokollok használatán alkalmazhat levelezési alkalmazásokban, például a natív levelezőalkalmazások a legtöbb okostelefonok Exchange ActiveSync programot használó ezt az állapotot. Jelenleg az ügyfélalkalmazások örökölt protokollok használatával kell az AD FS használatával kell védeni.

Csak választhatja a feltétel Ha **Office 365 Exchange Online** a kiválasztott csak felhőalkalmazás.

![Felhőalkalmazások](./media/active-directory-conditional-access-conditions/32.png)

Kiválasztásával **Exchange ActiveSync** ügyfélalkalmazások, a feltétel csak támogatott, ha más feltételek nem szerepel egy konfigurált szabályzatot. Azonban szűkíthető hatókörébe tartozó ezt az állapotot csak a támogatott platformok vonatkoznak.

 
![Támogatott platformok](./media/active-directory-conditional-access-conditions/33.png)

Ez a feltétel alkalmazása csak a támogatott platformokra megegyezik az összes eszközplatformot egy [eszköz platform feltétel](active-directory-conditional-access-conditions.md#device-platforms).

![Támogatott platformok](./media/active-directory-conditional-access-conditions/34.png)


 További információkért lásd:

- [Az Azure Active Directory feltételes hozzáférés beállítása a SharePoint Online és Exchange online-hoz](active-directory-conditional-access-no-modern-authentication.md)
 
- [Az Azure Active Directory, alkalmazásalapú feltételes hozzáférés](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Az örökölt hitelesítés  

Feltételes hozzáférés mostantól érvényes régebbi Office-ügyfelek, amelyek nem támogatják a modern hitelesítést, valamint az ügyfelek, amelyek levelezési protokollok, mint például a POP, IMAP, SMTP, stb. Ez lehetővé teszi, hogy konfigurálja a házirendek, például **más ügyfelek hozzáférésének blokkolása**.


![Az örökölt hitelesítés](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Ismert problémák

- A házirend beállítása **más ügyfelek** letiltja az egyes ügyfelek, például SPConnect az egész munkahely számára. Ezen régebbi ügyfelek hitelesítése nem várt módon okozza. A probléma nem vonatkozik a fő Office-alkalmazások, például a régebbi Office-ügyfelekhez. 

- A szabályzat érvénybe léptetéséhez akár 24 órát is igénybe vehet. 


#### <a name="frequently-asked-questions"></a>Gyakori kérdések

**Ez letiltja a Exchange webes szolgáltatások (EWS)?**

Attól függ, a hitelesítési protokoll, amely EWS használja. Ha az EWS alkalmazás modern hitelesítést használ, azt fedezi a "mobilalkalmazások és asztali ügyfelek" ügyfélalkalmazás. Ha az EWS alkalmazás egyszerű hitelesítést használ, azt fedezi az "Egyéb ügyfelek" ügyfélalkalmazás.


**Milyen vezérlők más ügyfelek számára használható**

Bármilyen vezérlőt "Más ügyfelek számára" konfigurálható. Azonban a felhasználói élmény lesz a minden esetben elérésének letiltása. "Más ügyfelek" nem támogatja a vezérlők, például a többtényezős hitelesítés, megfelelő eszköz, a tartományhoz való csatlakozás, stb. 
 
**Milyen feltételek mellett más ügyfelek számára használható?**

"Más ügyfelek számára" konfigurálhatja azokat a feltételeket.

**Támogatja az Exchange ActiveSync, az összes feltételeit és szabályzóit?**

Nem. Itt látható az Exchange ActiveSync (EAS) támogatását összegzi:

- EAS csak a felhasználó és a csoportokat fognak megcélozni támogatja. Nem támogatja a Vendég, szerepkörök. Ha vendég/szerepkör feltétel van konfigurálva, minden felhasználó óta a Microsoft nem tudja megállapítani, ha a alkalmazni kell a szabályzatot a felhasználó vagy nem lesz elérhetőségekre.

- EAS csak akkor működik az Exchange kiszolgálóval, a cloud app. 

- EAS nem támogatja a tetszőleges feltételt, kivéve az ügyfélalkalmazás magát.

- EAS is konfigurálhatók, bármilyen vezérlőt (eszközmegfelelőség kivételével az összes eredményezi a blokk).

**A házirendek vonatkoznak az összes ügyfél-alkalmazások a jövőben alapértelmezés szerint?**

Nem. Nem történik változás az alapértelmezett házirend viselkedés. A házirendek továbbra is alkalmazni kell a böngészőt, és mobil alkalmazások és asztali ügyfelek alapértelmezés szerint.



## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](active-directory-conditional-access-app-based-mfa.md).

- Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-best-practices.md). 

