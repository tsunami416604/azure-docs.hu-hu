---
title: Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban? | Microsoft Docs
description: Egyszeri bejelentkezés az SaaS- és webes alkalmazásokat, amelyekre szüksége van a vállalati összes engedélyezése az Azure Active Directoryt.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: article
ms.date: 09/11/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: e1bd45027ea9c3dbffc642afedc4e478c96bffe7
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?
Egyszeri bejelentkezés azt jelenti, hogy nem tudnak hozzáférni az összes alkalmazásokat és erőforrásokat, végre kell hajtani, üzleti által csak akkor, ha egyetlen felhasználói fiókkal jelentkezik be. Miután bejelentkezett, van-e hozzáférési összes szolgáltatásbeli hitelesítéshez szükséges nélkül kell alkalmazást (például adjon meg egy jelszót) még egyszer.

Számos szervezet alkalmazásokként egy szolgáltatott szoftverként (SaaS) például Office 365, a mezőben és a Salesforce a végfelhasználói termelékenység szoftver támaszkodnak. Hagyományosan informatikai személyzetet tart fenn kell egyenként létrehozása, és minden SaaS-alkalmazás a felhasználói fiókok frissítése, és a felhasználóknak kell egy jelszót az egyes SaaS-alkalmazásokhoz.

Az Azure Active Directory kiterjeszti a helyszíni Active Directory kiterjeszti a felhőbe, így a felhasználók használhatják a saját elsődleges szervezeti nem csak a tartományhoz csatlakoztatott eszközökre bejelentkezéshez és a vállalati erőforrásokat, de is a web- és SaaS-alkalmazások összes szükséges, a munkájukhoz.

Így nemcsak felhasználók nem rendelkeznek felhasználónevek több készleteinek kezelése, és jelszavak, az alkalmazások hozzáférési automatikusan kiépítése és rendszer leépíti a szervezet csoport tagjai, valamint egy alkalmazott állapotuk alapján. Az Azure Active Directory vezet be a biztonsági és irányítási vezérlők, amelyek segítségével központilag kezelheti a felhasználói hozzáférés SaaS-alkalmazások között.

Az Azure AD lehetővé teszi, hogy a mai népszerű SaaS-alkalmazásokhoz; számos egyszerű integráció identitás- és hozzáférés-felügyeletet biztosít, és lehetővé teszi az egyszeri bejelentkezés alkalmazásokhoz közvetlenül, vagy felderítését, és indítsa el őket a portálról, például az Office 365 vagy az Azure AD hozzáférési panel.

Az integráció architektúrájának a következő négy fő építőelemeket áll:

* Egyszeri bejelentkezés lehetővé teszi a felhasználók hozzáférhessenek az SaaS-alkalmazásokhoz az Azure ad-ben a saját szervezeti fiókjukba alapján. Egyszeri bejelentkezés mi lehetővé teszi, hogy a felhasználók a egyetlen szervezeti fiókjával alkalmazás felé történő hitelesítésre.
* Lehetővé teszi a felhasználók átadása, felhasználói üzembe helyezést és megszüntetést, a Szolgáltatottszoftver-alapú Windows Server Active Directory és/vagy az Azure AD a végrehajtott módosításokat a cél. A kiépített fiók pedig mi lehetővé teszi, hogy a felhasználó azt, hogy egy alkalmazás használatát, egyszeri bejelentkezést keresztül hitelesített után.
* Az Azure portál lehetővé teszi egyetlen pont, SaaS alkalmazás-hozzáférés és kezelésében, alkalmazás hozzáférés révén és a szervezet bármely jóváhagyások képes központi alkalmazáshozzáférés-kezeléshez
* Egységes jelentéskészítés és az Azure ad-ben a felhasználói tevékenység figyelése

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Hogyan működik az egyszeri bejelentkezés az Azure Active Directoryval?
A felhasználók bejelentkezésekor"" alkalmazáshoz, akkor nyissa meg egy hitelesítési folyamatot, hol bizonyítja, hogy azok mondja ki, hogy azok ki. Egyszeri bejelentkezést általában ehhez írja be egy jelszót, amely van tárolva, az alkalmazás és a felhasználó a jelszó ismerete szükséges.

