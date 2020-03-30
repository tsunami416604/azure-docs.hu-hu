---
title: Végfelhasználói élmény alkalmazásokhoz – Azure Active Directory
description: Az Azure Active Directory (Azure AD) számos testreszabható módon telepítheti az alkalmazásokat a szervezet végfelhasználói számára.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266623"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Végfelhasználói élmény az Azure Active Directoryban lévő alkalmazásokszámára

Az Azure Active Directory (Azure AD) számos testreszabható módot kínál az alkalmazások telepítésére a szervezet végfelhasználói számára:

* Az Azure AD hozzáférési panelje
* Az Office 365 alkalmazásindítója
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

A szervezetben üzembe helyezett metódus(ok) a saját belátása szerint telepíthetők.

## <a name="azure-ad-access-panel"></a>Az Azure AD hozzáférési panelje

A Hozzáférési https://myapps.microsoft.com panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban egy szervezeti fiókkal rendelkező végfelhasználó megtekinthesse és elindíthatja azokat az alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. Ha Ön az [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)végfelhasználója, önkiszolgáló csoportkezelési képességeket is használhat a Hozzáférési panelen keresztül.

![A képernyőkép az Azure AD Access Panel portálját jeleníti meg](media/what-is-single-sign-on/azure-ad-access-panel.png)

Alapértelmezés szerint az összes alkalmazás egy oldalon jelenik meg. A gyűjtemények segítségével azonban csoportosíthatja a kapcsolódó alkalmazásokat, és külön lapon mutathatja be őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például logikai csoportokat hozhat létre alkalmazásokból adott szerepkörökhöz, feladatokhoz, projektekhez és így tovább. További információt a [Saját alkalmazások gyűjtemények használata a felhasználói hozzáférési panelek testreszabása című témakörben talál.](access-panel-collections.md) 

A Hozzáférési panel elkülönül az Azure Portaltól, és nem követeli meg a felhasználóktól, hogy Azure-előfizetéssel vagy Office 365-előfizetéssel rendelkezjenek.

Az Azure AD hozzáférési panelről további információt a [hozzáférési panel bevezetet című témakörben talál.](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="office-365-application-launcher"></a>Az Office 365 alkalmazásindítója

Az Office 365-öt üzembe helyező szervezetek esetében az Azure AD-n keresztül a [https://portal.office.com/myapps](https://portal.office.com/myapps)felhasználókhoz rendelt alkalmazások az Office 365 portálon is megjelennek a webhelyen. Ez megkönnyíti és kényelmessé teszi a szervezet felhasználói számára, hogy egy második portál használata nélkül indítsák el alkalmazásaikat, és ez az ajánlott alkalmazásindítási megoldás az Office 365-öt használó szervezetek számára.

![A képernyőkép az Office 365 portált mutatja](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Az Office 365 alkalmazásindítójáról további információt A [bapp megjelenése az Office 365 alkalmazásindítójában](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)című témakörben talál.

## <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba

Az SAML 2.0, WS-Federation vagy OpenID connect szolgáltatást támogató legtöbb összevont alkalmazás szintén támogatja a felhasználók számára az alkalmazás indítását, majd az Azure AD-n keresztül imitálással vagy a bejelentkezéshez való hivatkozásra kattintva. Ezt nevezzük szolgáltató által kezdeményezett bejelentkezésnek, és az Azure AD-alkalmazásgyűjteményben található legtöbb összevont alkalmazás támogatja ezt (a részleteket az alkalmazás egyszeri bejelentkezési konfigurációs varázslójából csatolt dokumentációban találja).

![Példa mobilalkalmazás bejelentkezési lapjára](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Közvetlen bejelentkezési hivatkozások

Az Azure AD is támogatja a közvetlen egyszeri bejelentkezési hivatkozásokat az egyes alkalmazások, amelyek támogatják a jelszóalapú egyszeri bejelentkezés, a kapcsolódó egyszeri bejelentkezés, és bármilyen formában az összevont egyszeri bejelentkezés.

Ezek a hivatkozások kifejezetten kialakított URL-címek, amelyek egy felhasználót az Azure AD bejelentkezési folyamaton keresztül küldenek egy adott alkalmazáshoz anélkül, hogy a felhasználónak el kellene indítania őket az Azure AD hozzáférési panelről vagy az Office 365-ből. Ezek **a felhasználói hozzáférési URL-címek** a rendelkezésre álló vállalati alkalmazások tulajdonságai alatt találhatók. Az Azure Portalon válassza az **Azure Active Directory** > **Enterprise-alkalmazások**lehetőséget. Jelölje ki az alkalmazást, majd válassza **a Tulajdonságok lehetőséget.**

![Példa a Felhasználói hozzáférés URL-címére a Twitter tulajdonságaiban](media/end-user-experiences/direct-sign-on-link.png)

Ezek a hivatkozások bárhol másolhatók és beilleszthetők, ahol bejelentkezési hivatkozást szeretne biztosítani a kijelölt alkalmazáshoz. Ez lehet egy e-mailben, vagy bármely egyéni webalapú portálon, amelyet a felhasználói alkalmazások eléréséhez állított be. Íme egy példa egy Azure AD közvetlen egyszeri bejelentkezési URL-cím re:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

A hozzáférési panel szervezetspecifikus URL-címéhez hasonlóan ezt az URL-címet is testreszabhatja, ha a myapps.microsoft.com tartomány után hozzáadja a címtár egyik aktív vagy ellenőrzött tartományát. Ez biztosítja, hogy a szervezeti márkajelzés azonnal betöltődjön a bejelentkezési oldalra anélkül, hogy a felhasználónak először meg kellene adnia a felhasználói azonosítóját:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Amikor egy jogosult felhasználó rákattint az egyik ilyen alkalmazás-specifikus hivatkozások, először látni a szervezeti bejelentkezési oldalon (feltéve, hogy még nincs bejelentkezve), és miután a bejelentkezés után átirányítja az alkalmazásba megállás nélkül a hozzáférési panelen először. Ha a felhasználónak hiányoznak az alkalmazás eléréséhez szükséges előfeltételek, például a jelszóalapú egyjelű böngészőbővítmény, akkor a hivatkozás a hiányzó bővítmény telepítését kéri. A hivatkozás URL-címe is állandó marad, ha az alkalmazás egyszeri bejelentkezési konfigurációja megváltozik.

Ezek a hivatkozások ugyanazokat a hozzáférés-vezérlési mechanizmusokat használják, mint a hozzáférési panel és az Office 365, és csak azok a felhasználók vagy csoportok, akik az Azure Portalon az alkalmazáshoz vannak rendelve, sikeresen hitelesíthetik magukat. Azonban minden olyan felhasználó, aki nem jogosult, egy üzenetet fog látni, amely elmagyarázza, hogy nem kaptak hozzáférést, és kapnak egy hivatkozást a hozzáférési panel betöltéséhez, hogy megtekinthesse azokat az elérhető alkalmazásokat, amelyekhez hozzáféréssel rendelkeznek.

## <a name="next-steps"></a>További lépések

A központi telepítési csomagokról az [Azure Active Directory telepítési terveiben](../fundamentals/active-directory-deployment-plans.md) talál részt.
