---
title: Tudnivalók az Azure Key Vault kulcsok, titkos kódok és tanúsítványok – Azure Key Vault
description: Az Azure Key Vault REST felületet és fejlesztői részletei kulcsok, titkos kódok és tanúsítványok áttekintése.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: dccd685c240d6e007486be6a4f73e0fce0129a82
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405232"
---
# <a name="about-keys-secrets-and-certificates"></a>Tudnivalók a kulcsok, titkos kódok és tanúsítványok

Az Azure Key Vault lehetővé teszi, hogy a Microsoft Azure-alkalmazások és felhasználók tárolhatják és számos különböző típusú adatok titkos kulcs vagy kulcs használata:

- Titkosítási kulcsok: Több kulcstípusok és algoritmusokat támogatja, és lehetővé teszi az értékes kulcsok használható hardveres biztonsági modulok (HSM). 
- Titkos kódok: Titkos adatait, például a jelszavak és adatbázis-kapcsolati karakterláncok biztonságos tárolást biztosít.
- Tanúsítványok: Támogatja a tanúsítványok, amely a kulcsok és titkos kulcsok épülnek, és adja hozzá az automatikus megújítási funkciót.
- Azure Storage: Kezelheti az Ön számára az Azure Storage-fiók kulcsok. Belsőleg, a Key Vault egy Azure Storage-fiók kulcsok (sync) listában, és (elforgatás) újragenerálja a kulcsokat rendszeres időközönként. 

