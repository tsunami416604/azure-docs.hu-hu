---
title: Műszaki és szolgáltatásáttekintés – Azure Active Directory B2C
description: Az Azure Active Directory B2C szolgáltatásainak és technológiáinak részletes bemutatása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d6b33211f6f247d9f30c0f162b388085faabe6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332536"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Az Azure Active Directory B2C műszaki és szolgáltatásáttekintése

[Az Azure Active Directory B2C– relkapcsolatban](overview.md)ez a cikk részletesebb bevezetést nyújt a szolgáltatáshoz. Az itt tárgyalt az elsődleges erőforrások, amelyekkel a szolgáltatásban dolgozik, annak szolgáltatásai, és ezek lehetővé teszik, hogy teljes mértékben egyéni identitásélményt biztosítson az alkalmazásokban lévő ügyfelek számára.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C-bérlő

Az Azure Active Directory B2C (Azure AD B2C) egy *bérlő* képviseli a szervezet, és a felhasználók egy könyvtárat. Mindegyik Azure AD B2C-bérlő önálló, és elkülönül a többi Azure AD B2C-bérlőtől. Az Azure AD B2C-bérlő eltér az Azure Active Directory-bérlő, amely lehet, hogy már rendelkezik.

Az Azure AD B2C-bérlőkben az elsődleges erőforrások a következők:

* **Címtár** – A *címtár,* ahol az Azure AD B2C tárolja a felhasználók hitelesítő adatait és profiladatait, valamint az alkalmazás regisztrációk.
* **Alkalmazásregisztrációk** – Regisztrálja a webes, mobil- és natív alkalmazásokat az Azure AD B2C-vel az identitáskezelés engedélyezéséhez. Az Azure AD B2C-vel védeni kívánt API-kat is.
* **Felhasználói folyamatok** és **egyéni szabályzatok** – A beépített (felhasználói folyamatok) és teljes mértékben testreszabható (egyéni házirendek) identitásélményaz alkalmazások.
  * A *felhasználói folyamatok* segítségével gyors konfigurálása és engedélyezése a közös identitásfeladatok, mint a regisztráció, bejelentkezés és a profil szerkesztése.
  * *Egyéni házirendek* használatával nem csak a gyakori identitáskezelési feladatokhoz, hanem a szervezet, az ügyfelek, az alkalmazottak, a partnerek és a polgárok számára egyedi összetett identitás-munkafolyamatok támogatásához is lehetővé teszi a felhasználói élményt.
* **Identitásszolgáltatók** – összevonási beállítások:
  * *Olyan közösségi* identitásszolgáltatók, mint a Facebook, a LinkedIn vagy a Twitter, amelyeket támogatni szeretne az alkalmazásokban.
  * *Külső* identitásszolgáltatók, amelyek támogatják a szabványos identitásprotokollokat, például az OAuth 2.0-s, az OpenID Connect és más.
  * *Helyi* fiókok, amelyek lehetővé teszik a felhasználók számára, hogy felhasználónévvel (vagy e-mail címmel vagy más azonosítóval) és jelszóval regisztráljanak és jelentkezzenek be.
* **Kulcsok** – Titkosítási kulcsok hozzáadása és kezelése a jogkivonatok aláírásához és érvényesítéséhez.

Az Azure AD B2C-bérlő az első erőforrás, amelyet létre kell hoznia az Azure AD B2C használatával való ismerkedéshez. Ismerje meg, hogyan [oktatóanyag: Hozzon létre egy Azure Active Directory B2C-bérlő.](tutorial-create-tenant.md)

## <a name="accounts-in-azure-ad-b2c"></a>Fiókok az Azure AD B2C-ben

Az Azure AD B2C többféle felhasználói fiókot határoz meg. Az Azure Active Directory, az Azure Active Directory B2B és az Azure Active Directory B2C megosztják ezeket a fióktípusokat.

