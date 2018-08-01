---
title: Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? | Microsoft Docs
description: Azure Active Directory használatával engedélyezze az egyszeri bejelentkezés az összes szükséges üzleti SaaS- és webes alkalmazásokat.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 4ad1416f79b8cf9c03904da5f9efc1d1aae475d9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364030"
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?
Egyszeri bejelentkezés azt jelenti, nem tudnak hozzáférni az összes alkalmazásokat és erőforrásokat kell tennie az üzleti, csak egyszer, egyetlen felhasználói fiókkal jelentkezzen be. Miután bejelentkezett, elérheti összes alkalmazás hitelesítéséhez szükséges nélkül (például adjon meg egy jelszót) egy második alkalommal.

Számos szervezet támaszkodik a szoftvert, mint a szoftverszolgáltatások (SaaS) alkalmazások, például Office 365-höz, a Box vagy a Salesforce a végfelhasználói termelékenység. Hagyományosan informatikai munkatársak külön-külön létrehozni, és minden egyes SaaS-alkalmazás felhasználói fiókjainak frissíteni kell, és a felhasználók kell észben tartaniuk minden egyes SaaS-alkalmazáshoz tartozó jelszót.

Az Azure Active Directory kiterjeszti a helyszíni Active Directory a felhőben, felhasználók a saját elsődleges szervezeti fiókjukba, nem csak jelentkezzen be a tartományhoz csatlakoztatott eszközök és a vállalati erőforrásokhoz, de is a web- és SaaS-alkalmazások mindegyike szükséges a a feladat.

Ezért nem csak felhasználó nem rendelkezik több példányban felhasználónevek kezeléséhez, és jelszavakat, hozzáférési automatikusan lehet kiépített vagy Szolgáltatáskulcs alkalmazásaikat a szervezet csoport tagjai, valamint egy alkalmazott állapotuk alapján. Az Azure Active Directory biztonsági és cégirányítási vezérlők, amelyek lehetővé teszik, hogy központilag kezelheti a különböző SaaS-alkalmazások felhasználói hozzáférés mutatja be.

Az Azure AD lehetővé teszi, hogy megkönnyíti az integrációt a többhöz, napjaink népszerű SaaS-alkalmazások; identitás és hozzáférés-kezelést biztosít, és lehetővé teszi a felhasználóknak az egyszeri bejelentkezési alkalmazások közvetlenül, vagy meg és indíthassák el azokat a portálról, például az Office 365 vagy az Azure AD hozzáférési panel.

Az integráció architektúrája a következő négy építőelemeket áll:

* Egyszeri bejelentkezés lehetővé teszi a felhasználók számára azok alapján saját szervezeti fiókjukba, az Azure AD SaaS-alkalmazások eléréséhez. Egyszeri bejelentkezés, ami lehetővé teszi a felhasználóknak a hitelesítést az alkalmazáshoz egyetlen szervezeti fiókjával.
* Felhasználók létrehozásának lehetővé teszi a felhasználó üzembe helyezést és megszüntetést újra a számítógépet a Windows Server Active Directory és/vagy az Azure AD-ben végrehajtott változtatások alapján SaaS TARGET. A kiépített fiók, ami lehetővé teszi, hogy egy felhasználó engedélyt kapjon az alkalmazás használatára, miután elvégezte a hitelesítést az egyszeri bejelentkezés.
* Központosított alkalmazáshozzáférés-kezelés az Azure portal segítségével egyetlen pont SaaS-alkalmazás-hozzáférés és kezelésében, lehetővé teszi az alkalmazás hozzáférési döntéshozatalhoz és a szervezet minden tagja számára jóváhagyások delegálása
* Egységes jelentéskészítés és az Azure ad-ben a felhasználói tevékenység figyelése

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Hogyan működik az egyszeri bejelentkezés az Azure Active Directoryval?
Amikor a felhasználók bejelentkeznek egy alkalmazást, azok lépjen egy hitelesítési folyamat, hogy hol a-e ki mondják igazolásához szükséges. Nélkül egyszeri bejelentkezést a hitelesítési folyamat megadásakor egy jelszót, amelyet az alkalmazások általában történik, és ismernie kell a jelszót a felhasználóknak kell.