Tudnivalók a Key Vaultról további általános információkért lásd: [Mi az Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

Az alábbi szakaszok a Key Vault szolgáltatás megvalósítása között vonatkozó általános információkat kínálnak.

### <a name="supporting-standards"></a>Szabványok támogatása

A JavaScript Object Notation (JSON) és a JavaScript Object aláírás és titkosítás (JOSE) előírások olyan fontos háttér-információkat.  

-   [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (KMH)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Adattípusok

Tekintse meg a megfelelő adattípusokat kulcsok, titkosítási és aláírási JOSE előírásainak.  

-   **algoritmus** -kulcs művelet esetén például RSA1_5 egy támogatott algoritmus  
-   **ciphertext-érték** -szöveg oktettben Base64URL használatával lett kódolva cipher  
-   **a kivonatoló-érték** -kivonatoló algoritmust, Base64URL használatával lett kódolva kimenete  
-   **kulcs-típusú** – a támogatott kulcs típusú, például RSA (egyeztetéséhez a Rivest-Shamir-Adleman).  
-   **egyszerű szöveges-érték** – egyszerű szöveges oktettben Base64URL használatával  
-   **aláírás-érték** – kimeneti Base64URL használatával lett kódolva aláírási algoritmus  
-   **base64URL** – egy Base64URL [RFC4648] kódolású bináris érték  
-   **logikai** – igaz vagy hamis  
-   **Identitás** - identitás az Azure Active Directory (AAD).  
-   **IntDate** – 1970. a másodpercek számát jelölő JSON decimális-01-ig az adott UTC szerinti dátuma/ideje (UTC) 01T0:0:0Z. Lásd: különösen RFC3339 date/times észlelésével kapcsolatos részletekért és az UTC.  

### <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziókezelés

A Key Vaultban tárolt objektumok verziószámmal is, amikor létrejön egy objektum új példányát. Minden verzió van rendelve egy egyedi azonosító URL-CÍMÉT. Objektum létrehozásakor megadott egyedi verzióazonosítót, és az objektum aktuális verziója megjelölve. Az azonos nevű objektum egy új példányának létrehozása lehetővé teszi az új objektum egyedi verzióazonosító okozza, hogy a jelenlegi verzió válik.  

A Key Vaultban objektumokat is kezelhetők, a jelenlegi azonosító vagy egy verzió-specifikus azonosító használatával. Például a megadott nevű kulcs `MasterKey`, az aktuális azonosítóval rendelkező műveletek végrehajtása hatására a rendszer az elérhető legújabb verzióra. A rendszer, hogy az objektum adott verzióját használja a verziófüggő azonosítóval műveleteket okozza.  

Objektumok egyedileg azonosítja a Key Vault URL-címen belül. Két objektum a rendszer az azonos URL-CÍMÉT, függetlenül attól, földrajzi hely rendelkezik. Az objektum teljes URL-CÍMÉT az objektumazonosító nevezzük. Az URL-címet tartalmaz egy előtagot, amely azonosítja a Key Vault objektumtípus, felhasználó által megadott objektum nevét, és a egy objektum verziója. Az objektum neve, kis-és nagybetűket, és nem módosítható. Azonosítók, amelyeket nem tartalmazza az objektum verzió alapja azonosítók nevezzük.  

További információkért lásd: [hitelesítési kérelmek és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatás key vault neve.<br /><br /> A Key Vault neve a felhasználó ki van jelölve, és globálisan egyedi.<br /><br /> Key Vault-névre karakterláncnak kell lennie 3 – 24 karakter, csak 0-9, amely tartalmazza a – z, A-Z, - és.|  
|`object-type`|Az objektumot, vagy a "kulcsok", vagy a "titok" típusú.|  
|`object-name`|Egy `object-name` a felhasználó által megadott név és a egy Key Vaultot egyedinek kell lennie. A név csak 0-9, amely egy 1 – 127 karakter karakterlánc lehet a – z, A-Z, - és.|  
|`object-version`|Egy `object-version` a rendszer hozta létre, 32 karakter karakterlánc-azonosító, amely szükség esetén használja az objektum egyedi verzióját.|  

## <a name="key-vault-keys"></a>A Key Vault-kulcsok

### <a name="keys-and-key-types"></a>Kulcsok és a kulcs típusa

Titkosítási kulcsok a Key Vaultban JSON webes kulcs [JWK] objektumként jelennek meg. Az alap JWK/JWA előírásoknak is terjeszteni a Key Vault megvalósításához egyedi kulcstípusok engedélyezése. Például importálása a HSM szállító-specifikus csomagolás eszközzel, lehetővé teszi, hogy csak akkor használható, a Key Vault HSM-kulcsok biztonságos szállítása.  

- **"Soft" kulcsok**: Egy kulcsot a szoftver a Key Vault által feldolgozott, de titkosítása egy hardveres biztonsági modulban rendszer kulcs használatával. Az ügyfelek előfordulhat, hogy egy meglévő RSA vagy -EK (elliptikus görbe alapú) kulcs importálása, vagy kérje a Key Vault generál egyet.
- **"Rögzített" kulcsok**: Egy kulcs feldolgozása hardveres biztonsági modul (HSM-mel). Ezek a kulcsok a Key Vault HSM biztonsági Világai valamelyik védett (van az elszigetelés földrajzi hely szerint egy biztonsági világ). Ügyfelek előfordulhat, hogy importálja egy RSA vagy EK kulcsot a letölthető formában vagy exportálásával egy kompatibilis a HSM-eszközről. Az ügyfelek is kérheti a Key Vault-kulcs létrehozása. Ilyen típusú kulcs hozzáadja a T-attribútumot a JWK szerezze be a HSM-kulcs adatai számára.

     További információ a földrajzi határokon belül: [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

A Key Vault támogatja az RSA és az elliptikus görbe alapú kulcsok csak. 

-   **EK**: "Soft" elliptikus görbe alapú kulcsot.
-   **EC-HSM**: "Rögzített" elliptikus görbe alapú kulcsot.
-   **RSA**: "Soft" RSA-kulcsot.
-   **RSA-HSM**: "Rögzített" RSA-kulcsot.

A Key Vault támogatja a 2048, 3072 és 4096-méretek RSA-kulcsok. A Key Vault támogatja az elliptikus görbe alapú kulcs-típusok P-256, p-384, p-521 és P-256_K (SECP256K1).

### <a name="cryptographic-protection"></a>Titkosítási védelme

A Key Vault használó titkosítási moduljait HSM, illetve szoftver –, hogy-e FIPS szabványnak (FIPS) érvényesítve. Nem kell különösebb FIPS-módban történő futtatására. Kulcsok **létrehozott** vagy **importált** , HSM-védelemmel vannak feldolgozott belül HSM, érvényesítve a FIPS 140-2 2. szintje. Kulcsok **létrehozott** vagy **importált** szoftveres védelemmel ellátott, mint feldolgozása belül érvényesítve a 1-es szintű FIPS 140-2 titkosítási moduljait. További információkért lásd: [kulcsokat és a kulcstípusok](#keys-and-key-types).

###  <a name="ec-algorithms"></a>EK algoritmusok
 A Key Vault HSM-EK és a kulcsokat a következő algoritmus azonosítók használata támogatott. 

#### <a name="curve-types"></a>Görbe típusok

-   **P-256** -p-256, meg van határozva a NIST-görbét [DSS FIPS PUB 186 – 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** -SECP256K1, meg van határozva az mp-görbe [2 mp-ben: Ajánlott az elliptikus görbe alapú Domain paramétereket](http://www.secg.org/sec2-v2.pdf).
-   **P-384** -p-384, meg van határozva a NIST-görbét [DSS FIPS PUB 186 – 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -p-521, meg van határozva a NIST-görbét [DSS FIPS PUB 186 – 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>BEJELENTKEZÉSI/ELLENŐRZÉSE

-   **ES256** – digests ECDSA az SHA-256 algoritmust és görbe p-256 használatával létrehozott kulcsokat. Ez az algoritmus címen leírt [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** – digests ECDSA az SHA-256 algoritmust és a görbe P-256_K létre kulcsokat. Ez az algoritmus szabványügyi szervezet függőben van.
-   **ES384** – az SHA-384 ECDSA digests, és a görbe p-384 létre kulcsokat. Ez az algoritmus címen leírt [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** – az SHA-512 ECDSA digests, és a görbe p-521 létre kulcsokat. Ez az algoritmus címen leírt [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Az RSA algoritmus  
 A következő algoritmus azonosítók RSA és az RSA-HSM kulcsok a Key Vault használata támogatott.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁSI/VISSZAFEJTÉSI

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] key encryption  
-   **Az RSA-OAEP** – RSAES optimális aszimmetrikus titkosítási Padding (OAEP) [RFC3447], szakasz A.2.1 az RFC 3447 által meghatározott alapértelmezett paraméterek használatával. Alapértelmezett paraméterek SHA-1 algoritmussal SHA-1 kivonatoló függvényt és a egy MGF1 maszk generációs funkcióját használja.  

#### <a name="signverify"></a>BEJELENTKEZÉSI/ELLENŐRZÉSE

-   **RS256** - RSASSA-PKCS-v1_5 using SHA-256. Az alkalmazás megadott kivonatoló érték SHA-256 használatával kell számolni, és 32 bájt hosszú lehet.  
-   **RS384** – RSASSA-PKCS-v1_5 SHA-384-et használ. Az alkalmazás megadott kivonatoló érték SHA-384-et használó kell számolni, és 48 bájt hosszú lehet.  
-   **RS512** – RSASSA-PKCS-v1_5 SHA-512 használatával. Az alkalmazás megadott kivonatoló érték SHA-512 használatával kell számolni, és 64 bájt hosszú lehet.  
-   **RSNULL** – lásd: [RFC2437], egy speciális használati bizonyos TLS forgatókönyvek engedélyezéséhez.  

###  <a name="key-operations"></a>Kulcsműveletek

A Key Vault támogatja a következő műveleteket az objektumok:  

-   **Hozzon létre**: Engedélyezi, hogy egy ügyfél hozzon létre egy kulcsot a Key Vaultban. A kulcs értékét a Key Vault által generált és tárolja, és nem az ügyfél általánosan elérhető. A Key Vaultban aszimmetrikus kulcsokhoz lehet létrehozni.  
-   **Importálás**: Lehetővé teszi, hogy egy ügyfél egy meglévő kulcs importálása a Key vaulthoz. A Key Vault használatával számos különböző csomagolási módszerrel belül egy JWK-szerkezet aszimmetrikus kulcsokhoz is importálhatók. 
-   **Frissítés**: Lehetővé teszi, hogy korábban tárolja a Key Vault-kulcshoz tartozó metaadatok (kulcsattribútum) módosításához szükséges engedélyekkel rendelkező ügyfelet.  
-   **Törlés**: Lehetővé teszi, hogy egy ügyfél a megfelelő engedélyekkel a Key Vaultból kulcs törlése.  
-   **Lista**: Lehetővé teszi, hogy egy ügyfél egy adott Key vaultban a összes kulcsok listázását.  
-   **Verziók listázása**: Lehetővé teszi, hogy az ügyfél egy adott kulcs egy adott kulcstartó összes verzió listázása.  
-   **Első**: Engedélyezi, hogy egy ügyfél lekérheti az egy adott kulcs a Key Vaultban nyilvános részeit.  
-   **Biztonsági mentés**: Exportálja egy kulcsot, védett formátumban.  
-   **Visszaállítás**: Importál egy biztonsági másolatból kulcsot.  

További információkért lásd: [kulcs a Key Vault REST API-referencia-műveletek](/rest/api/keyvault).  

Egy kulcsot a Key Vaultban létrehozott, a következő titkosítási műveleteket végezhetik a kulccsal:  

-   **Jelentkezzen, és ellenőrizze**: Szigorúan, tato operace je "bejelentkezési hash" vagy "a kivonatoló hitelesítés", mivel a Key Vault nem támogatja a kivonatolás tartalom jogosultságkód létrehozásának részeként. Az alkalmazások kell ujjlenyomat-helyileg kell aláírni az adatokat, majd a Key Vault bejelentkezési az ujjlenyomat kérése. Az aláírt kivonatok ellenőrzési az alkalmazásokat, amelyek nem férhetnek [nyilvános] megosztottkulcs-anyag kényelmi műveletként használata támogatott. Ajánlott alkalmazások teljesítménye ellenőrizze, hogy műveleteket helyileg.  
-   **Titkosítási kulcs / Alkalmazásburkoló**: A Key Vaultban tárolt kulcs egy másik kulcsot, általában egy tartalom szimmetrikus titkosítási kulcsot (CEK) védelmére használhatók. Ha a kulcsot a Key Vault aszimmetrikus, kulcstitkosítási szolgál. Például RSA-OAEP és a WRAPKEY és UNWRAPKEY műveletek megfelelői TITKOSÍTÁSA/VISSZAFEJTÉSE. Ha a kulcsot a Key Vault szimmetrikus, alkalmazásburkoló kulcsot használja. Ha például az AES-KW. A könnyebb az alkalmazások, amelyek nem rendelkeznek [nyilvános] megosztottkulcs-anyag a hozzáférést a WRAPKEY művelet támogatott. Az ajánlott alkalmazásteljesítmény helyileg kell WRAPKEY műveleteket végezni.  
-   **Titkosítása és visszafejtése**: A Key Vaultban tárolt kulcs titkosítására vagy visszafejtésére egyetlen az adatblokk használhatók. A blokk méretét a kulcs típusa és a kiválasztott titkosítási algoritmus határozza meg. A titkosítás művelet kényelmi célokat szolgál, alkalmazások, amelyek nem rendelkeznek [nyilvános] megosztottkulcs-anyag hozzáférést biztosítunk. Ajánlott alkalmazások teljesítménye, titkosítása műveletek helyileg kell elvégezni.  

Miközben WRAPKEY/UNWRAPKEY aszimmetrikus kulcsokkal tűnhet felesleges (mivel a művelet egyenértékű TITKOSÍTÁSA), a különböző műveletek használata fontos. A különbséget szemantikai biztosít, és ezek műveletek, és a konzisztencia, ha a szolgáltatás által támogatott más kulcstípusok engedélyezési szétválasztása.  

A Key Vault nem támogatja az EXPORTÁLÁSI műveletekkel. Miután egy kulcsot a rendszer üzembe ne lehessen kinyerni, vagy módosította a kulcs adatai. Key vault felhasználók azonban megkövetelhetik kulcsát, a más használati eseteket, például, ha törölve lett. Ebben az esetben használhatják a biztonsági MENTÉSI és VISSZAÁLLÍTÁSI műveletek, az Exportálás/importálás a kulcsot, védett formátumban. A biztonsági MENTÉSI művelet által létrehozott kulcsok, amelyek nem használható a Key Vault kívül. Az IMPORTÁLÁSI művelet azt is megteheti, szemben a Key Vault több példány is használható.  

Felhasználók korlátozhatják a titkosítási műveletek, amely támogatja a Key Vault-JWK objektum key_ops tulajdonsággal kulcs alapon.  

JWK-objektumokra vonatkozó további információkért lásd: [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Legfőbb attribútumai

A kulcs adatai mellett a következő attribútumok adható meg. Egy JSON-kérelem a attribútumok kulcsszót, és kapcsos zárójelek a(z) {' "}", szükség, akkor is, ha a megadott attribútum sem.  

- *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Megadja, hogy a kulcs engedélyezve van, és a titkosítási műveletek gyakorlatot. A *engedélyezve* attribútum együtt használatos *nbf* és *exp*. Ha a művelet között történik *nbf* és *exp*, akkor lesz csak engedélyezett, ha *engedélyezve* értékre van állítva **igaz**. Műveletek kívül a *nbf* / *exp* ablak automatikusan engedélyezettek, kivéve a művelet bizonyos [adott feltételek](#date-time-controlled-operations).
- *nbf*: Kötelező megadni, alapértelmezett IntDate, már. A *nbf* (nem előtt) attribútum azonosítja az idő, ameddig a kulcs nem használható a titkosítási műveletek, kivéve a művelet bizonyos [adott feltételek](#date-time-controlled-operations). A feldolgozása a *nbf* attribútumot igényel, hogy az aktuális dátum/idő után kell vagy a nem egyenlő-szereplő dátum/idő előtt a *nbf* attribútum. A Key Vault néhány kisebb eltérést rendelkezhetnek általában legfeljebb pár percet, hogy óra figyelembe döntés. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *Exp*: IntDate, nem kötelező, alapértelmezett érték a "végtelen". A *exp* (lejárati ideje) attribútum azonosítja a lejárati időt, vagy azt követően, amely nem használja a rendszer kriptográfiai művelet, kivéve a művelet bizonyos [adott feltételek](#date-time-controlled-operations). A feldolgozása a *exp* attribútumot igényel, hogy az aktuális dátumot és időpontot kell-e, mielőtt a lejárati dátum/idő szerepel a *exp* attribútum. A Key Vault néhány kisebb eltérést rendelkezhetnek általában legfeljebb pár percet, hogy óra figyelembe döntés. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

Nincsenek további csak olvasható attribútumokat választ, amely tartalmazza a kulcsattribútum részét képező:  

- *created*: IntDate, nem kötelező. A *létrehozott* attribútum azt jelöli, ez a verzió, a kulcs létrehozásakor. Ez az attribútum hozzáadása előtt létrehozott kulcsok null érték. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A *frissített* attribútum azt jelöli, amikor ez a kulcs verziója módosult. Az érték null, a kulcsokhoz, a legutóbb frissített Ez az attribútum hozzáadása előtt. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

IntDate és más adatok további információkért lásd: [adattípusok](#data-types)  

#### <a name="date-time-controlled-operations"></a>Dátum-idő ellenőrzött műveletek

Nem, de érvényes és a kulcs, külső lejárt a *nbf* / *exp* ablakban fog működni az **visszafejtéséhez**, **kicsomagolása**, és **ellenőrzése** operations (nem ad vissza 403, tiltott). A közösségértékek nem még érvényes állapot segítségével, hogy a kulcs kell tesztelni, mielőtt éles környezetben való használathoz. A közösségértékek használata a lejárt állapotban, hogy a helyreállítási műveletek jött létre, amikor a kulcs érvényes volt-adatokat. Ezenkívül letilthatja a hozzáférést egy kulcsot a Key Vault-szabályzatok, vagy frissítésével a *engedélyezve* a kulcsattribútum **false (hamis)**.

Az adattípusok további információkért lásd: [adattípusok](#data-types).

Más lehetséges attribútumok további információkért lásd: a [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Kulcs címkék

További alkalmazások – is szolgáltatásspecifikus metaadatokat címkék formájában is megadhat. A Key Vault támogatja a legfeljebb 15 címkék, amelyek mindegyike egy 256 karakter hosszúságú név- és egy 256 karakter hosszúságú lehet.  

>[!Note]
>A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványokat).

###  <a name="key-access-control"></a>Kulcshozzáférés-vezérlés

Hozzáférés-vezérlés a Key Vault által kezelt kulcsok egy Key Vaultot, amely a kulcsok a tároló szintjén van megadva. A kulcsokhoz, a hozzáférés-vezérlési szabályzat az azonos Key Vault titkos hozzáférés-vezérlési házirend különbözik. Felhasználók előfordulhat, hogy hozzon létre egy vagy több tárolók, kulcsok tárolására, és a forgatókönyv megfelelő szegmentálását és a kulcsok kezelését fenntartásához szükséges. Hozzáférés-vezérlés a kulcsok nem függ a hozzáférés-vezérlés a titkos kulcsok.  

A következő is adhatók engedélyek, az egy / felhasználó / service egy tároló kulcsainak hozzáférés-vezérlési bejegyzés egyszerű rendszeresen. Ezek az engedélyek szorosan tükrözik a kulcs-objektum engedélyezett műveletek:  

- Kulcskezelési műveletek engedélyeket
  - *Első*: A nyilvános részét egy kulcsot, valamint a hozzá tartozó attribútumok olvasása
  - *Lista*: A kulcsok vagy a key vaultban tárolt kulcs verzióinak listázása
  - *Frissítés*: Frissítse az attribútumot a kulcshoz
  - *Hozzon létre*: Új kulcsok létrehozása
  - *Importálás*: Kulcs importálása egy kulcstartóba
  - *Törlés*: A kulcs-objektum törlése
  - *helyreállítás*: Egy törölt kulcs helyreállítása
  - *Biztonsági mentés*: A key vault-kulcs biztonsági mentése
  - *Visszaállítás*: Key vault kulcs biztonsági visszaállítása

- A titkosítási műveletek engedélyek
  - *visszafejtés*: A kulcs használatával bájt sorozatát feloldása
  - *titkosítása*: A kulcs használatával egy tetszőleges bájtsorrendet védelme
  - *unwrapKey*: A kulcs használatával a burkolt szimmetrikus kulcsokat a védelem megszüntetése
  - *wrapKey*: A kulcs használatával egy szimmetrikus kulcs védelme
  - *Győződjön meg arról*: A kulcs használatával emésztett ellenőrzése  
  - *bejelentkezési*: A kulcs használatával emésztett aláírása
    
- Privilegizált műveleteket vonatkozó engedélyek
  - *végleges törlés*: Végleges törlés (végleges törlése) egy törölt kulcs

Kulcsok való használatáról további információkért lásd: [kulcs a Key Vault REST API-referencia-műveletek](/rest/api/keyvault). Engedélyek meghatározása a további információkért lásd: [- tárolók létrehozása vagy frissítése](/rest/api/keyvault/vaults/createorupdate) és [tároló - hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>A Key Vault titkos kulcsok 

### <a name="working-with-secrets"></a>Titkos kulcsok használata

A fejlesztő szempontból a Key Vault API-k fogadja el, és karakterláncként titkos értéket ad vissza. A Key Vault belsőleg, tárolja és kezeli a titkos kulcsok sorrendje oktetben (8 bites bájt), mint minden 25 kilobájt maximális mérettel. A Key Vault szolgáltatás szemantika nem biztosít, például a következők. Csupán az adatokat fogad, titkosítja azokat, tárolja és egy titkos kulcs azonosítóját ("id") adja vissza. Az azonosító lekéréséhez a titkos kulcsot egy későbbi időpontban használható.  

Szigorúan bizalmas adatok esetén az ügyfelek figyelembe kell venni az kiegészítő védelmi rétegeket, az adatokat. Tárolás előtt egy külön védelmi kulcs használata a Key Vaultban az adatok titkosítása látható egy példa.  

A Key Vault titkos kódok contentType mező is támogatja. Ügyfelek, amelyek segítik az adatok értelmezéséhez, amikor lekéri azt a titkos kulcs tartalomtípusa megadhatja. Ez a mező maximális hossza 255 karakter lehet. Nincsenek előre meghatározott értékek. A javasolt használata gyors tippként annyit elmondunk, az adatok értelmezéséhez. Például egy megvalósítási előfordulhat, hogy tárolja a jelszavakat és a tanúsítványok titkos kódként, majd az ebben a mezőben megkülönböztetéséhez. Nincsenek előre meghatározott értékek.  

### <a name="secret-attributes"></a>Titkos kód attribútumok

A titkos adatok mellett a következő attribútumokkal lehet megadni:  

- *Exp*: IntDate, nem kötelező, alapértelmezett érték a **örökre**. A *exp* (lejárati ideje) attribútum azonosítja a lejárati időt, vagy azt követően, amely a titkos adatokat kell nem olvashatók be, kivéve az [adott helyzetekben](#date-time-controlled-operations). Ez a mező **tájékoztató** célra használja, csak, hogy tájékoztatja a felhasználókat a key vault szolgáltatást, hogy egy adott titkos kód nem használható. Az érték egy IntDate értéket tartalmazó számnak kell lennie.   
- *nbf*: IntDate, nem kötelező, alapértelmezett érték a **most**. A *nbf* (nem előtt) attribútum azonosítja az idő, ameddig a titkos adatokat kell nem olvashatók be, kivéve az [adott helyzetekben](#date-time-controlled-operations). Ez a mező **tájékoztató** csak célra használja. Az érték egy IntDate értéket tartalmazó számnak kell lennie. 
- *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Ez az attribútum meghatározza, hogy a titkos adatok lekérhetők. Az engedélyezett attribútum együtt használatos *nbf* és *exp* között-művelet bekövetkezésekor *nbf* és *exp*, csak akkor lesz engedélyezett, ha engedélyezve beállítva **igaz**. Műveletek kívül a *nbf* és *exp* ablak a program automatikusan nem megengedett, kivéve a [adott helyzetekben](#date-time-controlled-operations).  

Nincsenek további csak olvasható attribútumok telepítésben lévő semmilyen választ, amely tartalmazza a titkos attribútumok:  

- *created*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy jelen verziója a titkos kulcs létrehozásakor. Ez például a következők Ez az attribútum hozzáadása előtt létrehozott null értékű. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy ez a verzió, a titkos kód frissítésekor. Ez az érték értéke null, például a következők a legutóbb frissített Ez az attribútum hozzáadása előtt. Az érték egy IntDate értéket tartalmazó számnak kell lennie.

#### <a name="date-time-controlled-operations"></a>Dátum-idő ellenőrzött műveletek

A titkos kulcs **első** kívül nem még érvényes, és a lejárt titkos adatait, a fog működni a művelet a *nbf* / *exp* ablak. A titkos kód hívása **lekérése** művelet, nem még érvényes titkos kulcs, a tesztelési célokra használható. Beolvasása (**első**sítése) egy lejárt titkos helyreállítási műveletekhez használható.

Az adattípusok további információkért lásd: [adattípusok](#data-types).  

### <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

Hozzáférés-vezérlés a Key vaultban felügyelt titkos kulcsok, szintjén, amely tartalmazza ezeket a titkos kulcsok a Key Vault is biztosított. A titkos kulcsokat, a hozzáférés-vezérlési házirend különbözik a hozzáférés-vezérlési szabályzat az azonos Kulcstartóban lévő kulcsok. Felhasználók előfordulhat, hogy hozzon létre legalább egy tároló titkos kulcsok tárolására, és a forgatókönyv megfelelő szegmentálását és a titkos kulcsok kezelése fenntartásához szükséges.   

A következő engedélyeket is használható, egyszerű alapon, az egy tároló titkos kódok hozzáférés-vezérlési bejegyzés, és szorosan tükrözik a titkos objektum engedélyezett műveletek:  

- Titkoskód-kezelési műveletek vonatkozó engedélyek
  - *Első*: Olvassa el a titkos kulcs  
  - *Lista*: A titkos kulcsok vagy a Key Vaultban tárolt titkos kulcs verzióinak listázása  
  - *Állítsa be*: Titkos kulcs létrehozása  
  - *Törlés*: Titkos kulcs törlése  
  - *helyreállítás*: Törölt titkos kulcs helyreállítása
  - *Biztonsági mentés*: Készítsen biztonsági másolatot a kulcstartóban található titkos kulcs
  - *Visszaállítás*: A key vault titkos kulcs biztonsági visszaállítása

- Privilegizált műveleteket vonatkozó engedélyek
  - *végleges törlés*: Végleges törlés (végleges törlése) titkos kulcs törlése

Titkos kódok való használatáról további információkért lásd: [a Key Vault REST API-referencia a titkos műveletek](/rest/api/keyvault). Engedélyek meghatározása a további információkért lásd: [- tárolók létrehozása vagy frissítése](/rest/api/keyvault/vaults/createorupdate) és [tároló - hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Titkos kódok  
További alkalmazások – is szolgáltatásspecifikus metaadatokat címkék formájában is megadhat. A Key Vault támogatja a legfeljebb 15 címkék, amelyek mindegyike egy 256 karakter hosszúságú név- és egy 256 karakter hosszúságú lehet.  

>[!Note]
>A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványokat).

## <a name="key-vault-certificates"></a>A Key Vault-tanúsítványok

A x509 kezelését biztosítja a Key Vault tanúsítványokkal támogatási tanúsítványok és az alábbi viselkedés tapasztalható:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosát, hogy hozzon létre egy tanúsítványt, vagy egy meglévő tanúsítvány importálása a Key Vault létrehozási folyamata keresztül. Tartalmazza a mindkét önaláírt, és a hitelesítésszolgáltató tanúsítványokat jön létre.
-   Lehetővé teszi, hogy a Key Vault-tanúsítvány tulajdonosának biztonságos tárolását és kezelését a X509 megvalósításához tanúsítványok titkos kulcs adatai közreműködése nélkül.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosát, hogy olyan szabályzatot, amely arra utasítja a Key Vault a tanúsítvány életciklusának kezelésére.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonos kapcsolattartási kapcsolatos adatok megadása az értesítésre életciklus-események lejárati és a tanúsítvány megújítása.  
-   Támogatja az automatikus megújítását a kiválasztott kiállítók – Key Vault partner X509 tanúsítvány-szolgáltatók / hitelesítésszolgáltatók.

>[!Note]
>Nem-termékekért felelős csoportokkal együttműködésben szolgáltatók/hitelesítésszolgáltatók is használhatók, de nem támogatja az automatikus megújítási funkciót.

### <a name="composition-of-a-certificate"></a>A tanúsítvány összeállítás

Key Vault-tanúsítvánnyal jön létre, amikor egy címezhető kulcsot és titkos kulcsot is jönnek létre ugyanazzal a névvel. A Key Vault-kulcs lehetővé teszi, hogy a kulcsfontosságú műveletek, és a Key Vault titkos kulcsából lehetővé teszi, hogy a tanúsítvány értékét, a titkos kulcs lekéréséhez. Key Vault-tanúsítvánnyal is tartalmazza nyilvános x509 tanúsítvány metaadatait.  

Az azonosítóját, valamint a tanúsítványok verziója, valamint a kulcsok és titkos kulcsok hasonlít. A Key Vault tanúsítvány válasz-címmel rendelkező kulcs és a titkos kulcsot a Key Vault verziójának használatával létrehozott egy adott verzióját érhető el.
 
![Tanúsítványok olyan összetett objektumok](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportálható, vagy nem exportálható kulcs

Amikor a Key Vault-tanúsítvánnyal jön létre, azt kérhető le a megcímezhető titkos PFX vagy PEM formátumban a titkos kulccsal. A szabályzat, a tanúsítvány létrehozásához használt kell azt jelzi, hogy a kulcs exportálható. Ha a szabályzat nem exportálható, a titkos kulcs nem az értéket, ha egy titkos kulcsot beolvashatók egy részét.  

A megcímezhető kulcs nem exportálható KV tanúsítványokkal relevánsabb lesz. A megcímezhető KV kulcs műveletek vannak leképezve a *keyusage* mezőjében a KV tanúsítási szabályzat a KV tanúsítvány létrehozásához használt.  

Kétféle típusú kulcs támogatottak – *RSA* vagy *RSA HSM* tanúsítványokkal. Exportálható csak engedélyezett az RSA, RSA HSM által nem támogatott.  

### <a name="certificate-attributes-and-tags"></a>Tanúsítvány-attribútumok és címkék

Tanúsítvány metaadatait-címmel rendelkező kulcs és a megcímezhető titkos kívül Key Vault-tanúsítvánnyal azt is tartalmazza, attribútumok és címkék.  

#### <a name="attributes"></a>Attribútumok

A tanúsítvány attribútumok attribútumok a megcímezhető kulcs és titkos kulcsot hozott létre, amikor KV tanúsítvány jön létre tükrözött esetén.  

Key Vault-tanúsítvánnyal rendelkezik a következő attribútumokat:  

-   *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Jelzi, hogy a tanúsítvány adatainak titkos vagy működtethető kulcsként beolvasható adható meg. Együtt is használható *nbf* és *exp* között-művelet bekövetkezésekor *nbf* és *exp*, és csak akkor engedélyezhetők, ha engedélyezve van értéke TRUE. Műveletek kívül a *nbf* és *exp* ablakban automatikusan engedélyezettek.  

Nincsenek a válaszban szereplő további csak olvasható attribútumok:

-   *created*: IntDate: azt jelzi, hogy ez a verzió, a tanúsítvány létrehozásakor.  
-   *frissített*: IntDate: azt jelzi, hogy ez a verzió, a tanúsítvány frissítésekor.  
-   *Exp*: IntDate: tartalmazza az x509 lejárati idejét értékét tanúsítványt.  
-   *nbf*: IntDate: tartalmazza az érték a x509 számított tanúsítványt.  

> [!Note] 
> Ha lejár a Key Vault-tanúsítvánnyal, címezhető kulcs és titkos kulcs működésképtelenné válhatnak.  

#### <a name="tags"></a>Címkék

 Kulcs – érték sztringpárok, kulcsok és titkos kódok hasonló megadott szótára ügyfél.  

 > [!Note]
> A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványokat).

### <a name="certificate-policy"></a>Tanúsítvány-házirend

A tanúsítási szabályzat létrehozása és kezelése a Key Vault-tanúsítvánnyal életciklusát adatait tartalmazza. A titkos kulccsal rendelkező tanúsítványt a key vaultban való importálásakor, egy alapértelmezett szabályzat jön létre a x509 olvasásával tanúsítványt.  

Key Vault-tanúsítvánnyal létrehozása az alapoktól, egy házirendet kell adni. A szabályzat előírja a Key Vault tanúsítvány verziója vagy a következő Key Vault verziójának létrehozása. Szabályzat létrehozása után nem szükséges az egymást követő-létrehozási műveletek a későbbi verziókban. Nincs Key Vault-tanúsítvánnyal összes verzióját a házirendet csak egy példánya.  

Magas szinten a tanúsítási szabályzat a következő információkat tartalmazza:  

-   X509 tanúsítvány tulajdonságai: Tulajdonos neve, a tulajdonos alternatív neve és a más egy x509 létrehozásához használt tulajdonságokat tartalmaz tanúsítványkérelmet.  
-   Kulcs tulajdonságai: tartalmazza a kulcs típusa, kulcshossz, exportálható, és újra felhasználhatja kulcsmezők. Ezek a mezők kérje meg a key vault való hozzon létre egy kulcsot.  
-   Titkos kulcs tulajdonságai: tartalmazza a titkos kulcs tulajdonságai, például a tartalomtípusa címezhető titkos kulcs létrehozásához a titkos értéket, a tanúsítványt a titkos kód beolvasása.  
-   Élettartam műveletek: a KV tanúsítvány élettartama műveleteket tartalmazza. Minden egyes élettartamművelet tartalmazza:  

     - Eseményindító: nappal a lejárat vagy élettartama span százalékos előtt keresztül megadott  

     - Művelet: Adja meg a művelet típusa – *emailContacts* vagy *automatikus megújítás*  

-   Kiállító: A tanúsítvány kiállítója probléma x509 tanúsítványok használatával kapcsolatos paramétereket.  
-   Házirend attribútumainak: a szabályzathoz tartozó attribútumokat tartalmaz  

#### <a name="x509-to-key-vault-usage-mapping"></a>A Key Vault használatának leképezés X509

Az alábbi táblázat a Key Vault-tanúsítványok létrehozásának részeként létrehozott kulcs hatékony kulcsműveletek kulcshasználat szabályzatot x509 leképezése jelöli.

|**X509 kulcshasználat jelzői**|**A Key Vault-kulcs ops**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|DataEncipherment|titkosítása, visszafejtése| – |
|DecipherOnly|visszafejtés| –  |
|DigitalSignature|aláírása, ellenőrzése| A Key Vault alapértelmezett egy tanúsítvány létrehozáskor használati megadása nélkül | 
|EncipherOnly|encrypt| – |
|KeyCertSign|aláírása, ellenőrzése|–|
|KeyEncipherment|wrapKey, unwrapKey| A Key Vault alapértelmezett egy tanúsítvány létrehozáskor használati megadása nélkül | 
|NonRepudiation|aláírása, ellenőrzése| – |
|crlsign|aláírása, ellenőrzése| – |

### <a name="certificate-issuer"></a>Tanúsítvány kiállítója

A Key Vault tanúsítvány objektum egy rendelés x509 tanúsítványok kiválasztott tanúsítvány kiállítója szolgáltató folytatott kommunikáció során használt konfigurációt tartalmazza.  

-   A következő SSL-tanúsítványokat kiállító tanúsítványszolgáltatók Key Vault-partnerek

|**Szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|A nyilvános felhőben és az Azure Government minden a key vault szolgáltatás helyeken támogatott|
|GlobalSign|A nyilvános felhőben és az Azure Government minden a key vault szolgáltatás helyeken támogatott|

Egy tanúsítvány kiállítója hozhat létre egy Key Vaultot, mielőtt a következő előfeltételként felsorolt lépéseket 1. és 2 sikeresen meg kell végezni.  

1. Tanúsítványszolgáltatók (CA) származó megoldás előkészítése  

    -   Egy szervezet rendszergazdája fedélzeti kell (például vállalati Contoso) legalább egy hitelesítésszolgáltató-szolgáltatóval.  

2. A rendszergazda létrehozza a kérelmező hitelesítő adatokat a Key vault SSL-tanúsítványok regisztrálását (és megújítását)  

    -   A konfiguráció a szolgáltató kibocsátó objektum létrehozása a key vaultban használt biztosít  

-Tanúsítványok portáljáról a kibocsátó objektumok létrehozásával kapcsolatos további információkért lásd: a [Key Vault Certificates blog](https://aka.ms/kvcertsblog)  

Key Vault lehetővé teszi, hogy a különböző kibocsátó szolgáltató konfigurációjának több kibocsátó objektumok létrehozásához. Kiállító objektum létrehozása után a név egy vagy több tanúsítvány-házirendek lehet hivatkozni. A kibocsátó objektumra hivatkozik arra utasítja a Key Vault használata a kibocsátó objektumban megadott konfiguráció, a x509 kérésekor tanúsítványt a CA-szolgáltató a tanúsítvány-létrehozási és -megújítás során.  

Kibocsátó objektumok jönnek létre a tárolót, és csak akkor használható KV tanúsítványokkal az ugyanahhoz a tárolóhoz.  

### <a name="certificate-contacts"></a>Tanúsítványpartnerek

Tanúsítványpartnerek tartalmazhat elérhetőségi adatait a tanúsítvány élettartama események által kiváltott értesítések küldéséhez. A névjegyadatokhoz van osztva, minden tanúsítvány a key vaultban. Egy értesítést küld egy eseményt minden tanúsítványt a key vaultban az összes megadott ügyfelet.  

Ha egy tanúsítási szabályzat automatikus megújítás értékre van állítva, majd egy értesítés érkezik a következő események.  

-   Igényelje a tanúsítvány
-   Tanúsítványmegújítás figyelmezteti, ha a tanúsítvány megújítása sikeres volt, vagy hiba történt, ha a tanúsítvány manuális megújítás igénylő után.  

 A tanúsítási szabályzat, amely manuálisan kell megújítani (csak az e-mailekhez), amikor egy értesítés érkezik, amikor a tanúsítvány megújításához.  

### <a name="certificate-access-control"></a>Tanúsítvány hozzáférés-vezérlés

 Hozzáférés-vezérlés tanúsítványokat a Key Vault kezeli, és biztosítja, amely tartalmazza ezeket a tanúsítványokat a Key Vault. A tanúsítványok hozzáférés-vezérlési házirend különbözik a hozzáférés-vezérlési házirendeket, a kulcsok és titkos az azonos Key vaultban. Felhasználók egy vagy több tároló, amely tárolja a tanúsítványok, az eset megfelelő szegmentálását és felügyeleti tanúsítványok fenntartása érdekében előfordulhat, hogy létre.  

 Egyszerű – ellenében, a titkos kulcsok a key vault, és szorosan tükör titkos objektum engedélyezett műveletek hozzáférési szabály használható a következő engedélyekkel:  

- Tanúsítványkezelési műveletek vonatkozó engedélyek
  - *Első*: A tanúsítvány bármely verzióját, illetve a jelenlegi verziójának lekérése 
  - *Lista*: Az aktuális tanúsítványokat vagy tanúsítvány verzióinak listázása  
  - *Frissítés*: A tanúsítvány frissítése
  - *Hozzon létre*: Hozzon létre egy Key Vault-tanúsítvánnyal
  - *Importálás*: Tanúsítvány anyag importálása a Key Vault-tanúsítvánnyal
  - *Törlés*: Törölje a tanúsítványt, a házirendet, és annak összes verzióját  
  - *helyreállítás*: Helyreállítás egy tanúsítvány törlése
  - *Biztonsági mentés*: Készítsen biztonsági másolatot a kulcstartóban található tanúsítvány
  - *Visszaállítás*: Key vault a biztonsági másolat tanúsítvány visszaállítása
  - *managecontacts*: A Key Vault tanúsítványpartnerek kezelése  
  - *manageissuers*: A Key Vault hatóságok/tanúsítványkiállítók kezelése
  - *getissuers*: A tanúsítvány hatóságok/kiállítók beolvasása
  - *listissuers*: A tanúsítvány hatóságok/tanúsítványkibocsátók listája  
  - *setissuers*: Létrehozni vagy frissíteni a Key Vault-tanúsítvánnyal hatóságok/kiállítók  
  - *deleteissuers*: A Key Vault-tanúsítvánnyal hatóságok/kiállítók törlése  
 
- Privilegizált műveleteket vonatkozó engedélyek
  - *végleges törlés*: Végleges törlés (végleges törlése) tanúsítvány törlése

További információkért lásd: a [tanúsítvány-műveletek és a Key Vault REST API-hivatkozás](/rest/api/keyvault). Engedélyek meghatározása a további információkért lásd: [- tárolók létrehozása vagy frissítése](/rest/api/keyvault/vaults/createorupdate) és [tároló - hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Az Azure tárfiókkulcs-kezelés

A Key Vault az Azure storage-fiók kulcsainak kezelheti:

- Belsőleg a Key Vault listázhatja az Azure storage-fiók kulcsok (sync). 
- Újragenerálja a Key Vault (rotálja) a kulcsokat rendszeres időközönként.
- Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.
- A Key Vault kezeli a storage-fiókok és a klasszikus tárfiókok kulcsait.

További információkért lásd: [Azure Key Vault Tárfiókkulcsokat](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Storage-fiók hozzáférés-vezérlés

A következő engedélyek használható egy felhasználó vagy alkalmazás egyszerű engedélyezésekor egy felügyelt tárfiókot műveletek végrehajtásához:  

- A felügyelt tárfiókot és SaS-definíciója műveletek engedélyek
  - *Első*: Storage-fiók adatainak beolvasása 
  - *Lista*: A Key Vault kezeli tárfiókok listája
  - *Frissítés*: Tárfiók frissítése
  - *Törlés*: Tárfiók törlése  
  - *helyreállítás*: Helyreállítás törölt tárfiókokat
  - *Biztonsági mentés*: Készítsen biztonsági másolatot egy storage-fiók
  - *Visszaállítás*: Biztonsági másolat storage-fiók egy Key Vaultot visszaállítása
  - *Állítsa be*: Létrehozni vagy frissíteni a storage-fiók
  - *regeneratekey*: Storage-fiókok megadott kulcsérték újragenerálása
  - *getsas*: Storage-fiókok egy SAS-definíció adatainak beolvasása
  - *listsas*: SAS-definíciót tároló storage-fiókok listázása
  - *deletesas*: Egy tárfiókból egy SAS-definíció törlése
  - *setsas*: Létrehozása vagy frissítése egy új SAS definíció/attribútumok storage-fiókok

- Privilegizált műveleteket vonatkozó engedélyek
  - *végleges törlés*: Végleges törlés (végleges törlése) egy felügyelt tárfiókot

További információkért lásd: a [a Key Vault REST API-referencia a tárolási fiók műveletek](/rest/api/keyvault). Engedélyek meghatározása a további információkért lásd: [- tárolók létrehozása vagy frissítése](/rest/api/keyvault/vaults/createorupdate) és [tároló - hozzáférési szabályzat frissítése](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Lásd még:

- [Hitelesítési kérelmek és válaszok](authentication-requests-and-responses.md)
- [A Key Vault-verziók](key-vault-versions.md)
- [Key Vault fejlesztői útmutató](/azure/key-vault/key-vault-developers-guide)
