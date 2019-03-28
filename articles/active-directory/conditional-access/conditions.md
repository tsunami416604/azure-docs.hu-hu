---
title: Mik azok a feltételek az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, hogyan feltételek használatával az Azure Active Directory feltételes hozzáférési szabályzat aktiválása.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f95fd85b5a0fd9e905b93b9b90f18f963dbf1690
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518235"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Mik azok a feltételek az Azure Active Directory feltételes hozzáférés? 

Szabályozhatja a felhasználók miként férhetnek hozzá a felhőalapú alkalmazások használatával [Azure Active Directory (Azure AD) feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Egy feltételes hozzáférési szabályzatot határoz meg a választ ("majd ehhez"), az az oka a szabályzat elindítása ("e hiba megjelenésekor"). 

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

* **Minden vendégfelhasználó** célozza meg, olyan szabályzatot, amely B2B vendégfelhasználókat. Ez a feltétel megegyezik bármilyen felhasználói fiókot, amely rendelkezik a **userType** attribútum beállítása **vendég**. Akkor használja ezt a beállítást, ha egy szabályzatot kell alkalmazni, amint az a fiók létrejön a meghívó folyamatban, az Azure ad-ben.

* **Címtárbeli szerepkörök** célozza meg benne egy szabályzatot a felhasználó szerepkör-hozzárendelés alapján. Ez a feltétel támogatja a címtárbeli szerepköröket, például **globális rendszergazdai** vagy **jelszókezelő**.

* **Felhasználók és csoportok** célozza meg a felhasználók adott részhalmazához. Például kiválaszthatja a HR alkalmazás a cloud app választásakor a HR-osztály minden tagját tartalmazó csoport. Egy csoport is lehet bármilyen típusú csoportja az Azure Active Directory, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportok.

A szabályzat alól is konkrét felhasználókkal vagy csoportokkal is kizárhat. Egy gyakori használati eset szolgáltatásfiókok esetén a szabályzat kötelezővé teszi a többtényezős hitelesítés (MFA). 

Felhasználók adott részhalmazához célzó hasznos a központi telepítés egy új szabályzat. Egy új szabályzat érvényesítéséhez a csoportházirend-viselkedés felhasználók csak egy kezdeti készleteinek célozhat meg kell. 



## <a name="cloud-apps"></a>Felhőalkalmazások 

Egy felhőalapú alkalmazás esetében, egy webhelyre vagy szolgáltatásba. Webhelyek az Azure AD Application Proxy által védett felhőalapú alkalmazások is. Támogatott felhőbeli részletes ismertetését lásd: [felhőalapú alkalmazások hozzárendelések](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

A **felhőalkalmazások** feltétel megadása kötelező a feltételes hozzáférési szabályzat. A házirendben válassza ki is **az összes felhőalapú alkalmazások** vagy konkrét alkalmazások kiválasztása.

![Tartalmazza a felhőalapú alkalmazások](./media/conditions/03.png)

A következők szerint válasszon:

- **Az összes felhőalapú alkalmazások** alapkonfiguráció-szabályzatokhoz, a teljes szervezetre érvényesek. Használja ezt a lehetőséget a házirendekben, amelyek a többtényezős hitelesítés megkövetelése bejelentkezési kockázat észlelésekor bármely felhőalapú alkalmazás esetében. Alkalmazott házirend **az összes felhőalapú alkalmazások** hozzáférés vonatkozik az összes webhelyek és szolgáltatások. Ez a beállítás nem jelenik meg a felhőalkalmazások korlátozódik a **alkalmazások kiválasztása** listája. 

- **Alkalmazások kiválasztása** célként megadott konkrét szolgáltatások a szabályzat által. Ha például szükség lehet a felhasználókat, hogy egy [szabályzatnak megfelelő eszköz](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) a SharePoint Online-hoz. A házirend más szolgáltatások is érvényben van, a SharePoint-tartalmakhoz férhetnek. Ilyen például a Microsoft Teams. 

Konkrét alkalmazások kizárhat a szabályzat alól. Ezek az alkalmazások azonban továbbra is a alkalmazni szabályzatokat az elért szolgáltatások is. 



## <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt jelzi, hogy annak a valószínűségét (magas, közepes vagy alacsony) egy bejelentkezés nem volt végrehajtott egy felhasználói fiók jogos tulajdonosa. Az Azure AD számítja ki a bejelentkezési kockázati szint alatt egy felhasználó a bejelentkezést. A számított bejelentkezési kockázati szint használhatja feltételként egy feltételes hozzáférési házirendben.

![Bejelentkezési kockázati szintek](./media/conditions/22.png)

Ez a feltétel használatához rendelkeznie kell [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) engedélyezve van.
 
Ez a feltétel gyakori alkalmazási helyzetei, amelyek rendelkeznek a következő védelmi szabályzatok: 

- A magas bejelentkezési kockázat a felhasználók blokkolása. Ez a védelem megakadályozza, hogy a felhasználók vélhetően feldolgozásától a felhőalkalmazásokhoz hozzáférő. 
- Egy közepes méretű bejelentkezési kockázat rendelkező felhasználók számára a többtényezős hitelesítés szükséges. A többtényezős hitelesítés kényszerítése, megadhat további bizalom, hogy a bejelentkezési végzi el a fiók jogos tulajdonosa.

További információkért lásd: [letiltja a hozzáférést, a munkamenet kockázata észlelésekor](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Eszközplatformok

Az eszköz platformjától jellemzi az operációs rendszer, amely futtatja az eszközön. Azure ad-ben a platform az eszköz, például a felhasználói ügynök által biztosított információk alapján azonosítja. Ezekkel az információkkal már nem ellenőrzött. Azt javasoljuk, hogy minden platform vonatkozik egy házirend van-e. A szabályzat kell letiltja a hozzáférést, a Microsoft Intune-szabályzatok megfelelőségének megkövetelése vagy megkövetelése az eszköz tartományhoz legyen csatlakoztatva. Az alapértelmezett érték a szabályzat alkalmazásához az összes eszközplatformot. 


![Eszközplatformok konfigurálása](./media/conditions/24.png)

A támogatott eszközplatformok listáját lásd: [eszköz platform feltétel](technical-reference.md#device-platform-condition).


Egy közös használati eset, ez a feltétel van egy szabályzatot, amely korlátozza a hozzáférést, a felhőalapú alkalmazások [felügyelt eszközök](require-managed-devices.md). További forgatókönyvek, beleértve az eszköz platform feltétel, lásd: [Azure Active Directory alapján az alkalmazásalapú feltételes hozzáférési](app-based-conditional-access.md).



## <a name="device-state"></a>Eszköz állapota

Az eszköz Eszközállapot-feltételt nem tartalmazza a hibrid Azure AD-hoz csatlakoztatott eszközökre és a egy feltételes hozzáférési szabályzatot megfelelőként megjelölve eszközökre. 


![Eszközállapot konfigurálása](./media/conditions/112.png)

Ez az állapot akkor hasznos, ha a szabályzat csak további munkamenetek biztonsága nem felügyelt eszközről kell alkalmazni. Ha például csak a Microsoft Cloud App Security munkamenet-vezérlő érvényesítési Ha egy eszköz nem felügyelt. 

## <a name="locations"></a>Helyek

A helyek, ahol kísérlet történt a kapcsolat alapján feltételeket adhat meg. 

![Helyek konfigurálása](./media/conditions/25.png)

Gyakori alkalmazási esetei ezt az állapotot a következő védelmi szabályzatok:

- Többtényezős hitelesítés megkövetelése a felhasználók egy szolgáltatás elérésére, ha a vállalati hálózatról.  

- Hozzáférés letiltása a felhasználók bizonyos országokban vagy régiókban a szolgáltatás elérésére. 

További információkért lásd: [a helyfeltétel Mi az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Ügyfélalkalmazások

Alapértelmezés szerint a feltételes hozzáférési szabályzat vonatkozik a következő alkalmazásokat:

- **[Böngészőben megjelenő alkalmazásokba](technical-reference.md#supported-browsers)**  -böngésző alkalmazások közé tartoznak a webhelyeken a SAML, WS-Federation, vagy az OpenID Connect webes egyszeri bejelentkezés protokollok. Ez bármely webhelyre vagy webalkalmazásra szolgáltatás, amely regisztrálva van az OAuth bizalmas ügyfélként is vonatkozik. Ha például az Office 365 SharePoint-webhely. 

- **[Mobil- és asztali alkalmazások modern hitelesítést használó](technical-reference.md#supported-mobile-applications-and-desktop-clients)**  – az alkalmazások tartalmazzák az Office asztali alkalmazásokban, és a telefonos alkalmazások. 


Ezenkívül célként a szabályzat adott ügyfélalkalmazásokra nem modern hitelesítést használó, például:

- **[Exchange ActiveSync-ügyfelek](conditions.md#exchange-activesync-clients)**  – Ha a szabályzat akkor tiltja az Exchange ActiveSync segítségével, az érintett felhasználók beolvasása egyetlen karanténüzenetet információkat, miért van letiltva. Ha szükséges, akkor az e-mailben regisztrálták az eszközüket az Intune-nal útmutatást tartalmaz.

- **[Más ügyfelek](block-legacy-authentication.md)**  – az alkalmazások tartalmazzák a levelezési protokollok IMAP, MAPI, a jelenléti pontra Irányíthatja, SMTP és ne a modern hitelesítést használó régebbi Office-alkalmazások például az alapszintű hitelesítést használó ügyfelek számára. További információkért lásd: [modern hitelesítés működéséről, az Office 2013 és az Office 2016 ügyfélalkalmazások](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Ügyfélalkalmazások](./media/conditions/41.png)

Ez a feltétel gyakori alkalmazási helyzetei azok szabályzatok a következő követelményekkel:

- **[Egy felügyelt eszköz megkövetelése](require-managed-devices.md)**  adatletöltéshez eszköz mobil- és asztali alkalmazások esetében. Egy időben bármilyen eszközről böngészőalapú hozzáférés engedélyezése. Ebben a forgatókönyvben megakadályozza, hogy a dokumentum mentése folyamatban van, és szinkronizálja egy nem felügyelt eszközhöz. Ezzel a módszerrel csökkentheti annak valószínűsége az adatvesztést, ha az eszköz elveszett vagy ellopták.

- **[Egy felügyelt eszköz megkövetelése](require-managed-devices.md)**  alkalmazásokhoz, az ActiveSync használatával az Exchange Online-hoz.

- **[Az örökölt hitelesítés letiltása](block-legacy-authentication.md)**  az Azure AD (más ügyfelek)

- Webes alkalmazásokhoz való hozzáférés letiltása, de a mobil és asztali alkalmazásokhoz való hozzáférés.



### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-ügyfelek

Bejelölheti a **Exchange ActiveSync-ügyfelek** ha:


- A Microsoft Office 365 Exchange Online az egyetlen olyan kiválasztott felhőalapú alkalmazás.

    ![Felhőalkalmazások](./media/conditions/32.png)

- Nem kell más házirendben konfigurált feltételeknek. Azonban ez az állapot csak a alkalmazni hatókörének szűkíthető [által támogatott platformok](technical-reference.md#device-platform-condition).
 
    ![Házirend alkalmazása csak a támogatott platformokra](./media/conditions/33.png)


Ha a hozzáférés blokkolva van-e mert egy [felügyelt eszközök](require-managed-devices.md) van szükség esetén az érintett felhasználók beolvasása egyetlen e-mail, amely segítséget nyújt számukra az Intune-nal. 

Ha szükség egy jóváhagyott alkalmazáshoz, az érintett felhasználók beolvasása a szabályokat, telepítheti és használhatja az Outlook mobile ügyfél.

Egyéb esetben például MFA megadása kötelező, ha az érintett felhasználók le lesznek tiltva, mert az alapszintű hitelesítést használó ügyfelek nem támogatják a többtényezős hitelesítés.

Ezzel a beállítással a felhasználók és csoportok csak célba. Nem támogatja a Vendégek vagy szerepköröket. Ha Vendég vagy szerepkör feltétel van konfigurálva, minden felhasználó le lesznek tiltva, mert a feltételes hozzáférés nem tudja megállapítani, ha a alkalmazni kell a szabályzatot a felhasználó vagy nem.


 További információkért lásd:

- [Az Azure Active Directory feltételes hozzáférés beállítása a SharePoint Online és Exchange online-hoz](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Az Azure Active Directory, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>További lépések

- Feltételes hozzáférési szabályzat konfigurálása, lásd: [a rövid útmutató: Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

- Feltételes hozzáférési házirendjei a környezet konfigurálásához tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md). 