* **Munkahelyi fiók** – A munkahelyi fiókkal rendelkező felhasználók kezelhetik a bérlő erőforrásait, és rendszergazdai szerepkörrel a bérlők is kezelhetik. A munkahelyi fiókkal rendelkező felhasználók új fogyasztói fiókokat hozhatnak létre, alaphelyzetbe állíthatják a jelszavakat, letilthatják/feloldhatják a fiókokat, és engedélyeket állíthatnak be, vagy fiókot rendelhetnek egy biztonsági csoporthoz.
* **Vendégfiók** – A bérlőhöz vendégekként meghívott külső felhasználók. Egy tipikus forgatókönyv egy vendégfelhasználó meghívására az Azure AD B2C bérlő, hogy megosszák a felügyeleti feladatokat.
* **Fogyasztói fiók** – A fogyasztói fiókok az Azure AD B2C címtárban létrehozott fiókok, amikor a felhasználók befejezik a regisztrációs felhasználói utat a bérlőben regisztrált alkalmazásban.

![Az Azure AD B2C felhasználói felügyeleti lapja az Azure Portalon](media/technical-overview/portal-01-users.png)<br/>*Ábra: Felhasználói címtár egy Azure AD B2C-bérlőn belül az Azure Portalon*

### <a name="consumer-accounts"></a>Fogyasztói fiókok

A *fogyasztói* fiókkal a felhasználók bejelentkezhetnek az Azure AD B2C-vel védett alkalmazásokba. A fogyasztói fiókkal rendelkező felhasználók azonban nem férhetnek hozzá az Azure-erőforrásokhoz, például az Azure Portalhoz.

Fogyasztói fiók társítható az alábbi identitástípusokhoz:

* **Helyi** identitás, a felhasználónév és a jelszó helyileg az Azure AD B2C könyvtárban tárolt. Gyakran hivatkozunk ezekre az identitásokra , mint "helyi fiókok".
* **Közösségi** vagy **vállalati** identitások, ahol a felhasználó identitását egy összevont identitásszolgáltató, például a Facebook, a Microsoft, az ADFS vagy a Salesforce kezeli.

A felhasználói fiókkal rendelkező felhasználók több identitással is bejelentkezhetnek, például felhasználónévvel, e-mail-címvel, alkalmazottazonosítóval, kormányzati azonosítóval és egyebekkel. Egyetlen fiók több identitással is rendelkezhet, helyi és közösségi identitással egyaránt.

![Fogyasztói fiók identitásai](media/technical-overview/identities.png)<br/>*Ábra: Egyetlen fogyasztói fiók több identitással az Azure AD B2C-ben*

Az Azure AD B2C lehetővé teszi a fogyasztói fiókprofilok , például a megjelenítendő név, a vezetéknév, a megadott név, a város és mások általános attribútumainak kezelését. Az Azure AD-sémát is kiterjesztheti a felhasználóktovábbi adatainak tárolására. Például az országuk vagy tartózkodási helyük, az előnyben részesített nyelv és a preferenciák, például, hogy fel akarnak-e iratkozni egy hírlevélre, vagy engedélyezni kívánják-e a többtényezős hitelesítést.

Az Azure Active [Directory B2C felhasználói fiókjainak áttekintése című](user-overview.md)témakörben további információ az Azure AD B2C felhasználói fióktípusairól.

## <a name="external-identity-providers"></a>Külső identitásszolgáltatók

Konfigurálhatja az Azure AD B2C-t úgy, hogy a felhasználók külső közösségi vagy vállalati identitásszolgáltatók (IdP) hitelesítő adataival jelentkezhessenek be az alkalmazásba. Az Azure AD B2C támogatja a külső identitásszolgáltatókat, például a Facebookot, a Microsoft-fiókot, a Google-t, a Twittert és az OAuth 1.0, az OAuth 2.0, az OpenID Connect, az SAML vagy a WS-Federation protokollokat támogató identitásszolgáltatókat.

![Külső identitásszolgáltatók](media/technical-overview/external-idps.png)

A külső identitásszolgáltató-összevonással lehetővé teheti a fogyasztók számára, hogy meglévő közösségi vagy vállalati fiókjukkal jelentkezzenek be anélkül, hogy csak az alkalmazáshoz kellene új fiókot létrehozniuk.