Az Azure AD három különböző módon való bejelentkezéshez alkalmazásokat támogatja:

* **Összevont egyszeri bejelentkezés** lehetővé teszi az alkalmazások átirányítása az Azure AD-felhasználói hitelesítés helyett egy saját jelszót kér. Összevont egyszeri bejelentkezés alkalmazásokhoz, hogy támogatási protokollok, például a SAML 2.0, WS-Federation, vagy az OpenID Connect, és az egyszeri bejelentkezés richest mód esetén támogatott.
* **Jelszavas egyszeri bejelentkezés** lehetővé teszi a biztonságos tárolását és visszajátszani egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Jelszavas egyszeri bejelentkezés az alkalmazás által biztosított a meglévő folyamat használja, de lehetővé teszi, hogy egy rendszergazda kezelje a jelszavakat, és nem igényel a felhasználót, hogy ismeri a jelszavát.
* **Meglévő egyszeri bejelentkezés** lehetővé teszi bármely meglévő egyszeri bejelentkezésre van beállítva az alkalmazás, de lehetővé teszi, hogy ezeket az alkalmazásokat lehet kapcsolódni az Office 365 vagy az Azure AD hozzáférési panel portálok kihasználhatja az Azure AD, és lehetővé teszi a további jelentéskészítés az Azure ad-ben, amikor az alkalmazások elindítása van.

Egy felhasználó egy alkalmazással rendelkezik hitelesítést követően is szükségük van üzembe helyezve, amely arra utasítja az alkalmazás az alkalmazáson belüli engedélyeit és hozzáférési szintet, amelyeknél az alkalmazás egy ügyfélrekordot. A kiépítés, a fiók rekord vagy automatikusan történik, vagy akkor fordulhat elő, manuálisan egy rendszergazda előtt áll rendelkezésre a felhasználó egyszeri bejelentkezéses hozzáférést.

 További részleteket az alábbi egyszeri bejelentkezési módok és kiépítés alatt.

### <a name="federated-single-sign-on"></a>Összevont egyszeri bejelentkezés
Összevont egyszeri bejelentkezés lehetővé teszi a felhasználók a szervezetben, hogy automatikusan jelentkezzenek be külső SaaS-alkalmazásokban az Azure AD használata az Azure AD-ből származó felhasználói fiókadatok.

Ebben a forgatókönyvben már már bejelentkezett az Azure ad-ben, és a egy külső SaaS-alkalmazás által vezérelt erőforrásokat elérni kívánt összevonási szükségtelenné teszi a felhasználó hitelesíthető.

Az Azure AD összevont egyszeri bejelentkezést olyan alkalmazásokkal, amelyek támogatják a SAML 2.0, WS-Federation, is támogatja, vagy az OpenID connect protokollok.

Lásd még: [kezeléséről összevont egyszeri bejelentkezés](manage-certificates-for-federated-single-sign-on.md)

### <a name="password-based-single-sign-on"></a>Jelszóalapú egyszeri bejelentkezés
Jelszavas egyszeri bejelentkezés konfigurálása lehetővé teszi, hogy a felhasználók a szervezetben, hogy automatikusan jelentkezzenek be külső SaaS-alkalmazásokban az Azure AD a felhasználóifiók-adatokat a külső SaaS-alkalmazás használatával. Ha engedélyezi ezt a funkciót, az Azure ad-ben gyűjt, és biztonságosan tárolja a felhasználói fiók és a kapcsolódó jelszó.

Az Azure AD jelszóalapú egyszeri bejelentkezés bármely felhőalapú alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal támogatására. Egy egyéni böngészőbővítmény használatával AAD automatizálja a bejelentkezési folyamat használatával biztonságosan beolvasása a könyvtárból alkalmazás hitelesítő adatait, például a felhasználónevét és jelszavát, és ezeket a hitelesítő adatokat köt az alkalmazás bejelentkezési oldalán a felhasználó nevében. Nincsenek két használati esetek:

