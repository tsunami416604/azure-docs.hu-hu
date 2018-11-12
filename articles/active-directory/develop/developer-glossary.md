---
title: Az Azure Active Directory fejlesztői szószedet |} A Microsoft Docs
description: A gyakran használt Azure Active Directory fejlesztői alapfogalmait és jellemzőit neveinek listáját.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: dc3bbab2242321ae736f3f2cefb1eed5e96e834e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288730"
---
# <a name="azure-active-directory-developer-glossary"></a>Az Azure Active Directory fejlesztői szószedet

Ez a cikk az Azure Active Directory (AD) fejlesztői alapfogalmakat, amelynek hasznosak a megismerése alkalmazások fejlesztését az Azure ad bizonyos kapcsolatos definíciókat tartalmazza.

## <a name="access-token"></a>hozzáférési jogkivonat

Olyan típusú [biztonsági jogkivonat](#security-token) által kiadott egy [az engedélyezési kiszolgáló](#authorization-server), és használják egy [ügyfélalkalmazás](#client-application) ahhoz, hogy hozzáférhessen egy [védett erőforrás-kiszolgáló](#resource-server). Általában formájában egy [JSON webes jogkivonat (JWT)][JWT], a jogkivonatot az ügyfél által megadott engedély képviselni a [erőforrás tulajdonosa](#resource-owner), kért hozzáférési szintet. A jogkivonat érvényes tartalmaz [jogcímek](#claim) tudnivalók az e-mail tárgyát, az ügyfél az alkalmazás használatához hasonlóan a hitelesítő adatok, egy adott erőforrás elérésekor engedélyezése. Ez is kiküszöböli a hitelesítő adatokat, az ügyfél számára elérhetővé az erőforrás tulajdonosa.

Hozzáférési jogkivonatok vannak más néven "Felhasználó + alkalmazás" vagy "Alkalmazás csak", a hitelesítő adatok képviselt függően. Például amikor egy ügyfélalkalmazás a:

* ["Engedélyezés" engedélyezési kódmegadásának](#authorization-grant), a végfelhasználó az erőforrás tulajdonosa, az erőforrás eléréséhez az ügyfélnek engedély delegálása, először hitelesíti magát. Ezt követően hitelesíti az ügyfelet, a hozzáférési jogkivonat beszerzése során. A jogkivonat is lehet néven pontosabban "Felhasználó + alkalmazás" jogkivonatot, mivel mind a felhasználó engedélyezett az ügyfélalkalmazásban, és az alkalmazás.
* ["Ügyfél-hitelesítő adatok" engedélyezést](#authorization-grant), az ügyfél az egyedüli hitelesítést nyújt, működik-e nélkül az erőforrás tulajdonosa hitelesítés/engedélyezés, így a jogkivonat is lehet néven a "Csak az alkalmazásra vonatkozó" tokent.

Lásd: [Azure AD-jogkivonatok Referenciájából] [ AAD-Tokens-Claims] további részletekért.

## <a name="application-id-client-id"></a>alkalmazás azonosítója (ügyfél-azonosító)

A egyedi azonosítója az Azure AD-alkalmazás regisztrációja, amelyek egy adott alkalmazás és a kapcsolódó konfigurációk problémákat. Ezt az alkalmazásazonosítót ([ügyfél-azonosító](https://tools.ietf.org/html/rfc6749#page-15)) használatos, amikor végez hitelesítést kér, és van megadva a hitelesítési könyvtárak a fejlesztésre szánt időt. Az alkalmazásazonosítót (ügyfél-azonosító) nem egy titkos kulcsot. 

## <a name="application-manifest"></a>Alkalmazásjegyzék

A szolgáltatás által biztosított a [az Azure portal][AZURE-portal], az alkalmazás identitás konfiguráció, frissítésre hozzá van rendelve egy mechanizmusként használt JSON-reprezentációja állít elő [ Alkalmazás] [ AAD-Graph-App-Entity] és [ServicePrincipal] [ AAD-Graph-Sp-Entity] entitásokat. Lásd: [az Azure Active Directory alkalmazásjegyzékének megismerése] [ AAD-App-Manifest] további részletekért.

## <a name="application-object"></a>alkalmazásobjektum

Amikor, regisztrálni vagy frissíteni az alkalmazás a [az Azure portal][AZURE-portal], a portál frissítések alkalmazása objektum és a egy megfelelő [szolgáltatásnév-objektum](#service-principal-object) a bérlő. Az alkalmazásobjektum *meghatározása* az alkalmazás egy sablont, amelyről a megfelelő szolgáltatás egyszerű objektumok vannak nyújtó globálisan (összes bérlőn, ahol hozzáféréssel rendelkezik), identitás-konfiguráció  *származtatott* helyileg történő futtatása (az egyes bérlők) használható.

További információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok][AAD-App-SP-Objects].

## <a name="application-registration"></a>Alkalmazás regisztrálása

Annak érdekében, hogy egy alkalmazás integrálható, és delegálása az Azure AD identitáskezelési és hozzáférés-kezelési funkciók, regisztrálni kell egy Azure AD-vel [bérlői](#tenant). Ha regisztrálja az alkalmazás Azure AD-vel, meg van adva egy identitás-konfigurációt az alkalmazásnak, lehetővé téve, hogy az Azure AD integrálása és funkciók használata, mint például:

* Az egyszeri bejelentkezés használata az Azure AD Identity Management erőteljes felügyeleti és [OpenID Connect] [ OpenIDConnect] protokollmegvalósítás
* Felügyelt hozzáférést [védett erőforrások](#resource-server) által [ügyfélalkalmazások](#client-application), az Azure AD OAuth 2.0-n keresztül [az engedélyezési kiszolgáló](#authorization-server) végrehajtása
* [Hozzájárulási keretrendszer](#consent) a védett erőforrások erőforrás-tulajdonos engedélyezési ügyfélelérésének kezelése.

Lásd: [alkalmazások integrálása az Azure Active Directory] [ AAD-Integrating-Apps] további részletekért.

## <a name="authentication"></a>hitelesítés

A művelet egy entitás alapján biztosít az identitás- és hozzáférés-vezérlési szolgáltatásnév létrehozásához a jogosult hitelesítő adatok, nehéz. Során egy [OAuth2 engedélyezést](#authorization-grant) például a felek hitelesítéséhez, vagy a szerepkör tölti [erőforrás tulajdonosa](#resource-owner) vagy [ügyfélalkalmazás](#client-application), attól függően, a a támogatás használt.

## <a name="authorization"></a>Engedélyezési

A törvény, egy hitelesített biztonsági megoldást valósíthatunk egyszerű engedély megadására. Az Azure AD programozási modellben két alapvető használati eset létezik:

* Során egy [OAuth2 engedélyezést](#authorization-grant) folyamat: Ha a [erőforrás tulajdonosa](#resource-owner) engedélyt ad a [ügyfélalkalmazás](#client-application), így az ügyfél az erőforrás eléréséhez a tulajdonos az erőforrásokat.
* Az ügyfél által az erőforrás-hozzáférés során: által megvalósított módon a [erőforrás-kiszolgáló](#resource-server)révén a [jogcím](#claim) értékek szerepelnek a [hozzáférési jogkivonat](#access-token) , hogy döntést hozhasson a hozzáférésről alapján őket.

## <a name="authorization-code"></a>engedélyezési kód

Egy rövid élettartamú "token" megadott egy [ügyfélalkalmazás](#client-application) által a [engedélyezési végpont](#authorization-endpoint), a "hozzáférési kód" folyamatot, egyet a négy OAuth2 részeként [engedélyezésibiztosít](#authorization-grant). A kódot ad vissza az ügyfélalkalmazásnak hitelesítését válaszul egy [erőforrás tulajdonosa](#resource-owner), jelezve, az erőforrás tulajdonosa van delegált hitelesítést: a kért erőforrás elérésére. A folyamat részeként a később be a kódot egy [hozzáférési jogkivonat](#access-token).

## <a name="authorization-endpoint"></a>engedélyezési végpont

Egyik végpontja által megvalósított a [az engedélyezési kiszolgáló](#authorization-server)interakcióba szolgál a [erőforrás tulajdonosa](#resource-owner) annak érdekében, hogy egy [engedélyezést](#authorization-grant) az OAuth2 során engedélyezési megadási folyamatában. Attól függően, a támogatás használt folyamat, a tényleges engedély megadott eltérőek lehetnek, többek között egy [engedélyezési kód](#authorization-code) vagy [biztonsági jogkivonat](#security-token).

Tekintse meg az OAuth2 specifikációt [engedélyezéstípusok] [ OAuth2-AuthZ-Grant-Types] és [engedélyezési végpont] [ OAuth2-AuthZ-Endpoint] szakaszok és a [OpenIDConnect specifikáció] [ OpenIDConnect-AuthZ-Endpoint] további részletekért.

## <a name="authorization-grant"></a>engedélyek

A hitelesítő adatok jelölő a [erőforrás tulajdonosa](#resource-owner) [engedélyezési](#authorization) számára biztosított, a védett erőforrások eléréséhez egy [ügyfélalkalmazás](#client-application). Egy ügyfélalkalmazás egyikét használhatja a [négy adja meg az OAuth2 hitelesítési keretrendszer által megadott] [ OAuth2-AuthZ-Grant-Types] támogatás, attól függően az ügyfél adattípus-követelményekkel beszerzése: "engedélyezési kód", "ügyfél hitelesítő adatok megadása","típusú implicit engedélyezés"és"erőforrás tulajdonosának jelszavas hitelesítő adatai megadása". A hitelesítő adatokat, az ügyfél kisebb, mint egy [hozzáférési jogkivonat](#access-token), vagy egy [engedélyezési kód](#authorization-code) (cseréje később a hozzáférési jogkivonat) használt engedélyezést típusától függően.

## <a name="authorization-server"></a>az engedélyezési kiszolgáló

Által meghatározott a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], a kiszolgáló-hozzáférés kiadására jogkivonatok a [ügyfél](#client-application) a asikereshitelesítésután[erőforrás tulajdonosa](#resource-owner) és szerezhetők be az engedélyt. A [ügyfélalkalmazás](#client-application) az engedélyezési kiszolgáló futásidőben keresztül kommunikál a [engedélyezési](#authorization-endpoint) és [token](#token-endpoint) végpontok, összhangban az OAuth2 definiált [engedélyezések](#authorization-grant).

Esetén az Azure AD alkalmazás-integráció, az Azure AD valósítja meg az engedélyezési kiszolgálói szerepkör az Azure AD-alkalmazások és a Microsoft-szolgáltatás API-k, például [Microsoft Graph API-k][Microsoft-Graph].

## <a name="claim"></a>igénylés

A [biztonsági jogkivonat](#security-token) jogcímeket, adja meg a helyességi feltételek egy entitás tartalmaz (például egy [ügyfélalkalmazás](#client-application) vagy [erőforrás tulajdonosa](#resource-owner)) (például a egymásikentitáshoz[erőforrás-kiszolgáló](#resource-server)). Jogcímek olyan név/érték párok, amelyek a jogkivonat tárgyában kapcsolatos továbbítási (például a hitelesített rendszerbiztonsági tagot a [az engedélyezési kiszolgáló](#authorization-server)). A jogcímeket egy adott jogkivonat értékkel a változókat, például a típusuk jogkivonatot, tárgyát, az alkalmazás konfigurációja és egyéb hitelesítéséhez használandó hitelesítő adatok típusától függenek.

Lásd: [az Azure AD-jogkivonatok referenciájából] [ AAD-Tokens-Claims] további részletekért.

## <a name="client-application"></a>Ügyfélalkalmazás

Ahogyan a a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], olyan alkalmazás, amely lehetővé teszi a védett erőforrás-kérelmek nevében a [erőforrás tulajdonosa](#resource-owner). "Ügyfél" kifejezés nem jár semmilyen adott megvalósítási hardverjellemzők (például hogy az alkalmazás végrehajtja a kiszolgálón, asztali vagy más eszközök). 

Egy ügyfélalkalmazás kérelmek [engedélyezési](#authorization) az erőforrás tulajdonosa részt egy [OAuth2 engedélyezést](#authorization-grant) folyamat, és az erőforrás tulajdonosa nevében hozzáférhetnek az API-k/adatok. Az OAuth2 hitelesítési keretrendszer [határozza meg az ügyfelek kétféle][OAuth2-Client-Types], "bizalmas" és "nyilvános", az ügyfél képes a hitelesítő adatait bizalmasan alapján. Alkalmazások valósíthat meg egy [webes ügyfelet (bizalmas)](#web-client) amely egy webkiszolgálót futtat egy [natív ügyfél (nyilvános)](#native-client) telepítve az eszközön, vagy egy [felhasználói ügynök-alapú ügyfél (nyilvános)](#user-agent-based-client)amely fut egy eszköz böngészőjében.

## <a name="consent"></a>Hozzájárulás megadása

Folyamat egy [erőforrás tulajdonosa](#resource-owner) engedély egy [ügyfélalkalmazás](#client-application)elérésére vonatkozó alatt álló védett erőforrásokhoz [engedélyek](#permissions), nevében a erőforrás tulajdonosának. Az ügyfél által kért engedélyektől rendszergazda vagy a felhasználó meg kell adnia beleegyezést férhessenek hozzá a szervezet/egyéni adatokat jelölik. Vegye figyelembe a egy [több-bérlős](#multi-tenant-application) forgatókönyv, az alkalmazás [szolgáltatásnév](#service-principal-object) consenting felhasználó a bérlő is rögzíti.

Lásd: [hozzájárulási keretrendszer](consent-framework.md) további információt.

## <a name="id-token"></a>Azonosító jogkivonat

Egy [OpenID Connect] [ OpenIDConnect-ID-Token] [biztonsági jogkivonat](#security-token) által biztosított egy [az engedélyezési kiszolgáló](#authorization-server) [engedélyezésivégpont](#authorization-endpoint), tartalmazó [jogcímek](#claim) a felhasználó hitelesítésére vonatkozó [erőforrás tulajdonosa](#resource-owner). Például a hozzáférési tokent, azonosító-jogkivonatokat is szerepelnek, a digitálisan aláírt [JSON webes jogkivonat (JWT)][JWT]. Ellentétben a hozzáférési jogkivonatot, egy azonosító jogkivonat jogcímek nem használ a kapcsolódó erőforrások elérése céljából és külön hozzáférés-vezérlés.

Lásd: [az Azure AD-jogkivonatok referenciájából] [ AAD-Tokens-Claims] további részletekért.

## <a name="microsoft-identity-platform"></a>A Microsoft identity platform

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. Egy teljes körű platform, amely olyan hitelesítési szolgáltatás, könyvtárak, alkalmazásregisztráció és konfigurációs, teljes körű fejlesztői dokumentáció, Kódminták és egyéb fejlesztői tartalom áll. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet. Lásd: [kapcsolatos Microsoft identitásplatformja](about-microsoft-identity-platform.md) további részletekért.

## <a name="multi-tenant-application"></a>több-bérlős alkalmazást

Egy osztály, amely lehetővé teszi a bejelentkezési kérelem és [hozzájárulás](#consent) a felhasználók minden olyan Azure AD-ben üzembe helyezett [bérlői](#tenant), beleértve a bérlők számára, mint ahol az ügyfél regisztrálva van-e. [Natív ügyfél](#native-client) esetén alapértelmezés szerint több-bérlős alkalmazásokat mivel [webes ügyfél](#web-client) és [webes erőforrás/API](#resource-server) alkalmazásokat is képes egy vagy több-bérlős választhat. Ezzel szemben az egybérlős, regisztrált webalkalmazás csak lehetővé tenné létesített ugyanabban a bérlőben, mint a felhasználói fiókok történő bejelentkezések, az alkalmazás regisztrálva van-e.

Lásd: [az Azure AD-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával hogyan] [ AAD-Multi-Tenant-Overview] további részletekért.

## <a name="native-client"></a>Natív ügyfél

Olyan típusú [ügyfélalkalmazás](#client-application) telepített natív módon az eszközön. Mivel az összes kód végrehajtása egy eszközön, hitelesítő adatok közvetlenül a Microsoftnak/bizalmasan tárolására, mert a "nyilvános" ügyfél minősül. Lásd: [OAuth2 ügyfél típusokat és a profilok] [ OAuth2-Client-Types] további részletekért.

## <a name="permissions"></a>engedélyek

A [ügyfélalkalmazás](#client-application) megszerez egy [erőforrás-kiszolgáló](#resource-server) deklarálásával alkalmazásengedély-kérelmeket. Kétféle érhetők el:

* "Delegált" engedélyek, amelyek meghatározzák [hatókör-alapú](#scopes) elérése a bejelentkezett a delegált engedélyezési használatával [erőforrás tulajdonosa](#resource-owner), jelennek meg az erőforráshoz, futásidőben ["scp" jogcímek](#claim) az ügyfél [hozzáférési jogkivonat](#access-token).
* Adja meg, az "Alkalmazás" engedélyek [szerepköralapú](#roles) eléréséhez, az ügyfélalkalmazás hitelesítő adatok/identitással, jelennek meg az erőforráshoz, futásidőben ["szerepkör" jogcímek](#claim) az ügyfél hozzáférési jogkivonat.

Akkor is surface során a [hozzájárulás](#consent) folyamatot, így a rendszergazda vagy az erőforrás tulajdonosa a lehetőséget, hogy az ügyfél-hozzáférési erőforrások bérlőben grant vagy letiltani.

Alkalmazásengedély-kérelmeket megtörténik az "Alkalmazás" / "Beállítások" lapon a [az Azure portal][AZURE-portal], a "Szükséges engedélyek", kiválaszthatja a kívánt "Delegált engedélyek" és "alkalmazás Engedélyek"(az utóbbi tagjának kell lennie a globális rendszergazda szerepkörhöz). Mert egy [nyilvános ügyfél](#client-application) biztonságosan nem tudja kezelni a hitelesítő adatokat, azt csak kérhetnek delegált engedélyeket, amíg egy [bizalmas ügyfél](#client-application) kérése a delegált és az alkalmazás képes engedélyek. Az ügyfél [alkalmazásobjektum](#application-object) tárolja a deklarált engedélyeket a [requiredResourceAccess tulajdonság][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>erőforrás tulajdonosának

Ahogyan a a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], egy entitás, amely képes a védett erőforrásokhoz való hozzáférést. Ha az erőforrás tulajdonosa személy, azt nevezzük a felhasználó. Például, hogy amikor egy [ügyfélalkalmazás](#client-application) szeretne hozzáférni a felhasználók postaládáihoz keresztül a [Microsoft Graph API][Microsoft-Graph], az erőforrás tulajdonosa engedélyt igényel a postaláda.

## <a name="resource-server"></a>erőforrás-kiszolgáló

Ahogyan a a [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def], egy kiszolgálót, hogy a gazdagépek védett erőforrások, képes a elfogadása és válaszadás a védett erőforrás-kérelmek szerint [ügyfél alkalmazások](#client-application) , hogy jelen van egy [hozzáférési jogkivonat](#access-token). Más néven egy védett erőforrás-kiszolgáló, vagy erőforrás-alkalmazás.

Erőforrás-kiszolgáló API-kat, és érvényesíti a keresztül védett erőforrásokhoz való hozzáférés [hatókörök](#scopes) és [szerepkörök](#roles), az OAuth 2.0 engedélyezési keretrendszer használatával. Ilyenek például az Azure AD Graph API, amely az Azure AD-bérlő adatai hozzáférést tesz lehetővé, és az Office 365 API-k, amelyek például a levelezés és a naptár adatokhoz hozzáférést biztosítania. Mindkét esetben így is elérhetők a [Microsoft Graph API][Microsoft-Graph]. 

Csakúgy, mint egy ügyfélalkalmazás erőforrás-alkalmazás identitását konfigurációs keresztül létrehozott [regisztrációs](#application-registration) az Azure AD-bérlővel, így az alkalmazás és a szolgáltatásnév-objektumot. Bizonyos Microsoft által biztosított API-k, például az Azure AD Graph API-t, előre regisztrált egyszerű szolgáltatások elérhetők a bérlőknek üzembe helyezés során.

## <a name="roles"></a>roles

Például [hatókörök](#scopes), szerepkörök lehetőséget biztosíthat egy [erőforrás-kiszolgáló](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozására. Két típusa van: "felhasználói" szerepkör valósítja meg a szerepköralapú hozzáférés-vezérlést, míg az "alkalmazás" szerepkör valósít meg ugyanezt az erőforráshoz hozzáférést igénylő felhasználók/csoportok [ügyfélalkalmazások](#client-application) , amelyek hozzáférést igényelnek.

Szerepkörök olyan karakterláncok erőforrás által meghatározott (például "költségek jóváhagyó", "Csak Olvasás", "Directory.ReadWrite.All"), a felügyelt a [az Azure portal] [ AZURE-portal] az erőforráson keresztül [alkalmazás manifest](#application-manifest), és az erőforrásban tárolt [appRoles tulajdonság][AAD-Graph-Sp-Entity]. Az Azure Portalon is használható felhasználók hozzárendelése "user" szerepkörökhöz, és konfigurálja az ügyfél [Alkalmazásengedélyek](#permissions) egy "alkalmazás" szerepkör eléréséhez.

Az Azure AD Graph API által elérhetővé tett alkalmazás-szerepkörök részletes tárgyalását lásd: [Graph API-Engedélyhatókörök][AAD-Graph-Perm-Scopes]. A részletes megvalósítási példa: [rbac-RÓL és az Azure portal-hozzáférés kezelése][AAD-RBAC].

## <a name="scopes"></a>hatókörök

Például [szerepkörök](#roles), hatókörök lehetőséget biztosíthat egy [erőforrás-kiszolgáló](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozására. Hatókörök megvalósításához használhatók [hatókör-alapú] [ OAuth2-Access-Token-Scopes] hozzáférés-vezérlés, a egy [ügyfélalkalmazás](#client-application) , amelyek adott delegált hozzáférést az erőforráshoz a tulajdonosa.

Hatókörök olyan erőforrás által meghatározott karakterláncok (például "Mail.Read", "Directory.ReadWrite.All"), a felügyelt a [az Azure portal] [ AZURE-portal] az erőforráson keresztül [alkalmazásjegyzék](#application-manifest), és az erőforrásban tárolt [oauth2Permissions tulajdonság][AAD-Graph-Sp-Entity]. Az Azure Portalon is használható ügyfélalkalmazás konfigurálása [delegált engedélyek](#permissions) hatókör eléréséhez.

Ajánlott eljárás elnevezési szabályokat alkalmaz, az "resource.operation.constraint" formátumban. Az Azure AD Graph API által közzétett hatókörök részletes tárgyalását lásd: [Graph API-Engedélyhatókörök][AAD-Graph-Perm-Scopes]. Office 365-szolgáltatások által közzétett hatókörök, lásd: [Office 365 API-engedélyek referencia][O365-Perm-Ref].

## <a name="security-token"></a>biztonsági jogkivonat

Például az OAuth2 token vagy SAML 2.0-előfeltétel a jogcímeket tartalmazó aláírt dokumentum. Az egy oauth2 protokollt használó [engedélyezést](#authorization-grant), egy [hozzáférési jogkivonat](#access-token) (OAuth2) és a egy [azonosító jogkivonat](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) típusú biztonsági jogkivonatokat, amelyek használják, mint egy [JSON Webes jogkivonat (JWT)][JWT].

## <a name="service-principal-object"></a>szolgáltatásnév-objektum

Amikor, regisztrálni vagy frissíteni az alkalmazás a [az Azure portal][AZURE-portal], a portál frissítések is egy [alkalmazásobjektum](#application-object) és a egy megfelelő szolgáltatásnév-objektum a bérlő. Az alkalmazásobjektum *meghatározása* globálisan (összes bérlőn, az ahhoz kapcsolódó alkalmazás hozzáférési engedélyt kapott az), az alkalmazás identitását konfigurációja, amelyről a sablont, és a megfelelő egyszerű szolgáltatást objektum(ok) vannak *származtatott* helyileg történő futtatása (az egyes bérlők) használható.

További információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok][AAD-App-SP-Objects].

## <a name="sign-in"></a>bejelentkezés

Folyamat egy [ügyfélalkalmazás](#client-application) végfelhasználói hitelesítés kezdeményezése, és a rögzítés kapcsolatos állapot beszerzése céljából egy [biztonsági jogkivonat](#security-token) és hatókörének beállítása az alkalmazás munkamenet az adott állapotot. Állapot összetevőket, például a felhasználói profil adatait is tartalmazhat, és információkat származó jogkivonat.

Egy alkalmazás bejelentkezési funkciójának egyszeri bejelentkezéses (SSO) végrehajtása általában szolgál. Azt is előz "regisztráció" funkciót, a végfelhasználó hozzáfér az alkalmazáshoz (követően először bejelentkezik) belépési pontként. A regisztrációs függvény segítségével gyűjtse össze és további jellemző a felhasználói állapot megőrzése, valamint szükség lehet [felhasználói beleegyezés](#consent).

## <a name="sign-out"></a>kijelentkezés

A felhasználó a felhasználói állapot leválasztása unauthenticating folyamata társított a [ügyfélalkalmazás](#client-application) munkamenet során [jelentkezzen be](#sign-in)

## <a name="tenant"></a>bérlő

Az Azure AD-címtár példányát az Azure AD-bérlő nevezzük. Számos funkciót, beleértve a biztosít:

* egy beállításjegyzék-szolgáltatás integrált alkalmazások
* hitelesítés felhasználói fiókok és a regisztrált alkalmazások
* REST-végpontokat, beleértve az oauth2-t és a SAML, különböző protokollok támogatásához szükséges többek között a [engedélyezési végpont](#authorization-endpoint), [jogkivonat-végpont](#token-endpoint) és a "általános" végpont által használt [ több-bérlős alkalmazások](#multi-tenant-application).

Az Azure AD-bérlőt az Azure és az Office 365-előfizetések létrehozása vagy társítani alatt is az előfizetés-előfizetés, amely identitás és hozzáférés-kezelési szolgáltatásait. Azure-előfizetés rendszergazdák is létrehozhatnak további Azure AD-bérlőt az Azure Portalon keresztül. Lásd: [Azure Active Directory-bérlő beszerzése] [ AAD-How-To-Tenant] különböző módjait részleteiért hozzáférhet a bérlőhöz. Lásd: [kapcsolódnak az Azure-előfizetések az Azure Active Directory] [ AAD-How-Subscriptions-Assoc] előfizetések és az Azure AD-bérlő kapcsolata részleteiért.

## <a name="token-endpoint"></a>jogkivonat-végpont

Egyik végpontja által megvalósított a [az engedélyezési kiszolgáló](#authorization-server) támogatási OAuth2 [engedélyezések](#authorization-grant). Attól függően, a támogatás használat beszerezni egy [hozzáférési jogkivonat](#access-token) (és a kapcsolódó "frissítés" token), egy [ügyfél](#client-application), vagy [azonosító jogkivonat](#ID-token) együtt használva az [OpenID Csatlakozás] [ OpenIDConnect] protokollt.

## <a name="user-agent-based-client"></a>Felhasználói ügynök-alapú ügyfél

Olyan típusú [ügyfélalkalmazás](#client-application) , amely kód tölt le egy webkiszolgáló és a egy felhasználói ügynök (például egy webböngésző), ilyen lehet például egy egyoldalas alkalmazás (SPA) hajt végre. Mivel az összes kód végrehajtása egy eszközön, hitelesítő adatok közvetlenül a Microsoftnak/bizalmasan tárolására, mert a "nyilvános" ügyfél minősül. További információkért lásd: [OAuth2 ügyfél típusokat és a profilok][OAuth2-Client-Types].

## <a name="user-principal"></a>egyszerű

Hasonló egyszerű szolgál alkalmazáspéldány, egyszerű felhasználói objektum egy másik típusú rendszerbiztonsági tagot, amely a felhasználót jelöl. Az Azure AD Graph [felhasználó típusú entitás] [ AAD-Graph-User-Entity] felhasználói objektum esetén, beleértve a felhasználói biztonsággal kapcsolatos tulajdonságok, például az utónév és Vezetéknév, egyszerű felhasználónév, címtár-szerepköri tagság a séma határozza meg. Ez biztosítja, hogy a felhasználó egy egyszerű futásidőben létesíteni az Azure ad identity konfigurációja. Az egyszerű felhasználónév szolgál egy hitelesített felhasználó az egyszeri bejelentkezést, rögzítése [hozzájárulás](#consent) delegálás, hogy döntést hozhasson a hozzáférésről, és így tovább.

## <a name="web-client"></a>webes ügyféllel

Olyan típusú [ügyfélalkalmazás](#client-application) , amely végrehajtja az összes kód egy webkiszolgálón, és képes biztonságosan tárolja a hitelesítő adatait a kiszolgálón egy "bizalmas" ügyfélként működnek. További információkért lásd: [OAuth2 ügyfél típusokat és a profilok][OAuth2-Client-Types].

## <a name="next-steps"></a>További lépések

A [Azure AD fejlesztői útmutató] [ AAD-Dev-Guide] összes az Azure AD fejlesztői kapcsolódó témaköreit, beleértve az áttekintést használandó kezdőlapja [alkalmazásintegráció] [ AAD-How-To-Integrate] és alapjait [az Azure AD-hitelesítés és a támogatott hitelesítési forgatókönyvekről][AAD-Auth-Scenarios]. Is megtalálhatja Kódminták és oktatóanyagok a gyors megkezdésében beszerzésének módját [Github](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Használja a következő megjegyzéseket visszajelzést és pontosíthatja vagy formázhatja a tartalmat, beleértve az új definíciók kérések vagy a meglévőket frissítése érdekében!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
