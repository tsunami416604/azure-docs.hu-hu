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
ms.openlocfilehash: a04a1693e0f626e2ee6858132d6bd6d376840039
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531059"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Mik azok a feltételek az Azure Active Directory feltételes hozzáférés? 

Szabályozhatja, hogy jogosult felhasználók hozzáférésének a felhőalapú alkalmazások használatával [Azure Active Directory (Azure AD) feltételes hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal). Feltételes hozzáférési szabályzat adja meg a választ a szabályzat riasztást kiváltó okát. Egy példa válasz **majd ehhez**. Egy példa oka **ebben az esetben**.

![OK és válasz](./media/conditions/10.png)


Feltételes hozzáférés kontextusában **ebben az esetben** nevezzük egy **feltétel**. **Majd ehhez** nevezzük egy **hozzáférés-vezérlés**. A feltételek és a hozzáférés-vezérlés kombinációja a feltételes hozzáférési szabályzatot jelöli.

![Feltételes hozzáférési szabályzat](./media/conditions/61.png)


Még nem konfigurálta a feltételes hozzáférési szabályzat feltételek nem lesznek alkalmazva. Bizonyos feltételek [kötelező](best-practices.md) környezet feltételes hozzáférési szabályzat vonatkozik. 

Ez a cikk a feltételek és a használatuk a feltételes hozzáférési szabályzat nyújt áttekintést. 

## <a name="users-and-groups"></a>Felhasználók és csoportok

A felhasználók és csoportok feltétel megadása kötelező a feltételes hozzáférési szabályzat. A házirendben válassza ki is **minden felhasználó** , vagy válasszon konkrét felhasználókat és csoportokat.

![Felhasználók és csoportok](./media/conditions/111.png)

Ha bejelöli **minden felhasználó**, minden felhasználó a címtárban, beleértve a vendégfelhasználók a házirend vonatkozik.

Ha Ön **felhasználók és csoportok kiválasztása**, beállíthatja a következő beállításokat:

* **Minden vendégfelhasználó** célozza meg, olyan szabályzatot, amely B2B vendégfelhasználókat. Ez a feltétel megegyezik bármilyen felhasználói fiókot, amely rendelkezik a **userType** attribútum beállítása **vendég**. Ezt a beállítást is használhatja, ha egy szabályzatot kell alkalmazni, amint az a fiók létrejön a meghívó folyamatban, az Azure ad-ben.

* **Címtárbeli szerepkörök** célozza meg benne egy szabályzatot a felhasználó szerepkör-hozzárendelés alapján. Ez a feltétel támogatja a címtárbeli szerepköröket, például **globális rendszergazdai** vagy **jelszókezelő**.

* **Felhasználók és csoportok** célozza meg a felhasználók adott részhalmazához. Például kiválaszthatja a HR alkalmazás a cloud app választásakor a HR-osztály minden tagját tartalmazó csoport. Egy csoport is lehet bármilyen típusú csoportja az Azure Active Directory, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportok.

A szabályzat alól is konkrét felhasználókkal vagy csoportokkal is kizárhat. Egy gyakori használati eset szolgáltatásfiókok esetén a szabályzat kötelezővé teszi a többtényezős hitelesítés (MFA). 

Felhasználók adott részhalmazához célzó hasznos a központi telepítés egy új szabályzat. Egy új szabályzat érvényesítéséhez a csoportházirend-viselkedés felhasználók csak egy kezdeti készleteinek célozhat meg kell. 



## <a name="cloud-apps"></a>Felhőalkalmazások 

