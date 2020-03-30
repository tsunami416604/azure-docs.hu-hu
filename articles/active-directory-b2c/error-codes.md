---
title: Hibakód hivatkozása
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C szolgáltatás által visszaadott hibakódok listája.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188664"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Hibakódok: Azure Active Directory B2C

Az Azure Active Directory B2C szolgáltatás a következő hibákat adja vissza.

| Hibakód | Üzenet |
| ---------- | ------- |
| `AADB2C90002` | A CORS-erőforrás '{0}' egy 404-es értéket adott vissza. |
| `AADB2C90006` | A kérelemben{0}megadott átirányítási URI ' ' nincs regisztrálva az ügyfélazonosítóhoz '{1}'. |
| `AADB2C90007` | Az ügyfélazonosítóhoz '{0}' ' társított alkalmazásnak nincs regisztrált átirányítási URI-ja. |
| `AADB2C90008` | A kérelem nem tartalmaz ügyfélazonosító paramétert. |
| `AADB2C90010` | A kérelem nem tartalmaz hatókörparamétert. |
| `AADB2C90011` | A kérelemben{0}megadott ügyfélazonosító ' ' nem felel{1}meg a házirendben regisztrált ügyfélazonosítónak' ' . |
| `AADB2C90012` | A kérelemben megadott '{0}' hatókör nem támogatott. |
| `AADB2C90013` | A kérésben{0}megadott " " kért választípus nem támogatott. |
| `AADB2C90014` | A kérésben{0}megadott " ( ' kért válaszmód nem támogatott. |
| `AADB2C90016` | A kért ügyfélhelyesség-típus '{0}'{1}nem felel meg a várt típusnak ' '. |
| `AADB2C90017` | A kérelemben megadott ügyfél-kijelentés érvénytelen:{0} |
| `AADB2C90018` | A kérelemben{0}megadott ügyfélazonosító ' ' azonosító nincs{1}regisztrálva a bérlőben ' '. |
| `AADB2C90019` | A bérlői{1}azonosítóval{0}" rendelkező kulcstároló nem rendelkezik érvényes kulccsal. Indok: {2}. |
| `AADB2C90021` | A "{0}" technikai profil nem{1}létezik a{2}bérlő " házirendjében. |
| `AADB2C90022` | Nem lehet visszaadni a házirend{0}metaadatait{1}' ' a bérlőben ' . |
| `AADB2C90023` | A{0}Profile ' ' " profil nem{1}tartalmazza a szükséges metaadatkulcsot " ' . |
| `AADB2C90025` | A{0}" profil{1}" '{2}a bérlőben ' ' nem{3}tartalmazza a szükséges kriptográfiai kulcsot " ' . |
| `AADB2C90027` | A '{0}'-hoz megadott alapvető hitelesítő adatok érvénytelenek. Ellenőrizze, hogy a hitelesítő adatok helyesek-e, és hogy az erőforrás megadta-e a hozzáférést. |
| `AADB2C90028` | Az '{0}' -hoz megadott ügyféltanúsítvány érvénytelen. Ellenőrizze, hogy a tanúsítvány helyes-e, tartalmaz-e személyes kulcsot, és hogy az erőforrás megadta-e a hozzáférést. |
| `AADB2C90031` | A{0}házirend " ' nem ad meg alapértelmezett felhasználói utat. Győződjön meg arról, hogy a házirend vagy a szülők alapértelmezett felhasználói utat adnak meg egy függő entitás szakasz részeként. |
| `AADB2C90035` | A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra néhány perc múlva. |
| `AADB2C90036` | A kérelem nem tartalmaz URI-t a felhasználó nak a kijelentkezés utáni átirányításához. Adjon meg egy URI-t a post_logout_redirect_uri paramétermezőben. |
| `AADB2C90037` | Hiba történt a kérelem feldolgozása közben. Lépjen kapcsolatba az elérni kívánt webhely rendszergazdájával. |
| `AADB2C90039` | A kérelem ügyfélállítást tartalmaz, de{0}a megadott{1}" " házirend " a bérlőben " hiányzik egy client_secret a RelyingPartyPolicy.The request contains a client assertion, but the provided policy ' ' in tenant ' ' (A bérlőben ' ' egy client_secret a RelyingPartyPolicy.The request contains a client assertion, but the provided policy ' ' in tenant ' ' ' (A reklamáció " |
| `AADB2C90040` | A felhasználói{0}út ' ' nem tartalmaz küldési jogcímlépést. |
| `AADB2C90043` | A kérésben szereplő kérdés érvénytelen értékeket tartalmaz. A várt "nincs", "bejelentkezés", "hozzájárulás" vagy "select_account". |
| `AADB2C90044` | A "{0}" jogcímet a jogcímfeloldó "{1}nem támogatja. |
| `AADB2C90046` | Nem tudjuk betölteni a jelenlegi állapotát. Érdemes lehet a munkamenetelejétől kezdve kezdeni. |
| `AADB2C90047` | Az "{0}" erőforrás parancsfájlhibákat tartalmaz, amelyek megakadályozzák a betöltését. |
| `AADB2C90048` | Nem kezelt kivétel történt a kiszolgálón. |
| `AADB2C90051` | Nem található megfelelő kárrendezési szolgáltató. |
| `AADB2C90052` | Érvénytelen felhasználónév vagy jelszó. |
| `AADB2C90053` | A megadott hitelesítő adatokkal rendelkező felhasználó nem található. |
| `AADB2C90054` | Érvénytelen felhasználónév vagy jelszó. |
| `AADB2C90055` | A kérelemben megadott '{0}hatókörnek meg kellhttps://example.com/calendar.readadnia egy erőforrást, például " ". |
| `AADB2C90057` | A megadott alkalmazás nincs konfigurálva az OAuth implicit folyamat engedélyezéséhez. |
| `AADB2C90058` | A megadott alkalmazás nincs nyilvános ügyfelek engedélyezésére konfigurálva. |
| `AADB2C90067` | A kijelentkezés utáni{0}átirányítási URI ' ' formátuma érvénytelen. Adjon meg egy httpshttps://example.com/returnalapú URL-címet, például ' ' vagy natív ügyfelek számára az IETF natív ügyfélURI "urn:ietf:wg:oauth:2.0:oob" azonosítóját. |
| `AADB2C90068` | A megadott alkalmazás azonosítóval '{0}' nem érvényes ezzel a szolgáltatással szemben. Használjon a B2C portálon keresztül létrehozott alkalmazást, majd próbálkozzon újra. |
| `AADB2C90075` | A jogcímek{0}cseréje ' '{1}' ' a "{2}lépésben{3}megadott ' ' visszaadott HTTP-hibaválasz kód ' és ok ' " szavakkal. |
| `AADB2C90077` | A felhasználó nak nincs meglévő munkamenete, és{0}a kéréssor paraméter értéke ' '. |
| `AADB2C90079` | Az ügyfeleknek client_secret kell küldeniük egy bizalmas támogatás beváltásakor. |
| `AADB2C90080` | A megadott támogatás lejárt. Ellenőrizze újra a hitelesítést, majd próbálkozzon újra. Aktuális idő: {0}, Kiadott {1}támogatás ideje: , {2}Csúszó ablak lejárati ideje: . |
| `AADB2C90081` | A megadott client_secret nem egyezik meg az ügyfél várt értékével. Javítsa ki a client_secret, majd próbálkozzon újra. |
| `AADB2C90083` | A kérelemből hiányzik {0}a szükséges paraméter: . . |
| `AADB2C90084` | A nyilvánosan megszerzett támogatás beváltásakor a nyilvánosan megszerzett támogatás beváltásakor a nyilvános anamvalók nem küldhetnek client_secret. |
| `AADB2C90085` | A szolgáltatás belső hibát észlelt. Hitelesítse magát újra, majd próbálkozzon újra. |
| `AADB2C90086` | A mellékelt{0}grant_type [ ] nem támogatott. |
| `AADB2C90087` | A megadott támogatás nem lett kiadva a protokollvégpont ezen verziójához. |
| `AADB2C90088` | A megadott támogatást erre a végpontra nem bocsátották ki. Tényleges érték {0} : és Várható érték:{1} |
| `AADB2C90092` | A megadott alkalmazás azonosítóval "{0}' '{1}le van tiltva a bérlő " számára. Engedélyezze az alkalmazást, majd próbálkozzon újra. |
| `AADB2C90107` | Az azonosítóval rendelkező{0}alkalmazás " ' nem kaphat azonosítótokat, mert az openid hatókör nem lett megadva a kérelemben, vagy az alkalmazás nem jogosult rá. |
| `AADB2C90108` | A vezénylési lépés '{0}' nem ad meg CpimIssuerTechnicalProfileReferenceId-ot, amikor egy várt. |
| `AADB2C90110` | A hatókör paraméternek tartalmaznia kell az "openid"-et, amikor "id_token" tartalmazó response_type kér. |
| `AADB2C90111` | A fiókját zárolták. Lépjen kapcsolatba a támogatási személlyel a zárolás feloldásához, majd próbálkozzon újra. |
| `AADB2C90114` | Fiókját ideiglenesen zároljuk a jogosulatlan használat megakadályozása érdekében. Próbálkozzon újra később. |
| `AADB2C90115` | A "kód" response_type kérésekor a hatókör paraméternek tartalmaznia kell egy erőforrás- vagy ügyfélazonosítót a hozzáférési jogkivonatokhoz, és "openid" azonosítójogkivonatokat. Emellett a "offline_access" a frissítési jogkivonatok. |
| `AADB2C90117` | A kérelemben megadott '{0}hatókör nem támogatott. |
| `AADB2C90118` | A felhasználó elfelejtette a jelszavát. |
| `AADB2C90120` | A kérelemben{0}megadott ' maximális korparaméter érvénytelen. A maximális életkornak egész számnak kell lennie a '{1}és az '{2}' ' közé. |
| `AADB2C90122` | A kérésben kapott '{0}' ' bemenete nem felelt meg a HTTP-kérelmek érvényesítésén. Győződjön meg arról, hogy a bemenet nem tartalmaz karaktereket, például < vagy &. |
| `AADB2C90128` | A támogatáshoz társított fiók már nem létezik. Hitelesítse magát újra, majd próbálkozzon újra. |
| `AADB2C90129` | A megadott támogatást visszavonták. Hitelesítse magát újra, majd próbálkozzon újra. |
| `AADB2C90145` | Nem található nem ellenőrzött telefonszám, és a házirend nem engedélyezi a felhasználó által megadott számot. |
| `AADB2C90146` | A "{0}hatókör ' a kérelemben megadott egynél több erőforrást határoz meg egy hozzáférési jogkivonathoz, amely nem támogatott. |
| `AADB2C90149` | A{0}script ' ' betöltése nem sikerült. |
| `AADB2C90151` | A felhasználó túllépte a többtényezős hitelesítés újrapróbálkozásai maximális számát. |
| `AADB2C90152` | A többtényezős lekérdezési kérelem nem kapott választ a szolgáltatástól. |
| `AADB2C90154` | Egy többtényezős ellenőrzési kérelem nem tudott munkamenet-azonosítót lekérni a szolgáltatástól. |
| `AADB2C90155` | A többtényezős ellenőrzési kérelem nem{0}sikerült az oka " ". |
| `AADB2C90156` | Egy többtényezős érvényesítési kérelem{0}oka ' okból sikertelen volt. |
| `AADB2C90157` | A felhasználó túllépte az újrapróbálkozások maximális számát egy önérvényesítő lépéshez. |
| `AADB2C90158` | Egy önérvényesítő érvényesítési kérelem oknélkül "{0}nem sikerült. |
| `AADB2C90159` | Egy önérvényesítő ellenőrzési kérelem ok nélkül{0}elbukott " ". |
| `AADB2C90161` | Az önérvényesítő küldési válasz oka '{0}" miatt sikertelen volt. |
| `AADB2C90165` | Az id '{0}' azonosítóval megküldő SAML-üzenet nem található állapotban. |
| `AADB2C90168` | A HTTP-átirányítási kérelem nem tartalmazza{0}az aláírt kérelemhez szükséges ' paramétert. |
| `AADB2C90178` | Az aláíró{0}tanúsítványhoz " nincs titkos kulcsa. |
| `AADB2C90182` | A megadott code_verifier nem egyezik a társított code_challenge |
| `AADB2C90183` | A megadott code_verifier érvénytelen |
| `AADB2C90184` | A mellékelt code_challenge_method nem támogatott. A támogatott értékek egyszerűek vagy S256 |
| `AADB2C90188` | Az SAML technikai{0}profilja ' ' '{1}a PartnerEntity ' URL-címét adja{2}meg, de a metaadatok beolvasása okokkal ' okokkal sikertelen. |
| `AADB2C90194` | A{0}tulajdonosi jogkivonathoz megadott jogcím ' ' nem szerepel a rendelkezésre álló jogcímekben. Rendelkezésre{1}álló követelések " ". |
| `AADB2C90205` | Ez az alkalmazás nem rendelkezik a művelet végrehajtásához szükséges engedélyekkel a webes erőforrással szemben. |
| `AADB2C90206` | Időhosszabbítás történt az ügyfél inicializálása korinicializálása korinicializálása kornem. |
| `AADB2C90208` | A megadott id_token_hint paraméter lejárt. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90209` | A megadott id_token_hint paraméter nem tartalmaz elfogadott közönséget. Érvényes közönségértékek:{0}' ' . Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90210` | A megadott id_token_hint paraméter nem érvényesíthető. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90211` | A kérelem hiányos állapotcookie-t tartalmazott. |
| `AADB2C90212` | A kérelem érvénytelen állapotcookie-t tartalmazott. |
| `AADB2C90220` | A "storage identifier{0}{1}" bérlőkulcs-tárolója létezik, de nem tartalmaz érvényes tanúsítványt. Lehet, hogy a tanúsítvány lejárt, vagy a tanúsítvány a jövőben aktívvá válhat (nbf). |
| `AADB2C90223` | Hiba történt a CORS-erőforrás fertőtlenítése miatt. |
| `AADB2C90224` | Az erőforrás-tulajdonosi folyamat nincs engedélyezve az alkalmazáshoz. |
| `AADB2C90225` | A kérelemben megadott felhasználónév vagy jelszó érvénytelen. |
| `AADB2C90226` | A megadott jogkivonat-csere csak HTTP POST-on keresztül támogatott. |
| `AADB2C90232` | A megadott id_token_hint paraméter nem tartalmaz elfogadott kibocsátót. Érvényes kibocsátók:{0}' ' . Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90233` | A megadott id_token_hint paraméter nem sikerült az aláírás érvényesítése. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90235` | A megadott id_token lejárt. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90237` | A megadott id_token nem tartalmaz érvényes közönséget. Érvényes közönségértékek:{0}' ' . Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90238` | A megadott id_token nem tartalmaz érvényes kibocsátót. Érvényes kibocsátói értékek: '{0}' . Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90239` | A megadott id_token nem sikerült az aláírás ellenőrzése. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90240` | A megadott id_token hibás, és nem lehet elemezni. Adjon meg egy másik jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90242` | Az SAML technikai{0}profilja ' ' adja meg a PartnerEntity CDATA-t, amely ok miatt nem tölthető be '{1}' . |
| `AADB2C90243` | Az IDP ügyfélkulcsa/titkos kulcsa nincs megfelelően konfigurálva. |
| `AADB2C90244` | Túl sok kérés van ebben a pillanatban. Várjon egy ideig, majd próbálkozzon újra. |
| `AADB2C90248` | Az erőforrás-tulajdonosi folyamatot csak a B2C felügyeleti portálon keresztül létrehozott alkalmazások használhatják. |
| `AADB2C90250` | Az általános bejelentkezési végpont nem támogatott. |
| `AADB2C90255` | A technikai profilban{0}" ' megadott jogcímcsere nem a várt módon fejeződött be. Érdemes lehet a munkamenetelejétől kezdve kezdeni. |
| `AADB2C90261` | A jogcímcsere '{0}'{1}' lépésben megadott ' ' visszaadott HTTP-hibaválaszt adott vissza, amelyet nem lehetett elemezni. |
| `AADB2C90272` | A id_token_hint paraméter nincs megadva a kérelemben. Adja meg a jogkivonatot, majd próbálkozzon újra. |
| `AADB2C90273` | Érvénytelen válasz érkezett: '{0} |
| `AADB2C90274` | A szolgáltató metaadatai nem adnak meg egyetlen kijelentkezési szolgáltatást, vagy a végpontkötés nem az "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" vagy az 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | A kérelem nem felel meg{0}a "{1}" "{2}" '{3}' '{4}' ' ' ' ' ' " " " vezérlőbeállítással a házirendhez ' ' bérlő ' . |
| `AADB2C90277` | A házirend "{0}" "{1}vezénylési lépése nem{2}tartalmaz tartalomdefiníciós hivatkozást. |
| `AADB2C90279` | A megadott ügyfélazonosító{0}" ' nem egyezik meg a támogatást kibocsátó ügyfélazonosítóval. |
| `AADB2C90284` | A '{0}" azonosítóval rendelkező alkalmazás nem kapott beleegyezést, és nem használható helyi fiókokhoz. |
| `AADB2C90285` | Az " azonosítóval rendelkező alkalmazás nem{0}található. |
| `AADB2C90288` | UserJourney azonosítóval{0}' ' ' hivatkozott{1}TechnicalProfile ' '{2}a bérlői token{3}beváltásának " " frissítéséhez nem létezik a házirendben ' ' vagy annak bármely alapházirendjében. |
| `AADB2C90289` | Hibát észleltünk az identitásszolgáltatóhoz való csatlakozáskor. Próbálkozzon újra később. |
| `AADB2C90296` | Az alkalmazás nincs megfelelően konfigurálva. Lépjen kapcsolatba az elérni kívánt webhely rendszergazdájával. |
| `AADB2C99005` | A kérelem érvénytelen hatókörparamétert tartalmaz, amely{0}egy érvénytelen karaktert " tartalmaz. |
| `AADB2C99006` | Az Azure AD B2C nem találja a{0}bővítmények alkalmazást alkalmazásazonosítóval " ". Kérjük, látogasson el https://go.microsoft.com/fwlink/?linkid=851224 további információkért. |
| `AADB2C99011` | A '{0}' metaadat-érték nincs megadva{1}a TechnicalProfile ' ' -ban a házirendben "{2}' . |
| `AADB2C99013` | A megadott{0}grant_type [{1}] és token_type [ ] kombináció nem támogatott. |
| `AADB2C99015` | A{0}" profil{1}" '{2}a bérlőben ' ' , hiányzik az erőforrás-tulajdonos jelszóhitelesítő folyamatához szükséges összes inputjogcím. |
