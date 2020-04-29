---
title: Alkalmazások végfelhasználói élményei – Azure Active Directory
description: Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások végfelhasználók számára történő központi telepítéséhez a szervezetben.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266623"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>A Azure Active Directory alkalmazások végfelhasználói élményei

Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások végfelhasználók számára történő központi telepítéséhez a szervezetben:

* Azure AD-hozzáférési panel
* Office 365 Application Launcher
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Saját belátása szerint melyik módszer (ek) et szeretné üzembe helyezni a szervezetében.

## <a name="azure-ad-access-panel"></a>Azure AD-hozzáférési panel

A hozzáférési panel https://myapps.microsoft.com egy webalapú portál, amely lehetővé teszi, hogy a végfelhasználó szervezeti fiókkal rendelkezzen a Azure Active Directoryban azon alkalmazások megtekintéséhez és elindításához, amelyekhez az Azure ad-rendszergazda hozzáférést kapott. Ha Ön a [prémium szintű Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)felhasználója, akkor a hozzáférési panelen is használhatja az önkiszolgáló csoportok kezelésére szolgáló képességeket.

![Képernyőfelvétel – az Azure AD hozzáférési paneljének portálja](media/what-is-single-sign-on/azure-ad-access-panel.png)

Alapértelmezés szerint az összes alkalmazás egyetlen oldalon jelenik meg. Azonban a gyűjtemények segítségével csoportosíthatja a kapcsolódó alkalmazásokat, és egy külön lapon is bemutathatja őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például létrehozhat logikai csoportosításokat az alkalmazások számára adott feladatok, feladatok, projektek és így tovább. További információkért lásd: [az alkalmazások gyűjteményének használata a felhasználói hozzáférési panelek testreszabásához](access-panel-collections.md). 

A hozzáférési panel el van különítve a Azure Portaltól, és nem igényli, hogy a felhasználók Azure-előfizetéssel vagy Office 365-előfizetéssel rendelkezzenek.

Az Azure AD hozzáférési paneljével kapcsolatos további információkért tekintse meg a [hozzáférési panel bevezetését](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Office 365 Application Launcher

Az Office 365-et üzembe helyező szervezetek esetében az Azure AD-n keresztül a felhasználókhoz rendelt alkalmazások is megjelennek az Office [https://portal.office.com/myapps](https://portal.office.com/myapps)365-portálon. Így egyszerűen és kényelmesen elindíthatja az alkalmazásait anélkül, hogy második portált kellene használnia, és az Office 365-et használó szervezetek számára ajánlott alkalmazás-indítási megoldás.

![A képernyőképen az Office 365 portál látható](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Az Office 365 Application Launcher szolgáltatással kapcsolatos további információkért tekintse [meg az alkalmazás megjelenése az office 365 app launcherben](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)című témakört.

## <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba

Az SAML 2,0, a WS-Federation vagy az OpenID Connect szolgáltatást támogató legtöbb összevont alkalmazás támogatja a felhasználók számára az alkalmazások indításának lehetőségét, majd az Azure AD-be való bejelentkezést az automatikus átirányítás vagy egy hivatkozásra kattintva. Ezt a szolgáltatást szolgáltató által kezdeményezett bejelentkezésnek nevezik, és az Azure AD-alkalmazás-katalógusban található legtöbb összevont alkalmazás támogatja ezt (lásd az alkalmazás egyszeri bejelentkezési konfigurációs varázslójával csatolt dokumentációt a részletekért Azure Portal).

![Példa a Mobile apps bejelentkezési oldalára](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Közvetlen bejelentkezési hivatkozások

Az Azure AD Emellett támogatja a közvetlen egyszeri bejelentkezést a jelszó-alapú egyszeri bejelentkezést, a csatolt egyszeri bejelentkezést és az összevont egyszeri bejelentkezést támogató egyes alkalmazásokra.

Ezek a hivatkozások kifejezetten olyan URL-címek, amelyek az Azure AD bejelentkezési folyamatán keresztül küldenek felhasználót egy adott alkalmazáshoz anélkül, hogy a felhasználónak el kellene indítania azokat az Azure AD hozzáférési paneljéről vagy az Office 365-ből. Ezek a **felhasználói hozzáférési URL-címek** az elérhető vállalati alkalmazások tulajdonságai alatt találhatók. A Azure Portal válassza a **Azure Active Directory** > **vállalati alkalmazások**lehetőséget. Válassza ki az alkalmazást, majd válassza a **Tulajdonságok**lehetőséget.

![Felhasználói hozzáférési URL-cím – példa a Twitter tulajdonságaiban](media/end-user-experiences/direct-sign-on-link.png)

Ezeket a hivatkozásokat bárhol másolhatja és beillesztheti, ha bejelentkezési hivatkozást szeretne megadni a kijelölt alkalmazáshoz. Ez lehet egy e-mailben vagy bármely olyan egyéni webalapú portálon, amelyet a felhasználói alkalmazás eléréséhez beállított. Íme egy példa az Azure AD Direct egyszeri bejelentkezési URL-címére a Twitteren:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

A hozzáférési panel szervezeti URL-címeihez hasonlóan az URL-cím további testreszabásához a myapps.microsoft.com tartomány után hozzá kell férnie az adott címtárhoz tartozó aktív vagy ellenőrzött tartományok valamelyikéhez. Ez biztosítja, hogy a céges védjegyezés azonnal betöltődik a bejelentkezési oldalon anélkül, hogy a felhasználónak először meg kell adnia a felhasználói azonosítót:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Ha egy jogosult felhasználó rákattint az adott alkalmazásra vonatkozó hivatkozások egyikére, először a szervezeti bejelentkezési oldalt látják (feltéve, hogy még nincsenek bejelentkezve), és a bejelentkezés után a rendszer átirányítja az alkalmazásba a hozzáférési panelen való leállítás nélkül. Ha a felhasználó nem rendelkezik az alkalmazás eléréséhez szükséges előfeltételekkel, például a jelszó-alapú egyszeri bejelentkezés böngésző bővítményével, akkor a hivatkozás arra kéri a felhasználót, hogy telepítse a hiányzó bővítményt. A hivatkozás URL-címe szintén állandó marad, ha az alkalmazás egyszeri bejelentkezési konfigurációja megváltozik.

Ezek a hivatkozások ugyanazokat a hozzáférés-vezérlési mechanizmusokat használják, mint a hozzáférési panel és az Office 365, és csak azok a felhasználók vagy csoportok fognak tudni sikeres hitelesítést végezni, akik hozzá lettek rendelve az alkalmazáshoz a Azure Portal. Azonban minden jogosulatlan felhasználó számára megjelenik egy üzenet, amely arról tájékoztatja, hogy nem kaptak hozzáférést, és a hozzáférési panel betöltésére szolgáló hivatkozásra kattintva megtekintheti azokat az elérhető alkalmazásokat, amelyekhez hozzáférése van.

## <a name="next-steps"></a>További lépések

Központi telepítési csomagok: [Azure Active Directory telepítési tervek](../fundamentals/active-directory-deployment-plans.md)
