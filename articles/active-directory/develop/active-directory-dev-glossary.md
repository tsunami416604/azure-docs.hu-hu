---
title: "Az Azure Active Directory fejlesztői fogalmak |} Microsoft Docs"
description: "A gyakran használt Azure Active Directory fejlesztői fogalmak és szolgáltatások feltételei listáját."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1002ce29ff0d9ee2a6eac44c6e4402fc7e9ade31
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="azure-active-directory-developer-glossary"></a>Az Azure Active Directory fejlesztői szószedet
Ez a cikk az Azure Active Directory (AD) fejlesztői alapfogalmakat, amelyek hasznosak, ha az Azure AD alkalmazásfejlesztés megtanulni részénél definíciókat tartalmazza.

## <a name="access-token"></a>hozzáférési jogkivonat
Olyan típusú [biztonsági jogkivonat](#security-token) által kiadott egy [engedélyezési server](#authorization-server), és használják egy [ügyfélalkalmazás](#client-application) ahhoz, hogy hozzáférhessen egy [védett erőforrás kiszolgáló](#resource-server). Általában formájában egy [JSON webes jogkivonat (JWT)][JWT], a jogkivonat, úgy, hogy az ügyfél által megadott engedély a [erőforrás tulajdonosa](#resource-owner), kért hozzáférési szintet. A token tartalmazza az összes alkalmazható [jogcímek](#claim) tárgyú, engedélyezi az ügyfélalkalmazás használhatják a hitelesítő adatok űrlap egy adott erőforráshoz történő hozzáféréskor. Ez is szükségtelenné teszi közzé az ügyfél hitelesítő adatait az erőforrás tulajdonosa.

Hozzáférési jogkivonatok vannak más néven "Felhasználó + alkalmazás" vagy "Alkalmazás csak", attól függően, hogy a hitelesítő adatok képviselt. Például amikor egy ügyfél-alkalmazás használja a:

* ["Engedélyezési kód" hitelesítésengedélyezési](#authorization-grant), a végfelhasználó az erőforrás tulajdonosa erőforrás eléréséhez az ügyfél engedélyezési delegálása, először hitelesíti. Az ügyfél ezt követően hitelesíti, ha a hozzáférési jogkivonat beszerzése. A jogkivonat néha lehet hivatkozni pontosabban, a "Felhasználó + alkalmazás" jogkivonatot, mivel azt jelenti, hogy mind a felhasználó engedélyezett, az ügyfélalkalmazás és az alkalmazás.
* ["Ügyfél-hitelesítő adatok" hitelesítésengedélyezési](#authorization-grant), az ügyfél az egyetlen hitelesítést biztosít, működik-e nélkül az erőforrás-tulajdonos hitelesítési/engedélyezési, így a token néha lehet hivatkozni "Csak alkalmazás" tokenként.

Lásd: [Azure AD jogkivonat-hivatkozást] [ AAD-Tokens-Claims] további részleteket.

## <a name="application-manifest"></a>Az alkalmazásjegyzék
A szolgáltatás által biztosított a [Azure-portálon][AZURE-portal], az identitás alkalmazást, a frissítés hozzá van rendelve egy mechanizmusként JSON-ábrázolását állít elő [ Alkalmazás] [ AAD-Graph-App-Entity] és [szolgáltatásnév] [ AAD-Graph-Sp-Entity] entitásokat. Lásd: [az Azure Active Directory alkalmazásjegyzékének megismerése] [ AAD-App-Manifest] további részleteket.

## <a name="application-object"></a>alkalmazásobjektum
Ha Ön regisztrálása vagy frissítéséhez az alkalmazás a [Azure-portálon][AZURE-portal], a portál létrehoz frissítések alkalmazásobjektum és a hozzá tartozó [szolgáltatás egyszerű objektum](#service-principal-object) a bérlő. Az application objektum *meghatározása* az alkalmazás egy sablon, amelynek a megfelelő szolgáltatás egyszerű objektumok elő biztosító identitáskonfigurációs globálisan (ahol hozzáféréssel rendelkezik az összes bérlőre), között  *származtatott* helyileg történő futtatása (az egy adott bérlő) használható.

Lásd: [alkalmazás és szolgáltatás egyszerű objektumok] [ AAD-App-SP-Objects] további információt.

## <a name="application-registration"></a>alkalmazás regisztrálása
Annak érdekében, hogy egy alkalmazás integrálása, és delegálása az Azure AD-identitás- és hozzáférés-kezelési funkciók, regisztrálnia kell azt az Azure AD-val [bérlői](#tenant). Regisztrálnia az alkalmazást az Azure AD, ha meg van adva egy identitás-konfigurációt az alkalmazáshoz, lehetővé téve az Azure AD integrálása és használatát, mint:

* Az egyszeri bejelentkezés az Azure AD Identity Management használatával robusztus felügyeletet és [OpenID Connect] [ OpenIDConnect] protokollmegvalósítás
* Való hozzáférés közvetítőalapú [védett erőforrások](#resource-server) által [ügyfélalkalmazások](#client-application), az Azure AD OAuth 2.0 keresztül [engedélyezési server](#authorization-server) végrehajtása
* [Hozzájárulás keretrendszer](#consent) kezeléséhez az ügyfél a védett erőforrások eléréséhez, erőforrás tulajdonosa engedélyezési alapján.

Lásd: [alkalmazások integrálása az Azure Active Directory] [ AAD-Integrating-Apps] további részleteket.

## <a name="authentication"></a>Hitelesítés
A folyamat, kihívást egy entitás jogos hitelesítő adatokat, az alapot biztosít egy rendszerbiztonsági tag identitás- és hozzáférés-vezérlési létrehozásához. Során egy [OAuth2 hitelesítésengedélyezési](#authorization-grant) például a felek hitelesítéséhez a következők a szerepkör tölti [erőforrás tulajdonosa](#resource-owner) vagy [ügyfélalkalmazás](#client-application)attól függően, hogy a a támogatás használt.

## <a name="authorization"></a>Engedélyezési
A folyamat, egy hitelesített biztonsági egyszerű engedély kell végrehajtani valamit. Az Azure AD programozási modell két elsődleges használati esetek szerepelnek:

* Során egy [OAuth2 hitelesítésengedélyezési](#authorization-grant) folyamata: Ha a [erőforrás tulajdonosa](#resource-owner) engedélyt biztosít a [ügyfélalkalmazás](#client-application), így az ügyfél az erőforrás eléréséhez a tulajdonos erőforrásokat.
* Az ügyfél által erőforrások elérése során: által megvalósított módon a [erőforrás-kiszolgáló](#resource-server)használatával a [jogcím](#claim) értékek szerepelnek a [hozzáférési jogkivonat](#access-token) való hozzáférés-vezérlési döntéseket alapján őket.

## <a name="authorization-code"></a>Engedélyezési kód
Egy rövid élettartamú "token" megadott egy [ügyfélalkalmazás](#client-application) által a [engedélyezési végpont](#authorization-endpoint), a "engedélyezési kód" folyamata, egyet a négy OAuth2 részeként [engedélyezési biztosít](#authorization-grant). A kód válaszul hitelesítését az ügyfélalkalmazás küld vissza a [erőforrás tulajdonosa](#resource-owner), amely jelzi, az erőforrás tulajdonosa a kért erőforrások elérésére engedélyt adta át. A folyamat részeként a később beváltott a kód egy [hozzáférési jogkivonat](#access-token).

## <a name="authorization-endpoint"></a>engedélyezési végpont
A végpont által megvalósított közül a [engedélyezési server](#authorization-server)használatával kommunikál a [erőforrás tulajdonosa](#resource-owner) annak érdekében, hogy egy [hitelesítésengedélyezési](#authorization-grant) az OAuth2 során engedélyezési grant flow. Attól függően, hogy a használt engedélyezési grant folyamatot, a tényleges támogatás megadott változhat, beleértve egy [engedélyezési kód](#authorization-code) vagy [biztonsági jogkivonat](#security-token).

Tekintse meg az OAuth2-specifikációt [engedélyezési biztosítani típusok] [ OAuth2-AuthZ-Grant-Types] és [engedélyezési végpont] [ OAuth2-AuthZ-Endpoint] szakaszokat, és a [OpenIDConnect specification] [ OpenIDConnect-AuthZ-Endpoint] további részleteket.

## <a name="authorization-grant"></a>hitelesítésengedélyezési
A hitelesítő adatok képviselő a [erőforrás tulajdonosa](#resource-owner) [engedélyezési](#authorization) a védett erőforrások eléréséhez, hogy engedélyezni egy [ügyfélalkalmazás](#client-application). Egy ügyfélalkalmazás egyikét használhatja a [négy adja meg az OAuth2 hitelesítési keretrendszer által megadott] [ OAuth2-AuthZ-Grant-Types] támogatás, attól függően, hogy az ügyfél/szemben támasztott követelményeit megszerezni: "hitelesítésengedélyezési kód", "ügyfél hitelesítő adatai megadják","implicit grant"és"erőforrás tulajdonosa jelszó hitelesítő adatai megadják". A hitelesítő adatokat küld vissza az ügyfélnek esik, vagy egy [hozzáférési jogkivonat](#access-token), vagy egy [engedélyezési kód](#authorization-code) (cseréje később a hozzáférési token) használt hitelesítésengedélyezési típusától függően.

## <a name="authorization-server"></a>engedélyezési kiszolgáló
Meghatározása szerint a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], a kiszolgáló hozzáférés kiadására jogkivonatok a [ügyfél](#client-application) a asikereshitelesítésután[erőforrás tulajdonosa](#resource-owner) és annak engedélyezéséhez. A [ügyfélalkalmazás](#client-application) a hitelesítési kiszolgáló futásidőben keresztül kommunikál a [engedélyezési](#authorization-endpoint) és [token](#token-endpoint) végpontok, összhangban az OAuth2 definiált [engedélyezési biztosít](#authorization-grant).

Alkalmazások integrálása az Azure AD, ha az Azure AD megvalósítja a hitelesítési kiszolgálói szerepkört az Azure AD-alkalmazások és a Microsoft service API-k, például [Microsoft Graph API-k][Microsoft-Graph].

## <a name="claim"></a>Jogcímszabály
A [biztonsági jogkivonat](#security-token) jogcímeket, amely helyességi feltételek egy entitást tartalmaz (például egy [ügyfélalkalmazás](#client-application) vagy [erőforrás tulajdonosa](#resource-owner)) (például a egymásikentitáshoz[erőforrás-kiszolgáló](#resource-server)). Jogcímek olyan név/érték párok, amely a token tárgyú tények továbbítása (például a hitelesített rendszerbiztonsági tagot az [engedélyezési server](#authorization-server)). A jogcímeket egy adott jogkivonat több változók, például a típusuk, a tulajdonos, az alkalmazás konfigurációja stb hitelesítéséhez használt hitelesítő adat típusának token függenek.

Lásd: [az Azure AD-jogkivonatok referenciájából] [ AAD-Tokens-Claims] további részleteket.

## <a name="client-application"></a>ügyfélalkalmazás
Meghatározása szerint a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], egy alkalmazás, amelynek eredményeképpen a védett erőforrás-kérelem a következő nevében: a [erőforrás tulajdonosa](#resource-owner). Az "ügyfél" kifejezés nem feltétlenül jelenti azt bármely adott megvalósítási hardverjellemzők (például, hogy az alkalmazás végrehajtja a kiszolgálón, asztali vagy más eszközök).  

Egy ügyfélalkalmazás kérelmek [engedélyezési](#authorization) az erőforrás tulajdonosa részt egy [OAuth2 hitelesítésengedélyezési](#authorization-grant) egymást követő, és előfordulhat, hogy adatelérés API-k vagy az erőforrás tulajdonosa nevében. Az OAuth2 hitelesítési keretrendszer [határozza meg az ügyfelek kétféle][OAuth2-Client-Types], "bizalmas" és "nyilvános", az ügyfél képes bizalmasan kezeljék a hitelesítő adatok alapján. Alkalmazások is létrehozható egy [webes ügyfél (bizalmas)](#web-client) egy webkiszolgálón, amely fut egy [natív ügyfél (nyilvános)](#native-client) telepítve az eszközön, vagy egy [felhasználói ügynök-alapú ügyfél (nyilvános)](#user-agent-based-client)amely fut egy eszköz böngészőjében.

## <a name="consent"></a>Hozzájárulás
Folyamatán a [erőforrás tulajdonosa](#resource-owner) engedély egy [ügyfélalkalmazás](#client-application), specifikus alatt lévő védett erőforrások eléréséhez [engedélyek](#permissions), a következő nevében: a erőforrás tulajdonosa. Attól függően, hogy az engedélyeket, az ügyfél által kért rendszergazda vagy a felhasználó fogja kérni a hozzájárulásukat adják, illetve hogy férjen hozzá a szervezet vagy egyéni adatok. Vegye figyelembe a egy [több-bérlős](#multi-tenant-application) forgatókönyvet, az alkalmazás [egyszerű](#service-principal-object) consenting felhasználó bérlői is rögzíti.

## <a name="id-token"></a>Azonosító token
Egy [OpenID Connect] [ OpenIDConnect-ID-Token] [biztonsági jogkivonat](#security-token) által biztosított egy [engedélyezési server](#authorization-server) [engedélyezésivégpont](#authorization-endpoint), amely tartalmazza [jogcímek](#claim) a felhasználó hitelesítésére alkalmas [erőforrás tulajdonosa](#resource-owner). Például a hozzáférési tokent, azonosító-jogkivonatokat is képviselt digitális aláírások [JSON webes jogkivonat (JWT)][JWT]. Olyan hozzáférési jogkivonatot eltérően azonban az ID-tokent jogcímek nem használt erőforrások eléréséhez kapcsolódó célokra és kifejezetten hozzáférés-vezérlés.

Lásd: [az Azure AD-jogkivonatok referenciájából] [ AAD-Tokens-Claims] további részleteket.

## <a name="multi-tenant-application"></a>több-bérlős alkalmazás
Egy osztály, amely lehetővé teszi, hogy a bejelentkezési kérelem és [hozzájárulás](#consent) bármely Azure AD-ban kiépített felhasználók [bérlői](#tenant), ahol az ügyfél regisztrálva van-e más bérlők beleértve. [Natív ügyfél](#native-client) olyan több-bérlős alapértelmezés szerint mivel [webes ügyfél](#web-client) és [webes erőforrás/API-t](#resource-server) alkalmazások is választhat egy vagy több-bérlős képes. Ezzel szemben egyetlen-bérlő regisztrálva webalkalmazás csak révén ugyanazzal a bérlővel, mint a kiépített felhasználói fiókok bejelentkezések ahol az alkalmazás regisztrálva van.

Lásd: [bármely Azure AD-felhasználó, a több-bérlős alkalmazás minta használatával bejelentkezés] [ AAD-Multi-Tenant-Overview] további részleteket.

## <a name="native-client"></a>natív ügyfél
Olyan típusú [ügyfélalkalmazás](#client-application) telepített natív módon az eszközön. Összes kódot az eszközön végrehajtása, mert egy "nyilvános" ügyfél hitelesítő adatok közvetlenül a Microsoftnak/bizalmasan tárolására, mert minősül. Lásd: [OAuth2-ügyfél meg kell adnia, és profilok] [ OAuth2-Client-Types] további részleteket.

## <a name="permissions"></a>Engedélyek
A [ügyfélalkalmazás](#client-application) fér hozzá egy [erőforrás-kiszolgáló](#resource-server) is deklarálni kell engedélyekre vonatkozó kéréseit. Kétféle érhetők el:

* "Delegált" engedéllyel, amelynek [hatókör-alapú](#scopes) delegált engedélyezése a bejelentkezett a hozzáférési [erőforrás tulajdonosa](#resource-owner), jelenjenek meg az erőforráshoz, futásidőben ["scp" jogcímek](#claim) az ügyfél [hozzáférési jogkivonat](#access-token).
* Adja meg, az "Alkalmazás" engedélyek [szerepköralapú](#roles) eléréséhez használ az ügyfélalkalmazás hitelesítő adatok/identitás, jelenjenek meg az erőforráshoz, futásidőben ["szerepkörök" jogcímek](#claim) az ügyfél hozzáférési jogkivonat.

Akkor is surface során a [hozzájárulás](#consent) folyamat, egyúttal jogosultságot ad a rendszergazda vagy az erőforrás tulajdonosa grant/megtagadja az ügyfél a bérlői erőforrásokhoz lehetőséget.

Engedélykéréseket vannak konfigurálva, a "kérelmek" / "Beállítások" lapon a [Azure-portálon][AZURE-portal], a "Szükséges engedélyek", kiválaszthatja a kívánt "Delegált engedélyek" és "alkalmazás Engedélyek"(ez utóbbi szükséges a globális rendszergazdai szerepkör tagjának kell lennie). Mivel egy [nyilvános ügyfél](#client-application) biztonságosan nem tudja kezelni a hitelesítő adatokat, csak kérheti delegált jogosultságokkal sikeresen telepítették, amíg egy [bizalmas ügyfél](#client-application) delegált és a alkalmazás képes engedélyek. Az ügyfél [alkalmazásobjektum](#application-object) a deklarált engedélyek szükségesek a tárolja a [requiredResourceAccess tulajdonság][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>erőforrás tulajdonosa
Meghatározása szerint a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], képes a védett erőforrásokhoz való hozzáférés biztosítása entitás. Ha az erőforrás tulajdonosa személy, azt nevezzük felhasználó. Például, ha egy [ügyfélalkalmazás](#client-application) hozzá kíván férni a felhasználó postaládájához keresztül a [Microsoft Graph API][Microsoft-Graph], az erőforrás tulajdonosa engedélyt igényel a postaláda.

## <a name="resource-server"></a>erőforrás-kiszolgáló
Meghatározása szerint a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], egy kiszolgálót, hogy a gazdagépek védett erőforrások, képes elfogadása és válaszadás a védett erőforrás kérések [ügyfél alkalmazások](#client-application) , hogy jelen van egy [hozzáférési jogkivonat](#access-token). Más néven egy védett erőforrás-kiszolgáló, vagy erőforrás-alkalmazáshoz.

Egy erőforrás-kiszolgáló API-k mutatja, és érvényesíti a keresztül a védett erőforrásokhoz való hozzáférés [hatókörök](#scopes) és [szerepkörök](#roles), az OAuth 2.0 hitelesítési keretrendszer használatával. Például az Azure AD Graph API-hozzáférést biztosít az Azure AD bérlő adatokhoz és az Office 365 API-kat, amelyek például a levelezés és a naptári adatok eléréséhez. Mindkét esetben is elérhetők a [Microsoft Graph API][Microsoft-Graph].  

Csakúgy, mint egy ügyfélalkalmazást, erőforrás identitás alkalmazást létrejön keresztül [regisztrációs](#application-registration) az Azure AD-bérlő, így az alkalmazás és a szolgáltatás egyszerű objektum. Egyes Microsoft által biztosított API-k, például az Azure AD Graph API előre regisztrált szolgáltatásnevekről kiépítése során minden bérlők elérhetik.

## <a name="roles"></a>roles
Például [hatókörök](#scopes), szerepkörök teszik lehetővé az egy [erőforrás-kiszolgáló](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozása érdekében. Két típusa van: "user" szerepkör valósítja meg a szerepköralapú hozzáférés-vezérlés közben megegyezik az "alkalmazás" szerepkör valósítja meg az erőforrás elérését igénylő felhasználók/csoportok [ügyfélalkalmazások](#client-application) , amelyek hozzáférést igényelnek.

Szerepkörök olyan erőforrás-definiált karakterláncok (például "költség jóváhagyó", "Csak Olvasás", "Directory.ReadWrite.All"), a felügyelt a [Azure-portálon] [ AZURE-portal] keresztül az erőforrás [alkalmazás manifest](#application-manifest), és az erőforrás tárolt [appRoles tulajdonság][AAD-Graph-Sp-Entity]. Az Azure-portálon is szolgál a "user" szerepkörökhöz rendeljen hozzá a felhasználókat, és -ügyfél konfigurálása [Alkalmazásengedélyek](#permissions) "alkalmazás" szerepet eléréséhez.

Az Azure AD Graph API által elérhetővé tett alkalmazás-szerepkörök részletes tárgyalását lásd: [Graph API-Engedélyhatókörök][AAD-Graph-Perm-Scopes]. A részletes megvalósítási példa, lásd: [szerepköralapú hozzáférés-vezérlés az Azure AD felhőalapú alkalmazások][Duyshant-Role-Blog].

## <a name="scopes"></a>Hatókörök
Például [szerepkörök](#roles), hatókörök teszik lehetővé az egy [erőforrás-kiszolgáló](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozása érdekében. Hatókörök végrehajtásához használt [hatókör-alapú] [ OAuth2-Access-Token-Scopes] a hozzáférés-vezérlést, egy [ügyfélalkalmazás](#client-application) , amelyek adott delegált hozzáférést az erőforráshoz a tulajdonosa.

Hatókörök olyan erőforrás-definiált karakterláncok (például "Mail.Read", "Directory.ReadWrite.All"), a felügyelt a [Azure-portálon] [ AZURE-portal] keresztül az erőforrás [alkalmazásjegyzék](#application-manifest), és az erőforrás tárolt [oauth2Permissions tulajdonság][AAD-Graph-Sp-Entity]. Az Azure-portálon is használt ügyfélalkalmazás konfigurálása [delegált engedélyekkel](#permissions) hatókör eléréséhez.

Bevált gyakorlat elnevezési szabályokat alkalmaz, az "resource.operation.constraint" formátumának a használatára. A hatókörök jelennek meg, ha az Azure AD Graph API részletes tárgyalását lásd: [Graph API-Engedélyhatókörök][AAD-Graph-Perm-Scopes]. A hatókörök jelennek meg, ha az Office 365-szolgáltatásokhoz, lásd: [Office 365 API-engedélyek referencia][O365-Perm-Ref].

## <a name="security-token"></a>biztonsági jogkivonat
Jogcímek, például egy OAuth2 token vagy a SAML 2.0 helyességi feltételt tartalmazó aláírt dokumentumot. Az egy oauth2 protokollt használó [hitelesítésengedélyezési](#authorization-grant), egy [hozzáférési jogkivonat](#access-token) (OAuth2) és egy [azonosító Token](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) különböző típusú biztonsági jogkivonatokat, amelyek mindegyikét használják, mint egy [JSON Webalkalmazás-jogkivonat (JWT)][JWT].

## <a name="service-principal-object"></a>szolgáltatás egyszerű objektum
Ha Ön regisztrálása vagy frissítéséhez az alkalmazás a [Azure-portálon][AZURE-portal], a portál létrehoz frissítések is egy [alkalmazásobjektum](#application-object) és egy megfelelő szolgáltatás egyszerű objektum a bérlő. Az application objektum *meghatározása* az identitás alkalmazást globálisan (ahol egy társított alkalmazásban hozzáférést kapott minden bérlők), között, és a sablon, amelynek a megfelelő egyszerű szolgáltatásnév objektumok vannak *származtatott* helyileg történő futtatása (az egy adott bérlő) használható.

Lásd: [alkalmazás és szolgáltatás egyszerű objektumok] [ AAD-App-SP-Objects] további információt.

## <a name="sign-in"></a>bejelentkezés
Folyamat egy [ügyfélalkalmazás](#client-application) kapcsolódó állapot céljából az beszerzése a felhasználói hitelesítést kezdeményező, és rögzítése egy [biztonsági jogkivonat](#security-token) és az alkalmazás munkamenet arra az állapotra hatókörének. Összetevők, például a felhasználói profillal kapcsolatos információk is tartalmazhat, és információt származó jogkivonat jogcímek állapotban.

Egy alkalmazás bejelentkezési funkciójának általában egyszeri bejelentkezéses (SSO) végrehajtásához használatos. Azt is előz "előfizetés" függvény, mint a belépési pont, a felhasználó hozzáfér egy alkalmazás (után első bejelentkezés). A regisztrációs függvény segítségével gyűjtse össze és egyes felhasználók további állapotban maradnak, és előfordulhat, hogy [felhasználói hozzájárulás](#consent).

## <a name="sign-out"></a>Kijelentkezés
A folyamatot nem hitelesítő felhasználó, a felhasználói állapot leválasztása társított a [ügyfélalkalmazás](#client-application) munkamenet során [bejelentkezés](#sign-in)

## <a name="tenant"></a>Bérlői
Az Azure AD-címtár egy példányát az Azure AD-bérlő nevezzük. Több funkciót, beleértve a tartalmazza:

* a beállításjegyzék szolgáltatás integrált alkalmazások
* hitelesítés a felhasználói fiókoknak és a társított alkalmazások
* Az OAuth2 és SAML, beleértve különböző protokollok támogatásához szükséges REST-végpontok többek között a [engedélyezési végpont](#authorization-endpoint), [token-végpont](#token-endpoint) és a "általános" végpont által használt [ több-bérlős alkalmazásokhoz](#multi-tenant-application).

Az Azure AD-bérlő Office 365 és az Azure-előfizetések létrehozása vagy társítani alatt is az előfizetéshez tartozó regisztráció, olyan Identity & Access Management szolgáltatásokat. Azure-előfizetés rendszergazdái is létrehozhat további Azure AD-bérlőt az Azure-portálon. Lásd: [Azure Active Directory-bérlő beszerzése] [ AAD-How-To-Tenant] talál részletes információt a különböző módokon kaphat hozzáférést a bérlőt. Lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory] [ AAD-How-Subscriptions-Assoc] talál részletes információt az előfizetések és az Azure AD-bérlő közötti kapcsolat.

## <a name="token-endpoint"></a>token-végpont
A végpont által megvalósított közül a [engedélyezési server](#authorization-server) támogatásához OAuth2 [engedélyezési biztosít](#authorization-grant). Attól függően, hogy a grant használható szerezni egy [hozzáférési jogkivonat](#access-token) (és a kapcsolódó "frissítés" token) a egy [ügyfél](#client-application), vagy [azonosító token](#ID-token) használata esetén a [OpenID Csatlakozás] [ OpenIDConnect] protokoll.

## <a name="user-agent-based-client"></a>Felhasználói ügynök-alapú ügyfél
Olyan típusú [ügyfélalkalmazás](#client-application) , amely kód webkiszolgálókról és hajtanak végre belül egy felhasználói ügynök (például egy webes böngésző), például egy egyetlen oldal alkalmazás (SPA) fogunk. Összes kódot az eszközön végrehajtása, mert egy "nyilvános" ügyfél hitelesítő adatok közvetlenül a Microsoftnak/bizalmasan tárolására, mert minősül. Lásd: [OAuth2-ügyfél meg kell adnia, és profilok] [ OAuth2-Client-Types] további részleteket.

## <a name="user-principal"></a>egyszerű felhasználónév
Hasonló ahhoz, ahogy a szolgáltatás egyszerű objektum egy alkalmazáspéldányt megjelenítésére szolgál, a felhasználó elsődleges objektumot egy rendszerbiztonsági tagot, amely képviseli a felhasználót egy másik típusú. Az Azure AD Graph [felhasználói entitás] [ AAD-Graph-User-Entity] határozza meg a sémában az olyan felhasználói objektum, beleértve a felhasználóval kapcsolatos tulajdonságait, például utónév és Vezetéknév, felhasználó egyszerű felhasználóneve, directory szerepköri tagság, stb. Ez lehetővé teszi a felhasználó identitása konfigurációja az Azure AD egy egyszerű futásidőben létrehozásához. Egyszerű felhasználónév az egyszeri bejelentkezést, hitelesített felhasználók megjelenítésére szolgál rögzítése [hozzájárulás](#consent) delegálás, így a hozzáférés-vezérlési döntéseket stb.

## <a name="web-client"></a>webes ügyfél
Olyan típusú [ügyfélalkalmazás](#client-application) , amely végrehajtja az összes kód egy webkiszolgálón, és képes biztonságosan tárolja a hitelesítő adatok a kiszolgálón egy "bizalmas" ügyfél működhet. Lásd: [OAuth2-ügyfél meg kell adnia, és profilok] [ OAuth2-Client-Types] további részleteket.

## <a name="next-steps"></a>Következő lépések
A [Azure AD fejlesztői útmutató] [ AAD-Dev-Guide] minden Azure AD-fejlesztési célra a portál kapcsolódó témakörök, valamint áttekintést [alkalmazásintegráció] [ AAD-How-To-Integrate] és alapjait [az Azure AD-alapú hitelesítés és a támogatott hitelesítési forgatókönyvek][AAD-Auth-Scenarios].

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom, például a kérelmekről új definíciók vagy meglévőket frissítése!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ../active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