A regisztrációs vagy bejelentkezési lapon az Azure AD B2C a felhasználó által a bejelentkezéshez választható külső identitásszolgáltatók listáját mutatja be. Miután kiválasztotta az egyik külső identitásszolgáltatót, a rendszer átirányítja őket a kiválasztott szolgáltató webhelyére a bejelentkezési folyamat befejezéséhez. Miután a felhasználó sikeresen bejelentkezik, visszakerülnek az Azure AD B2C-be az alkalmazásfiókjának hitelesítéséhez.

![Mobilbejelentkezési példa közösségi fiókkal (Facebook)](media/technical-overview/external-idp.png)

Ha meg szeretné tekinteni, hogyan adhat hozzá identitásszolgáltatókat az Azure AD B2C-ben, olvassa el [az Oktatóanyag: Identitásszolgáltatók hozzáadása az alkalmazásokhoz az Azure Active Directory B2C-ben című témakört.](tutorial-add-identity-providers.md)

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identitáskezelési élmény: felhasználói folyamatok vagy egyéni házirendek

Az Azure AD B2C bővíthető házirendkerete az alapvető erőssége. A házirendek leírják a felhasználók identitásélményét, például a regisztrációt, a bejelentkezést és a profilszerkesztést.

Az Azure AD B2C két elsődleges elérési utak, amelyek et az identitásélmények: felhasználói folyamatok és egyéni szabályzatok.

* **A felhasználói folyamatok** előre definiált, beépített, konfigurálható szabályzatok, amelyeket biztosítunk, így percek alatt létrehozhat regisztrációs, bejelentkezési és házirend-szerkesztési élményeket.

* **Az egyéni házirendek** lehetővé teszik, hogy saját felhasználói utakat hozzon létre az összetett identitáskezelési élményforgatókönyvekhez.

Mind a felhasználói folyamatok és az egyéni szabályzatok powered by az *Identity Experience Framework,* az Azure AD B2C házirend vezénylési motor.

### <a name="user-flow"></a>Felhasználói folyamat

A leggyakoribb identitáskezelési feladatok gyors beállítása érdekében az Azure Portal számos előre definiált és konfigurálható szabályzatot tartalmaz, amelyeket *felhasználói folyamatoknak*neveznek.

A felhasználói folyamat beállításait az alábbihoz hasonló módon szabályozhatja az alkalmazások identitáskezelési viselkedésének szabályozásához:

* A bejelentkezéshez használt fióktípusok, például közösségi fiókok, például Facebook-fiókok, illetve a bejelentkezéshez e-mail címet és jelszót használó helyi fiókok
* A fogyasztótól begyűjtendő attribútumok, például a keresztnév, az irányítószám vagy a lakóhely szerinti ország
* Azure Multi-Factor Authentication (MFA)
* A felhasználói felület testreszabása
* Jogcímek készlete egy jogkivonatban, amelyet az alkalmazás a felhasználói folyamat befejezése után kap
* Munkamenet-kezelés
* ... és így tovább.

A mobil-, webes és egyoldalas alkalmazások többségének leggyakoribb identitáskezelési forgatókönyvei a felhasználói folyamatokkal hatékonyan definiálhatók és valósíthatók meg. Azt javasoljuk, hogy használja a beépített felhasználói folyamatok, kivéve, ha összetett felhasználói út forgatókönyvek, amelyek megkövetelik az egyéni szabályzatok teljes rugalmasságát.

További információ az [Azure Active Directory B2C felhasználói folyamataiból.](user-flow-overview.md)

### <a name="custom-policy"></a>Egyéni szabályzat

Egyéni szabályzatok az Identity Experience Framework (IEF) vezénylési motor teljes teljesítményéhez való hozzáférést. Az egyéni szabályzatokkal az IEF segítségével szinte bármilyen hitelesítést, felhasználói regisztrációt vagy profilszerkesztési élményt készíthet, amelyet el tud képzelni.

Az identitáskezelési keretrendszer lehetővé teszi a felhasználói utazások bármilyen lépés kombinációjával való összeállítását. Példa:

* Összevont más identitásszolgáltatókkal
* Első és harmadik fél től származó többtényezős hitelesítési (MFA) kihívások
* Bármilyen felhasználói bevitel gyűjtése
* Integrálható külső rendszerekkel a REST API kommunikációval

