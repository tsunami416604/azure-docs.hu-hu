---
title: Hibakód-hivatkozás
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C szolgáltatás által visszaadott hibakódok listája.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188664"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Hibakódok: Azure Active Directory B2C

A Azure Active Directory B2C szolgáltatás a következő hibákat adhatja vissza.

| Hibakód | Üzenet |
| ---------- | ------- |
| `AADB2C90002` | A (z){0}"" CORS-erőforrás 404-as értéket adott vissza. |
| `AADB2C90006` | A kérelemben megadott{0}átirányítási URI ("") nincs regisztrálva a (z{1}) "" ügyfél-azonosítóhoz. |
| `AADB2C90007` | A ({0}z) "" ügyfél-azonosítóhoz társított alkalmazás nem rendelkezik regisztrált átirányítási URI azonosítóval. |
| `AADB2C90008` | A kérelem nem tartalmaz ügyfél-azonosító paramétert. |
| `AADB2C90010` | A kérelem nem tartalmaz hatókör-paramétert. |
| `AADB2C90011` | A kérelemben megadott{0}"" ügyfél-azonosító nem egyezik a házirendben regisztrált{1}"" ügyfél-azonosítóval. |
| `AADB2C90012` | A kérelemben{0}megadott hatókör nem támogatott. |
| `AADB2C90013` | A kérelemben megadott "{0}" válasz típusa nem támogatott. |
| `AADB2C90014` | A kérelemben megadott,{0}kért válasz mód nem támogatott. |
| `AADB2C90016` | A kért "" ügyfél-{0}érvényesítési típus nem egyezik a várt "{1}" típussal. |
| `AADB2C90017` | A kérelemben megadott ügyfél-kijelentés érvénytelen:{0} |
| `AADB2C90018` | A kérelemben megadott{0}ügyfél-azonosító nincs regisztrálva a (z) bérlőben{1}. |
| `AADB2C90019` | A (z) "{0}" azonosítójú kulcstároló a ({1}z) "" bérlőn nem rendelkezik érvényes kulccsal. Ok: {2}. |
| `AADB2C90021` | A (z){0}"" technikai profil nem létezik a ({1}z) ""{2}bérlő "" szabályzatában. |
| `AADB2C90022` | Nem lehet visszaadni a (z){0}"" házirend metaadatait a (z) bérlőben{1}. |
| `AADB2C90023` | A ({0}z) "" profil nem tartalmazza a szükséges{1}metaadat-kulcsot (""). |
| `AADB2C90025` | {0}A (z) ""{1}alkalmazás "" szabályzatában{2}található "" profil nem tartalmazza a szükséges titkosítási kulcsot ({3}""). |
| `AADB2C90027` | A (z) "{0}" számára megadott alapszintű hitelesítő adatok érvénytelenek. Győződjön meg arról, hogy a hitelesítő adatok helyesek, és hogy az erőforrás hozzáférést kapott. |
| `AADB2C90028` | A (z) "{0}" számára megadott ügyféltanúsítvány érvénytelen. Győződjön meg arról, hogy a tanúsítvány helyes, tartalmaz egy titkos kulcsot, és azt, hogy az erőforrás hozzáférést kapott. |
| `AADB2C90031` | A ({0}z) "" szabályzat nem ad meg alapértelmezett felhasználói utat. Győződjön meg arról, hogy a házirend vagy a szülője alapértelmezett felhasználói utat ad meg egy függő entitás szakaszának részeként. |
| `AADB2C90035` | A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra néhány perc múlva. |
| `AADB2C90036` | A kérelem nem tartalmaz olyan URI-t, amely átirányítja a felhasználót a kijelentkezés közzétételéhez. A post_logout_redirect_uri paraméter mezőben írjon be egy URI-t. |
| `AADB2C90037` | Hiba történt a kérelem feldolgozása közben. Forduljon a rendszergazdához, és keresse meg az elérni kívánt webhelyet. |
| `AADB2C90039` | A kérelem tartalmaz egy ügyfél-kijelentést, de a{0}(z) "{1}" bérlőben megadott "" házirendből hiányzik egy client_secret a ügyféltényt. |
| `AADB2C90040` | A (z{0}) "" felhasználói út nem tartalmaz küldési jogcím lépést. |
| `AADB2C90043` | A kérelemben szereplő kérés érvénytelen értékeket tartalmaz. Várt érték: "None", "bejelentkezhessen", "beleegyezett" vagy "select_account". |
| `AADB2C90044` | A (z{0}) "" jogcímet a ({1}z) "" jogcím-feloldó nem támogatja. |
| `AADB2C90046` | Nem sikerül betölteni a jelenlegi állapotát. Előfordulhat, hogy az elejéről szeretné elindítani a munkamenetet. |
| `AADB2C90047` | A (z{0}) "" erőforrás parancsfájl-hibákat tartalmaz, ami megakadályozza a betöltését. |
| `AADB2C90048` | Kezeletlen kivétel történt a kiszolgálón. |
| `AADB2C90051` | Nem található megfelelő jogcím-szolgáltató. |
| `AADB2C90052` | Érvénytelen felhasználónév vagy jelszó. |
| `AADB2C90053` | Nem található a megadott hitelesítő adatokkal rendelkező felhasználó. |
| `AADB2C90054` | Érvénytelen felhasználónév vagy jelszó. |
| `AADB2C90055` | A kérelemben{0}megadott hatókörnek meg kell adnia egy erőforrást, például ahttps://example.com/calendar.readkövetkezőt: "". |
| `AADB2C90057` | A megadott alkalmazás nincs konfigurálva, hogy engedélyezze a OAuth implicit folyamatát. |
| `AADB2C90058` | A megadott alkalmazás nincs konfigurálva a nyilvános ügyfelek engedélyezésére. |
| `AADB2C90067` | A kilépés utáni átirányítási URI{0}("") formátuma érvénytelen. Adjon meg egy https-alapú URL-https://example.com/returncímet, például a következőt: "", vagy a natív ügyfelekhez használja az IETF natív ügyfél URI-ját "urn: IETF: WG: OAuth: 2.0: OOB". |
| `AADB2C90068` | A megadott "{0}" azonosítójú alkalmazás nem érvényes a szolgáltatásra. Használjon a B2C-portálon keresztül létrehozott alkalmazást, és próbálkozzon újra. |
| `AADB2C90075` | A (z){0}"{1}" lépésben megadott "" jogcím-csere http-hibát adott{2}vissza a (z{3}) "" kóddal és a következő okból: "". |
| `AADB2C90077` | A felhasználó nem rendelkezik meglévő munkamenettel, és a kérés prompt paraméterének értéke "{0}". |
| `AADB2C90079` | A bizalmas támogatás beváltásakor az ügyfeleknek client_secret kell küldeniük. |
| `AADB2C90080` | A megadott engedély lejárt. Ellenőrizze újra a hitelesítést, és próbálkozzon újra. Aktuális idő: {0}, a kiállított idő {1}:, a lejárati idő {2}megadása:. |
| `AADB2C90081` | A megadott client_secret nem felel meg az ügyfél várt értékének. Javítsa ki a client_secret, és próbálkozzon újra. |
| `AADB2C90083` | A kérelemből hiányzik a kötelező paraméter {0}:. |
| `AADB2C90084` | A nyilvános ügyfelek nem küldhetnek client_secret a nyilvánosan beszerzett engedélyek beváltásakor. |
| `AADB2C90085` | A szolgáltatás belső hibát észlelt. Ellenőrizze újra a hitelesítést, és próbálkozzon újra. |
| `AADB2C90086` | A megadott grant_type [{0}] nem támogatott. |
| `AADB2C90087` | A megadott engedély nem lett kiadva a protokoll végpontjának jelen verziójához. |
| `AADB2C90088` | A megadott engedély nem lett kiadva ehhez a végponthoz. Tényleges érték: {0} és várt érték:{1} |
| `AADB2C90092` | A (z) "{0}" azonosítójú megadott alkalmazás le van tiltva a{1}(z) "" bérlőnél. Engedélyezze az alkalmazást, és próbálkozzon újra. |
| `AADB2C90107` | A (z) "{0}" azonosítójú alkalmazás nem kaphat azonosító jogkivonatot, mert az OpenID hatóköre nem lett megadva a kérelemben, vagy az alkalmazás nincs engedélyezve. |
| `AADB2C90108` | A (z) "{0}" előkészítési lépés nem határoz meg CpimIssuerTechnicalProfileReferenceId, ha az egyik várt érték. |
| `AADB2C90110` | A hatókör-paraméternek "OpenID"-nek kell lennie, ha olyan response_type kér, amely tartalmazza a következőt: "id_token". |
| `AADB2C90111` | A fiók zárolva van. A zárolás feloldásához forduljon a támogatási szolgálathoz, majd próbálkozzon újra. |
| `AADB2C90114` | A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később. |
| `AADB2C90115` | A "code" response_type kérésekor a hatókör-paraméternek tartalmaznia kell egy erőforrást vagy egy ügyfél-azonosítót a hozzáférési jogkivonatokhoz, és az "OpenID" azonosítót az azonosító tokenekhez. Emellett a frissítési tokenek esetében a "offline_access" is szerepel. |
| `AADB2C90117` | A kérelemben{0}megadott hatókör nem támogatott. |
| `AADB2C90118` | A felhasználó elfelejtette a jelszavát. |
| `AADB2C90120` | A kérelemben megadott Max{0}Age paraméter érvénytelen. A maximális életkor értéke csak "{1}" és "{2}" közötti egész szám lehet. |
| `AADB2C90122` | A kérelemben{0}kapott "" bemenethez nem sikerült a HTTP-kérelem érvényesítése. Győződjön meg arról, hogy a bemenet nem tartalmaz olyan karaktereket, mint a < vagy a &. |
| `AADB2C90128` | Az ehhez a támogatáshoz társított fiók már nem létezik. Ellenőrizze újra a hitelesítést, és próbálkozzon újra. |
| `AADB2C90129` | A megadott támogatás vissza lett vonva. Ellenőrizze újra a hitelesítést, és próbálkozzon újra. |
| `AADB2C90145` | Nem található nem ellenőrzött telefonszám, és a szabályzat nem engedélyezi a felhasználó által megadott számot. |
| `AADB2C90146` | A kérelemben{0}megadott hatókör egynél több erőforrást határoz meg egy hozzáférési jogkivonathoz, amely nem támogatott. |
| `AADB2C90149` | Nem sikerült{0}betölteni a (z) "" szkriptet. |
| `AADB2C90151` | A felhasználó túllépte a többtényezős hitelesítésre vonatkozó újrapróbálkozások maximális számát. |
| `AADB2C90152` | A többtényezős lekérdezési kérés nem kapott választ a szolgáltatástól. |
| `AADB2C90154` | A multi-Factor ellenőrzési kérelem nem tudta lekérni a munkamenet-azonosítót a szolgáltatástól. |
| `AADB2C90155` | A multi-Factor ellenőrzési kérelem végrehajtása a következő okból meghiúsult: "{0}". |
| `AADB2C90156` | Egy multi-Factor Validation kérelem meghiúsult a következő{0}okból: "". |
| `AADB2C90157` | A felhasználó túllépte az újrapróbálkozások maximális számát egy önérvényesített lépéshez. |
| `AADB2C90158` | Egy önérvényesített érvényesítési kérelem meghiúsult a következő okból{0}: "". |
| `AADB2C90159` | Egy önérvényesített ellenőrzési kérelem meghiúsult a következő okból:{0}"". |
| `AADB2C90161` | Egy önérvényesített küldési válasz meghiúsult a következő okból{0}: "". |
| `AADB2C90165` | A ({0}z) azonosítójú SAML-kezdeményező üzenet nem található az állapotban. |
| `AADB2C90168` | A HTTP-átirányítási kérelem nem tartalmazza a (z){0}"" kötelező paramétert egy aláírt kérelemhez. |
| `AADB2C90178` | A (z){0}"" aláíró tanúsítványnak nincs titkos kulcsa. |
| `AADB2C90182` | A megadott code_verifier nem egyezik a társított code_challenge |
| `AADB2C90183` | A megadott code_verifier érvénytelen |
| `AADB2C90184` | A megadott code_challenge_method nem támogatott. A támogatott értékek egyszerűek vagy S256 |
| `AADB2C90188` | A ({0}z) "" SAML technikai profil a (z{1}) "" PartnerEntity URL-címét határozza meg, de{2}a metaadatok beolvasása a következő okból meghiúsul: "". |
| `AADB2C90194` | A tulajdonosi jogkivonathoz megadott "{0}" jogcím nem szerepel az elérhető jogcímek között. Rendelkezésre álló jogcímek{1}. |
| `AADB2C90205` | Ez az alkalmazás nem rendelkezik megfelelő engedélyekkel a webes erőforráshoz a művelet elvégzéséhez. |
| `AADB2C90206` | Időtúllépés történt az ügyfél inicializálásakor. |
| `AADB2C90208` | A megadott id_token_hint paraméter lejárt. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90209` | A megadott id_token_hint paraméter nem tartalmaz elfogadott célközönséget. Érvényes célközönség-értékek:{0}"". Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90210` | A megadott id_token_hint paraméter nem érvényesíthető. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90211` | A kérelemben hiányos állapotú cookie szerepel. |
| `AADB2C90212` | A kérelem érvénytelen állapotú cookie-t foglalt. |
| `AADB2C90220` | A (z) ""{0}tárolási azonosítóval{1}rendelkező "" bérlő kulcstárolója létezik, de nem tartalmaz érvényes tanúsítványt. Lehet, hogy a tanúsítvány lejárt, vagy előfordulhat, hogy a tanúsítvány később aktív lesz (NBF). |
| `AADB2C90223` | Hiba történt a CORS-erőforrás fertőtlenítése során. |
| `AADB2C90224` | Az erőforrás-tulajdonosi folyamat nincs engedélyezve az alkalmazáshoz. |
| `AADB2C90225` | A kérelemben megadott Felhasználónév vagy jelszó érvénytelen. |
| `AADB2C90226` | A megadott jogkivonat-Exchange csak HTTP-POSTon támogatott. |
| `AADB2C90232` | A megadott id_token_hint paraméter nem tartalmaz elfogadott kiállítót. Érvényes kibocsátók: "{0}". Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90233` | A megadott id_token_hint paraméter aláírás-ellenőrzése sikertelen. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90235` | A megadott id_token lejárt. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90237` | A megadott id_token nem tartalmaz érvényes célközönséget. Érvényes célközönség-értékek:{0}"". Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90238` | A megadott id_token nem tartalmaz érvényes kiállítót. Érvényes kiállítói értékek: "{0}". Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90239` | A megadott id_token sikertelen aláírás-ellenőrzés. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90240` | A megadott id_token formátuma helytelen, és nem elemezhető. Adjon meg egy másik tokent, és próbálkozzon újra. |
| `AADB2C90242` | Az SAML technikai profilja{0}("") megadja a PartnerEntity CDATA-t,{1}amely nem tölthető be a következő okból: "". |
| `AADB2C90243` | A IDENTITÁSSZOLGÁLTATÓ/titkos kulcs nem megfelelően van konfigurálva. |
| `AADB2C90244` | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |
| `AADB2C90248` | Az erőforrás-tulajdonosi folyamat csak a B2C felügyeleti portálon keresztül létrehozott alkalmazások által használható. |
| `AADB2C90250` | Az általános bejelentkezési végpont nem támogatott. |
| `AADB2C90255` | A (z) "{0}" technikai profilban megadott jogcím-Exchange nem a várt módon fejeződött be. Előfordulhat, hogy az elejéről szeretné elindítani a munkamenetet. |
| `AADB2C90261` | A (z){0}"{1}" lépésben megadott "" jogcím-csere olyan HTTP-hibaüzenetet adott vissza, amely nem elemezhető. |
| `AADB2C90272` | A kérelemben nincs megadva a id_token_hint paraméter. Adja meg a tokent, és próbálkozzon újra. |
| `AADB2C90273` | Érvénytelen válasz érkezett: "{0}" |
| `AADB2C90274` | A szolgáltatói metaadatok nem határoznak meg egyszeri kijelentkezési szolgáltatást, vagy a végpont kötése nem az "urn: Oasis: Names: TC: SAML: 2.0: kötések: HTTP-átirányítás" vagy az "urn: Oasis: Names: TC: SAML: 2.0: kötések: HTTP-POST". |
| `AADB2C90276` | A kérelem nem konzisztens a (z) ""{0}bérlő "{4}"{1}kivonatjogcím "{2}{3}" vezérlő beállításával ("": ""). |
| `AADB2C90277` | A (z) "{0}" szabályzat{2}"" felhasználói{1}útjának "" előkészítési lépése nem tartalmaz Content definition-referenciát. |
| `AADB2C90279` | A megadott ügyfél-azonosító{0}("") nem egyezik meg a jogosultságot kiállító ügyfél-azonosítóval. |
| `AADB2C90284` | A (z) "{0}" azonosítójú alkalmazás nem kapott beleegyezett, és nem használható helyi fiókokhoz. |
| `AADB2C90285` | A ({0}z) azonosítójú alkalmazás nem található. |
| `AADB2C90288` | A (z){0}"" kivonatjogcím "{1}" azonosítójú UserJourney a (z) ""{2}bérlő frissítési jogkivonat-beváltásához{3}nem létezik a (z) "" házirendben vagy annak egyik alapházirendjében sem. |
| `AADB2C90289` | Hiba történt az identitás-szolgáltatóhoz való csatlakozás során. Próbálkozzon újra később. |
| `AADB2C90296` | Az alkalmazás konfigurációja nem megfelelő. Forduljon a rendszergazdához, és keresse meg az elérni kívánt webhelyet. |
| `AADB2C99005` | A kérelem érvénytelen hatókör-paramétert tartalmaz, amely tartalmaz egy "{0}" szabálytalan karaktert. |
| `AADB2C99006` | A Azure AD B2C nem találja a bővítmények alkalmazást a következő{0}azonosítójú alkalmazással: "". További információért https://go.microsoft.com/fwlink/?linkid=851224 látogasson el ide. |
| `AADB2C99011` | A (z){0}"" metaadat-érték nincs megadva a{1}(z) "{2}" házirendben a (z) "" kivonatjogcím. |
| `AADB2C99013` | A megadott grant_type [{0}] és a token_type{1}[] kombináció nem támogatott. |
| `AADB2C99015` | A (z) ""{1}a (z){2}"" alkalmazásban a (z) "" szabályzatban található "" profilból hiányzik az erőforrás-tulajdonosi jelszó{0} |