1. **Kezeli a rendszergazdai hitelesítő adatokat** – rendszergazdák létrehozhatnak és alkalmazás hitelesítő adatok kezelése, és ezeket a hitelesítő adatokat hozzárendelése a felhasználókhoz vagy csoportokhoz kell hozzáférni az alkalmazáshoz. Ezekben az esetekben a végfelhasználónak nem kell tudni, hogy a hitelesítő adatokat, de továbbra is a hozzáférési paneljükön vagy egy hivatkozásra kattintva átveszi az egyszeri bejelentkezéses hozzáférést az alkalmazáshoz. Ez a folyamat lehetővé teszi, hogy mindkét, életciklus-felügyelet a hitelesítő adatok a rendszergazdák, valamint a kényelem által a végfelhasználók számára, amellyel nincs szükségük ne felejtse el, vagy alkalmazásspecifikus jelszavak kezeléséhez. A hitelesítő adatok vannak rejtjelezett a végfelhasználó az automatikus bejelentkezési folyamat során; azonban a felhasználó webhelyen-hibakereső eszközeivel technikailag felderíthető azok, és a felhasználók és rendszergazdák érdemes követnie az azonos biztonsági házirendek, mintha közvetlenül a felhasználó által a hitelesítő adatok számára. Rendszergazda által megadott hitelesítő adatok hasznosak, ha sok felhasználó, például a közösségi médiában vagy a dokumentumok megosztása az alkalmazások által közösen használt fiók számára hozzáférést biztosít.
2. **Kezeli a felhasználói hitelesítő adatokat** – rendszergazdák alkalmazások, felhasználók vagy csoportok hozzárendelése, és lehetővé teszi a végfelhasználók számára, hogy adja meg a saját hitelesítő adatait, közvetlenül az alkalmazás hozzáférési paneljükön először elérésekor. Ez egy kényelmes a végfelhasználók számára, amellyel, nem kell folyamatosan adja meg az alkalmazás-specifikus jelszavak férhetnek hozzá az alkalmazást hoz létre. Felhasználók továbbra is frissíteni vagy törölni, szükség szerint kezelheti a jelszavukat. Ezt a használati esetet, egy almodell korongot rendszergazdai felügyelet a hitelesítő adatok, amellyel a rendszergazda beállíthatja az alkalmazás új hitelesítő adatait egy későbbi időpontban a végfelhasználó app access élménye módosítása nélkül is használható.

Mindkét esetben a hitelesítő adatok titkosítására a könyvtárban van tárolva, és csak átadott HTTPS-kapcsolaton keresztül az automatikus bejelentkezési folyamat során. Jelszavas egyszeri bejelentkezés használata esetén az Azure AD kínál egy kényelmes identitás hozzáférés-kezelési megoldása az alkalmazásokat, amelyek nem képesek összevonási protokollok támogatása.

Jelszóalapú egyszeri bejelentkezés böngészőbővítmény biztonságos az alkalmazás és a felhasználó-specifikus információk beolvasása az Azure ad-ből, és alkalmazza azt a szolgáltatás támaszkodik. A legtöbb külső SaaS-alkalmazásokhoz az Azure AD által támogatott támogatja ezt a szolgáltatást.

A jelszóalapú egyszeri bejelentkezés a végfelhasználó böngészők lehet:
* Az Internet Explorer 11 – Windows 7-es vagy újabb
* Edge a Windows 10 Évfordulós kiadása vagy újabb 
* Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb
* A Firefox 26.0 vagy később – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb

### <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezés
Egyszeri bejelentkezés az alkalmazás konfigurálásakor az Azure portal biztosít egy harmadik lehetőség a "meglévő egyszeri bejelentkezés". Ezt a beállítást egyszerűen lehetővé teszi a rendszergazda hozzon létre egy hivatkozást egy alkalmazásba, és helyezze el a hozzáférési panelen a kiválasztott felhasználók számára.

Például ha egy alkalmazás, amely hitelesíti a felhasználót az Active Directory összevonási szolgáltatások 2.0 van beállítva, a rendszergazda segítségével a "meglévő egyszeri bejelentkezéshez" lehetőséget a hozzáférési panelen, egy hivatkozás létrehozásához. Amikor a felhasználó hozzáfér a hivatkozás, csak a hitelesítést az Active Directory összevonási szolgáltatások 2.0 vagy az alkalmazás által biztosított bármely meglévő egyszeri bejelentkezési megoldás használatával.