Minden ilyen felhasználói út egy szabályzat határozza meg, és hozhat létre annyi vagy kevés szabályzatot, amennyire szüksége van, hogy a legjobb felhasználói élményt a szervezet számára.

![Az IEF által engedélyezett összetett felhasználói út ra mutató diagram](media/technical-overview/custom-policy.png)

Az egyéni házirendet több, hierarchikus láncban lévő, egymásra hivatkozó XML-fájl határozza meg. Az XML-elemek határozzák meg a jogcímsémát, a jogcímátalakításokat, a tartalomdefiníciókat, a jogcímszolgáltatókat, a technikai profilokat, a felhasználói utazás vezénylési lépéseit és az identitásélmény egyéb szempontjait.

Az egyéni házirendek hatékony rugalmassága akkor a legmegfelelőbb, ha összetett identitás-forgatókönyveket kell készítenie. Az egyéni házirendeket konfiguráló fejlesztőknek részletesen meg kell határozniuk a megbízható kapcsolatokat, hogy tartalmazzák a metaadat-végpontokat, a pontos jogcímcsere-definíciókat, és szükség szerint konfigurálják a titkos kulcsokat, kulcsokat és tanúsítványokat az egyes identitásszolgáltatók által szükség szerint.

További információ az egyéni szabályzatokról az [Egyéni szabályzatokban az Azure Active Directory B2C-ben.](custom-policy-overview.md)

## <a name="protocols-and-tokens"></a>Protokollok és tokenek

Az Azure AD B2C támogatja az [OpenID Connect és az OAuth 2.0 protokollokat](protocols-overview.md) a felhasználói utazásokhoz. Az OpenID Connect Azure AD B2C-implementációjában az alkalmazás az Azure AD B2C felé irányuló hitelesítési kérések küldésével indítja el a felhasználói utat.

Az Azure AD B2C-nek küldött kérelem eredménye egy biztonsági jogkivonat, például egy [azonosító jogkivonat vagy egy hozzáférési jogkivonat.](tokens-overview.md) Ez a biztonsági jogkivonat határozza meg a felhasználó identitását. Jogkivonatok érkeznek az Azure AD B2C végpontok, például a vagy `/token` `/authorize` végpont. Ezekkel a jogkivonatok, hozzáférhet a jogcímek, amelyek segítségével érvényesítheti az identitást, és lehetővé teszi a hozzáférést a biztonságos erőforrásokhoz.

Külső identitások esetén az Azure AD B2C bármely OAuth 1.0, OAuth 2.0, OpenID Connect, SAML és WS-Fed identitásszolgáltató val támogatja az összevonást.

![SAML-alapú IdP-vel összevont OIDC-alapú ügyfélalkalmazás diagramja](media/technical-overview/protocols.png)

Az előző ábra bemutatja, hogy az Azure AD B2C hogyan kommunikálhat különböző protokollok használatával ugyanazon a hitelesítési folyamaton belül:

1. A függő entitás alkalmazás hitelesítési kérelmet kezdeményez az Azure AD B2C openID Connect használatával.
1. Amikor az alkalmazás egyik felhasználója úgy dönt, hogy az SAML protokollt használó külső identitásszolgáltató használatával jelentkezik be, az Azure AD B2C meghívja az SAML protokollt az adott identitásszolgáltatóval való kommunikációhoz.
1. Miután a felhasználó befejezte a bejelentkezési műveletet a külső identitásszolgáltatóval, az Azure AD B2C majd visszaadja a jogkivonatot a függő entitás alkalmazás openid connect használatával.

## <a name="application-integration"></a>Alkalmazásintegráció

Ha egy felhasználó be szeretne jelentkezni az alkalmazásba, legyen az webes, mobil- vagy asztali vagy egyoldalas alkalmazás (SPA), az alkalmazás engedélyezési kérelmet kezdeményez egy felhasználói folyamat- vagy egyéni házirend által biztosított végponthoz. A felhasználói folyamat vagy az egyéni házirend határozza meg és szabályozza a felhasználói élményt. Amikor egy felhasználói folyamat, például a *regisztrációs vagy bejelentkezési* folyamat, az Azure AD B2C létrehoz egy jogkivonatot, majd átirányítja a felhasználót vissza az alkalmazásba.

