---
title: Az Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok – Azure Key Vault
description: Az Azure Key Vault REST-felületének áttekintése és a kulcsok, titkos kulcsok és tanúsítványok fejlesztői adatai.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271732"
---
# <a name="about-keys-secrets-and-certificates"></a>A kulcsok, titkos kódok és tanúsítványok ismertetése

Az Azure Key Vault lehetővé teszi a Microsoft Azure-alkalmazások és felhasználók számára, hogy többféle titkos/kulcsadatot tároljanak és használjanak:

- Kriptográfiai kulcsok: Több kulcstípust és algoritmust támogat, és lehetővé teszi a hardveres biztonsági modulok (HSM) használatát a nagy értékű kulcsokhoz. 
- Titkos kulcsok: Biztonságos tárolást biztosít a titkos kulcsok, például a jelszavak és az adatbázis-kapcsolat ihúrjai.
- Tanúsítványok: Támogatja a kulcsokra és titkos kulcsokra épülő tanúsítványokat, és automatikus megújítási szolgáltatást ad hozzá.
- Azure Storage: Az Azure Storage-fiók kulcsait kezelheti. Belsőleg a Key Vault listázhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeres időközönként újragenerálhatja (elforgatja) a kulcsokat. 

A Key Vaultról további általános információt a [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

A következő szakaszok általános információkat nyújtanak a Key Vault szolgáltatás megvalósításában.

### <a name="supporting-standards"></a>Támogató szabványok

A JavaScript objektumjelölés (JSON) és a JavaScript-objektumaláíró és -titkosítási (JOSE) specifikációk fontos háttérinformációk.  

-   [JSON webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON webes titkosítás (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON webes algoritmusok (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Adattípusok

A kulcsok, a titkosítás és az aláírás megfelelő adattípusait a JOSE specifikációiban olvassa el.  

-   **algoritmus** - egy támogatott algoritmus egy kulcsművelethez, például RSA1_5  
-   **rejtjelszöveg-érték** - titkosítási szöveg oktett, Base64URL használatával kódolva  
-   **emészthető érték** - a kimenetegy kivonatoló algoritmus, kódolt segítségével Base64URL  
-   **kulcs-típus** - az egyik támogatott kulcstípus, például az RSA (Rivest-Shamir-Adleman).  
-   **egyszerű szöveg-érték** - egyszerű szöveges oktett, Base64URL használatával kódolva  
-   **aláírás-érték** - egy aláírási algoritmus kimenete, Base64URL használatával kódolva  
-   **base64URL** - base64URL [RFC4648] kódolt bináris érték  
-   **logikai -** igaz vagy hamis  
-   **Identitás** – az Azure Active Directory (AAD) identitása.  
-   **IntDate** - JSON decimális érték, amely az 1970-01-01T0:0:0Z UTC és a megadott UTC dátum/idő között eltelt másodpercek számát jelöli. Lásd az RFC3339-et a dátumra/időpontokra vonatkozó részletekért, általában és különösen az UTC-t illetően.  

### <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámmal rendelkeznek, amikor egy objektum új példányát hozják létre. Minden verzió hoz egy egyedi azonosítót és URL-címet. Amikor egy objektumot először hoznak létre, egyedi verzióazonosítót kap, és az objektum aktuális verziójaként van megjelölve. Az azonos objektumnévvel rendelkező új példány létrehozása egyedi verzióazonosítót ad az új objektumnak, így az lesz az aktuális verzió.  

A Key Vault objektumai az aktuális azonosító vagy egy verzióspecifikus azonosító használatával címezhetők. Ha például egy kulcsot `MasterKey`ad meg a nevével, az aktuális azonosítóval végzett műveletek végrehajtása a rendszer a legújabb elérhető verziót használja. Ha a verzióspecifikus azonosítóval hajt végre műveleteket, a rendszer az objektum adott verzióját használja.  

Az objektumok egyedileg azonosíthatók a Key Vaultban egy URL-cím használatával. A rendszerben nincs két objektum azonos URL-címe, függetlenül a földrajzi helytől. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely azonosítja a Key Vaultot, az objektumtípust, a felhasználó által megadott objektumnevet és az objektumverziót. Az objektumneve nem i. és nem módosítható. Az objektumverziót nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatás egyik kulcstartójának neve.<br /><br /> A Key Vault-neveket a felhasználó választja ki, és globálisan egyediek.<br /><br /> A Key Vault nevének 3-24 karakterből álló karakterláncnak kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-type`|Az objektum típusa, "kulcsok" vagy "titkos kulcsok".|  
|`object-name`|Az `object-name` egy felhasználó által megadott nevet, és egyedinek kell lennie a Key Vault.An is a user provided name for and must be unique within a Key Vault. A névnek 1-127 karakterből kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-version`|A `object-version` rendszer által létrehozott, 32 karakteres karakterlánc-azonosító, amely opcionálisan egy objektum egyedi verziójának címzésére szolgál.|  

## <a name="key-vault-keys"></a>Key Vault-kulcsok

### <a name="keys-and-key-types"></a>Kulcsok és kulcstípusok

A Key Vault ban lévő kriptográfiai kulcsok JSON webkulcs [JWK] objektumokként jelennek meg. Az alap JWK/JWA specifikációk is ki vannak bővítve, hogy a Key Vault implementációjában egyedi kulcstípusok at is lehetővé tegyék. Például a kulcsok importálása HSM szállító-specifikus csomagoláshasználatával lehetővé teszi a kulcsok biztonságos szállítását, amely csak a Key Vault HSM-ekben használható.  

- **"Puha" kulcsok:** A Key Vault által szoftverben feldolgozott kulcs, de nyugalmi állapotban egy HSM-ben lévő rendszerkulccsal van titkosítva. Az ügyfelek importálhatnak egy meglévő RSA- vagy EK-(Elliptikus ív) kulcsot, vagy kérhetik, hogy a Key Vault hozzon létre egyet.
- **"Kemény" billentyűk:** HSM-ben (hardverbiztonsági modul) feldolgozott kulcs. Ezek a kulcsok a Key Vault HSM biztonsági világok egyikében védettek (földrajzi helyenként egy biztonsági világ van az elkülönítés fenntartásához). Az ügyfelek importálhatnak RSA- vagy EK-kulcsot, ideiglenes formában vagy kompatibilis HSM-eszközről exportálva. Az ügyfelek is kérhetik a Key Vault egy kulcs létrehozásához. Ez a kulcstípus hozzáadja a key_hsm attribútumot a JWK-beszerzéshez a HSM kulcsanyag hordozásához.

     A földrajzi határokról a [Microsoft Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/privacy/) talál további információt.  

A Key Vault csak az RSA és az Elliptikus görbe billentyűket támogatja. 

-   **EC**: "Lágy" elliptikus görbe gomb.
-   **EC-HSM**: "Kemény" elliptikus görbe gomb.
-   **RSA**: "Soft" RSA kulcs.
-   **RSA-HSM**: "Kemény" RSA kulcs.

A Key Vault támogatja a 2048, 3072 és 4096 méretű RSA-kulcsokat. A Key Vault támogatja a P-256, P-384, P-521 és P-256K (SECP256K1) elliptikus görbe kulcstípusokat.

### <a name="cryptographic-protection"></a>Kriptográfiai védelem

A Key Vault által használt kriptográfiai modulok, legyen ek HSM vagy szoftver, FIPS (Federal Information Processing Standards) ellenőrzöttek. FiPS módban való futtatáshoz nem kell semmi különlegeset tennie. A HSM-védelemmel ellátottként **létrehozott** vagy **importált** kulcsok feldolgozása egy HSM-en belül történik, fips 140-2 2 szintű 2-re érvényesítve. A szoftvervédelemmel **ellátottként létrehozott** vagy **importált** kulcsokat a FIPS 140-2 1. További információt a [Kulcsok és kulcstípusok](#keys-and-key-types)című témakörben talál.

###  <a name="ec-algorithms"></a>EK-algoritmusok
 A következő algoritmusazonosítók a Key Vault EK- és EC-HSM-kulcsaival támogatottak. 

#### <a name="curve-types"></a>Ívtípusok

-   **P-256** - A NIST görbe P-256, meghatározott [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - A SEC256K1 SECcurve curve curve , amelyet a [SEC 2: Ajánlott elliptikus görbe tartományparaméterei határoznak](https://www.secg.org/sec2-v2.pdf)meg.
-   **P-384** - A NIST görbe P-384, meghatározott [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A NIST görbe P-521, meghatározott [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **ES256** - ECDSA az SHA-256 emésztéshez és a P-256 görbével létrehozott billentyűkhez. Ezt az algoritmust az [RFC7518](https://tools.ietf.org/html/rfc7518)ismerteti.
-   **ES256K** - ECDSA Az SHA-256 digests és gombok létre görbe P-256K. Ez az algoritmus szabványosítás a függőben van.
-   **ES384** - ECDSA az SHA-384 emésztéshez és a P-384 görbével létrehozott billentyűkhez. Ezt az algoritmust az [RFC7518](https://tools.ietf.org/html/rfc7518)ismerteti.
-   **ES512** - ECDSA az SHA-512 emésztéshez és a P-521 görbével létrehozott billentyűkhez. Ezt az algoritmust az [RFC7518](https://tools.ietf.org/html/rfc7518)ismerteti.


###  <a name="rsa-algorithms"></a>RSA algoritmusok  
 A következő algoritmusazonosítók a Key Vault RSA- és RSA-HSM-kulcsaival támogatottak.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁS/VISSZAFEJTÉS

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] kulcstitkosítás  
-   **RSA-OAEP** - RSAES optimal aszimmetrikus titkosítási kitöltéssel (OAEP) [RFC3447], az A.2.1 szakaszban az RFC 3447 által megadott alapértelmezett paraméterekkel. Ezek az alapértelmezett paraméterek az SHA-1 kivonatoló funkcióját és az MGF1 SGF-1 maszkgenerálási funkcióját használják.  

#### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **PS256** - RSASSA-PSS SHA-256 és MGF1 SHA-256 használatával, az [RFC7518-ban](https://tools.ietf.org/html/rfc7518)leírtak szerint.
-   **PS384** - RSASSA-PSS SHA-384 és MGF1 SHA-384 használatával, az [RFC7518-ban](https://tools.ietf.org/html/rfc7518)leírtak szerint.
-   **PS512** - RSASSA-PSS SHA-512 és MGF1 SHA-512 használatával, az [RFC7518-ban](https://tools.ietf.org/html/rfc7518)leírtak szerint.
-   **RS256** - RSASSA-PKCS-v1_5 SHA-256 használatával. Az alkalmazás által megadott kivonatoló értéket Az SHA-256 használatával kell kiszámítani, és 32 bájt hosszúnak kell lennie.  
-   **RS384** - RSASSA-PKCS-v1_5 SHA-384 használatával. Az alkalmazás által megadott kivonatolási értéket Az SHA-384 használatával kell kiszámítani, és 48 bájt hosszúnak kell lennie.  
-   **RS512** - RSASSA-PKCS-v1_5 SHA-512 használatával. Az alkalmazás által megadott kivonatoló értéket Az SHA-512 használatával kell kiszámítani, és 64 bájt hosszúnak kell lennie.  
-   **RSNULL** – Lásd a [RFC2437] speciális használati eset, amely bizonyos TLS-forgatókönyveket tesz lehetővé.  

###  <a name="key-operations"></a>Kulcsfontosságú műveletek

A Key Vault a következő műveleteket támogatja a kulcsobjektumokon:  

-   **Létrehozás**: Lehetővé teszi, hogy egy ügyfél hozzon létre egy kulcsot a Key Vaultban. A kulcs értékét a Key Vault hozza létre, és tárolja, és nem adja ki az ügyfélnek. Aszimmetrikus kulcsok hozhatók létre a Key Vaultban.  
-   **Importálás:** Lehetővé teszi, hogy egy ügyfél importáljon egy meglévő kulcsot a Key Vaultba. Az aszimmetrikus kulcsok importálhatók a Key Vaultba a JWK-konstrukción belül számos különböző csomagolási módszerrel. 
-   **Frissítés**: Lehetővé teszi, hogy a key vaultban korábban tárolt kulcshoz társított metaadatok (kulcsattribútumok) módosításához megfelelő ügyfél módosítsa a szükséges engedélyeket.  
-   **Törlés**: Lehetővé teszi, hogy a megfelelő engedélyekkel rendelkező ügyfél törölje a kulcsot a Key Vaultból.  
-   **Lista**: Lehetővé teszi, hogy egy ügyfél egy adott key vaultban felsorolja az összes kulcsot.  
-   **Listaverziók:** Lehetővé teszi, hogy egy ügyfél egy adott kulcs összes verzióját felsorolja egy adott Key Vaultban.  
-   **Lekérés:** Lehetővé teszi, hogy az ügyfél lekérje egy adott kulcs nyilvános részeit a Key Vaultban.  
-   **Biztonsági mentés**: Kulcs exportálása védett formában.  
-   **Visszaállítás**: Korábban biztonsági másolatot deszka importálása.  

További információ: [Key operations in the Key Vault REST API reference.](/rest/api/keyvault)  

Miután létrehozott egy kulcsot a Key Vaultban, a következő titkosítási műveletek hajthatók végre a kulccsal:  

-   **Aláírás és ellenőrzés:** Szigorúan ez a művelet "kivonatoló" vagy "ellenőrizze a kivonatot", mivel a Key Vault nem támogatja a tartalom kivonatolását az aláírás létrehozása részeként. Az alkalmazásoknak meg kell kivonatolniuk a helyileg aláírandó adatokat, majd kérniük kell, hogy a Key Vault írja alá a kivonatot. Az aláírt kijelentések ellenőrzése a [nyilvános] kulcsanyagokhoz esetleg nem hozzáférő alkalmazások kényelmi műveleteként támogatott. A legjobb alkalmazásteljesítmény érdekében ellenőrizze, hogy a műveletek helyileg történnek-e.  
-   **Kulcstitkosítás / tördelés:** A Key Vaultban tárolt kulcs egy másik kulcs, általában egy szimmetrikus tartalomtitkosítási kulcs (CEK) védelmére használható. Ha a key vault aszimmetrikus kulcs, kulcstitkosítást használ. Az RSA-OAEP és a WRAPKEY/UNWRAPKEY műveletek például egyenértékűek az ENCRYPT/DECRYPT művelettel. Ha a Key Vault kulcsa szimmetrikus, a rendszer kulcstördelést használ. Például: AES-KW. A WRAPKEY művelet olyan alkalmazások kényelmeként támogatott, amelyek nem férnek hozzá a [nyilvános] kulcsanyagokhoz. A legjobb alkalmazásteljesítmény érdekében a WRAPKEY műveleteket helyileg kell végrehajtani.  
-   **Titkosítás és visszafejtés:** A Key Vaultban tárolt kulcs egyetlen adatblokk titkosítására vagy visszafejtésére használható. A blokk méretét a kulcs típusa és a kiválasztott titkosítási algoritmus határozza meg. A titkosítási művelet a kényelem érdekében érhető el olyan alkalmazások számára, amelyek nem férnek hozzá a [nyilvános] kulcsanyagokhoz. A legjobb alkalmazásteljesítmény érdekében a titkosítási műveleteket helyileg kell végrehajtani.  

Bár az aszimmetrikus billentyűkkel használt WRAPKEY/UNWRAPKEY feleslegesnek tűnhet (mivel a művelet egyenértékű az ENCRYPT/DECRYPT művelettel), fontos a különböző műveletek használata. A megkülönböztetés biztosítja a műveletek szemantikai és engedélyezési elkülönítését, valamint a konzisztenciát, ha a szolgáltatás más kulcstípusokat is támogat.  

A Key Vault nem támogatja az EXPORT műveleteket. Ha egy kulcs ki van építve a rendszerben, nem lehet kinyerni, vagy a kulcsanyag módosítása. A Key Vault felhasználói azonban más használati esetekhez is megkövetelhetik a kulcsukat, például a törlés után. Ebben az esetben a BIZTONSÁGI MENTÉS és visszaállítás műveletek et használhatják a kulcs védett formában történő exportálásához/importálásához. A BACKUP művelet által létrehozott kulcsok nem használhatók a Key Vaulton kívül. Másik lehetőségként az IMPORT művelet több Key Vault-példány ellen is használható.  

A felhasználók korlátozhatják a Key Vault által a JWK objektum key_ops tulajdonságának használatával a kulcsonként támogatott titkosítási műveletekbármelyikét.  

A JWK-objektumokról a [JSON webkulcsban (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)talál további információt.  

###  <a name="key-attributes"></a>Kulcsjellemzők

A fő elemek mellett a következő attribútumok is megadhatók. JSON-kérelemben a kulcsszó és a kapcsos zárójelek ({' '}) attribútumokra akkor is szükség van, ha nincsenek megadva attribútumok.  

- *engedélyezve*: logikai, nem kötelező, az alapértelmezett **érték igaz**. Itt adható meg, hogy a kulcs engedélyezve van-e, és használható-e kriptográfiai műveletekhez. Az *engedélyezett* attribútum ot az *nbf* és exp együttesen *használjuk.* Ha az *nbf* és *exp*közötti művelet csak akkor engedélyezett, ha true **értékre**van *beállítva.* Az *nbf* / *exp* ablakon kívüli műveletek automatikusan nem engedélyezettek, kivéve bizonyos művelettípusokat bizonyos [feltételek mellett.](#date-time-controlled-operations)
- *nbf*: IntDate, nem kötelező, az alapértelmezett most. Az *nbf* (nem korábban) attribútum azonosítja azt az időt, amely előtt a kulcsot nem szabad kriptográfiai műveletekhez használni, kivéve bizonyos művelettípusokat [bizonyos feltételek mellett.](#date-time-controlled-operations) Az *nbf* attribútum feldolgozása megköveteli, hogy az aktuális dátum/idő az *nbf* attribútumban felsorolt nem-before dátum/idő után legyen. Key Vault nyújthat néhány kis mozgásteret, általában nem több, mint néhány perc, hogy figyelembe óra döntés. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  
- *exp*: IntDate, nem kötelező, az alapértelmezett "örökre". Az *exp* (lejárati idő) attribútum azonosítja a lejárati időt, vagy amely után a kulcsot nem szabad használni a kriptográfiai művelet, kivéve bizonyos művelettípusok [bizonyos feltételek mellett](#date-time-controlled-operations). Az *exp* attribútum feldolgozása megköveteli, hogy az aktuális dátum/idő az *exp* attribútumban felsorolt lejárati dátum/idő előtt legyen. Key Vault nyújthat néhány kis mozgásteret, általában nem több, mint néhány perc, figyelembe óra döntés. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  

Vannak további írásvédett attribútumok, amelyek a legfontosabb attribútumokat tartalmazó válaszokban szerepelnek:  

- *létrehozva*: IntDate, nem kötelező. A *létrehozott* attribútum jelzi, hogy a kulcs ezen verziója mikor jött létre. Az érték null értékű az attribútum hozzáadása előtt létrehozott kulcsok esetében. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  
- *frissítve*: IntDate, nem kötelező. A *frissített* attribútum azt jelzi, hogy a kulcs ezen verziója mikor lett frissítve. Az érték null értékű azattribútum hozzáadása előtt utoljára frissített kulcsok esetében. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  

Az IntDate-ről és más adattípusokról az [Adattípusok című](#data-types) témakörben talál további információt.  

#### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A még nem érvényes és lejárt kulcsok az *nbf* / *exp* ablakon kívül a **visszafejtéshez,** **a kicsomagoláshoz**és a műveletek **ellenőrzéséhez** (nem ad vissza 403, Tiltott). A még nem érvényes állapot használatának oka, hogy lehetővé teszi egy kulcs tesztelése az éles használat előtt. A lejárt állapot használatának oka az, hogy engedélyezi a helyreállítási műveleteket a kulcs érvényessége esetén létrehozott adatokon. Emellett letilthatja a kulcshoz való hozzáférést a Key Vault-házirendek használatával, vagy az *engedélyezett* kulcsattribútum **hamis**ra frissítésével.

Az adattípusokról az [Adattípusok](#data-types)című témakörben talál további információt.

A többi lehetséges attribútumról a [JSON webkulcsban (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)talál további információt.

### <a name="key-tags"></a>Kulcscímkék

További alkalmazásspecifikus metaadatokat adhat meg címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakternévvel és 256 karakterértékkel rendelkezhet.  

>[!Note]
>A címkéket a hívó akkor tudja olvasni, ha rendelkezik a *listával,* vagy engedélyt *kap* az adott objektumtípusra (kulcsokra, titkos kulcsokra vagy tanúsítványokra).

###  <a name="key-access-control"></a>Kulcshozzáférés-vezérlés

A Key Vault által kezelt kulcsok hozzáférés-vezérlése a kulcsok tárolójaként szolgáló Key Vault szintjén érhető el. A kulcsok hozzáférés-vezérlési házirendje különbözik az ugyanabban a Key Vaultban lévő titkos kulcsok hozzáférés-vezérlési házirendjétől. A felhasználók létrehozhatnak egy vagy több tárolót a kulcsok tárolására, és a forgatókönyv megfelelő szegmentálásának és a kulcsok kezelésének fenntartásához szükségesek. A kulcsok hozzáférés-vezérlése független a titkos kulcsok hozzáférés-vezérlésátótól.  

A következő engedélyeket lehet adni, felhasználónként / egyszerű szolgáltatás alapon, a kulcsok hozzáférés-vezérlési bejegyzés egy tárolóban. Ezek az engedélyek szorosan tükrözik a kulcsobjektumon engedélyezett műveleteket.  A key vaultban egy egyszerű szolgáltatáshoz való hozzáférés egyszeri művelet, és ugyanaz marad az összes Azure-előfizetések. Segítségével annyi tanúsítványt helyezhet üzembe, amennyit csak szeretne. 

- A kulcskezelési műveletek engedélyei
  - *get*: Olvassa el a nyilvános része a kulcs, valamint annak attribútumait
  - *lista*: A kulcstartóban tárolt kulcsok vagy verziók felsorolása
  - *update*: Kulcs attribútumainak frissítése
  - *létrehozás*: Új kulcsok létrehozása
  - *importálás:* Kulcs importálása key vaultba
  - *törlés*: A kulcsobjektum törlése
  - *helyreállítás*: Törölt kulcs helyreállítása
  - *biztonsági mentés*: Kulcs biztonsági mentése a kulcstartóban
  - *visszaállítás*: Biztonsági másolatot tett kulcs visszaállítása kulcstartóba

- Kriptográfiai műveletek engedélyei
  - *visszafejtés:* A kulcs segítségével oldja meg a bájtsorozatok
  - *titkosítás:* A kulcs segítségével védje a bájtok tetszőleges sorozatát
  - *kicsomagolásKey:* Használja a gombot, hogy unprotect csomagolt szimmetrikus kulcsok
  - *wrapKey*: Használja a gombot, hogy megvédje a szimmetrikus kulcs
  - *ellenőrzés*: Használja a gombot az emésztések  
  - *jel*: A billentyű vel írja alá digests
    
- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Törölt kulcs törlése (végleges törlése)

A kulcsok használata című témakörben talál további információt [a Key Vault REST API-hivatkozáskulcs-műveleteicímű témakörben.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy) 

## <a name="key-vault-secrets"></a>Key Vault titkai 

### <a name="working-with-secrets"></a>Titkok kalkulálása

A fejlesztő szemszögéből a Key Vault API-k titkos értékeket fogadnak el és adnak vissza karakterláncként. Belsőleg a Key Vault oktettsorozatként (8 bites bájtok) tárolja és kezeli a titkos kulcsokat, egyenként legfeljebb 25 k bájt méretű. A Key Vault szolgáltatás nem biztosít szemantikát a titkos kulcsokhoz. Csupán elfogadja az adatokat, titkosítja, tárolja, és egy titkos azonosítót ("id") ad vissza. Az azonosító segítségével később is beolvasható a titok.  

A szigorúan bizalmas adatokhoz az ügyfeleknek ajánlott további adatvédelmi rétegeket is használni. Ez lehet például az adatok külön védelmi kulccsal történő titkosítása a Key Vaultba helyezés előtt.  

Key Vault is támogatja a contentType mező titkos kulcsok. Az ügyfelek megadhatják egy titkos titok tartalomtípusát, amely segítséget nyújt a titkos adatok értelmezéséhez a beolvasáskor. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésére vonatkozó tipp. Például egy implementáció titkos jelszóként és tanúsítványként is tárolhatja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetéshez. Nincsenek előre definiált értékek.  

### <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatokon kívül a következő attribútumok is megadhatók:  

- *exp*: IntDate, nem kötelező, az alapértelmezett **örökre**. Az *exp* (lejárati idő) attribútum azonosítja a lejárati időt, vagy amely után a titkos adatokat NEM szabad letölteni, kivéve az [adott helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű, mivel tájékoztatja a felhasználókat a key vault szolgáltatás, hogy egy adott titkos kulcs nem használható. Értéke intdate értéket tartalmazó szám kell, hogy legyen.   
- *nbf*: IntDate, nem kötelező, az alapértelmezett **most**. Az *nbf* (nem korábban) attribútum azonosítja azt az időt, amely előtt a titkos adatokat NEM szabad beolvasni, kivéve [bizonyos eseteket](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű. Értéke intdate értéket tartalmazó szám kell, hogy legyen. 
- *engedélyezve*: logikai, nem kötelező, az alapértelmezett **érték igaz**. Ez az attribútum határozza meg, hogy a titkos adatok beolvashatók-e. Az engedélyezett attribútum az *nbf* és *exp* együttesen használatos, ha az *nbf* és *exp*közötti művelet csak akkor engedélyezett, ha az engedélyezve van **true**értékre. Az *nbf* és *exp* ablakon kívüli műveletek automatikusan nem engedélyezettek, kivéve [bizonyos eseteket.](#date-time-controlled-operations)  

Vannak további írásvédett attribútumok, amelyek szerepelnek minden válasz, amely tartalmazza a titkos attribútumok:  

- *létrehozva*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy a titkos titok ezen verziója mikor jött létre. Ez az érték null az attribútum hozzáadása előtt létrehozott titkos kulcsok esetében. Az értéknek IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy a titkos titok ezen verziója mikor lett frissítve. Ez az érték null az attribútum hozzáadása előtt utoljára frissített titkos kulcsok esetében. Az értéknek IntDate értéket tartalmazó számnak kell lennie.

#### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A titkos **get** művelet fog működni a még nem érvényes és lejárt titkok, kívül *nbf* / *exp* ablak. Egy titkos **lehívási** művelet hívása, egy még nem érvényes titok esetén, használható tesztelési célokra. Beolvasása **(get**ting) egy lejárt titkos, helyreállítási műveletekhez használható.

Az adattípusokról az [Adattípusok](#data-types)című témakörben talál további információt.  

### <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

Hozzáférés-vezérlés a Key Vaultban kezelt titkos kulcsok, a kulcstartó szintjén, amely tartalmazza ezeket a titkos kulcsokat. A titkos kulcsok hozzáférés-vezérlési házirendje különbözik az ugyanabban a Key Vaultban lévő kulcsok hozzáférés-vezérlési házirendjétől. A felhasználók létrehozhatnak egy vagy több tárolót a titkos kulcsok tárolására, és a forgatókönyv megfelelő szegmentálása és a titkos kulcsok kezelése karbantartásához szükségesek.   

A következő engedélyek használhatók, egy fő alapon, a titkos kulcshozzáférés-vezérlési bejegyzés egy tárolóban, és szorosan tükrözi a műveletek engedélyezett egy titkos objektum:  

- Titkos kezelési műveletek engedélyei
  - *get*: Olvassa el a titkos  
  - *lista*: A Key Vaultban tárolt titkos fájlok titkos kulcsainak vagy verzióinak felsorolása  
  - *set*: Titkos kapcsolat létrehozása  
  - *törlés*: Titkos fájl törlése  
  - *helyreállítás*: Törölt titkos titok helyreállítása
  - *biztonsági mentés*: Titkos biztonsági másolat a kulcstárolóban
  - *visszaállítás*: Biztonsági másolatot kell adni egy titkos kulcstárolóba

- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Törölt titok törlése (végleges törlése)

A titkos kulcsok ról további információt [a Key Vault REST API-hivatkozástitkos műveletei című témakörben talál.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy) 

### <a name="secret-tags"></a>Titkos címkék  
További alkalmazásspecifikus metaadatokat adhat meg címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakternévvel és 256 karakterértékkel rendelkezhet.  

>[!Note]
>A címkéket a hívó akkor tudja olvasni, ha rendelkezik a *listával,* vagy engedélyt *kap* az adott objektumtípusra (kulcsokra, titkos kulcsokra vagy tanúsítványokra).

## <a name="key-vault-certificates"></a>Key Vault-tanúsítványok

A Key Vault-tanúsítványok támogatása biztosítja az x509-es tanúsítványok kezelését és a következő viselkedéseket:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa hozzon létre egy tanúsítványt egy Key Vault létrehozási folyamat vagy egy meglévő tanúsítvány importálása révén. Tartalmazza mind az önaláírt, mind a hitelesítésszolgáltató által létrehozott tanúsítványokat.
-   Lehetővé teszi, hogy a Key Vault-tanúsítvány tulajdonosa biztonságos anamtikát valósítson meg és az X509-tanúsítványokat a személyes kulcsanyagokkal való interakció nélkül valósítsa meg.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa hozzon létre egy szabályzatot, amely arra utasítja a Key Vault a tanúsítvány életciklusának kezelésére.  
-   Lehetővé teszi a tanúsítványtulajdonosok számára, hogy kapcsolattartási adatokat adjanak meg a tanúsítvány lejáratának és megújításának életciklus-eseményeiről szóló értesítéshez.  
-   Támogatja az automatikus megújítást a kiválasztott kibocsátókkal - Key Vault partner X509 tanúsítványszolgáltatók / hitelesítésszolgáltatók.

>[!Note]
>A nem partnerszolgáltatók/hatóságok is engedélyezettek, de nem támogatják az automatikus megújítási funkciót.

### <a name="composition-of-a-certificate"></a>A bizonyítvány összetétele

Key Vault-tanúsítvány létrehozásakor egy címezhető kulcs és titkos kulcs is létrejön az azonos nevű. A Key Vault kulcs lehetővé teszi a kulcsműveleteket, és a Key Vault titkos kulcs lehetővé teszi a tanúsítvány értékének titkos lekérését. A Key Vault-tanúsítvány nyilvános x509-es tanúsítvány metaadatait is tartalmazza.  

A tanúsítványok azonosítója és verziója hasonló a kulcsok és titkos kulcsok azonosítójához. A Key Vault tanúsítványverziójával létrehozott címezhető kulcs és titkos kulcs egy adott verziója érhető el a Key Vault tanúsítványválaszában.
 
![A tanúsítványok összetett objektumok](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportálható vagy nem exportálható kulcs

Key Vault-tanúsítvány létrehozásakor a címezhető titkos kulcsból lehívható, pfx- vagy PEM formátumú titkos kulcsesetén. A tanúsítvány létrehozásához használt házirendnek jeleznie kell, hogy a kulcs exportálható. Ha a házirend azt jelzi, hogy nem exportálható, akkor a titkos kulcs nem része az értéknek, ha titkos kulcsként olvassa be.  

A címezhető kulcs relevánsabbá válik a nem exportálható KV-tanúsítványok nál. A címezhető KV-kulcs műveletei a KV-tanúsítvány létrehozásához használt KV tanúsítványházirend *kulcshasználati* mezőjéből vannak leképezve.  

Kétféle kulcs támogatott : *RSA* vagy *RSA HSM* tanúsítványokkal. Az RSA csak rsa-val használható, az RSA HSM nem támogatja.  

### <a name="certificate-attributes-and-tags"></a>Tanúsítvány attribútumai és címkék

A tanúsítvány metaadatai, egy címezhető kulcs és címezhető titkos kulcs mellett a Key Vault-tanúsítvány attribútumokat és címkéket is tartalmaz.  

#### <a name="attributes"></a>Attribútumok

A tanúsítványattribútumok a címezhető kulcs és titkos kulcs attribútumainak a KV-tanúsítvány létrehozásakor létrehozott attribútumaira lesznek tükrözve.  

A Key Vault-tanúsítvány attribútumai a következő attribútumokkal rendelkeznek:  

-   *engedélyezve*: logikai, nem kötelező, az alapértelmezett **érték igaz**. Megadható annak jelzésére, hogy a tanúsítvány adatok titkosként vagy kulcsként működtethetők-e. Az *nbf* és *exp* együttesen is használható, ha az *nbf* és *exp*közötti művelet történik, és csak akkor engedélyezett, ha az engedélyezve van true értékre. Az *nbf* és *exp* ablakon kívüli műveletek automatikusan nem engedélyezettek.  

A válaszban további írásvédett attribútumok is szerepelnek:

-   *létrehozva*: IntDate: azt jelzi, hogy a tanúsítvány ezen verziója mikor jött létre.  
-   *frissítve*: IntDate: azt jelzi, hogy a tanúsítvány ezen verziója mikor lett frissítve.  
-   *exp*: IntDate: az x509 tanúsítvány lejárati dátumának értékét tartalmazza.  
-   *nbf*: IntDate: az x509 tanúsítvány dátumának értékét tartalmazza.  

> [!Note] 
> Ha egy Key Vault-tanúsítvány lejár, a címezhető kulcs és titkos titkos működésképtelenné válik.  

#### <a name="tags"></a>Címkék

 A kulcsértékpárok ügyféláltal megadott szótára, hasonlóan a kulcsokban és titkos kulcsokban lévő címkékhez.  

 > [!Note]
> A címkéket a hívó akkor tudja olvasni, ha rendelkezik a *listával,* vagy engedélyt *kap* az adott objektumtípusra (kulcsokra, titkos kulcsokra vagy tanúsítványokra).

### <a name="certificate-policy"></a>Tanúsítványházirend

A tanúsítványházirend a Key Vault-tanúsítványok életciklusának létrehozásáról és kezeléséről tartalmaz információkat. Amikor egy titkos kulccsal rendelkező tanúsítványt importál a key vaultba, az x509-es tanúsítvány olvasásával egy alapértelmezett házirend jön létre.  

Ha egy Key Vault-tanúsítvány teljesen új, házirendet kell megadni. A házirend meghatározza, hogyan lehet létrehozni ezt a Key Vault-tanúsítvány verzió, vagy a következő Key Vault-tanúsítvány verziója. A házirend létrehozása után nem szükséges a jövőbeli verziók egymást követő létrehozási műveleteihez. A Key Vault-tanúsítványok összes verziójához csak egy példány a házirend egy példánya van.  

Magas szinten a tanúsítványházirend a következő információkat tartalmazza:  

-   X509 tanúsítvány tulajdonságai: Az x509-es tanúsítványkérelem létrehozásához használt tulajdonosnevet, tulajdonosi alternatív neveket és egyéb tulajdonságokat tartalmaz.  
-   Kulcstulajdonságai: kulcstípust, kulcshosszt, exportálható és újrafelhasználható kulcsmezőket tartalmaz. Ezek a mezők utasítják a kulcstartót a kulcs létrehozásának módjára.  
-   Titkos tulajdonságok: titkos tulajdonságokat tartalmaz, például a címezhető titkos kulcsot tartalmazó tartalomtípust a titkos érték létrehozásához, a tanúsítvány titkos könyvtárként történő beolvasásához.  
-   Élettartam-műveletek: a KV-tanúsítvány élettartamra szóló műveleteket tartalmaz. Minden élettartamra szóló művelet a következőket tartalmazza:  

     - Trigger: a lejárat előtti napokon vagy élettartam-százalékos arányban adható meg  

     - Művelet: művelettípus megadása – *e-mailKapcsolatok* vagy *automatikus megújítás*  

-   Kiállító: Az x509-es tanúsítványok kiállításához használandó tanúsítványkibocsátó paraméterei.  
-   Házirend-attribútumok: a házirendhez társított attribútumokat tartalmaz  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 és a Key Vault használati leképezése

Az alábbi táblázat az x509-es kulcshasználati házirend leképezését mutatja be a Key Vault-tanúsítványok létrehozásának részeként létrehozott kulcs hatékony kulcsműveleteihez.

|**X509 kulcshasználat jelzők**|**Key Vault kulcsműveletek**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|DataEncipherment (Adattitkosítás)|titkosítás, visszafejtés| N/A |
|Csak a megfejtés|Visszafejteni| N/A  |
|DigitalSignature (Digitális aláírás)|jel, ellenőrzés| A Key Vault alapértelmezett használati specifikáció nélkül a tanúsítvány létrehozásakor | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign (KulcscertSign)|jel, ellenőrzés|N/A|
|KeyEncipherment (Kulcsfejtés)|wrapKey, kicsomagolásKulcs| A Key Vault alapértelmezett használati specifikáció nélkül a tanúsítvány létrehozásakor | 
|Nem repudiation|jel, ellenőrzés| N/A |
|crlsign (jel)|jel, ellenőrzés| N/A |

### <a name="certificate-issuer"></a>Tanúsítvány kiállítója

A Key Vault tanúsítványobjektum rendelkezik egy konfigurációval, amely a kijelölt tanúsítványkiállítóval az x509-es tanúsítványok rendeléséhez használt konfigurációt tartalmaz.  

-   A Key Vault a következő tanúsítványkiállító-szolgáltatókkal foglalkozik a TLS/SSL-tanúsítványokhoz

|**Szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|A nyilvános felhőben és az Azure Government ben található összes kulcstartó-szolgáltatásban támogatott|
|Globalsign|A nyilvános felhőben és az Azure Government ben található összes kulcstartó-szolgáltatásban támogatott|

Mielőtt egy tanúsítvány kiállítója létre jöhet ne a Key Vaultban, az 1.  

1. Befelé a hitelesítésszolgáltató (CA) szolgáltatói  

    -   A szervezet rendszergazdájának a vállalatot (pl. Contoso) legalább egy hitelesítésszolgáltatóval.  

2. A rendszergazda meghozza a kérelemelő hitelesítő adatait a Key Vault számára a TLS/SSL-tanúsítványok igényléséhez (és megújításához)  

    -   A kulcstartóban lévő szolgáltató kibocsátóobjektumának létrehozásához használandó konfigurációt biztosítja  

A Kiállítói objektumok tanúsítványok portálról történő létrehozásáról a [Key Vault-tanúsítványok blogjában](https://aka.ms/kvcertsblog) talál további információt.  

A Key Vault lehetővé teszi több kibocsátó objektum létrehozását különböző kibocsátószolgáltató-konfigurációval. A kiállítóobjektum létrehozása után a neve egy vagy több tanúsítványházirendben hivatkozhat rá. A kiállító objektumra való hivatkozás arra utasítja a Key Vaultot, hogy a tanúsítvány létrehozása és megújítása során az x509-es tanúsítvány hitelesítésszolgáltatótól történő igénylésekor a kiállító objektumban megadott konfigurációt használjon.  

A kiállítói objektumok a tárolóban jönnek létre, és csak ugyanabban a tárolóban lévő KV-tanúsítványokkal használhatók.  

### <a name="certificate-contacts"></a>Tanúsítvány névjegyei

A tanúsítványkapcsolattartók kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartam-eseményei által kiváltott értesítések küldéséhez. A névjegyek adatait a key vault összes tanúsítványa megosztja. A rendszer értesítést küld az összes megadott kapcsolattartónak a key vaultbármely tanúsítványához.  

Ha egy tanúsítvány házirendje automatikus megújításra van állítva, akkor a rendszer értesítést küld a következő eseményekről.  

- A tanúsítvány megújítása előtt
- A tanúsítvány megújítása után, amely jelzi, hogy a tanúsítvány t sikerült-e megújítani, vagy hiba történt, a tanúsítvány manuális megújítását igényli.  

  Ha egy manuálisan megújított tanúsítványházirend (csak e-mailben) van beállítva, értesítést küld a rendszer, amikor a tanúsítvány megújítása szükséges.  

### <a name="certificate-access-control"></a>Tanúsítvány-hozzáférés-vezérlés

 A tanúsítványok hozzáférés-vezérlését a Key Vault kezeli, és az ezeket a tanúsítványokat tartalmazó Key Vault biztosítja. A tanúsítványok hozzáférés-vezérlési házirendje különbözik az ugyanabban a Key Vaultban lévő kulcsok és titkos kulcsok hozzáférés-vezérlési házirendjeitől. A felhasználók létrehozhatnak egy vagy több tárolót a tanúsítványok tárolására, a forgatókönyv megfelelő szegmentálásához és kezeléséhez.  

 A következő engedélyek használhatók, egy egyszerű alapon, a titkos kulcs-hozzáférés-vezérlési bejegyzés egy key vault, és szorosan tükrözi a titkos objektum engedélyezett műveletek:  

- Tanúsítványkezelési műveletek engedélyei
  - *bekés:* A tanúsítvány aktuális verziójának vagy a tanúsítvány bármely verziójának beszereznie 
  - *lista*: Sorolja fel az aktuális tanúsítványokat vagy a tanúsítványok verzióit  
  - *frissítés*: Tanúsítvány frissítése
  - *létrehozás*: Key Vault-tanúsítvány létrehozása
  - *importálás*: Tanúsítványanyag importálása Key Vault-tanúsítványba
  - *törlés*: Tanúsítvány, annak házirendje és annak összes verziója törlése  
  - *helyreállítás*: Törölt tanúsítvány helyreállítása
  - *biztonsági mentés*: Tanúsítvány biztonsági mentése a kulcstartóban
  - *visszaállítás*: Biztonsági másolatot álló tanúsítvány visszaállítása key vaultba
  - *managecontacts*: A Kulcstár-tanúsítvány névjegyének kezelése  
  - *managers*: Key Vault-hitelesítésszolgáltatók/-kibocsátók kezelése
  - *getkiállítók*: Szerezzen be egy tanúsítvány hatóságát/kibocsátóját
  - *listkiállítók*: A tanúsítvány hatóságainak/kibocsátóinak felsorolása  
  - *setkiállítók*: Key Vault-tanúsítvány hatóságainak/kibocsátóinak létrehozása vagy frissítése  
  - *deleters*: A Key Vault-tanúsítvány hatóságainak/kibocsátóinak törlése  
 
- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Törölt tanúsítvány törlése (végleges törlése)

További információt a [Key Vault REST API-hivatkozástanúsítvány-műveletei című témakörben talál.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="azure-storage-account-key-management"></a>Az Azure Storage-fiók kulcsának kezelése

A Key Vault képes kezelni az Azure storage-fiók kulcsait:

- Belsőleg key vault listázhatja (szinkronizálja) kulcsok egy Azure-tárfiókkal. 
- A Key Vault rendszeres időközönként újragenerálja (elforgatja) a kulcsokat.
- A főértékek et soha nem adja vissza a hívónak adott válaszként.
- A Key Vault kezeli a tárfiókok és a klasszikus tárfiókok kulcsait.

További információ: [Azure Key Vault Storage-fiókkulcsok](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Tárfiók-hozzáférés-vezérlés

A következő engedélyek használhatók, ha egy felhasználó vagy az egyszerű felhasználó számára engedélyezi a felügyelt tárfiókon végzett műveleteket:  

- A felügyelt tárfiók és az SaS-definíciós műveletek engedélyei
  - *bekés:* Információ beszerez egy tárfiókról 
  - *list:* A Key Vault által kezelt tárfiókok listázása
  - *frissítés*: Tárfiók frissítése
  - *törlés*: Tárfiók törlése  
  - *helyreállítás*: Törölt tárfiók helyreállítása
  - *biztonsági mentés*: Tárfiók biztonsági mentése
  - *visszaállítás*: Biztonsági másolatot álló tárfiók visszaállítása Key Vaultba
  - *set*: Tárfiók létrehozása vagy frissítése
  - *regeneratekey*: Egy tárfiók megadott kulcsértékének újragenerálása
  - *getsas:* Információ beszerez egy SAS-definícióegy tárfiók
  - *listsas*: Storage SAS-definíciók listázása tárfiókhoz
  - *deletesas*: SAS-definíció törlése tárfiókból
  - *setsas*: Új SAS-definíció/attribútumok létrehozása vagy frissítése tárfiókhoz

- Jogosultsággal rendelkező műveletek engedélyei
  - *kiürítés*: Felügyelt tárfiók törlése (végleges törlése)

További információ: [A Storage-fiók műveletei a Key Vault REST API-referencia.](/rest/api/keyvault) Az engedélyek létrehozásáról további információt a [Tárolók – Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és [tárolók – hozzáférési szabályzat című témakörben talál.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="see-also"></a>Lásd még:

- [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](/azure/key-vault/key-vault-developers-guide)
