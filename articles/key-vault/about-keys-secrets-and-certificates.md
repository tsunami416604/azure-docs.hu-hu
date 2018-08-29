---
title: A kulcsok, titkos kódok és tanúsítványok ismertetése
description: REST-felület és KV fejlesztői részleteket – áttekintés
services: key-vault
documentationcenter: ''
author: BryanLa
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: bryanla
ms.openlocfilehash: f36e0e3ddc605d960ed764252308cbf09578832c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126142"
---
# <a name="about-keys-secrets-and-certificates"></a>Tudnivalók a kulcsok, titkos kódok és tanúsítványok
Az Azure Key Vault lehetővé teszi, hogy a felhasználók tárolhatják és a Microsoft Azure-környezeten belül titkosítási kulcsok használata. A Key Vault több kulcstípusok és algoritmusokat támogatja, és lehetővé teszi az értékes kulcsok használható hardveres biztonsági modulok (HSM). Emellett a Key Vault lehetővé teszi a felhasználók számára a titkos kulcsok biztonságos tárolása. A titkok olyan mérete korlátozott oktett objektumok nem adott sémantikou. A Key Vault támogatja a tanúsítványok, kulcsok és titkos kulcsok épülnek, és adja hozzá az automatikus megújítási szolgáltatást is.

Azure Key Vaulttal kapcsolatos további általános információkért lásd: [Mi az Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**A Key Vault általános adatok**

-   [Szabványok támogatása](#BKMK_Standards)
-   [Adattípusok](#BKMK_DataTypes)  
-   [Objektumok, azonosítók és verziókezelés](#BKMK_ObjId)  

**Kulcsok**

-   [Kulcsok és a kulcs típusa](#BKMK_KeyTypes)  
-   [Az RSA algoritmus](#BKMK_RSAAlgorithms)  
-   [RSA-HSM algoritmusok](#BKMK_RSA-HSMAlgorithms)  
-   [Titkosítási védelme](#BKMK_Cryptographic)
-   [Kulcsműveletek](#BKMK_KeyOperations)  
-   [Legfőbb attribútumai](#BKMK_KeyAttributes)  
-   [Kulcs címkék](#BKMK_Keytags)  

**Tudnivalók a titkos kulcsok** 

-   [Titkos kulcsok használata](#BKMK_WorkingWithSecrets)  
-   [Titkos kód attribútumok](#BKMK_SecretAttrs)  
-   [Titkos kódok](#BKMK_SecretTags)  
-   [Titkoskulcs-hozzáférés vezérlése](#BKMK_SecretAccessControl)  

**A tanúsítványokról**

-   [A tanúsítvány összeállítás](#BKMK_CompositionOfCertificate)  
-   [Tanúsítvány-attribútumok és címkék](#BKMK_CertificateAttributesAndTags)  
-   [Tanúsítvány-házirend](#BKMK_CertificatePolicy)  
-   [Tanúsítvány kiállítója](#BKMK_CertificateIssuer)  
-   [Tanúsítványpartnerek](#BKMK_CertificateContacts)  
-   [Tanúsítvány hozzáférés-vezérlés](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>A Key Vault általános adatok

Az alábbi szakaszok nyújtanak vonatkozó általános információk megvalósítása az Azure Key Vault szolgáltatás között.

###  <a name="BKMK_Standards"></a> Szabványok támogatása

A JavaScript Object Notation (JSON) és a JavaScript Object aláírás és titkosítás (JOSE) előírások olyan fontos háttér-információkat.  

-   [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (KMH)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> adattípusok

Tekintse meg a [JOSE specifikációk](#BKMK_Standards) kulcsok, titkosítási és aláírási vonatkozó adatok esetében.  

-   **algoritmus** -kulcs művelet esetén például RSA1_5 egy támogatott algoritmus  
-   **ciphertext-érték** -szöveg oktettben Base64URL használatával lett kódolva cipher  
-   **a kivonatoló-érték** -kivonatoló algoritmust, Base64URL használatával lett kódolva kimenete  
-   **kulcs-típusú** – a támogatott kulcs típusú, például RSA.  
-   **egyszerű szöveges-érték** – egyszerű szöveges oktettben Base64URL használatával  
-   **aláírás-érték** – kimeneti Base64URL használatával lett kódolva aláírási algoritmus  
-   **base64URL** – egy Base64URL [RFC4648] kódolású bináris érték  
-   **logikai** – igaz vagy hamis  
-   **Identitás** - identitás az Azure Active Directory (AAD).  
-   **IntDate** – 1970. a másodpercek számát jelölő JSON decimális-01-ig az adott UTC szerinti dátuma/ideje (UTC) 01T0:0:0Z. Lásd: [RFC3339] dátum részletei és általánosságban időpontok és különösen (UTC).  

###  <a name="BKMK_ObjId"></a> Objektumok, azonosítók és verziókezelés

Az Azure Key Vaultban tárolt objektumok megtartják a verziók, amikor létrejön egy objektum új példányát, és minden verzió egy egyedi azonosító és URL-CÍMÉT. Az objektum létrehozásakor egy egyedi Alkalmazásverzió-azonosító van megadva, és az objektum aktuális verziója van megjelölve. Az azonos nevű objektum egy új példány létrehozása lehetővé teszi az új objektum egyedi verzióazonosító, és miatt a válnak a jelenlegi verziót.  

Objektumok az Azure Key Vault használatával az aktuális azonosítót vagy egy verzió-specifikus azonosító lehet megoldani. Például adja meg a "MasterKey" nevű kulcsot, az aktuális azonosítóval rendelkező műveletek végrehajtása hatására a rendszer az elérhető legújabb verzióra. A rendszer, hogy az objektum adott verzióját használja a verziófüggő azonosítóval műveleteket okozza.  

Objektumok belül az Azure Key Vault URL-cím használatával, hogy nincs a rendszerben, függetlenül attól, földrajzi hely, két objektum rendelkezik az azonos URL-cím egyedileg azonosítja. Objektum teljes URL-címe az objektumazonosító nevezik, és a egy előtagot részét, amely azonosítja a Key Vault, a objektumtípus áll, a felhasználó a megadott objektum nevét, és a egy objektum verziója. Az objektum neve, kis-és nagybetűket, és nem módosítható. Azonosítók, amely tartalmazza az objektum verzió alapja azonosítók nevezzük.  

További információkért lásd: [hitelesítési kérelmek és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatás key vault neve.<br /><br /> A Key Vault neve a felhasználó ki van jelölve, és globálisan egyedi.<br /><br /> A kulcstároló neve 3–24 karakter hosszúságú sztring lehet, és csak a következőket tartalmazhatja: 0–9, a–z, A–Z, és -.|  
|`object-type`|Az objektumot, vagy a "kulcsok", vagy a "titok" típusú.|  
|`object-name`|Egy `object-name` a felhasználó által megadott név és a egy Key Vaultot egyedinek kell lennie. A név nem lehet 1 – 127 karakter hosszúságú csak 0-9, a – z, A-Z, - és.|  
|`object-version`|Egy `object-version` egy rendszer által létrehozott, az objektumok egyedi verziójának megoldására opcionálisan használt 32 karakter hosszúságú karakterlánc-azonosító.|  

## <a name="key-vault-keys"></a>A Key Vault-kulcsok

###  <a name="BKMK_KeyTypes"></a> Kulcsok és a kulcs típusa

Titkosítási kulcsok Azure Key vaultban JSON webes kulcs [JWK] objektumként jelennek meg. Az alap JWK/JWA előírásoknak is ki vannak bővítve engedélyezéséhez az egyedi az Azure Key Vault-megvalósítás kulcstípusok, például a kulcsok Azure Key Vault biztonságos szállítása engedélyezéséhez a HSM szállítójával (Thales) adott csomagolás használatával történő importálását például kulcsok, Előfordulhat, hogy csak az használni őket az Azure Key Vault HSM.  

- **"Soft" kulcsok**: A kulcs a szoftver a Key Vault által feldolgozott, de titkosítása egy hardveres biztonsági modulban rendszer kulcs használatával. Az ügyfelek előfordulhat, hogy egy meglévő RSA vagy EK kulcs importálása, vagy kérje a az Azure Key Vault generál egyet.
- **"Rögzített" kulcsok**: egy kulcs feldolgozása hardveres biztonsági modul (HSM-mel). Ezek a kulcsok az Azure Key Vault HSM biztonsági Világai egyik védett (van egy Biztonságivilág az elszigetelés földrajzi hely szerint). Az ügyfelek előfordulhat, hogy egy RSA vagy EK kulcs, vagy letölthető formában vagy exportálásával egy kompatibilis a HSM-eszközről, importálása, vagy kérje meg, hogy az Azure Key Vault generál egyet. Ilyen típusú kulcs hozzáadja a T-attribútumot a JWK szerezze be a HSM-kulcs adatai számára.

     További információ a földrajzi határokon belül: [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Az Azure Key Vault támogatja az RSA és az elliptikus görbe alapú kulcsok csak; jövőbeli kiadások például előfordulhat, hogy támogatja a más kulcstípusok szimmetrikus.

-   **EK**: "Soft" elliptikus görbe alapú kulcsot.
-   **HSM-EK**: "Merevlemez" elliptikus görbe alapú kulcsot.
-   **RSA**: "Soft" RSA-kulcsot.
-   **Az RSA-HSM**: "Rögzített" RSA-kulcsot.

Az Azure Key Vault támogatja a 2048, 3072 és 4096-méretek RSA-kulcsok, és az elliptikus görbe alapú kulcsok írja be a P-256, p-384, p-521 és P-256_K.

### <a name="BKMK_Cryptographic"></a> Titkosítási védelme

A titkosítási modulok által használt Azure Key Vaultban, HSM, illetve szoftver –, hogy-e FIPS használatával érvényesített. Nem kell különösebb FIPS-módban történő futtatására. Ha Ön **létrehozása** vagy **importálása** HSM által védett kulcsokat, amelyek garantált belül érvényesítve a FIPS 140-2 2 vagy magasabb szintű hardveres biztonsági modulok dolgozzák. Ha Ön **létrehozása** vagy **importálása** kulcsokat, a szoftveres védelemmel ellátott majd belül titkosítási moduljait feldolgozása érvényesítve a FIPS 140-2 1 vagy magasabb szintű. További információkért lásd: [kulcsokat és a kulcstípusok](#BKMK_KeyTypes).

###  <a name="BKMK_ECAlgorithms"></a> EK algoritmusok
 Az Azure Key Vault HSM-EK és a kulcsokat a következő algoritmus azonosítók használata támogatott. 

#### <a name="signverify"></a>BEJELENTKEZÉSI/ELLENŐRZÉSE

-   **ES256** – digests ECDSA az SHA-256 algoritmust és görbe p-256 használatával létrehozott kulcsokat. Ez az algoritmus leírása: [RFC7518].
-   **ES256K** – digests ECDSA az SHA-256 algoritmust és a görbe P-256_K létre kulcsokat. Ez az algoritmus szabványügyi szervezet függőben van.
-   **ES384** – az SHA-384 ECDSA digests, és a görbe p-384 létre kulcsokat. Ez az algoritmus leírása: [RFC7518].
-   **ES512** – az SHA-512 ECDSA digests, és a görbe p-521 létre kulcsokat. Ez az algoritmus leírása: [RFC7518].

###  <a name="BKMK_RSAAlgorithms"></a> Az RSA algoritmus  
 A következő algoritmus azonosítók RSA és az RSA-HSM kulcsok az Azure Key Vault használata támogatott.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁSI/VISSZAFEJTÉSI

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] kulcsú titkosítás  
-   **Az RSA-OAEP** – RSAES optimális aszimmetrikus titkosítási Padding (OAEP) [RFC3447], szakasz A.2.1 az RFC 3447 által meghatározott alapértelmezett paraméterek használatával. Alapértelmezett paraméterek SHA-1 algoritmussal SHA-1 kivonatoló függvényt és a egy MGF1 maszk generációs funkcióját használja.  

#### <a name="signverify"></a>BEJELENTKEZÉSI/ELLENŐRZÉSE

-   **RS256** – RSASSA-PKCS-v1_5 SHA-256 használatával. Az alkalmazás megadott kivonatoló érték SHA-256 használatával kell számolni, és 32 bájt hosszú lehet.  
-   **RS384** – RSASSA-PKCS-v1_5 SHA-384-et használ. Az alkalmazás megadott kivonatoló érték SHA-384-et használó kell számolni, és 48 bájt hosszú lehet.  
-   **RS512** – RSASSA-PKCS-v1_5 SHA-512 használatával. Az alkalmazás megadott kivonatoló érték SHA-512 használatával kell számolni, és 64 bájt hosszú lehet.  
-   **RSNULL** – lásd: [RFC2437], egy speciális használati bizonyos TLS forgatókönyvek engedélyezéséhez.  

###  <a name="BKMK_KeyOperations"></a> Kulcsműveletek

Az Azure Key Vault támogatja a következő műveleteket az objektumok:  

-   **Hozzon létre**: engedélyezi, hogy egy ügyfél hozzon létre egy kulcsot az Azure Key Vaultban. A kulcs értékét az Azure Key Vault és tárolása és az ügyfél nem szabadul fel. Aszimmetrikus (és a jövőben elliptikus görbe alapú és Symmetric) lehetséges, hogy hozza létre a kulcsokat, az Azure Key Vaultban.  
-   **Importálása**: lehetővé teszi, hogy egy ügyfél egy meglévő kulcs importálása az Azure Key Vaultba. Aszimmetrikus (és a jövőben elliptikus görbe alapú és Symmetric) kulcsok Azure Key Vault használatával számos különböző csomagolási módszerrel belül egy JWK-szerkezet importálható.  
-   **Frissítés**: lehetővé teszi, hogy a korábban az Azure Key Vaultban tárolt kulcs társított metaadatokat (kulcsattribútum) módosításához szükséges engedélyekkel rendelkező ügyfelet.  
-   **Törlés**: lehetővé teszi, hogy egy ügyfél a megfelelő engedélyekkel az Azure Key Vault kulcs törlése.  
-   **Lista**: lehetővé teszi, hogy egy ügyfél egy adott Azure Key vaultban a összes kulcsok listázását.  
-   **Verziók listázása**: lehetővé teszi, hogy az ügyfél egy adott kulcs egy adott Azure Key vaultban a összes verzió listázása.  
-   **Első**: engedélyezi, hogy egy ügyfél lekérheti az egy adott kulcs egy Azure Key vaultban nyilvános részeit.  
-   **Biztonsági mentés**: egy kulcs, védett formátumban exportálja.  
-   **Visszaállítás**: importál egy biztonsági másolatból kulcsot.  

További információkért lásd: [kulcs a Key Vault REST API-referencia-műveletek](/rest/api/keyvault).  

Kulcs létrehozása az Azure Key Vaultban, után a következő titkosítási műveleteket végezhetik a kulccsal:  

-   **Bejelentkezés, és győződjön meg arról**: szigorúan, ez a művelet nem "bejelentkezési hash" vagy "a kivonatoló hitelesítés", az Azure Key Vault nem támogatja a kivonatolás tartalom jogosultságkód létrehozásának részeként. Alkalmazások a helyileg alá kell írni, majd kérik, hogy az Azure Key Vault bejelentkezési a kivonatot adatok kell kivonata. Az alkalmazásokat, amelyek nem férhetnek megosztottkulcs-anyag [nyilvános]; a kényelem műveletként támogatott aláírt kivonatok ellenőrzése azt javasoljuk, hogy, ajánlott alkalmazásteljesítmény-, ellenőrizze a műveletek elvégzése helyi.  
-   **Titkosítási kulcs / Alkalmazásburkoló**: az Azure Key Vaultban tárolt kulcs védelme egy másik kulcsot, általában egy tartalom szimmetrikus titkosítási kulcsot (CEK) is használható. Ha a kulcsot az Azure Key Vaultban aszimmetrikus, kulcsú titkosítás, például RSA-OAEP és a WRAPKEY és UNWRAPKEY műveletek megfelelői TITKOSÍTÁSA/VISSZAFEJTÉSE. Ha a kulcsot az Azure Key Vaultban szimmetrikus, alkalmazásburkoló kulccsal; Ha például AES-KW. Az alkalmazásokat, amelyek nem férhetnek megosztottkulcs-anyag [nyilvános]; a kényelem is támogatott, a WRAPKEY művelet Javasoljuk, hogy a legjobb teljesítményhez alkalmazás WRAPKEY műveleteket helyileg.  
-   **Titkosítása és visszafejtése**: az Azure Key Vaultban tárolt kulcs titkosítására vagy visszafejtésére az adatokat, a mérete határozza meg a kulcs típusa és a kiválasztott titkosítási algoritmus egyetlen blokkot is használható. A titkosítás művelet biztosított kényelmi célokat szolgál, az alkalmazásokat, amelyek nem férhetnek megosztottkulcs-anyag [nyilvános]; Javasoljuk, hogy, a legjobb alkalmazást teljesítmény, a titkosítás helyileg hajthat végre műveleteket.  

WRAPKEY/UNWRAPKEY aszimmetrikus kulcsokkal felesleges tűnhet, mivel a művelet TITKOSÍTÁSA/VISSZAFEJTÉSE egyenértékű, amíg a különböző műveletek használatát számít fontos adja meg a szemantikai, és ezeket a műveleteket és konzisztencia engedélyezési szétválasztása Ha más kulcstípusok a szolgáltatás által támogatott.  

Az Azure Key Vault nem támogatja az EXPORTÁLÁSI műveleteket: egy kulcs kiosztása után már a rendszer ne lehessen kinyerni, vagy módosította a kulcs adatai.  Azonban az Azure Key Vaultban, akinek szüksége saját kulcs más felhasználói használati esetek, vagy után törölték az Azure Key Vaultban, előfordulhat, hogy használja a biztonsági MENTÉSI és VISSZAÁLLÍTÁSI műveletek exportálási/importálási a kulcsot, védett formátumban. A biztonsági MENTÉSI művelet által létrehozott kulcsok, amelyek nem használható az Azure Key Vault kívül. Az IMPORTÁLÁSI művelet azt is megteheti, szemben az Azure Key Vault több példány is használható.  

Felhasználók korlátozhatják a titkosítási műveletek, amely az Azure Key Vault támogatja a key_ops tulajdonságot használja a JWK objektum kulcs alapon.  

JWK-objektumokra vonatkozó további információkért lásd: [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Legfőbb attribútumai

A kulcs adatai mellett a következő attribútumok adható meg. Egy JSON-kérelem a attribútumok kulcsszót, és kapcsos zárójelek a(z) {' "}", szükség, akkor is, ha a megadott attribútum sem.  

- *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Megadja, hogy a kulcs engedélyezve van, és a titkosítási műveletek gyakorlatot. A *engedélyezve* attribútum együtt használatos *nbf* és *exp*. Ha a művelet között történik *nbf* és *exp*, akkor lesz csak engedélyezett, ha *engedélyezve* értékre van állítva **igaz**. Műveletek kívül a *nbf* / *exp* ablak automatikusan engedélyezettek, kivéve a művelet bizonyos [adott feltételek](#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, nem kötelező, alapértelmezett értéke jelenleg. A *nbf* (nem előtt) attribútum azonosítja az idő, ameddig a kulcs nem használható a titkosítási műveletek, kivéve a művelet bizonyos [adott feltételek](#BKMK_key-date-time-ctrld-ops). A feldolgozása a *nbf* attribútumot igényel, hogy az aktuális dátum/idő után kell vagy a nem egyenlő-szereplő dátum/idő előtt a *nbf* attribútum. Az Azure Key Vault rendelkezhetnek néhány kisebb eltérést általában nem több mint néhány percet, hogy torzulása óra figyelembe. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *Exp*: IntDate, nem kötelező, alapértelmezett érték a "végtelen". A *exp* (lejárati ideje) attribútum azonosítja a lejárati időt, vagy azt követően, amely nem használja a rendszer kriptográfiai művelet, kivéve a művelet bizonyos [adott feltételek](#BKMK_key-date-time-ctrld-ops). A feldolgozása a *exp* attribútumot igényel, hogy az aktuális dátumot és időpontot kell-e, mielőtt a lejárati dátum/idő szerepel a *exp* attribútum. Az Azure Key Vault rendelkezhetnek néhány kisebb eltérést általában nem több mint néhány percet, hogy torzulása óra figyelembe. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

Nincsenek további csak olvasható attribútumokat választ, amely tartalmazza a kulcsattribútum részét képező:  

- *létrehozott*: IntDate, nem kötelező. A *létrehozott* attribútum azt jelöli, ez a verzió, a kulcs létrehozásakor. Ez az attribútum hozzáadása előtt létrehozott kulcsok null értékű. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A *frissített* attribútum azt jelöli, amikor ez a kulcs verziója módosult. Ez a legutóbb frissített Ez az attribútum hozzáadása előtt kulcsok null értékű. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

IntDate és más adatok további információkért lásd: [adattípusok](#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Dátum-idő ellenőrzött műveletek

Nincs még érvényes, és a lejárt kulcsok, ezen kívül a *nbf* / *exp* ablakban fog működni az **visszafejtéséhez**, **kicsomagolása** és **ellenőrzése** operations (nem ad vissza 403, tiltott). A közösségértékek nem még érvényes állapot segítségével, hogy a kulcs kell tesztelni, mielőtt éles környezetben való használathoz. A közösségértékek használata a lejárt állapotban, hogy a helyreállítási műveletek jött létre, amikor a kulcs érvényes volt-adatokat. Ezenkívül letilthatja a hozzáférést egy kulcsot a Key Vault-szabályzatok, vagy frissítésével a *engedélyezve* a kulcsattribútum **false (hamis)**.

További információ az adatok típusok:, [adattípusok](#BKMK_DataTypes).

Más lehetséges attribútumok kapcsolatban további információért tekintse meg a [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Kulcs címkék

További alkalmazások – is szolgáltatásspecifikus metaadatokat címkék formájában is megadhat. Az Azure Key Vault támogatja a legfeljebb 15 címkék, amelyek mindegyike egy 256 karakter hosszúságú név- és egy 256 karakter hosszúságú lehet.  

>[!Note]
>A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus; kulcsok, titkos kódok és tanúsítványok.

###  <a name="BKMK_KeyAccessControl"></a> Kulcshozzáférés-vezérlés

Hozzáférés-vezérlés a Key Vault által kezelt kulcsok egy Key Vaultot, amely a kulcsok a tároló szintjén van megadva. Egy hozzáférés-vezérlési házirend kulcsok esetén, amely különbözik az azonos Key Vault titkos hozzáférés-vezérlési házirend van. Felhasználók előfordulhat, hogy hozzon létre egy vagy több tárolók, kulcsok tárolására, és a forgatókönyv megfelelő szegmentálását és kulcsok kezelése fenntartásához szükséges. Hozzáférés-vezérlés a kulcsok nem függ a hozzáférés-vezérlés a titkos kulcsok.  

A következő is adhatók engedélyek, az egy / felhasználó / service egy tároló kulcsainak hozzáférés-vezérlési bejegyzés egyszerű rendszeresen. Ezek az engedélyek szorosan tükrözik a kulcs-objektum engedélyezett műveletek:  

-   *Hozzon létre*: új kulcsok létrehozása
-   *első*: a nyilvános részét egy kulcsot, valamint a hozzá tartozó attribútumok olvasása
-   *lista*: a kulcsok vagy a key vaultban tárolt kulcs verzióinak listázása
-   *frissítés*: egy kulcs az attribútumok frissítése
-   *Törlés*: a kulcs-objektum törlése
-   *bejelentkezési*: a kulcs aláírásához digests használata
-   *Győződjön meg arról*: a kulcs használatával emésztett ellenőrzése
-   *wrapKey*: a kulcs használatával egy szimmetrikus kulcs védelme
-   *unwrapKey*: a kulcs használatát a védelem beburkolt szimmetrikus kulcsokat
-   *titkosítása*: a kulcs használatával egy tetszőleges bájtsorrendet védelme
-   *visszafejtés*: a kulcs használatával bájt sorozatát feloldása
-   *importálása*: kulcs importálása egy kulcstartóba
-   *biztonsági mentési*: a key vault kulcs biztonsági mentése
-   *visszaállítás*: állítsa vissza a biztonsági mentése a key vault kulcs
-   *az összes*: minden engedély

## <a name="key-vault-secrets"></a>A Key Vault titkos kulcsok 

###  <a name="BKMK_WorkingWithSecrets"></a> Titkos kulcsok használata

Az Azure Key Vault titkos oktett sorozatok maximális mérettel minden 25 kilobájt. Az Azure Key Vault szolgáltatás nem biztosít semmilyen szemantika titkok; csupán fogadja az adatok, titkosítja és tárolja azokat a titkos azonosító, az "id", a titkos kulcs lekéréséhez egy későbbi időpontban esetleg használt visszaadása.  

Szigorúan bizalmas adatok esetén az ügyfelek érdemes kiegészítő védelmi rétegeket, az Azure Key Vault; tárolt adatok például előre adatok titkosításával egy külön védelmi kulcs használatával.  

Az Azure Key Vault titkos kódok contentType mező is támogatja. Ügyfelek előfordulhat, hogy adja meg a tartalom típusát, a "contentType", egy titkos kulcsot, amelyek segítik az adatok értelmezése során, amikor lekéri azt. Ez a mező maximális hossza 255 karakter lehet. Nincsenek előre meghatározott értékek. A javasolt használata gyors tippként annyit elmondunk, az adatok értelmezéséhez. Implementálhat például tárolja a jelszavakat és a tanúsítványok titkos kódként akkor használja ezt a mezőt, amely jelzi. Nincsenek előre meghatározott értékek.  

###  <a name="BKMK_SecretAttrs"></a> Titkos kód attribútumok

A titkos adatok mellett a következő attribútumokkal lehet megadni:  

- *Exp*: IntDate, nem kötelező, alapértelmezett érték a **örökre**. A *exp* (lejárati ideje) attribútum azonosítja a lejárati időt, vagy azt követően, amely a titkos adatokat kell nem olvashatók be, kivéve az [adott helyzetekben](#BKMK_secret-date-time-ctrld-ops). Ez a mező **tájékoztató** célra használja, csak, hogy tájékoztatja a felhasználókat a key vault szolgáltatást, hogy egy adott titkos kód nem használható. Az érték egy IntDate értéket tartalmazó számnak kell lennie.   
- *NBF*: IntDate, nem kötelező, alapértelmezett érték a **most**. A *nbf* (nem előtt) attribútum azonosítja az idő, ameddig a titkos adatokat kell nem olvashatók be, kivéve az [adott helyzetekben](#BKMK_secret-date-time-ctrld-ops). Ez a mező **tájékoztató** csak célra használja. Az érték egy IntDate értéket tartalmazó számnak kell lennie. 
- *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Ez az attribútum meghatározza, hogy a titkos adatok lekérhetők-e. Az engedélyezett attribútum együtt használatos, és *exp* között-művelet bekövetkezésekor exp, akkor lesz csak engedélyezett, ha engedélyezve van, és értékre van állítva **igaz**. Műveletek kívül a *nbf* és *exp* ablak a program automatikusan nem megengedett, kivéve a [adott helyzetekben](#BKMK_secret-date-time-ctrld-ops).  

Nincsenek további csak olvasható attribútumok telepítésben lévő semmilyen választ, amely tartalmazza a titkos attribútumok:  

- *létrehozott*: IntDate, nem kötelező. A létrehozott attribútum jelzi, hogy jelen verziója a titkos kulcs létrehozásakor. Ez például a következők Ez az attribútum hozzáadása előtt létrehozott null értékű. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy ez a verzió, a titkos kód frissítésekor. Ez az érték értéke null, például a következők a legutóbb frissített Ez az attribútum hozzáadása előtt. Az érték egy IntDate értéket tartalmazó számnak kell lennie.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Dátum-idő ellenőrzött műveletek

A titkos kulcs **első** kívül nem még érvényes, és a lejárt titkos adatait, a fog működni a művelet a *nbf* / *exp* ablak. A titkos kód hívása **lekérése** művelet, nem még érvényes titkos kulcs, a tesztelési célokra használható. Beolvasása (**első**ing) egy lejárt titkos helyreállítási műveletekhez használható.

További információ az adatok típusok:, [adattípusok](#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Titkoskulcs-hozzáférés vezérlése

Hozzáférés-vezérlés az Azure Key vaultban felügyelt titkos kulcsok szintjén is, hogy működik-e a tároló a ezeket a titkos kulcsok a Key Vault biztosítja. Nincs egy hozzáférés-vezérlési házirend titkos kulcsok számára, amely különbözik a hozzáférés-vezérlési szabályzat az azonos Kulcstartóban lévő kulcsok. Felhasználók előfordulhat, hogy hozzon létre legalább egy tároló titkos kulcsok tárolására, és a forgatókönyv megfelelő szegmentálását és a titkos kulcsok kezelése fenntartásához szükséges. Hozzáférés-vezérlést a titkos kulcsok hozzáférés-vezérlés függetlenek.  

A következő engedélyeket is használható, egyszerű alapon, az egy tároló titkos kódok hozzáférés-vezérlési bejegyzés, és szorosan tükrözik a titkos objektum engedélyezett műveletek:  

-   *Állítsa be*: új titkos kulcsok létrehozása  
-   *első*: olvassa el a titkos kulcs  
-   *lista*: a titkos kulcsok vagy a Key Vaultban tárolt titkos kulcs verzióinak listázása  
-   *Törlés*: a titkos kulcs törlése  
-   *az összes*: minden engedély  

Titkos kódok való használatáról további információkért lásd: [a Key Vault REST API-referencia a titkos műveletek](/rest/api/keyvault).  

###  <a name="BKMK_SecretTags"></a> Titkos kódok  
További alkalmazások – is szolgáltatásspecifikus metaadatokat címkék formájában is megadhat. Az Azure Key Vault támogatja a legfeljebb 15 címkék, amelyek mindegyike egy 256 karakter hosszúságú név- és egy 256 karakter hosszúságú lehet.  

>[!Note]
>A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus; kulcsok, titkos kódok és tanúsítványok.

## <a name="key-vault-certificates"></a>A Key Vault-tanúsítványok

A x509 kezelését biztosítja a Key Vault tanúsítványokkal támogatási tanúsítványok és az alábbi viselkedés tapasztalható:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosát, hogy hozzon létre egy tanúsítványt, vagy egy meglévő tanúsítvány importálása a Key Vault létrehozási folyamata keresztül. Ez magában foglalja, mindkettő önaláírt, és a hitelesítésszolgáltató generált tanúsítványok.
-   Lehetővé teszi, hogy a Key Vault-tanúsítvány tulajdonosának biztonságos tárolását és kezelését a X509 megvalósításához tanúsítványok titkos kulcs adatai közreműködése nélkül.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosát, hogy olyan szabályzatot, amely arra utasítja a Key Vault a tanúsítvány életciklusának kezelésére.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonos kapcsolattartási kapcsolatos adatok megadása az értesítésre életciklus-események lejárati és a tanúsítvány megújítása.  
-   Támogatja az automatikus megújítását a kiválasztott kiállítók – Key Vault partner X509 tanúsítvány-szolgáltatók / hitelesítésszolgáltatók.

>[!Note]
>Nem-termékekért felelős csoportokkal együttműködésben szolgáltatók/hitelesítésszolgáltatók is használhatók, de nem támogatja az automatikus megújítási funkciót.

###  <a name="BKMK_CompositionOfCertificate"></a> A tanúsítvány összeállítás

Key Vault-tanúsítvánnyal jön létre, amikor egy címezhető kulcsot és titkos kulcsot is jönnek létre ugyanazzal a névvel. A Key Vault-kulcs lehetővé teszi, hogy a kulcsfontosságú műveletek, és a Key Vault titkos kulcsából lehetővé teszi, hogy a tanúsítvány értékét, a titkos kulcs lekéréséhez. Key Vault-tanúsítvánnyal is tartalmazza nyilvános x509 tanúsítvány metaadatait.  

Az azonosítóját, valamint a tanúsítványok verziója, valamint a kulcsok és titkos kulcsok hasonlít. A Key Vault tanúsítvány válasz-címmel rendelkező kulcs és a titkos kulcsot a Key Vault verziójának használatával létrehozott egy adott verzióját érhető el.
 
![Tanúsítványok állíthatók olyan összetett objektumok](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Exportálható, vagy nem exportálható kulcs

Key Vault-tanúsítvánnyal jön létre, amikor azt kérhető le a megcímezhető titkos PFX vagy PEM formátumban a titkos kulccsal, ha a szabályzatot, a tanúsítvány létrehozásához használt jelzi, hogy a kulcs exportálható:. Ha a házirend a Key Vault-tanúsítvány létrehozásához használt jelezni a kulcs nem exportálható, majd a titkos kulcsot, nem az értéket, ha egy titkos kulcsot beolvashatók egy részét.  

A megcímezhető kulcs nem exportálható KV tanúsítványokkal relevánsabb lesz. A megcímezhető KV kulcs műveletek vannak leképezve a *keyusage* mezőjében a KV tanúsítási szabályzat a KV tanúsítvány létrehozásához használt.  

Kétféle típusú kulcs támogatottak – *RSA* vagy *RSA HSM* tanúsítványokkal. Exportálható csak engedélyezett az RSA, RSA HSM által nem támogatott.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Tanúsítvány-attribútumok és címkék

Mellett tanúsítvány metaadatait,-címmel rendelkező kulcs, és -címmel rendelkező titkos Key Vault-tanúsítvánnyal azt is tartalmazza, attribútumok és címkék.  

#### <a name="attributes"></a>Attribútumok

A tanúsítvány attribútumok attribútumok a megcímezhető kulcs és titkos kulcsot hozott létre, amikor KV tanúsítvány jön létre tükrözött esetén.  

Key Vault-tanúsítvánnyal rendelkezik a következő attribútumokat:  

-   *engedélyezett*: logikai érték, nem kötelező, alapértelmezett érték a **igaz**. Ez az attribútum jelzi a tanúsítványadatokat titkos vagy működtethető kulcsként beolvasható adható meg. Együtt használatos *nbf* és *exp* között-művelet bekövetkezésekor *nbf* és *exp*, akkor lesz csak engedélyezett, ha engedélyezve van értéke TRUE. Műveletek kívül a *nbf* és *exp* ablakban automatikusan engedélyezettek.  

Nincsenek a válaszban szereplő további csak olvasható attribútumok:

-   *létrehozott*: IntDate: azt jelzi, hogy a jelen verziója a tanúsítvány létrehozásakor.  
-   *frissített*: IntDate: azt jelzi, hogy a jelen verziója a tanúsítvány frissítésekor.  
-   *Exp*: IntDate: a x509 lejárati idejét értéke tartalmaz tanúsítványt.  
-   *NBF*: IntDate: a x509 időpontjától értéke tartalmaz tanúsítványt.  

> [!Note] 
> Ha lejár a Key Vault-tanúsítvánnyal, címezhető kulcs és titkos kulcs működésképtelenné válhatnak.  

#### <a name="tags"></a>Címkék

 Kulcs – érték sztringpárok, kulcsok és titkos kódok hasonló megadott szótára ügyfél.  

 > [!Note]
> A címkék olyan a hívó által is olvasható, ha rendelkeznek a *lista* vagy *első* engedélyt adott objektumtípus; kulcsok, titkos kódok és tanúsítványok.

###  <a name="BKMK_CertificatePolicy"></a> Tanúsítvány-házirend

A tanúsítási szabályzat ismerteti, hogyan hozhat létre, és a egy KV tanúsítvány életciklusának kezelése. A titkos kulccsal rendelkező tanúsítványt a key vaultban való importálásakor, egy alapértelmezett szabályzat jön létre a x509 olvasásával tanúsítványt.  

KV tanúsítvány létrehozásakor teljesen új házirend kell adni a Key vaultba tájékoztatnia a KV tanúsítvány verziója vagy a következő KV verziójának létrehozása. Szabályzat létrehozása után már nem szükséges az egymást követő-létrehozási műveletek létrehozásához a következő KV tanúsítvány verziók.  

Nincs KV tanúsítvány összes verzióját a házirendet csak egy példánya.  

Magas szinten a tanúsítási szabályzat a következőket tartalmazza:  

-   X509 tanúsítvány tulajdonságai: tartalmaz tulajdonos neve, a tulajdonos alternatív neveket stb. egy x509 létrehozásához használt tanúsítványt igényelni.  
-   Kulcs tulajdonságai: tartalmazza a kulcs típusa, kulcshossz, exportálható, és újra felhasználhatja kulcsmezők. Ezek a mezők kérje meg a key vault való hozzon létre egy kulcsot.  
-   Titkos kulcs tulajdonságai: tartalmazza a titkos kulcs tulajdonságai, például a tartalomtípusa címezhető titkos kulcs létrehozásához a titkos értéket, a tanúsítványt a titkos kód beolvasása.  
-   Élettartam műveletek: a KV tanúsítvány élettartama műveleteket tartalmazza. Minden egyes élettartamművelet tartalmazza:  

     - Eseményindító: nappal a lejárat vagy élettartama span százalékos előtt keresztül megadott  

     - Művelet: Adja meg a művelet típusa – *emailContacts* vagy *automatikus megújítás*  

-   Kibocsátója: A tanúsítvány kiállítója probléma x509 tanúsítványok használatával kapcsolatos paraméterek.  
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
|A letagadhatatlanság|aláírása, ellenőrzése| – |
|crlsign|aláírása, ellenőrzése| – |

###  <a name="BKMK_CertificateIssuer"></a> Tanúsítvány kiállítója

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

-Tanúsítványok portáljáról a kibocsátó objektumok létrehozásával kapcsolatos további információkért lásd: a [Key Vault Certificates blog](http://aka.ms/kvcertsblog)  

Key Vault lehetővé teszi, hogy a különböző kibocsátó szolgáltató konfigurációjának több kibocsátó objektumok létrehozásához. Kiállító objektum létrehozása után a név egy vagy több tanúsítvány-házirendek lehet hivatkozni. A kibocsátó objektumra hivatkozik arra utasítja a Key Vault használata a kibocsátó objektumban megadott konfiguráció, a x509 kérésekor tanúsítványt a CA-szolgáltató a tanúsítvány-létrehozási és -megújítás során.  

Kibocsátó objektumok jönnek létre a tárolót, és csak akkor használható KV tanúsítványokkal az ugyanahhoz a tárolóhoz.  

###  <a name="BKMK_CertificateContacts"></a> Tanúsítványpartnerek

Tanúsítványpartnerek tartalmazhat elérhetőségi adatait a tanúsítvány élettartama események által kiváltott értesítések küldéséhez. A névjegyadatokhoz van osztva, minden tanúsítvány a key vaultban. Egy értesítést küld egy eseményt minden tanúsítványt a key vaultban az összes megadott ügyfelet.  

Ha egy tanúsítási szabályzat automatikus megújítás értékre van állítva, majd egy értesítés érkezik a következő események.  

-   Igényelje a tanúsítvány
-   Tanúsítványmegújítás figyelmezteti, ha a tanúsítvány megújítása sikeres volt, vagy hiba történt, ha a tanúsítvány manuális megújítás igénylő után.  

 Ha egy tanúsítvány-házirend van beállítva, manuálisan megújított (csak e-mailekhez), majd értesítést küld, ha a tanúsítvány megújításához ideje.  

###  <a name="BKMK_CertificateAccessControl"></a> Tanúsítvány hozzáférés-vezérlés

 Hozzáférés-vezérlés tanúsítványokat a Key Vault kezeli és a egy Key Vaultot, amely a tanúsítványok a tároló szintjén. Egy hozzáférés-vezérlési házirend, tanúsítványok, amely különbözik a kulcsok és titkok az azonos Key Vault hozzáférés-vezérlési házirend van. Felhasználók előfordulhat, hogy hozzon létre egy vagy több tároló, amely a tanúsítványokat tárolja, és a forgatókönyv megfelelő szegmentálását és a felügyeleti tanúsítványok szükségesek.  

 Egyszerű – ellenében, a titkos kulcsok a key vault, és szorosan tükör titkos objektum engedélyezett műveletek hozzáférési szabály használható a következő engedélyekkel:  

-   *első*: lehetővé teszi, hogy a jelenlegi verziójának vagy bármelyik verziója, a tanúsítvány lekérése 
-   *lista*: lehetővé teszi, hogy az aktuális tanúsítványokat vagy a tanúsítvány listája  
-   *Törlés*: lehetővé teszi, hogy a tanúsítványt, a házirend-és annak összes verzióját delete  
-   *Hozzon létre*: lehetővé teszi a Key Vault-tanúsítvánnyal, hozzon létre.  
-   *importálása*: lehetővé teszi, hogy az importálás tanúsítvány anyag be a Key Vault-tanúsítvánnyal.  
-   *frissítés*: lehetővé teszi, hogy a tanúsítvány frissítése.  
-   *managecontacts*: lehetővé teszi, hogy a Key Vault tanúsítványpartnerek kezelése  
-   *getissuers*: lehetővé teszi, hogy a get-tanúsítványkiállítókat  
-   *listissuers*: lehetővé teszi, hogy a tanúsítvány tanúsítványkibocsátók listája  
-   *setissuers*: lehetővé teszi a létrehozni vagy frissíteni a Key Vault tanúsítványkiállítókat  
-   *deleteissuers*: lehetővé teszi, hogy a Key Vault tanúsítványkiállítókat delete  
-   *az összes*: az összes engedélyt megadja  

További információkért lásd: a [tanúsítvány-műveletek és a Key Vault REST API-hivatkozás](/rest/api/keyvault). 

## <a name="see-also"></a>Lásd még:

- [Hitelesítési kérelmek és válaszok](authentication-requests-and-responses.md)
- [A Key Vault-verziók](key-vault-versions.md)
- [Key Vault fejlesztői útmutató](/azure/key-vault/key-vault-developers-guide)