![Mobilalkalmazás nyilakkal, amelyek az Azure AD B2C bejelentkezési lapja közötti áramlást mutatják](media/technical-overview/app-integration.png)

Több alkalmazás használhatja ugyanazt a felhasználói folyamatot vagy egyéni házirendet. Egyetlen alkalmazás több felhasználói folyamatot vagy egyéni házirendet is használhat.

Például egy alkalmazásba való bejelentkezéshez az alkalmazás a *regisztrációt vagy a* felhasználói folyamatot használja. Miután a felhasználó bejelentkezett, előfordulhat, hogy szeretné, hogy szerkesztheti a profilt, így az alkalmazás kezdeményez egy másik engedélyezési kérelmet, ezúttal a *profil szerkesztése* felhasználói folyamat.

## <a name="seamless-user-experiences"></a>Zökkenőmentes felhasználói élmény

Az Azure AD B2C-ben létrehozhatja a felhasználók identitásélményét, így a megjelenített oldalak zökkenőmentesen illeszkednek a márka megjelenéséhez és érzetéhez. Szinte teljes mértékben szabályozhatja a felhasználóknak bemutatott HTML- és CSS-tartalmakat, amikor azok végighaladnak az alkalmazás identitáskezelési útjain. Ezzel a rugalmassággal fenntarthatja a márka és a vizuális konzisztencia az alkalmazás és az Azure AD B2C között.

![Képernyőképek a márka által testre szabott regisztrációs lapról](media/technical-overview/seamless-ux.png)

A felhasználói felület testreszabásáról a [Felhasználói felület testreszabása az Azure Active Directory B2C szolgáltatásban található felhasználói felület testreszabása című témakörben talál.](customize-ui-overview.md)

## <a name="localization"></a>Honosítás

Az Azure AD B2C nyelvi testreszabása lehetővé teszi, hogy az ügyfelek igényeinek megfelelően különböző nyelveket fogadjon el. A Microsoft 36 nyelv fordítását biztosítja, de saját fordításokat is biztosíthat bármely nyelvhez. Még akkor is testreszabhatja az oldalakon lévő szövegeket, ha a felhasználói élmény csak egy nyelvre van megadva.

![Három feliratkozási oldal, amelyek en ui szöveget jelenítek meg különböző nyelveken](media/technical-overview/localization.png)

Tekintse meg, hogyan működik a honosítás [a nyelvi testreszabás az Azure Active Directory B2C.](user-flow-language-customization.md)

## <a name="add-your-own-business-logic"></a>Saját üzleti logika hozzáadása

Ha úgy dönt, hogy egyéni szabályzatokat használ, integrálhatja a RESTful API-t egy felhasználói út, hogy saját üzleti logikát adjon hozzá az utazáshoz. Az Azure AD B2C például adatokat cserélhet egy RESTful szolgáltatással a következő célokra:

* Egyéni felhasználóbarát hibaüzenetek megjelenítése.
* Ellenőrizze a felhasználói bevitelt, hogy a hibásan formázott adatok ne maradjanak meg a felhasználói címtárban. Módosíthatja például a felhasználó által bevitt adatokat, például nagybetűssé teheti a keresztnevüket, ha azokat kisbetűvel adták meg.
* A felhasználói adatok további integrálásával gazdagodjon a vállalati üzletági alkalmazással.
* A RESTful hívások használatával leküldéses értesítéseket küldhet, frissítheti a vállalati adatbázisokat, futtathat felhasználói áttelepítési folyamatot, kezelheti az engedélyeket, naplózhatja az adatbázisokat stb.

A hűségprogramok egy másik forgatókönyv, amelyet az Azure AD B2C támogatja a REST API-k hívásához. Például a RESTful szolgáltatás kaphat egy felhasználó e-mail címét, lekérdezheti az ügyfél-adatbázis, majd visszaad a felhasználó hűségszámát az Azure AD B2C. A visszatérési adatok tárolhatók a felhasználó címtárfiókjában az Azure AD B2C, majd további kiértékelése a szabályzat következő lépései, vagy a hozzáférési jogkivonat.

![Üzletági integráció mobilalkalmazásokban](media/technical-overview/lob-integration.png)

