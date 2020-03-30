---
title: Microsoft identity platform fejlesztői szószedet | Azure
description: A Microsoft identity platform fejlesztői koncepcióinak és szolgáltatásainak kifejezései.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263048"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform fejlesztői szószedet

Ez a cikk definíciókat tartalmaz néhány alapvető fejlesztői fogalmak és terminológia, amelyek hasznosak, ha megismerik az alkalmazásfejlesztés a Microsoft identity platform használatával.

## <a name="access-token"></a>hozzáférési jogkivonat

Az [engedélyezési kiszolgáló](#authorization-server)által kibocsátott és egy [ügyfélalkalmazás](#client-application) által [védett erőforrás-kiszolgáló](#resource-server)eléréséhez használt [biztonsági jogkivonat.](#security-token) Általában [JSON webtoken (JWT)][JWT]formájában a jogkivonat az [erőforrás tulajdonosa](#resource-owner)által az ügyfélnek megadott engedélyt testesíti meg a kért hozzáférési szintre. A jogkivonat tartalmazza a tárgyra vonatkozó összes vonatkozó [jogcímeket,](#claim) így az ügyfélalkalmazás hitelesítő adatok ként használhatja azt egy adott erőforrás elérésekor. Ez azt is szükségtelenné teszi, hogy az erőforrás tulajdonosa az ügyfél számára elérhetővé tegye a hitelesítő adatokat.

A hozzáférési jogkivonatokat a képviselt hitelesítő adatoktól függően "User+App" vagy "Csak alkalmazás" néven is nevezik. Ha például egy ügyfélalkalmazás a következőket használja:

* ["Engedélyezési kód" engedélyezési engedély](#authorization-grant), a végfelhasználó először hitelesíti magát, mint az erőforrás tulajdonosa, delegálása engedélyt az ügyfél számára az erőforrás eléréséhez. Az ügyfél ezt követően hitelesíti magát a hozzáférési jogkivonat beszerzésekének során. A jogkivonat néha hivatkozhat pontosabban a "User+App" jogkivonat, mivel képviseli mind a felhasználó, amely engedélyezte az ügyfélalkalmazást, és az alkalmazás.
* ["Ügyfél hitelesítő adatok" engedélyezési támogatás](#authorization-grant), az ügyfél biztosítja az egyetlen hitelesítés, az erőforrás-tulajdonos hitelesítése/engedélyezése nélkül működik, így a jogkivonat néha "Csak alkalmazás" jogkivonatként is hivatkozhat.

További részletekért tekintse meg a [Microsoft identity platform token hivatkozási.][AAD-Tokens-Claims]

## <a name="application-id-client-id"></a>alkalmazásazonosító (ügyfélazonosító)

Az Azure AD egyedi azonosítója egy alkalmazásregisztrációval kapcsolatos, amely egy adott alkalmazást és a kapcsolódó konfigurációkat azonosítja. Ez az alkalmazásazonosító ([ügyfélazonosító](https://tools.ietf.org/html/rfc6749#page-15)) a hitelesítési kérelmek végrehajtásakor használatos, és a fejlesztési idő alatt a hitelesítési kódtárak számára biztosított. Az alkalmazásazonosító (ügyfélazonosító) nem titkos.

## <a name="application-manifest"></a>alkalmazás jegyzékfájlja

Az [Azure Portal][AZURE-portal]által biztosított szolgáltatás, amely az alkalmazás identitáskonfigurációjának JSON-ábrázolását hozza létre, amely a társított [alkalmazás-][Graph-App-Resource] és [ServicePrincipal][Graph-Sp-Resource] entitások frissítésének mechanizmusaként szolgál. További részletekért [olvassa el az Azure Active Directory alkalmazásjegyzék ismertetése][AAD-App-Manifest] című témakört.

## <a name="application-object"></a>alkalmazásobjektum

Amikor regisztrál/frissít egy alkalmazást az [Azure Portalon,][AZURE-portal]a portál létrehoz/frissít egy alkalmazásobjektumot és egy megfelelő [egyszerű szolgáltatásobjektumot](#service-principal-object) az adott bérlőhöz. Az alkalmazásobjektum globálisan *határozza meg* az alkalmazás identitáskonfigurációját (az összes olyan bérlőn, ahol hozzáféréssel rendelkezik), egy sablont biztosítva, amelyből a megfelelő egyszerű szolgáltatásobjektum(ok) futásidőben (egy adott bérlőben) helyihasználatra *származnak.*

További információt az [Alkalmazás- és egyszerű szolgáltatásobjektumok című témakörben talál.][AAD-App-SP-Objects]

## <a name="application-registration"></a>alkalmazásregisztráció

Annak érdekében, hogy egy alkalmazás integrálható legyen az Azure AD-vel, és delegálja az identitás- és hozzáférés-kezelési funkciókat, regisztrálnia kell egy Azure [AD-bérlővel.](#tenant) Amikor regisztrálja az alkalmazást az Azure AD-vel, identitáskonfigurációt biztosít az alkalmazáshoz, lehetővé téve az Azure AD-vel való integrációt és az olyan funkciók használatát, mint például:

* Az egyszeri bejelentkezés hatékony felügyelete az Azure AD Identity Management és [az OpenID Connect][OpenIDConnect] protokoll implementációjával
* Felügyelt hozzáférés a [védett erőforrásokhoz](#resource-server) [az ügyfélalkalmazások](#client-application)által, az OAuth 2.0 [engedélyezési kiszolgálón](#authorization-server) keresztül
* [Hozzájárulási keretrendszer](#consent) a védett erőforrásokhoz való ügyfélhozzáférés kezeléséhez az erőforrás-tulajdonosi engedélyezés alapján.

További részletekaz [Alkalmazások integrálása az Azure Active Directoryval.][AAD-Integrating-Apps]

## <a name="authentication"></a>hitelesítés

Az a cselekmény, hogy meghívja a felet a törvényes hitelesítő adatokért, és amely megalapítja az identitás- és hozzáférés-vezérléshez használandó rendszerbiztonsági tag létrehozását. Az [OAuth2 engedélyezési engedély](#authorization-grant) során például a hitelesítő fél tölti be az [erőforrás tulajdonosának](#resource-owner) vagy [az ügyfélalkalmazásnak](#client-application)a szerepét , a felhasznált támogatástól függően.

## <a name="authorization"></a>engedélyezés

Az a törvény, hogy egy hitelesített rendszerbiztonsági tag engedélyt ad, hogy tegyen valamit. Az Azure AD programozási modellben két alapvető használati eset létezik:

* [OAuth2 engedélyezési folyamat](#authorization-grant) során: amikor az [erőforrás tulajdonosa](#resource-owner) engedélyt ad az [ügyfélalkalmazásnak,](#client-application)amely lehetővé teszi az ügyfél számára az erőforrás tulajdonosának erőforrásaihoz való hozzáférést.
* Az ügyfél erőforrás-hozzáférése során: az [erőforrás-kiszolgáló](#resource-server)által megvalósított módon, a [hozzáférési jogkivonatban](#access-token) lévő [jogcímértékek](#claim) használatával hozza meg a hozzáférés-vezérlési döntéseket azok alapján.

## <a name="authorization-code"></a>engedélyezési kód

Az [engedélyezési végpont](#authorization-endpoint)által az [ügyfélalkalmazásnak](#client-application) biztosított rövid élettartamú "token", az "engedélyezési kód" folyamat részeként a négy OAuth2 [engedélyezési támogatás](#authorization-grant)egyike. A kód az [erőforrás tulajdonosának](#resource-owner)hitelesítésére válaszul kerül vissza az ügyfélalkalmazásba, jelezve, hogy az erőforrás tulajdonosa delegált engedélyt adott a kért erőforrások elérésére. A folyamat részeként a kód később egy [hozzáférési jogkivonatra](#access-token)váltát.

## <a name="authorization-endpoint"></a>engedélyezési végpont

Az [engedélyezési kiszolgáló](#authorization-server)által megvalósított egyik végpont, amely az [erőforrás tulajdonosával](#resource-owner) való együttműködéshez használható az OAuth2 engedélyezési engedélyezési folyamat során [történő engedélyezési támogatás](#authorization-grant) biztosításához. A használt engedélyezési engedélyezési folyamattól függően a ténylegesen megadott támogatás változhat, beleértve az [engedélyezési kódot](#authorization-code) vagy a [biztonsági tokent.](#security-token)

További részletekért tekintse meg az OAuth2 specifikáció [engedélyezési támogatási típusait][OAuth2-AuthZ-Grant-Types] és [engedélyezési végpontszakaszait,][OAuth2-AuthZ-Endpoint] valamint az [OpenIDConnect specifikációt.][OpenIDConnect-AuthZ-Endpoint]

## <a name="authorization-grant"></a>engedélyezési támogatás

Az erőforrás [tulajdonosának](#resource-owner) a védett erőforrásokhoz való hozzáférésre [vonatkozó,](#authorization) [ügyfélalkalmazásnak](#client-application)biztosított hitelesítő adatait képviselő hitelesítő adat. Az ügyfélalkalmazások [az OAuth2 engedélyezési keretrendszer által meghatározott négy támogatási típus][OAuth2-AuthZ-Grant-Types] egyikét használhatják a támogatás megszerzéséhez, az ügyfél típusától/követelményeitől függően: "engedélyezési kód megadása", "ügyfélhitelesítő adatok megadása", "implicit támogatás" és "erőforrás-tulajdonosi jelszó hitelesítő adatok megadása". Az ügyfélnek visszaadott hitelesítő adat vagy egy [hozzáférési jogkivonat,](#access-token)vagy egy [engedélyezési kód](#authorization-code) (amelyet később egy hozzáférési jogkivonatra cserélnek), a használt engedélyezési engedély típusától függően.

## <a name="authorization-server"></a>engedélyezési kiszolgáló

Az [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def]meghatározása szerint, a hozzáférési jogkivonatok az [ügyfél](#client-application) számára történő kiadásáért felelős kiszolgáló, miután sikeresen engedélyezte az [erőforrás tulajdonosát,](#resource-owner) és megszerezte annak engedélyezését. Az [ügyfélalkalmazás](#client-application) futásidőben kommunikál az engedélyezési kiszolgálóval az [engedélyezési](#authorization-endpoint) és [a tokenvégpontokon](#token-endpoint) keresztül, az OAuth2 által meghatározott engedélyezési [támogatásokkal](#authorization-grant)összhangban.

A Microsoft identity platform alkalmazásintegrációja esetén a Microsoft identity platform megvalósítja az Azure AD-alkalmazások és a Microsoft szolgáltatás API-jainak engedélyezési kiszolgálói szerepkörét, például a [Microsoft Graph API-kat.][Microsoft-Graph]

## <a name="claim"></a>Azt állítják

A [biztonsági jogkivonat jogcímeket](#security-token) tartalmaz, amelyek egy entitásra (például [ügyfélalkalmazásra](#client-application) vagy [erőforrás-tulajdonosra)](#resource-owner)vonatkozó állításokat szolgáltatnak egy másik entitásnak (például az [erőforrás-kiszolgálónak).](#resource-server) A jogcímek olyan név-/értékpárok, amelyek a jogkivonat tulajdonosával (például az [engedélyezési kiszolgáló](#authorization-server)által hitelesített rendszerbiztonsági taggal) kapcsolatos tényeket továbbítják. Egy adott jogkivonatban jelen lévő jogcímek több változótól függenek, beleértve a jogkivonat típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatok típusát, az alkalmazás konfigurációját stb.

További részletekért tekintse meg a [Microsoft identity platform tokenhivatkozását.][AAD-Tokens-Claims]

## <a name="client-application"></a>ügyfélalkalmazás

Az [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def]meghatározása szerint egy olyan alkalmazás , amely védett erőforrás-kérelmeket küld az [erőforrás tulajdonosa](#resource-owner)nevében . Az "ügyfél" kifejezés nem jelent konkrét hardvermegvalósítási jellemzőket (például azt, hogy az alkalmazás kiszolgálón, asztalon vagy más eszközön hajt-e végre).

Az ügyfélalkalmazás [engedélyt](#authorization) kér egy erőforrás tulajdonosától, hogy részt vegyen egy [OAuth2 engedélyezési engedélyezési](#authorization-grant) folyamatban, és hozzáférhet az API-khoz/adatokhoz az erőforrás tulajdonosának nevében. Az OAuth2 engedélyezési keretrendszer [két típusú ügyfelet határoz meg][OAuth2-Client-Types], a "bizalmas" és a "nyilvános" típusú, az ügyfél hitelesítő adatainak bizalmas kezelése alapján. Az alkalmazások webes [ügyfelet (bizalmas)](#web-client) valósíthatnak meg, amely egy webkiszolgálón, az eszközön telepített [natív ügyfélen (nyilvánosan)](#native-client) vagy az eszköz böngészőjében futó [felhasználói ügynök-alapú ügyfélen (nyilvános)](#user-agent-based-client) fut.

## <a name="consent"></a>Beleegyezés

Az a folyamat, amelynek során az [erőforrás-tulajdonos](#resource-owner) engedélyt ad egy [ügyfélalkalmazásnak,](#client-application)hogy az erőforrás tulajdonosa nevében meghatározott [engedélyek](#permissions)alapján hozzáférjen a védett erőforrásokhoz. Az ügyfél által kért engedélyektől függően a rendszergazda vagy a felhasználó beleegyezését kéri a szervezet/egyéni adatok hozvaló hozzáféréshez. Megjegyzés: egy [több-bérlős](#multi-tenant-application) forgatókönyvben az alkalmazás [egyszerű szolgáltatás is](#service-principal-object) rögzítésre kerül a hozzájáruló felhasználó bérlőjében.

További információkért lásd a [hozzájárulási keretet.](consent-framework.md)

## <a name="id-token"></a>Azonosító token

Az [engedélyezési kiszolgáló](#authorization-server) engedélyezési [végpontja](#authorization-endpoint)által biztosított [OpenID Connect][OpenIDConnect-ID-Token] [biztonsági jogkivonat](#security-token) , amely a végfelhasználói [erőforrás tulajdonosának](#resource-owner)hitelesítésével kapcsolatos [jogcímeket](#claim) tartalmaz. A hozzáférési jogkivonathoz hasonlóan az azonosító jogkivonatok is digitálisan aláírt [JSON webtokenként (JWT)][JWT]jelennek meg. A hozzáférési jogkivonattal ellentétben azonban az azonosító jogkivonatjogcímek nem használhatók az erőforrás-hozzáféréshez és a különösen a hozzáférés-vezérléshez kapcsolódó célokra.

További részletekért tekintse meg a [Microsoft identity platform tokenhivatkozását.][AAD-Tokens-Claims]

## <a name="microsoft-identity-platform"></a>Microsoft-identitásplatform

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. Ez egy teljes funkcionalitású platform, amely egy hitelesítési szolgáltatásból, könyvtárakból, alkalmazásregisztrációból és -konfigurációból, teljes fejlesztői dokumentációból, kódmintákból és egyéb fejlesztői tartalmakból áll. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet. További részletek a [Microsoft identity platformról.](about-microsoft-identity-platform.md)

## <a name="multi-tenant-application"></a>több-bérlős alkalmazás

Egy alkalmazás, amely lehetővé teszi a bejelentkezést és [a jóváhagyást](#consent) a felhasználók által kiépített bármely Azure [AD-bérlő,](#tenant)beleértve a bérlők nem az ügyfél, ahol az ügyfél regisztrálva van. [Natív ügyfélalkalmazások](#native-client) több-bérlős alapértelmezés szerint, mivel a [webes ügyfél](#web-client) és a [webes erőforrás/API-alkalmazások](#resource-server) képesek választani egy- vagy több-bérlős között. Ezzel szemben egy egybérlős webalkalmazás csak az alkalmazás regisztrálása szerinti bérlővel azonos bérlőben kiépített felhasználói fiókokbejelentkezését engedélyezné.

További részletekért [tekintse meg, hogyan jelentkezhet be bármely Azure AD-felhasználó a több-bérlős alkalmazás minta használatával.][AAD-Multi-Tenant-Overview]

## <a name="native-client"></a>natív ügyfél

Olyan [ügyfélalkalmazástípus,](#client-application) amely natív módon van telepítve az eszközre. Mivel az összes kód végrehajtása egy eszközön történik, "nyilvános" ügyfélnek minősül, mivel nem képes a hitelesítő adatokat bizalmasan/bizalmasan tárolni. További részletekért lásd [az OAuth2 ügyféltípusokat és -profilokat.][OAuth2-Client-Types]

## <a name="permissions"></a>Engedélyek

Az [ügyfélalkalmazás](#client-application) az engedélykérelmek deklarálásával jut el az [erőforrás-kiszolgálóhoz.](#resource-server) Kétféle áll rendelkezésre:

* A "Delegált" engedélyek, amelyek a bejelentkezett erőforrás [tulajdonosának](#resource-owner)delegált engedélyezése alapján a [hatóköralapú](#scopes) hozzáférést határozzák meg, futásidőben ["scp" jogcímként](#claim) jelennek meg az erőforrás számára az ügyfél [hozzáférési jogkivonatában](#access-token).
* Az "Alkalmazás" engedélyek, amelyek az ügyfélalkalmazás hitelesítő adatait/identitását használó [szerepköralapú](#roles) hozzáférést adnak meg, futásidőben ["szerepkör" jogcímként](#claim) jelennek meg az ügyfél hozzáférési jogkivonatában.

A [jóváhagyási](#consent) folyamat során is megjelennek, így a rendszergazda vagy az erőforrás tulajdonosa lehetőséget ad az ügyfélnek a bérlőerőforrásaihoz való hozzáférés megadására/megtagadására.

Az engedélykérelmek az [Azure Portalon][AZURE-portal]lévő alkalmazások **API-engedélyek** lapján vannak konfigurálva a kívánt "Delegált engedélyek" és "Alkalmazásengedélyek" kiválasztásával (ez utóbbihoz a globális rendszergazdai szerepkör tagsága szükséges). Mivel egy [nyilvános ügyfél](#client-application) nem tudja biztonságosan karbantartani a hitelesítő adatokat, csak delegált engedélyeket kérhet, míg a [bizalmas ügyfél](#client-application) delegált és alkalmazásengedélyeket is kérhet. Az ügyfél [alkalmazásobjektuma](#application-object) a deklarált engedélyeket a [requiredResourceAccess tulajdonságban][Graph-App-Resource]tárolja.

## <a name="resource-owner"></a>erőforrás tulajdonosa

Az [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def]meghatározása szerint olyan entitás, amely képes hozzáférést biztosítani egy védett erőforráshoz. Ha az erőforrás tulajdonosa személy, akkor a rendszer végfelhasználónak nevezi. Ha például egy [ügyfélalkalmazás](#client-application) a [Microsoft Graph API-n][Microsoft-Graph]keresztül szeretné elérni egy felhasználó postaládáját, a postaládája erőforrás-tulajdonosának engedélye szükséges.

## <a name="resource-server"></a>erőforrás-kiszolgáló

Az [OAuth2 engedélyezési keretrendszer][OAuth2-Role-Def]által meghatározott kiszolgáló, amely védett erőforrásokat üzemeltet, és képes elfogadni és megválaszolni a [hozzáférési jogkivonatot](#access-token)tartalmazó [ügyfélalkalmazások](#client-application) védett erőforrás-kéréseit. Más néven védett erőforrás-kiszolgáló vagy erőforrás-alkalmazás.

Az erőforrás-kiszolgáló api-kat biztosít, és az OAuth 2.0 engedélyezési keretrendszer használatával kényszeríti ki a védett erőforrásokhoz való hozzáférést a [hatókörökés](#scopes) [szerepkörök](#roles)használatával. Ilyenek például a [Microsoft Graph API,][Microsoft-Graph] amely hozzáférést biztosít az Azure AD-bérlői adatokhoz, és az Office 365 API-k, amelyek hozzáférést biztosítanak az adatokhoz, például a levelezéshez és a naptárhoz. 

Az ügyfélalkalmazáshoz hasonlóan az erőforrás-alkalmazás identitáskonfigurációja is létrejön egy Azure AD-bérlőben [történő regisztrációval,](#application-registration) amely az alkalmazás és az egyszerű szolgáltatásobjektum ot is biztosítja. Egyes Microsoft által biztosított API-k, például a Microsoft Graph API, előre regisztrált szolgáltatásnévi elérhetővé minden bérlőben a kiépítés során elérhetővé.

## <a name="roles"></a>roles

A [hatókörökhöz](#scopes)hasonlóan a szerepkörök leirányítják az [erőforrás-kiszolgálót](#resource-server) a védett erőforrásokhoz való hozzáférés szabályozására. Két típusa van: a "felhasználói" szerepkör szerepkör-alapú hozzáférés-vezérlést valósít meg az erőforráshoz hozzáférést igénylő felhasználók/csoportok számára, míg az "alkalmazás" szerepkör ugyanazt az [ügyfélalkalmazások](#client-application) esetében valósítja meg, amelyek hozzáférést igényelnek.

A szerepkörök erőforrás-definiált karakterláncok (például "Költségjóváhagyó", "Írásvédett", "Directory.ReadWrite.All"), az [Azure Portalon][AZURE-portal] az erőforrás [alkalmazásjegyzékén](#application-manifest)keresztül kezelik, és az erőforrás [appRoles tulajdonságában][Graph-Sp-Resource]tárolják. Az Azure Portal is a felhasználók hozzárendelése a "felhasználói" szerepkörök, és konfigurálja az [ügyfélalkalmazás engedélyek](#permissions) eléréséhez egy "alkalmazás" szerepkör.

A Microsoft Graph API által elérhetővé tett alkalmazásszerepkörök részletes ismertése a [Graph API engedélyezési hatókörei című témakörben látható.][Graph-Perm-Scopes] Részletes megvalósítási példát a [Hozzáférés kezelése az RBAC és az Azure Portal használatával című témakörben.][AAD-RBAC]

## <a name="scopes"></a>hatókörök

A [szerepkörökhöz](#roles)hasonlóan a hatókörök lekínálják az [erőforrás-kiszolgáló](#resource-server) számára a védett erőforrásokhoz való hozzáférést. A hatókörök a [hatóköralapú hozzáférés-vezérlés][OAuth2-Access-Token-Scopes] megvalósítására szolgálnak egy olyan [ügyfélalkalmazás](#client-application) esetében, amely a tulajdonos által delegált hozzáférést kapott az erőforráshoz.

A hatókörök erőforrás-definiált karakterláncok (például "Mail.Read", "Directory.ReadWrite.All"), amelyeket az [Azure Portalon][AZURE-portal] kezelnek az erőforrás [alkalmazásjegyzékén](#application-manifest)keresztül, és az erőforrás [oauth2Permissions tulajdonságában][Graph-Sp-Resource]tárolnak. Az Azure Portal is konfigurálhatja az ügyfélalkalmazás [delegált engedélyeket](#permissions) egy hatókör eléréséhez.

Az ajánlott eljárás elnevezési konvenció, hogy egy "resource.operation.constraint" formátumban. A Microsoft Graph API által elérhetővé tett hatókörök részletes ismertése a [Graph API engedélyezési hatókörei című témakörben látható.][Graph-Perm-Scopes] Az Office 365-szolgáltatások által elérhetővé tett hatókörökről az [Office 365 API-engedélyek hivatkozása című témakörben található.][O365-Perm-Ref]

## <a name="security-token"></a>biztonsági jogkivonat

Jogcímeket tartalmazó aláírt dokumentum, például egy OAuth2 token vagy SAML 2.0-s állítás. OAuth2 [engedélyezési engedély](#authorization-grant)esetén a [hozzáférési jogkivonat](#access-token) (OAuth2) és az [id token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) biztonsági jogkivonatok, amelyek mindegyike [JSON webtokenként (JWT)][JWT]van megvalósítva.

## <a name="service-principal-object"></a>szolgáltatásegyszerű objektum

Amikor regisztrál/frissít egy alkalmazást az [Azure Portalon,][AZURE-portal]a portál létrehoz/frissít egy [alkalmazásobjektumot](#application-object) és egy megfelelő egyszerű szolgáltatásobjektumot az adott bérlőhöz. Az alkalmazásobjektum globálisan *határozza meg* az alkalmazás identitáskonfigurációját (az összes olyan bérlőn, ahol a társított alkalmazás hozzáférést kapott), és az a sablon, amelyből a megfelelő egyszerű szolgáltatásobjektum(ok) futásidőben (egy adott bérlőben) helyileg *használható.*

További információt az [Alkalmazás- és egyszerű szolgáltatásobjektumok című témakörben talál.][AAD-App-SP-Objects]

## <a name="sign-in"></a>sign-in

A folyamat egy [ügyfélalkalmazás,](#client-application) amely a végfelhasználói hitelesítés t és a kapcsolódó állapot rögzítése, abból a célból, hogy egy [biztonsági jogkivonatot,](#security-token) és az alkalmazás munkamenete az adott állapotba. Az állapot tartalmazhat összetevőket, például a felhasználói profil adatait és a jogkivonat-jogcímekből származó információkat.

Az alkalmazás bejelentkezési funkciója általában egyszeri bejelentkezés (SSO) megvalósításához használatos. Azt is megelőzheti a "regisztrációs" funkció, mint a belépési pont a végfelhasználó számára, hogy hozzáférjenek egy alkalmazás (az első bejelentkezéskor). A regisztrációs funkció a felhasználóra jellemző további állapotok összegyűjtésére és megőrzésére szolgál, és [felhasználói hozzájárulásra](#consent)lehet szükség.

## <a name="sign-out"></a>kijelentkezés

A végfelhasználó hitelesítésének megszüntetésének folyamata, amely leválasztja az [ügyfélalkalmazás-munkamenethez](#client-application) társított felhasználói állapotot a [bejelentkezés](#sign-in) során

## <a name="tenant"></a>Bérlő

Egy Azure AD-címtár egy példányát nevezik egy Azure AD-bérlő. Számos funkciót kínál, többek között:

* beállításjegyzék-szolgáltatás integrált alkalmazásokhoz
* felhasználói fiókok és regisztrált alkalmazások hitelesítése
* A különböző protokollok, például az OAuth2 és az SAML támogatásához szükséges REST-végpontok, beleértve az [engedélyezési végpontot,](#authorization-endpoint)a [jogkivonat-végpontot](#token-endpoint) és a [több-bérlős alkalmazások](#multi-tenant-application)által használt "közös" végpontot.

Az Azure AD-bérlők a regisztráció során jönnek létre/társítanak az Azure-előfizetésekhez és az Office 365-előfizetésekhez, így az előfizetéshez identitás- & hozzáférés-kezelési funkciókat biztosítanak. Az Azure-előfizetés-rendszergazdák további Azure AD-bérlőket is létrehozhatnak az Azure Portalon keresztül. [Olvassa el: Hogyan szerezhetbe be egy Azure Active Directory-bérlőt][AAD-How-To-Tenant] a bérlők höz való hozzáférés különböző módjairól. [Tekintse meg, hogy az Azure-előfizetések hogyan vannak társítva][AAD-How-Subscriptions-Assoc] az Azure Active Directoryhoz az előfizetések és az Azure AD-bérlők közötti kapcsolat részleteiért.

## <a name="token-endpoint"></a>token végpont

Az [engedélyezési kiszolgáló](#authorization-server) által az OAuth2 [engedélyezési támogatások](#authorization-grant)támogatására megvalósított egyik végpont. A támogatástól függően használható [egy ügyfélnek](#access-token) (és a kapcsolódó "frissítési" jogkivonat) megszerzéséhez, [client](#client-application)vagy az [OpenID Connect][OpenIDConnect] protokollhasználatával használt azonosító [jogkivonathoz.](#id-token)

## <a name="user-agent-based-client"></a>Felhasználóügynök-alapú ügyfél

Olyan [ügyfélalkalmazástípus,](#client-application) amely letölti a kódot egy webkiszolgálóról, és egy felhasználói ügynökön (például egy webböngészőn) belül hajt végre, például egy egyoldalas alkalmazáson (SPA). Mivel az összes kód végrehajtása egy eszközön történik, "nyilvános" ügyfélnek minősül, mivel nem képes a hitelesítő adatokat bizalmasan/bizalmasan tárolni. További információ: [OAuth2 ügyféltípusok és profilok][OAuth2-Client-Types].

## <a name="user-principal"></a>felhasználó egyszerű

Hasonlóan ahhoz, ahogyan egy egyszerű szolgáltatásobjektumot használnak egy alkalmazáspéldány ábrázolására, a felhasználó egyszerű objektuma a rendszerbiztonsági tag egy másik típusa, amely egy felhasználót jelöl. A Microsoft Graph [User erőforrástípusa][Graph-User-Resource] határozza meg egy felhasználói objektum sémáját, beleértve a felhasználóval kapcsolatos tulajdonságokat, például a vezeték- és utónevet, a felhasználó egyszerű nevét, a könyvtárszerepkör-tagságot stb. Ez biztosítja a felhasználói identitás konfigurációját az Azure AD-hez egy egyszerű felhasználó létrehozásához futásidőben. Az egyszerű felhasználó az egyszeri bejelentkezés hitelesített felhasználójának, a [hozzájárulásdelegálás](#consent) rögzítésének, a hozzáférés-vezérlési döntések meghozatalának stb.

## <a name="web-client"></a>webes ügyfél

Olyan [ügyfélalkalmazástípus,](#client-application) amely a webkiszolgálón lévő összes kódot végrehajtja, és "bizalmas" ügyfélként működhet a hitelesítő adatok kiszolgálón való biztonságos tárolásával. További információ: [OAuth2 ügyféltípusok és profilok][OAuth2-Client-Types].

## <a name="next-steps"></a>További lépések

A [Microsoft identity platform fejlesztői útmutatója][AAD-Dev-Guide] a céloldal, amelyet a Microsoft identitásplatformfejlesztéssel kapcsolatos összes témaköréhez használhat, beleértve az [alkalmazásintegráció][AAD-How-To-Integrate] áttekintését, valamint a [Microsoft identitásplatform-hitelesítés és a támogatott hitelesítési forgatókönyvek][AAD-Auth-Scenarios]alapjait. Kódmintákat is találhat & oktatóanyagokat arról, hogyan lehet gyorsan felkelni és futni a [GitHubon.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

A következő megjegyzések részben visszajelzést adhat, és segíthet a tartalom finomításában és alakításában, beleértve az új definíciókra vonatkozó kéréseket vagy a meglévők frissítését!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
[Graph-User-Resource]: /graph/api/resources/user
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
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