Az Azure AD három különböző módon bejelentkezni alkalmazások támogatja:

* **Összevont egyszeri bejelentkezést** lehetővé teszi az alkalmazások lehessen irányítani az Azure AD-hez a felhasználók hitelesítése nem a saját jelszót kér. A támogatott alkalmazások támogatási SAML 2.0, a WS-Federation, vagy az OpenID Connect, például a protokollokat, és az egyszeri bejelentkezés richest mód.
* **Jelszó-alapú egyszeri bejelentkezést** lehetővé teszi, hogy biztonságos jelszó alkalmazástárolót és visszajátszásos a webes bővítmény vagy mobilalkalmazást használ. Ez kihasználja a meglévő bejelentkezési folyamat az alkalmazás által biztosított, de lehetővé teszi, hogy a rendszergazda számára, hogy a jelszavak kezelése és nem igényel a felhasználót, hogy a jelszó ismerete.
* **Meglévő egyszeri bejelentkezés** lehetővé teszi, hogy az Azure AD kihasználhatják a meglévő egyszeri bejelentkezést, amely még nincs beállítva az alkalmazás, de lehetővé teszi, hogy ezeket az alkalmazásokat az Office 365 vagy Azure AD hozzáférési panel portálok kell társítani, és lehetővé teszi további jelentéskészítés az Azure AD, ha az alkalmazások működését van.

Miután egy felhasználó egy alkalmazást a hitelesítette, ezek is csak egy fiók bejegyzést, az alkalmazás, amely közli az alkalmazás üzembe ahol nincs engedélyekkel és hozzáférési szintje van az alkalmazáson belüli. Ez a fiók rekord kiépítés vagy automatikusan is megtörténhet, vagy akkor fordulhat elő manuálisan egy rendszergazda előtt a felhasználó egyszeri bejelentkezéses hozzáférést biztosítja.

 Egyszeri bejelentkezés módokban és a kiépítés alább olvashat.

### <a name="federated-single-sign-on"></a>Összevont egyszeri bejelentkezést.
Összevont egyszeri bejelentkezés lehetővé teszi a felhasználók a szervezet számára automatikusan megtörténik a külső SaaS-alkalmazás által a felhasználói fiók adatait az Azure AD használatával az Azure AD.

Ebben a forgatókönyvben már kijelentkezett az Azure AD-be, és egy külső SaaS-alkalmazás által vezérelt erőforrások eléréséhez összevonási szükségtelenné teszi a felhasználó hitelesíthető.

Az Azure AD is támogatja az összevont egyszeri bejelentkezést a WS-Federation, SAML 2.0-t támogató alkalmazásokkal, vagy az OpenID connect protokollok.