Egy felhőalapú alkalmazás esetében, egy webhelyre vagy szolgáltatásba. Webhelyek az Azure AD Application Proxy által védett felhőalapú alkalmazások is. Támogatott felhőbeli részletes ismertetését lásd: [felhőalapú alkalmazások hozzárendelések](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

A **felhőalkalmazások** feltétel megadása kötelező a feltételes hozzáférési szabályzat. A házirendben válassza ki is **az összes felhőalapú alkalmazások** vagy konkrét alkalmazások kiválasztása.

![Tartalmazza a felhőalapú alkalmazások](./media/conditions/03.png)

- Válassza ki **az összes felhőalapú alkalmazások** alapkonfiguráció-szabályzatokhoz, a teljes szervezetre érvényesek. Használja ezt a lehetőséget a házirendekben, amelyek a többtényezős hitelesítés megkövetelése bejelentkezési kockázat észlelésekor bármely felhőalapú alkalmazás esetében. Alkalmazott házirend **az összes felhőalapú alkalmazások** hozzáférés vonatkozik az összes webhelyek és szolgáltatások. Ez a beállítás nem jelenik meg a felhőalkalmazások korlátozódik a **alkalmazások kiválasztása** listája. 

- Válassza ki a célként megadott konkrét szolgáltatások az egyes felhőalapú alkalmazásokat házirend. Ha például szükség lehet a felhasználókat, hogy egy [szabályzatnak megfelelő eszköz](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) a SharePoint Online-hoz. A házirend más szolgáltatások is érvényben van, a SharePoint-tartalmakhoz férhetnek. Ilyen például a Microsoft Teams. 

Konkrét alkalmazások kizárhat a szabályzat alól. Ezek az alkalmazások azonban továbbra is a alkalmazni szabályzatokat az elért szolgáltatások is. 



## <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt jelzi, hogy a magas, közepes vagy alacsony annak valószínűsége, hogy egy bejelentkezés nem volt kísérlet egy felhasználói fiók jogos tulajdonosa. Az Azure AD számítja ki a bejelentkezési kockázati szint alatt egy felhasználó a bejelentkezést. A számított bejelentkezési kockázati szint lehet egy feltétel, a feltételes hozzáférési szabályzat. 

![Bejelentkezési kockázati szintek](./media/conditions/22.png)

Ez a feltétel használatához rendelkeznie kell [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) engedélyezve van.
 
Ez a feltétel gyakori alkalmazási helyzetei, amelyek rendelkeznek a következő védelmi szabályzatok: 

- A magas bejelentkezési kockázat a felhasználók blokkolása. Ez a védelem megakadályozza, hogy a felhasználók vélhetően feldolgozásától a felhőalkalmazásokhoz hozzáférő. 
- Egy közepes méretű bejelentkezési kockázat rendelkező felhasználók számára a többtényezős hitelesítés szükséges. A többtényezős hitelesítés kényszerítése, megadhat további bizalom, hogy a bejelentkezési végzi el a fiók jogos tulajdonosa.

További információkért lásd a [kockázatos bejelentkezésekkel](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins) foglalkozó részt.  

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformjától jellemzi az operációs rendszer, amely futtatja az eszközön. Azure ad-ben a platform az eszköz, például a felhasználói ügynök által biztosított információk alapján azonosítja. Ezekkel az információkkal már nem ellenőrzött. Azt javasoljuk, hogy minden platform vonatkozik egy házirend van-e. A szabályzat kell letiltja a hozzáférést, a Microsoft Intune-szabályzatok megfelelőségének megkövetelése vagy megkövetelése az eszköz tartományhoz legyen csatlakoztatva. Az alapértelmezett érték a szabályzat alkalmazásához az összes eszközplatformot. 


![Eszközplatformok konfigurálása](./media/conditions/24.png)

A támogatott eszközplatformok listáját lásd: [eszköz platform feltétel](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition).


Egy közös használati eset, ez a feltétel van egy szabályzatot, amely korlátozza a hozzáférést, a felhőalapú alkalmazások [felügyelt eszközök](../active-directory-conditional-access-policy-connected-applications.md#managed-devices). További forgatókönyvek, beleértve az eszköz platform feltétel, lásd: [Azure Active Directory alapján az alkalmazásalapú feltételes hozzáférési](../active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Eszköz állapota

Az eszköz Eszközállapot-feltételt nem tartalmazza a hibrid Azure AD-hoz csatlakoztatott eszközökre és a egy feltételes hozzáférési szabályzatot megfelelőként megjelölve eszközökre. Ez az állapot akkor hasznos, ha a szabályzat csak további munkamenetek biztonsága nem felügyelt eszközről kell alkalmazni. Ha például csak a Microsoft Cloud App Security munkamenet-vezérlő érvényesítési Ha egy eszköz nem felügyelt. 


![Eszközállapot konfigurálása](./media/conditions/112.png)

Ha azt szeretné, a nem felügyelt eszközök hozzáférésének letiltására, alkalmazzon [eszközalapú feltételes hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Helyek

A helyek, ahol kísérlet történt a kapcsolat alapján feltételeket adhat meg. 

![Helyek konfigurálása](./media/conditions/25.png)

Gyakori alkalmazási esetei ezt az állapotot a következő védelmi szabályzatok:

- Többtényezős hitelesítés megkövetelése a felhasználók egy szolgáltatás elérésére, ha a vállalati hálózatról.  

- Hozzáférés letiltása a felhasználók bizonyos országokban vagy régiókban a szolgáltatás elérésére. 

További információkért lásd: [a helyfeltétel Mi az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Ügyfélalkalmazások

Az ügyfél alkalmazások feltétel használatával különböző típusú alkalmazás egy házirendet alkalmazhatja. Példák webhelyek, szolgáltatások, mobilalkalmazások és asztali alkalmazásokhoz. 



Egy alkalmazás osztályozása a következő:

- Egy webhelyre vagy szolgáltatásba, ha azt webes egyszeri bejelentkezés protokollokat használ, SAML, WS-Fed, vagy az OpenID Connect bizalmas ügyfél számára.

- A mobilalkalmazás vagy asztali alkalmazás, ha a mobilalkalmazás, OpenID Connect egy natív ügyfél használ.

Az ügyfélalkalmazások, használhatja a feltételes hozzáférési házirend listájáért lásd: [ügyfél alkalmazások feltétel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) az Azure Active Directory feltételes hozzáférés műszaki útmutatóban.

Gyakori alkalmazási esetei ezt az állapotot a következő védelmi szabályzatok: 

- Szükséges egy [szabályzatnak megfelelő eszköz](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) mobil- és asztali alkalmazásokhoz, amelyek nagy mennyiségű adat letöltése az eszközön. Egy időben bármilyen eszközről böngészőalapú hozzáférés engedélyezése.

- Webes alkalmazásokhoz való hozzáférés letiltása, de a mobil és asztali alkalmazásokhoz való hozzáférés.

Ezt az állapotot webes egyszeri bejelentkezés és a modern hitelesítési protokollok is alkalmazható. Is alkalmazhat, levelezési alkalmazásokban, amely a Microsoft Exchange ActiveSync programot használ. Például a legtöbb okostelefonok natív levelezőalkalmazásait. 

Az ügyfél alkalmazások feltétel csak válassza ki, ha a Microsoft Office 365 Exchange Online az egyetlen olyan kiválasztott felhőalapú alkalmazás.

![Felhőalkalmazások](./media/conditions/32.png)

Kiválasztásával **Exchange ActiveSync** ügyfélként alkalmazások feltétel csak akkor, ha nincs más házirendben konfigurált feltételek támogatott. Azonban szűkíthető hatóköre csak a támogatott platformokra alkalmazza ezt az állapotot.

 
![Házirend alkalmazása csak a támogatott platformokra](./media/conditions/33.png)

Ez a feltétel alkalmazása csak a támogatott platformokra megegyezik az összes eszközplatformot egy [eszköz platform feltétel](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Eszközplatformok konfigurálása](./media/conditions/34.png)


 További információval a következő cikkek szolgálnak:

- [Az Azure Active Directory feltételes hozzáférés beállítása a SharePoint Online és Exchange online-hoz](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Az Azure Active Directory, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Örökölt hitelesítés  

Feltételes hozzáférés mostantól a régebbi Microsoft Office ügyfelekről, amelyek nem támogatják a modern hitelesítést vonatkozik. Levelezési protokollok, például a POP, IMAP és SMTP használó ügyfelek is vonatkozik. Az örökölt hitelesítés használata esetén is beállítható szabályzatok, például **más ügyfelek hozzáférésének blokkolása**.


![Ügyfélalkalmazások konfigurálása](./media/conditions/160.png)  


#### <a name="known-issues"></a>Ismert problémák

- A házirend beállítása **más ügyfelek** letiltja az egyes ügyfelek, például SPConnect az egész munkahely számára. Ezt a blokkot, mert a régebbi ügyfelek hitelesítése nem várt módon történik. A probléma nem vonatkozik a fő Office-alkalmazások, például a régebbi Office-ügyfelekhez. 

- A házirend érvénybe lép, akár 24 órát is igénybe vehet. 


#### <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** letiltja ezt a hitelesítést a Microsoft Exchange-webszolgáltatások?

Ez a hitelesítési protokoll által használt webes Exchange-szolgáltatások függ. Az Exchange-webszolgáltatások alkalmazás modern hitelesítést használja, ha alá tartozó a **mobilalkalmazások és asztali ügyfelek** ügyfélalkalmazás. Alapszintű hitelesítés hatálya alá tartozik a **más ügyfelek** ügyfélalkalmazás.


**K:** vezérlési lehetőségeket is felhasználhatom **más ügyfelek**?

Bármilyen vezérlőt is konfigurálható a **más ügyfelek**. A végfelhasználói élmény lesz azonban minden esetben a hozzáférés blokkolva. **Más ügyfelek** nem támogatják a vezérlők, például a többtényezős hitelesítés, a megfelelő eszköz és a tartományhoz való csatlakozást. 
 
**K:** milyen feltételek mellett is felhasználhatom **más ügyfelek**?

Tetszőleges feltételt is konfigurálható a **más ügyfelek**.

**K:** Does Exchange ActiveSync támogatja az összes feltételeit és szabályzóit?

Nem. A következő lista foglalja össze az Exchange ActiveSync-támogatását: 

- Exchange ActiveSync csak a felhasználó és a csoportokat fognak megcélozni támogatja. Nem támogatja a Vendégek vagy szerepköröket. Ha Vendég vagy szerepkör feltétel van konfigurálva, az összes felhasználók le lesznek tiltva. Exchange ActiveSync minden felhasználó letiltja, mert nem tudja megállapítani, ha a alkalmazni kell a szabályzatot a felhasználó vagy nem.

- Exchange ActiveSync csak a Microsoft Exchange online-hoz a cloud app működik. 

- Exchange ActiveSync kivételével az ügyféloldali alkalmazás bármilyen feltétel nem támogatja. 

- Exchange ActiveSync bármilyen vezérlőt is konfigurálhatók. Eszközmegfelelőség kivételével az összes vezérlő egy blokk vezethet.

**K:** a házirendek vonatkoznak az összes ügyfél-alkalmazások a jövőben alapértelmezés szerint?

Nem. Nem történik változás az alapértelmezett házirend viselkedés. A házirendek továbbra is a böngésző és a mobilalkalmazások és asztali ügyfelek alapértelmezés szerint vonatkozik.



## <a name="next-steps"></a>További lépések

- Feltételes hozzáférési szabályzat konfigurálása, lásd: [a rövid útmutató: többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

- Feltételes hozzáférési házirendjei a környezet konfigurálásához tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md). 