### <a name="user-provisioning"></a>Felhasználók átadása
Válassza ki az alkalmazások az Azure AD lehetővé teszi automatizált felhasználókiépítése és megszüntetést külső SaaS-alkalmazásaiból az Azure Portalon található példa fióklistán használata a Windows Server Active Directory vagy az Azure AD identity adatokat. Amikor egy felhasználó engedélyekkel alkalmazások közül legalább egy Azure AD-ben, egy fiók automatikusan létrehozhatók (kiépített), a célzott SaaS-alkalmazás.

Amikor a felhasználót törlik, vagy adataik módosításokat az Azure ad-ben, ezeket a változásokat is is megjelennek a SaaS-alkalmazás. Ez azt jelenti, automatizált identitás életciklus-kezelés konfigurálása lehetővé teszi a rendszergazdák a vezérlőelemet, és adja meg az automatikus üzembe helyezést és megszüntetést az SaaS-alkalmazásokhoz. Az identitás-életciklus-felügyelet automatizálása az Azure AD-ben engedélyezve van a felhasználó kiépítésével.

További tudnivalókért lásd: [automatizált Felhasználókiépítése és -megszüntetés SaaS-alkalmazásokhoz](../active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Ismerkedés az Azure AD alkalmazáskatalógusában
Készen áll a kezdésre? Egyszeri bejelentkezés az Azure AD között üzembe helyezéséhez és SaaS-alkalmazásokhoz, amelyek a szervezet használja, kövesse ezeket az irányelveket.

### <a name="using-the-azure-ad-application-gallery"></a>Az Azure AD alkalmazáskatalógusában használatával
A [Azure Active Directory Alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/) biztosít, amely támogatja az egyszeri bejelentkezés az Azure Active Directoryval egyfajta ismert alkalmazások listája.

![Az Azure online alkalmazás-katalógusban](media/what-is-single-sign-on/onlineappgallery.png)

Íme néhány tipp az alkalmazások által az általuk támogatott funkciókról kereséséhez:

* Az Azure AD automatikus üzembe helyezést és megszüntetést, szereplő összes "Kiemelt" alkalmazás támogatja a [Azure Active Directory Alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/).
* Kifejezetten támogató összevont alkalmazások listájának összevont egyszeri bejelentkezéshez a SAML, WS-Federation, például a protokoll használatával, vagy OpenID Connect található [Itt](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Miután az alkalmazás talált, részletes utasításokat követve az alkalmazáskatalógusban, és az Azure Portalon való egyszeri bejelentkezés engedélyezése megkezdheti.

### <a name="application-not-in-the-gallery"></a>A katalógusban nem szereplő alkalmazást?
Ha az alkalmazás nem található az Azure AD alkalmazáskatalógusában, majd ezen lehetőségek állnak rendelkezésére:

* **Adjon hozzá egy listán nem szereplő alkalmazást használ** – az egyéni kategória használata az Azure Portalon az alkalmazásgyűjtemény listán nem szereplő alkalmazást, amely a szervezet használja. Minden olyan alkalmazás, amely támogatja az SAML 2.0, mint az összevont alkalmazások vagy bármilyen alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal egyszeri bejelentkezés jelszó alkalmazásként is hozzáadhat. További részletekért lásd: Ez a cikk a [saját alkalmazás hozzáadása](../application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Adjon hozzá saját alkalmazást fejleszt** – Ha az alkalmazás saját maga is rendelkezik fejlesztett összevont egyszeri bejelentkezés megvalósítása az Azure AD fejlesztői dokumentáció útmutatását, vagy üzembe az Azure AD graph API-t. További információkért tekintse meg ezeket az erőforrásokat:
  
  * [Hitelesítési forgatókönyvek az Azure AD-hez](../active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Kérése egy app-integráció** -kérelem használatával van szüksége az alkalmazás támogatása a [az Azure AD Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Active Directory-bővítményt az Azure Portal segítségével konfigurálhatja az alkalmazás egyszeri bejelentkezést. Első lépésként ki kell választania egy könyvtárat a portálon az Active Directory szakaszban:

![](./media/what-is-single-sign-on/azuremgmtportal.png)

A külső SaaS-alkalmazások kezeléséhez, válthat az alkalmazások lapon a kiválasztott címtár be. Ez a nézet lehetővé teszi a rendszergazdáknak:

* Az Azure AD katalógusából származó, valamint a fejleszt alkalmazásokat az új alkalmazások hozzáadása
* Integrált alkalmazások törlése
* Már integrálta az alkalmazások kezeléséhez

Egy külső SaaS-alkalmazás a szokásos felügyeleti feladatok a következők:

* Egyszeri bejelentkezés az Azure AD-jelszavas egyszeri Bejelentkezést használ, vagy ha elérhető a cél SaaS, összevont egyszeri bejelentkezés engedélyezése
* Szükség esetén a felhasználókiépítés az üzembe helyezést és megszüntetést (identity lifecycle management) felhasználó engedélyezése
* Alkalmazások 
* Amennyiben a felhasználók átadásának engedélyezve van, kiválasztása, hogy mely felhasználók hozzáférhetnek, amelyek

Katalógusbeli alkalmazásokat, amelyek támogatják az összevont egyszeri bejelentkezés, a konfiguráció jellemzően megadását igényli további konfigurációs beállítások, például a tanúsítványok és a metaadatok között, a harmadik féltől származó alkalmazások és az Azure AD összevont megbízhatósági kapcsolat létrehozásához. A konfigurációs varázsló végigvezeti Önt a részleteket, és a SaaS-alkalmazás-specifikus adatokhoz és az utasításokat is egyszerű hozzáférést biztosít.

Katalógusbeli alkalmazásokat, amelyek támogatják a felhasználók automatikus, az ehhez az Azure AD engedélyt a SaaS-alkalmazásban a fiókokat szeretne kezelni. Minimális meg kell adnia a hitelesítő adatokat az Azure AD meg a célalkalmazás keresztül hitelesítéséhez használt. E további konfigurációs beállításokat kell megadni attól függ, hogy az alkalmazás követelményeinek.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>A felhasználók számára az Azure Active Directoryba integrált alkalmazások üzembe helyezése
Az Azure AD több testre szabható módszert is biztosít a végfelhasználók számára a szervezetben az alkalmazások központi telepítése:

* Az Azure AD hozzáférési panel
* Az Office 365 alkalmazásindítójában
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Melyik mód(ok) a szervezetében telepíteni kíván a saját belátása szerint értékeli.

### <a name="azure-ad-access-panel"></a>Az Azure AD hozzáférési panel
A hozzáférési Panel Itt https://myapps.microsoft.com egy olyan webes portál, amely lehetővé teszi, hogy a végfelhasználó szervezeti fiókkal az Azure Active Directoryban megtekintése és indítása felhőalapú alkalmazásokat, amelyhez már rendelkezik hozzáféréssel az Azure AD-rendszergazda által. Ha a végfelhasználó [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), úgy is használhatja az önkiszolgáló csoportkezelési képességeinek a hozzáférési panelen keresztül.

![Az Azure AD hozzáférési Panel](media/what-is-single-sign-on/azure-ad-access-panel.png)

A hozzáférési panelen elkülönül az Azure Portalon, és nem igényel a felhasználók számára egy Azure-előfizetés vagy az Office 365-előfizetéssel rendelkezik.

Az Azure AD hozzáférési panel további információkért lásd: a [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Az Office 365 alkalmazásindítójában
A szervezet számára, amely az Office 365 üzembe helyezett alkalmazások hozzárendelve a felhasználókhoz az Azure AD-n keresztül is megjelennek, az Office 365 portálon https://portal.office.com/myapps. Segítségével könnyen és kényelmesen használható a felhasználók számára egy szervezet alkalmazások indítása a második a portál használata nélkül, és az ajánlott alkalmazás indításakor megoldás használja az Office 365 szervezetek számára.

![](./media/what-is-single-sign-on/officeapphub.png)

Az Office 365 alkalmazásindítójában kapcsolatos további információkért lásd: [jelennek meg az Office 365 appindítóban alkalmazása](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba
Legtöbb összevont alkalmazásokhoz, amelyek támogatják a SAML 2.0, WS-Federation vagy OpenID connect is támogatás lehetővé teszi a felhasználók számára az alkalmazások indítása, és ezután első bejelentkezett az Azure AD-n keresztül automatikus átirányítása vagy egy hivatkozásra kattintva jelentkezzen be. Ez az úgynevezett szolgáltató-kezdeményezett bejelentkezési, és leginkább összevont alkalmazások az Azure AD alkalmazáskatalógusában támogatja a (lásd a dokumentációt a részletek az Azure Portalon az alkalmazás egyszeri bejelentkezési konfigurációjának varázsló társított).

![](./media/what-is-single-sign-on/workdaymobile.png)

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Közvetlen bejelentkezés hivatkozásokat összevont, jelszóalapú vagy meglévő alkalmazásokhoz
Az Azure AD közvetlen egyszeri bejelentkezés mutató hivatkozások jelszóalapú egyszeri bejelentkezés, a meglévő egyszeri bejelentkezést és a összevont egyszeri bejelentkezés bármilyen támogató egyéni alkalmazások is támogatja.

Ezek a hivatkozások akkor kifejezetten kialakított URL-címeket, anélkül, hogy azokat az Azure AD hozzáférési panel vagy az Office 365 felhasználói bevezetésével a felhasználó leállította az Azure AD bejelentkezési folyamat egy adott alkalmazáshoz küldése. Az egyszeri bejelentkezési URL-címek megtalálhatók az irányítópult lapon minden olyan előre integrált alkalmazás az Azure Portalon, az Active Directory szakaszában az alábbi képernyőképen látható módon.

![](./media/what-is-single-sign-on/deeplink.png)

Ezek a hivatkozások másolható, és a beillesztett bárhol lehetővé szeretné tenni a bejelentkezési hivatkozásra a kijelölt alkalmazáshoz. Ez lehet egy e-mailben, vagy a webes bármely egyéni portálon, amely a felhasználó alkalmazás-hozzáférési beállítása. Íme egy példa egy Azure AD közvetlen egyszeri bejelentkezési URL-cím a Twitter használatához:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Hasonlóan a szervezet konkrét URL-címek esetében a hozzáférési panelen, további testre szabhatja az URL-címet a myapps.microsoft.com tartomány ellenőrzése után a aktív vagy egy ellenőrzött tartomány a címtár egyik hozzáadásával. Ez biztosítja a vállalati védjegyek betöltött azonnal a bejelentkezési oldalon a felhasználónak meg kellene először a felhasználói azonosító megadása nélkül:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Amikor egy jogosult felhasználó az alábbi alkalmazás-specifikus hivatkozások kattint, akkor először tekintse meg a szervezeti bejelentkezési oldal (feltéve, hogy azok még nem már tette), és bejelentkezést követően a rendszer átirányítja az alkalmazás a hozzáférési panelen, először leállítása nélkül. Ha a felhasználó hiányoznak az Előfeltételek hozzáférni az alkalmazáshoz, például a jelszavas egyszeri bejelentkezés böngészőbővítmény majd a hivatkozás fogja kérni a felhasználót a hiányzó bővítmény telepítéséhez. A hivatkozás URL-CÍMÉT is állandó marad, ha az alkalmazás egyszeri bejelentkezési konfiguráció módosításait.

Ezeket a hivatkozásokat használja ugyanazt a hozzáférési vezérlési mechanizmust a hozzáférési panelen és az Office 365-höz, és csak adott felhasználók vagy csoportok, akiknek vannak hozzárendelve az alkalmazás az Azure Portalon lesz képes sikeresen hitelesíteni. Azonban bármely felhasználó, aki nem jogosult megjelenik egy üzenet tájékoztatja, hogy azok még nem kapott hozzáférést, és betöltése, amelyhez hozzáférésük van az elérhető alkalmazások megtekintéséhez a hozzáférési panel egy hivatkozást kapnak.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
* [Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery)
* [Bevezetés az alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
* [Az Azure AD külső identitások kezelésére szolgáló képességek összehasonlítása](../active-directory-b2b-compare-b2c.md)