További információ: [tartozó tanúsítványok összevont egyszeri bejelentkezést.](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Jelszó-alapú egyszeri bejelentkezést.
Jelszó-alapú egyszeri bejelentkezés beállítása lehetővé teszi, hogy a felhasználók a szervezet számára automatikusan megtörténik a külső SaaS-alkalmazás használatával a felhasználói fiók adatait a harmadik fél SaaS-alkalmazás az Azure ad. Ha engedélyezi ezt a szolgáltatást, az Azure AD gyűjt, és biztonságos helyen tárolja a felhasználói fiók adatait, és a kapcsolódó jelszót.

Az Azure AD bármely felhőalapú alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal jelszó-alapú egyszeri bejelentkezést is támogatja. Egy egyéni böngésző beépülő modul használatával AAD automatizálja a felhasználói bejelentkezési folyamat segítségével biztonságosan beolvasása az alkalmazás hitelesítő adatait, például a felhasználónevét és jelszavát a könyvtárból, és megadja ezeket a hitelesítő adatokat az alkalmazás bejelentkezési lapot a következő nevében: a a felhasználó. Nincsenek két használati esetek:

1. **Kezeli a rendszergazdai hitelesítő adatokat** – rendszergazdák hozzon létre és kezelheti az alkalmazás hitelesítő adatait, és ezen hitelesítő adatok hozzárendelése felhasználókhoz vagy csoportokhoz hozzáférni az alkalmazáshoz szükséges. Ezekben az esetekben a végfelhasználó nem kell tudni, hogy a hitelesítő adatokat, de továbbra is átveszi az egyszeri bejelentkezés hozzáférni az alkalmazáshoz a hozzáférési panel között, illetve a megadott hivatkozásra kattintva. Ez lehetővé teszi azon végfelhasználók számára, amellyel nincs szükségük megjegyzése vagy alkalmazás-specifikus jelszavak kezelése a hitelesítő adatok a rendszergazdák, valamint a kényelem által életciklus-felügyeletének is. A hitelesítő adatok vannak rejtjelezett a végfelhasználó az automatikus bejelentkezési folyamat során; azonban technikailag felderíthető, a felhasználó webes hibakeresés eszközökkel, és a felhasználók és rendszergazdák kell követnie a ugyanazt biztonsági házirendek, mintha közvetlenül a felhasználó által a hitelesítő adatok infrastruktúrakialakítás. Rendszergazda által megadott hitelesítő adatok hasznosak, ha sok felhasználó, például a közösségi média vagy dokumentumok megosztása az alkalmazások által közösen használt fiók számára hozzáférést biztosít.
2. **Kezeli a felhasználói hitelesítő adatokat** – rendszergazdák alkalmazásokat a felhasználók vagy csoportok rendelhet, és a végfelhasználók számára, hogy adja meg a saját hitelesítő adatait, közvetlenül a hozzáférési panelen először az alkalmazás elérésének engedélyezése. A végfelhasználók számára, amelyek révén azokat nem kell folyamatosan írja be az alkalmazás-specifikus jelszavakat az alkalmazás minden alkalommal létrejön a könnyebb elérhetőség érdekében. Felhasználók továbbra is kezelem a jelszavaikat frissítése vagy törléssel, igény szerint. A használati eset egy almodell köve rendszergazdai felügyelet a hitelesítő adatok, amelyek a rendszergazda beállíthatja az alkalmazás új hitelesítő adatait egy későbbi időpontban a felhasználói hozzáférés élményt a végfelhasználó módosítása nélkül is használható.

Mindkét esetben a hitelesítő adatok titkosítására a könyvtárban van tárolva, és az automatikus bejelentkezési folyamat során csak átadott HTTPS-KAPCSOLATON keresztül. Jelszó-alapú egyszeri bejelentkezést használ, az Azure AD egy kényelmes identitáskezelési hozzáférési megoldás nem képes a összevonási protokollok alkalmazások kínál.

Jelszó-alapú egyszeri Bejelentkezést olyan módon, az alkalmazás és a felhasználó-specifikus adatok beolvasása az Azure AD és alkalmazása a szolgáltatáson bővítmény támaszkodik. A legtöbb külső SaaS-alkalmazásokhoz az Azure AD által támogatott támogatja ezt a szolgáltatást.

Jelszó-alapú egyszeri bejelentkezéshez a végfelhasználó böngészőkkel lehet:
* Az Internet Explorer 11 – a Windows 7 vagy újabb
* Peremhálózati Windows 10 évforduló Edition vagy újabb 
* Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb
* Firefox 26.0 vagy újabb – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb verzió

### <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezést.
Egyszeri bejelentkezés egy alkalmazás konfigurálásakor az Azure-portálon biztosít egy harmadik beállítás, a "meglévő egyszeri bejelentkezéshez". Ez a beállítás egyszerűen rendszergazda az alkalmazásokra mutató hivatkozás létrehozásához, és helyezze el a kiválasztott felhasználók hozzáférési panelje.

Például ha egy alkalmazás, amely hitelesíti a felhasználókat az Active Directory összevonási szolgáltatások 2.0 eszköz használatával van konfigurálva, a rendszergazda segítségével a "meglévő egyszeri bejelentkezéshez" beállítás hozható létre hivatkozás azt a hozzáférési panelen. Amikor a felhasználók hozzáférnek a hivatkozás, hitelesített Active Directory összevonási szolgáltatások 2.0 eszköz vagy az alkalmazás által biztosított bármilyen meglévő egyszeri bejelentkezés megoldás használatával.

### <a name="user-provisioning"></a>A felhasználók átadása
Alkalmazások esetében, válassza ki az Azure AD lehetővé teszi a felhasználó automatizált üzembe helyezést és megszüntetést, külső SaaS-alkalmazásokhoz az az Azure felügyeleti portálon, a Windows Server Active Directory vagy az Azure AD identitásinformációi segítségével belül fiókok. Amikor a felhasználó engedélyeket kap az Azure AD egy ezeket az alkalmazásokat, egy fiók lehet automatikusan létrehozni (kiosztott) a cél SaaS-alkalmazáshoz.

A felhasználót törlik, vagy adataikat módosítja az Azure ad-ben, ezeket a módosításokat is megjelennek a SaaS-alkalmazáshoz. Ez azt jelenti, automatikus identitás életciklus-felügyeletének beállítása lehetővé teszi a rendszergazdáknak vezérlik, és adja meg az automatizált üzembe helyezést és megszüntetést az SaaS-alkalmazásokhoz. Az Azure AD-e identitás életciklus-felügyeletének automatizálását szerint engedélyezve van a felhasználók átadása.

További tudnivalókért lásd: [automatizált Felhasználókiépítése és megszüntetés automatizálása a SaaS-alkalmazásokhoz](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Ismerkedés az Azure AD application gallery
Próbálja ki. Egyszeri bejelentkezés az Azure AD között telepítéséhez és a szervezet által használt, SaaS-alkalmazásokhoz az alábbiakra.

### <a name="using-the-azure-ad-application-gallery"></a>Az Azure AD application gallery használatával
A [Azure Active Directory Alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/) felsorolja alkalmazásokat, amelyek kat közismerten támogató egy formája, amelyet az egyszeri bejelentkezés az Azure Active Directoryban.

![Az Azure online alkalmazásgyűjtemény](media/active-directory-appssoaccess-whatis/onlineappgallery.png)

Az alábbiakban néhány tipp által milyen lehetőségek támogatják-e alkalmazások kereséséhez:

* Az Azure AD támogatja az automatikus üzembe helyezést és megszüntetést az összes "Kiemelt" alkalmazást a [Azure Active Directory Alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/).
* Összevont kifejezetten támogató alkalmazások listájának összevont egyszeri bejelentkezést SAML, WS-Federation, például protokoll használatát, vagy az OpenID Connect található [Itt](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Ha az alkalmazás már megtalálta, elkezdheti által kövesse a részletes útmutatás a jelenik meg a gyűjtemény és az Azure felügyeleti portálhoz való egyszeri bejelentkezés engedélyezése.

### <a name="application-not-in-the-gallery"></a>Alkalmazás nem található a katalógusban?
Ha az alkalmazás nem található az Azure AD application gallery, majd telepítette ezeket a beállításokat:

* **Használja fel nem sorolt alkalmazás hozzáadása** – a gyűjtemény az Azure felügyeleti portálon az egyéni kategória segítségével a szervezet által használt fel nem sorolt alkalmazást. Bármely alkalmazás, amely támogatja az SAML 2.0, mint egy összevont alkalmazás vagy a bármely alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal jelszó SSO alkalmazásként is hozzáadhat. További részletekért lásd: Ez a cikk a [saját alkalmazás hozzáadása](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Adja hozzá a saját alkalmazást fejleszt** - szert az alkalmazás saját magának, ha az Azure AD fejlesztői dokumentáció összevont egyszeri bejelentkezés megvalósítása útmutatását vagy kiépítése az Azure ad graph API. További információkért lásd: ezek az erőforrások:
  
  * [Hitelesítési forgatókönyvek az Azure AD-hez](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Az alkalmazásintegráció kérelem** -kérése az alkalmazás használatával kell támogatása a [az Azure AD-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Active Directory-bővítményt az Azure portálon segítségével konfigurálhatja az alkalmazás egyszeri bejelentkezést. Első lépésként kell jelöljön ki egy könyvtárat a portál az Active Directory szakaszából:

![][2]

A külső SaaS-alkalmazások kezelése, megváltoztathatja az azokat a kijelölt könyvtár az alkalmazások fülre. Ez a nézet lehetővé teszi a rendszergazdáknak:

* Az Azure AD gyűjteménye, valamint a fejleszt alkalmazásokat az új alkalmazások hozzáadása
* Integrált alkalmazások törlése
* Már van integrálva az alkalmazások kezelése

Egy külső SaaS-alkalmazáshoz a szokásos felügyeleti feladatok a következők:

* Egyszeri bejelentkezés az Azure AD, Egyszeri jelszóval, vagy a cél SaaS érhető el, ha összevont egyszeri bejelentkezés engedélyezése
* Szükség esetén a felhasználók átadásához felhasználó üzembe helyezést és megszüntetést (identity életciklus-felügyeletének) engedélyezése
* Alkalmazások esetében, ahol a felhasználók átadása engedélyezve van-e mely felhasználók férhetnek hozzá az alkalmazás kiválasztása

Összevont egyszeri bejelentkezést támogatja gyűjtemény alkalmazások esetén konfigurációs általában meg kell adnia egy összevont megbízhatósági kapcsolat a külső alkalmazás és az Azure AD között létrehozásához például a tanúsítványok és a metaadatok további konfigurációs beállításokat. A konfiguráló varázsló végigvezeti a részletes adatait, és egyszerűen hozzáférhetnek a SaaS-alkalmazás-specifikus adatok és útmutatást nyújt.

Gyűjteményelem alkalmazások, amelyek támogatják az automatikus felhasználók átadásához ehhez telepítenie kell az Azure AD engedélyt a SaaS-alkalmazás a fiókokat szeretne kezelni. Mindenképpen meg kell adnia a hitelesítő adatokat az Azure AD meg a célalkalmazás keresztül hitelesítéséhez használt. E további konfigurációs beállításokat kell megadni attól függ, hogy az alkalmazás követelményeinek.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Üzembe helyezése az Azure AD integrált alkalmazások felhasználók számára
Az Azure AD többféleképpen testreszabható is telepíthet központilag alkalmazásokat a végfelhasználók számára a szervezetében:

* Az Azure AD hozzáférési panel
* Az Office 365 alkalmazásindító
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Melyik úgy dönt, hogy központi telepítése a szervezetben metódussal közül.

### <a name="azure-ad-access-panel"></a>Az Azure AD hozzáférési panel
A hozzáférési panelre a https://myapps.microsoft.com egy webes portál, amely lehetővé teszi a felhasználó szervezeti fiókkal az Azure Active Directoryban megtekintése és indítása felhőalapú alkalmazások, amelyhez már rendelkezik hozzáféréssel az Azure AD-rendszergazda által. Ha a végfelhasználó [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), az önkiszolgáló csoportkezelési képességeinek a hozzáférési Panel keresztül is használhatja.

![Az Azure AD hozzáférési Panel](media/active-directory-appssoaccess-whatis/azure-ad-access-panel.png)

A hozzáférési Panel elkülönül az Azure-portálon, és nem igényli a felhasználók számára egy Azure-előfizetés vagy az Office 365-előfizetéssel rendelkezik.

Az Azure AD hozzáférési panel további információkért tekintse meg a [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Az Office 365 alkalmazásindító
A szervezet számára, amely az Office 365 telepített alkalmazások, felhasználókon az Azure AD kijelölt is jelennek meg: az Office 365-portál https://portal.office.com/myapps. Segítségével egyszerűen és kényelmes, a felhasználók számára egy szervezet alkalmazások indítása a második portál használata nélkül, és az Office 365-tel szervezetek ajánlott alkalmazás indító megoldás.

![][4]

Az Office 365 alkalmazásindító kapcsolatos további információkért lásd: [az alkalmazás jelennek meg az Office 365 alkalmazás indító](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba
Legtöbb összevont alkalmazásokhoz, amelyek támogatják a SAML 2.0, a WS-Federation vagy az OpenID connect is támogatja: az alkalmazás-kor indulnak, és majd beolvasása bejelentkezett az Azure AD keresztül automatikus átirányítása vagy a hivatkozásra kattintva jelentkezzen be a felhasználók. Ez az úgynevezett szolgáltató-bejelentkezés kezdeményezett, és a legtöbb összevont alkalmazások az Azure AD application gallery támogatja a (lásd a dokumentációt a az alkalmazás egyszeri bejelentkezés konfigurálása varázsló segítségével az Azure felügyeleti portálján, részletekért kapcsolódó).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Közvetlen bejelentkezés hivatkozások összevont, jelszóalapú vagy meglévő alkalmazásokhoz
Az Azure AD közvetlen egyszeri bejelentkezés alkalmazásokra mutató hivatkozások egyedi, amely támogatja a jelszó-alapú egyszeri bejelentkezést, a meglévő egyszeri bejelentkezést és a bármely formájára vonatkozó összevont egyszeri bejelentkezést is támogatja.

Ezek a hivatkozások akkor kifejezetten kialakított URL-címek küldött a felhasználót az Azure AD bejelentkezési folyamat során egy adott alkalmazáshoz anélkül, hogy a felhasználó az Azure AD hozzáférési panel vagy az Office 365 elindítani őket. Egyszeri bejelentkezési URL-található az összes előre integrált alkalmazás irányítópult lapon az Active Directory szakaszában az Azure felügyeleti portálján az alábbi képernyőfelvételen látható módon.

![][6]

Ezek a hivatkozások másolható és beillesztett bárhol lehetővé szeretné tenni a bejelentkezési hivatkozásra a kijelölt alkalmazáshoz. Ennek oka az lehet, e-mailben, vagy a web-alapú bármilyen egyéni portálon, hogy a felhasználó az alkalmazások elérésére vonatkozó beállítása. Íme egy példa egy Azure AD közvetlen egyetlen bejelentkezési URL-CÍMÉT a Twitteren:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Hasonló szervezet-specifikus URL-címek, a hozzáférési panel, még jobban testre szabható az URL-cím után a myapps.microsoft.com tartomány egyik a címtáron aktív vagy ellenőrzött tartományok hozzáadásával. Ez biztosítja a vállalati védjegyek be van töltve azonnal a bejelentkezési oldal a felhasználó először adja meg a felhasználó Azonosítóját kell nélkül:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Amikor egy alkalmazás-specifikus csatolást hitelesített felhasználó kattint, akkor először tekintse meg a szervezeti bejelentkezési oldalára (feltéve, hogy nem már bejelentkezés), és bejelentkezés után a rendszer átirányítja az alkalmazás a hozzáférési panelre, először leállítása nélkül. Ha a felhasználó hiányoznak az Előfeltételek hozzáférni az alkalmazáshoz, például a jelszó-alapú egyszeri bejelentkezési bővítmény, majd a hivatkozás fogja kérni a felhasználót a hiányzó bővítmény telepítésére. A hivatkozás URL-CÍMÉT is állandó marad, ha az alkalmazás egyszeri bejelentkezés konfiguráció módosul.

Ezek a hivatkozások a hozzáférési panel és az Office 365 access control ugyanazt a mechanizmust használni, és csak ezeket a felhasználókat vagy csoportokat, amelyekre az alkalmazást az Azure felügyeleti portálon rendelt lesz tudja sikeresen hitelesíteni. Azonban bármely felhasználó, aki nem jogosult üzenet jelenik meg azzal az információval, hogy ezek nem rendelkezik hozzáféréssel, és egy hivatkozást, amelynek érik elérhető alkalmazások megtekintéséhez a hozzáférési panel betöltése vannak megadva.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [A Cloud App Discovery megállapítás nem engedélyezett a felhőalapú alkalmazásokhoz](manage-apps/cloud-app-discovery.md)
* [Bevezetés az alkalmazásokhoz való hozzáférés kezelése](active-directory-managing-access-to-apps.md)
* [Az Azure AD külső identitások kezelésére szolgáló képességeket összehasonlítása](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
