---
title: Az Azure Key Vault-kulcsok – Azure Key Vault
description: Az Azure Key Vault REST-felületének és a kulcsok fejlesztői részleteinek áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d89275e1418035fed8aad3ffddd8def2c1d59ce
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686060"
---
# <a name="about-azure-key-vault-keys"></a>Az Azure Key Vault-kulcsok –

Az Azure Key Vault több kulcstípust és algoritmust támogat, és lehetővé teszi a hardveres biztonsági modulok (HSM) használatát a nagy értékű kulcsokhoz.

A Key Vault ban lévő kriptográfiai kulcsok JSON webkulcs [JWK] objektumokként jelennek meg. A JavaScript object notation (JSON) és a JavaScript Object Signing and Encryption (JOSE) specifikációi a következők:

-   [JSON webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Az alap JWK/JWA specifikációk is ki vannak bővítve, hogy a Key Vault implementációjában egyedi kulcstípusok at is lehetővé tegyék. Például a kulcsok importálása HSM szállító-specifikus csomagoláshasználatával lehetővé teszi a kulcsok biztonságos szállítását, amely csak a Key Vault HSM-ekben használható. 

Az Azure Key Vault támogatja a soft és a hard kulcsokat is:

- **"Puha" kulcsok:** A Key Vault által szoftverben feldolgozott kulcs, de nyugalmi állapotban egy HSM-ben lévő rendszerkulccsal van titkosítva. Az ügyfelek importálhatnak egy meglévő RSA- vagy EK-(Elliptikus ív) kulcsot, vagy kérhetik, hogy a Key Vault hozzon létre egyet.
- **"Kemény" billentyűk:** HSM-ben (hardverbiztonsági modul) feldolgozott kulcs. Ezek a kulcsok a Key Vault HSM biztonsági világok egyikében védettek (földrajzi helyenként egy biztonsági világ van az elkülönítés fenntartásához). Az ügyfelek importálhatnak RSA- vagy EK-kulcsot, ideiglenes formában vagy kompatibilis HSM-eszközről exportálva. Az ügyfelek is kérhetik a Key Vault egy kulcs létrehozásához. Ez a kulcstípus hozzáadja a key_hsm attribútumot a JWK-beszerzéshez a HSM kulcsanyag hordozásához.

A földrajzi határokról a [Microsoft Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/privacy/) talál további információt.  

## <a name="cryptographic-protection"></a>Kriptográfiai védelem

A Key Vault csak az RSA és az Elliptikus görbe billentyűket támogatja. 

-   **EC**: "Lágy" elliptikus görbe gomb.
-   **EC-HSM**: "Kemény" elliptikus görbe gomb.
-   **RSA**: "Soft" RSA kulcs.
-   **RSA-HSM**: "Kemény" RSA kulcs.

A Key Vault támogatja a 2048, 3072 és 4096 méretű RSA-kulcsokat. A Key Vault támogatja a P-256, P-384, P-521 és P-256K (SECP256K1) elliptikus görbe kulcstípusokat.

A Key Vault által használt kriptográfiai modulok, legyen ek HSM vagy szoftver, FIPS (Federal Information Processing Standards) ellenőrzöttek. FiPS módban való futtatáshoz nem kell semmi különlegeset tennie. A HSM-védelemmel ellátottként **létrehozott** vagy **importált** kulcsok feldolgozása egy HSM-en belül történik, fips 140-2 2 szintű 2-re érvényesítve. A szoftvervédelemmel **ellátottként létrehozott** vagy **importált** kulcsokat a FIPS 140-2 1.

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

##  <a name="key-operations"></a>Kulcsfontosságú műveletek

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

## <a name="key-attributes"></a>Kulcsjellemzők

A fő elemek mellett a következő attribútumok is megadhatók. JSON-kérelemben a kulcsszó és a kapcsos zárójelek ({' '}) attribútumokra akkor is szükség van, ha nincsenek megadva attribútumok.  

- *engedélyezve*: logikai, nem kötelező, az alapértelmezett **érték igaz**. Itt adható meg, hogy a kulcs engedélyezve van-e, és használható-e kriptográfiai műveletekhez. Az *engedélyezett* attribútum ot az *nbf* és exp együttesen *használjuk.* Ha az *nbf* és *exp*közötti művelet csak akkor engedélyezett, ha true **értékre**van *beállítva.* Az *nbf* / *exp* ablakon kívüli műveletek automatikusan nem engedélyezettek, kivéve bizonyos művelettípusokat bizonyos [feltételek mellett.](#date-time-controlled-operations)
- *nbf*: IntDate, nem kötelező, az alapértelmezett most. Az *nbf* (nem korábban) attribútum azonosítja azt az időt, amely előtt a kulcsot nem szabad kriptográfiai műveletekhez használni, kivéve bizonyos művelettípusokat [bizonyos feltételek mellett.](#date-time-controlled-operations) Az *nbf* attribútum feldolgozása megköveteli, hogy az aktuális dátum/idő az *nbf* attribútumban felsorolt nem-before dátum/idő után legyen. Key Vault nyújthat néhány kis mozgásteret, általában nem több, mint néhány perc, hogy figyelembe óra döntés. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  
- *exp*: IntDate, nem kötelező, az alapértelmezett "örökre". Az *exp* (lejárati idő) attribútum azonosítja a lejárati időt, vagy amely után a kulcsot nem szabad használni a kriptográfiai művelet, kivéve bizonyos művelettípusok [bizonyos feltételek mellett](#date-time-controlled-operations). Az *exp* attribútum feldolgozása megköveteli, hogy az aktuális dátum/idő az *exp* attribútumban felsorolt lejárati dátum/idő előtt legyen. Key Vault nyújthat néhány kis mozgásteret, általában nem több, mint néhány perc, figyelembe óra döntés. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  

Vannak további írásvédett attribútumok, amelyek a legfontosabb attribútumokat tartalmazó válaszokban szerepelnek:  

- *létrehozva*: IntDate, nem kötelező. A *létrehozott* attribútum jelzi, hogy a kulcs ezen verziója mikor jött létre. Az érték null értékű az attribútum hozzáadása előtt létrehozott kulcsok esetében. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  
- *frissítve*: IntDate, nem kötelező. A *frissített* attribútum azt jelzi, hogy a kulcs ezen verziója mikor lett frissítve. Az érték null értékű azattribútum hozzáadása előtt utoljára frissített kulcsok esetében. Értéke intdate értéket tartalmazó szám kell, hogy legyen.  

Az IntDate és más adattípusokról további információt a [Kulcsok, titkos kulcsok és tanúsítványok: [Adattípusok](../general/about-keys-secrets-certificates.md#data-types)– ismertet:

### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A még nem érvényes és lejárt kulcsok az *nbf* / *exp* ablakon kívül a **visszafejtéshez,** **a kicsomagoláshoz**és a műveletek **ellenőrzéséhez** (nem ad vissza 403, Tiltott). A még nem érvényes állapot használatának oka, hogy lehetővé teszi egy kulcs tesztelése az éles használat előtt. A lejárt állapot használatának oka az, hogy engedélyezi a helyreállítási műveleteket a kulcs érvényessége esetén létrehozott adatokon. Emellett letilthatja a kulcshoz való hozzáférést a Key Vault-házirendek használatával, vagy az *engedélyezett* kulcsattribútum **hamis**ra frissítésével.

Az adattípusokról az [Adattípusok](../general/about-keys-secrets-certificates.md#data-types)című témakörben talál további információt.

A többi lehetséges attribútumról a [JSON webkulcsban (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)talál további információt.

## <a name="key-tags"></a>Kulcscímkék

További alkalmazásspecifikus metaadatokat adhat meg címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakternévvel és 256 karakterértékkel rendelkezhet.  

>[!Note]
>A címkéket a hívó akkor tudja olvasni, ha rendelkezik a *listával,* vagy engedélyt *kap* az adott objektumtípusra (kulcsokra, titkos kulcsokra vagy tanúsítványokra).

##  <a name="key-access-control"></a>Kulcshozzáférés-vezérlés

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

## <a name="next-steps"></a>További lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [Kulcsok, titkos kulcsok és tanúsítványok –](../general/about-keys-secrets-certificates.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