Rest API-hívást adhat hozzá az egyéni szabályzat által meghatározott felhasználói út bármely lépésében. Meghívhat például egy REST API-t:

* A bejelentkezés során, közvetlenül azelőtt, hogy az Azure AD B2C érvényesíti a hitelesítő adatokat
* Közvetlenül a bejelentkezés után
* Mielőtt az Azure AD B2C új fiókot hoz na létre a címtárban
* Miután az Azure AD B2C új fiókot hoz létre a címtárban
* Mielőtt az Azure AD B2C kiad egy hozzáférési jogkivonatot

Ha meg szeretné tekinteni, hogyan használhatja az egyéni szabályzatokat a RESTful API-integrációhoz az Azure AD B2C-ben, [olvassa el a REST API-jogcímek integrálása az Azure AD B2C egyéni szabályzatában című témakört.](custom-policy-rest-api-intro.md)

## <a name="protect-customer-identities"></a>Az ügyfelek identitásának védelme

Az Azure AD B2C megfelel a [Microsoft Azure Adatvédelmi központban](https://www.microsoft.com/trustcenter/cloudservices/azure)ismertetett biztonsági, adatvédelmi és egyéb kötelezettségvállalásoknak.

A munkamenetek titkosított adatokként vannak modellezve, és a visszafejtési kulcs csak az Azure AD B2C biztonsági jogkivonat-szolgáltatás által ismert. Egy erős titkosítási algoritmust, az AES-192-t használják. Minden kommunikációs útvonal tls-sel védett a titkosság és az integritás érdekében. Biztonsági jogkivonat-szolgáltatásunk kiterjesztett érvényesítési (EV) tanúsítványt használ a TLS-hez. A Biztonsági jogkivonat-szolgáltatás általában úgy enyhíti a helyek közötti parancsfájlok (XSS) támadásait, hogy nem teszi le a nem megbízható bemenetet.

![A biztonságos adatok ábrája az átvitel és a nyugalom alatt](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Hozzáférés a felhasználói adatokhoz

Az Azure AD B2C-bérlők számos jellemzővel rendelkeznek az alkalmazottak és partnerek számára használt vállalati Azure Active Directory-bérlőkkel. A megosztott szempontok közé tartoznak a felügyeleti szerepkörök megtekintésére, a szerepkörök hozzárendelésére és a naplózási tevékenységekre szolgáló mechanizmusok.

Szerepköröket rendelhet hozzá annak szabályozásához, hogy ki hajthat végre bizonyos felügyeleti műveleteket az Azure AD B2C-ben, többek között a következőket:

* A felhasználói folyamatok minden aspektusának létrehozása és kezelése
* Az összes felhasználói folyamat számára elérhető attribútumséma létrehozása és kezelése
* Identitásszolgáltatók konfigurálása a közvetlen összevonásban való használatra
* Megbízhatósági keretházirendek létrehozása és kezelése az identitáskezelési keretrendszerben (egyéni házirendek)
* Összevonási és titkosítási titkos kulcsok kezelése az identitáskezelési keretrendszerben (egyéni házirendek)

Az Azure AD-szerepkörökről, köztük az Azure AD B2C felügyeleti szerepkör-támogatásról további információt az [Azure Active Directory rendszergazdai szerepkör-engedélyei című témakörben](../active-directory/users-groups-roles/directory-assign-admin-roles.md)talál.

### <a name="multi-factor-authentication-mfa"></a>Multi-factor authentication (MFA)

Az Azure AD B2C többtényezős hitelesítés (MFA) segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben megőrzi a felhasználók egyszerűségét. További biztonságot nyújt a hitelesítés második formájának megkövetelésével, és erős hitelesítést biztosít a könnyen használható hitelesítési módszerek széles választékával. Előfordulhat, hogy a felhasználók at rendszergazdaként meghozhatja az MFA-ban, de előfordulhat, hogy nem támadják meg az MFA-t.

Tekintse meg, hogyan engedélyezheti az MFA-t a felhasználói folyamatokban a [Többtényezős hitelesítés engedélyezése az Azure Active Directory B2C-ben című témakörben.](custom-policy-multi-factor-authentication.md)

### <a name="smart-account-lockout"></a>Intelligens fiókzárolás

A találgatásos jelszó-találgatások megelőzése érdekében az Azure AD B2C kifinomult stratégiát használ a fiókok zárolására a kérelem IP-címe, a megadott jelszavak és számos más tényező alapján. A zárolás időtartama automatikusan növekszik a kockázat és a kísérletek száma alapján.

![Fiók intelligens zárolása](media/technical-overview/smart-lockout1.png)

A jelszavas védelmi beállítások kezeléséről az [Erőforrásokkal és adatokkal kapcsolatos fenyegetések kezelése az Azure Active Directory B2C-ben című témakörben](threat-management.md)olvashat bővebben.

### <a name="password-complexity"></a>Jelszó bonyolultsága

A regisztráció vagy a jelszó alaphelyzetbe állítása során a felhasználóknak olyan jelszót kell megadniuk, amely megfelel az összetettségi szabályoknak. Alapértelmezés szerint az Azure AD B2C erős jelszóházirendet kényszerít ki. Az Azure AD B2C konfigurációs beállításokat is biztosít az ügyfelek által használt jelszavak összetettségi követelményeinek megadásához.

A jelszó összetettségi követelményeit mind a [felhasználói folyamatokban,](user-flow-password-complexity.md) mind az [egyéni házirendekben](custom-policy-password-complexity.md)konfigurálhatja.

## <a name="auditing-and-logs"></a>Naplózás és naplók

Az Azure AD B2C naplót bocsát ki, amely az erőforrásokkal, a kiadott jogkivonatokkal és a rendszergazdai hozzáféréssel kapcsolatos tevékenységinformációkat tartalmazza. Ezekkel a naplóksegítségével megismerheti a platform tevékenységét, és diagnosztizálhatja a problémákat. A naplóbejegyzések nem sokkal az eseményt létrehozó tevékenység bekövetkezése után érhetők el.

Az Azure AD B2C-bérlő vagy egy adott felhasználó számára elérhető naplóban többek között a következőket találhatja meg:

* A felhasználó nak a B2C-erőforrásokhoz való hozzáférésengedélyezésére vonatkozó tevékenységek (például a B2C-házirendek listájához hozzáférő rendszergazda)
* Az Azure Portal használata korbekért címtárattribútumokkal kapcsolatos tevékenységek
* Műveletek létrehozása, olvasása, frissítése és törlése B2C-alkalmazásokon
* CRUD műveletek a B2C kulcstárolóban tárolt kulcsokon
* CRUD műveletek A B2C-erőforrások (például a politikák és az identitásszolgáltatók)
* A felhasználói hitelesítő adatok és a tokenek kiállításának érvényesítése

![Az Azure Portalon megjelenő egyéni felhasználói napló](media/technical-overview/audit-log.png)

A naplóktovábbi részletekért olvassa el [az Azure AD B2C naplózási naplóinak elérése.](view-audit-logs.md)

### <a name="usage-insights"></a>Használati elemzések

Az Azure AD B2C segítségével felfedezheti, hogy az emberek mikor regisztrálnak vagy jelentkeznek be a webalkalmazásba, hol találhatók a felhasználók, és milyen böngészőket és operációs rendszereket használnak. Az Azure Application Insights integrálásával az Azure AD B2C egyéni szabályzatok használatával betekintést nyerhet abba, hogy az emberek hogyan regisztrálnak, jelentkezzenbe, hogyan alaphelyzetbe állítják a jelszavukat, vagy szerkeszthetik a profiljukat. Ilyen tudással adatvezérelt döntéseket hozhat a következő fejlesztési ciklusaihoz.

További információ a használati elemzésekről az [Azure Active Directory B2C felhasználói viselkedésének nyomon követése az Application Insights használatával](analytics-with-application-insights.md)című dokumentumban.

## <a name="next-steps"></a>További lépések

Most, hogy mélyebben áttekintheti az Azure Active Directory B2C funkcióit és technikai szempontjait, a B2C-bérlő létrehozásával ismerkedhet meg a szolgáltatással:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzon létre egy Azure Active Directory B2C->](tutorial-create-tenant.md)