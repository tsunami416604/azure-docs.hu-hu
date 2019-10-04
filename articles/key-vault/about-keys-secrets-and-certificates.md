---
title: Tudnivalók Azure Key Vault kulcsokról, titkokról és tanúsítványokról – Azure Key Vault
description: A kulcsok, titkok és tanúsítványok Azure Key Vault REST-felületének és fejlesztői adatainak áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: cca4f794fd3f84b991c7882307f74bcfadf6835b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241057"
---
# <a name="about-keys-secrets-and-certificates"></a>Kulcsok, titkos kódok és tanúsítványok

A Azure Key Vault lehetővé teszi Microsoft Azure alkalmazások és felhasználók számára, hogy több típusú titkos/kulcsos adatot tároljanak és használjanak:

- Titkosítási kulcsok: A több kulcs típust és algoritmust is támogat, és lehetővé teszi a hardveres biztonsági modulok (HSM) használatát a nagy értékű kulcsokhoz. 
- Titkok Biztonságos tárhelyet biztosít a titkok, például a jelszavak és az adatbázis-kapcsolatok karakterláncai számára.
- Tanúsítványok A a kulcsokra és titkokra épülő tanúsítványokat támogatja, és egy automatikus megújítási funkciót is felvesz.
- Azure Storage: Kezelheti az Azure Storage-fiók kulcsait. Belsőleg Key Vault a kulcsokat egy Azure Storage-fiókkal listázhatja (szinkronizálhatja), és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. 

További általános információk a Key Vaultről: [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

A következő részekben a Key Vault szolgáltatás megvalósítására vonatkozó általános információk találhatók.

### <a name="supporting-standards"></a>Támogató szabványok

A JavaScript Object Notation (JSON) és a JavaScript Object aláírási és titkosítási (JOSE) specifikációi fontos háttér-információk.  

-   [JSON-webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Adattípusok

Tekintse meg a következő témakört: a kulcsok, a titkosítás és az aláírás releváns adattípusaihoz tartozó JOSE-specifikációk.  

-   **algoritmus** – a kulcs műveletének támogatott algoritmusa, például RSA1_5  
-   **rejtjelezett-érték** -titkosítatlan szöveges oktettek, Base64URL használatával kódolva  
-   **kivonatoló érték** – a Base64URL használatával kódolt kivonatoló algoritmus kimenete  
-   **kulcs-típus** – az egyik támogatott kulcs típusa, például RSA (Rivest-a-Adleman).  
-   **egyszerű szöveges érték** – egyszerű szöveges oktettek, Base64URL használatával kódolva  
-   **aláírás-érték** – az Base64URL használatával kódolt aláírási algoritmus kimenete  
-   **base64URL** – a BASE64URL [RFC4648] kódolású bináris érték  
-   **logikai** – igaz vagy hamis  
-   **Identity (identitás** ) – Azure Active Directory identitása (HRE).  
-   **IntDate** – egy JSON decimális érték, amely az 1970-01-01T0:0: 0z UTC és a megadott UTC dátum/idő közötti másodpercek számát jelöli. A dátum-és időpontokra vonatkozó részletekért, valamint az általános és az UTC RFC3339 lásd:.  

### <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámozást kapnak, amikor egy objektum új példánya jön létre. Minden verzióhoz egyedi azonosító és URL-cím tartozik. Egy objektum első létrehozásakor a rendszer egyedi verzióazonosító-azonosítót kap, és az objektum aktuális verziójaként van megjelölve. Egy új példány ugyanazzal az objektummal való létrehozása lehetővé teszi az új objektum egyedi verziószámát, ami azt eredményezi, hogy az aktuális verzió lesz.  

A Key Vaultban lévő objektumok az aktuális azonosítóval vagy egy Version-specifikus azonosítóval kezelhetők. Például, ha egy kulcs a névvel `MasterKey`van megadva, az aktuális azonosítóval rendelkező műveletek végrehajtásával a rendszer a legújabb elérhető verziót használja. A verzió-specifikus azonosítóval végzett műveletek végrehajtása azt eredményezi, hogy a rendszer az objektum adott verzióját használja.  

Az objektumok egyedileg azonosíthatók a Key Vaulton belül egy URL-cím használatával. A rendszeren nincs két objektum ugyanazzal az URL-címmel, a földrajzi helytől függetlenül. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely a Key Vault, az Objektumtípus, a felhasználó által megadott objektumnév és az objektum verziószámát azonosítja. Az Objektumnév megkülönbözteti a kis-és nagybetűket, és nem változtathatók meg. Az objektum verziószámát nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító a következő általános formátumú:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatásban található kulcstartó neve.<br /><br /> A felhasználók a Key Vault neveket választják, és globálisan egyediek.<br /><br /> Key Vault neve csak 0-9, a-z, A-Z és-. karakterláncot tartalmazó 3-24 karakterből állhat.|  
|`object-type`|Az objektum típusa "Keys" vagy "Secrets".|  
|`object-name`|`object-name` Az a felhasználó által megadott név, és egyedinek kell lennie egy Key Vaulton belül. A névnek 1-127 karakterből álló karakterláncnak kell lennie, amely csak 0-9, a-z, A-Z és-.|  
|`object-version`|Az `object-version` egy rendszer által létrehozott, 32 karakterből álló karakterlánc-azonosító, amely opcionálisan * o-címekkel rendelkezik egy objektum egyedi verziójához.|  

## <a name="key-vault-keys"></a>Kulcsok Key Vault

### <a name="keys-and-key-types"></a>Kulcsok és kulcsok típusai

A Key Vaultban található titkosítási kulcsok JSON webkulcs [JWK] objektumként jelennek meg. Az alap JWK-/JWA-specifikációk is kiterjeszthetők a Key Vault implementációban egyedi kulcs típusú típusok engedélyezéséhez. Ha például a kulcsokat a HSM gyártótól függő csomagolás használatával importálja, a lehetővé teszi a kulcsok biztonságos szállítását, amelyek csak Key Vault HSM használhatók.  

- **"Soft" kulcsok**: A szoftverben Key Vault által feldolgozott kulcs, de a rendszer egy HSM-ben lévő rendszerkulcs használatával titkosítja a nyugalmi állapotban. Az ügyfelek importálhatók egy meglévő RSA-vagy EC-(elliptikus görbe-) kulcs, vagy kérhetik, hogy Key Vault létrehozzon egyet.
- **"Kemény" kulcsok**: Egy HSM-ben feldolgozott kulcs (hardveres biztonsági modul). Ezeket a kulcsokat a Key Vault HSM biztonsági világok egyikén védik (az elkülönítés fenntartása érdekében a földrajz egy biztonsági világa van). Előfordulhat, hogy az ügyfelek nem importálnak egy RSA-vagy EK-kulcsot, vagy egy kompatibilis HSM-eszközről exportálnak. Előfordulhat, hogy az ügyfelek Key Vault kérhetnek a kulcsok létrehozásához. Ez a kulcspár hozzáadja a key_hsm attribútumot a JWK a HSM-kulcs anyagának elvégzéséhez.

     A földrajzi határokra vonatkozó további információkért lásd: [Microsoft Azure Adatvédelmi központ](https://azure.microsoft.com/support/trust-center/privacy/)  

A Key Vault csak az RSA és az elliptikus görbe kulcsait támogatja. 

-   **EK**: "Soft" elliptikus görbe kulcsa.
-   **EC-HSM**: "Hard" elliptikus görbe kulcsa.
-   **RSA**: "Soft" RSA-kulcs.
-   **RSA-HSM**: "Hard" RSA-kulcs.

A Key Vault a 2048, 3072 és 4096 méretű RSA-kulcsokat támogatja. Key Vault támogatja a P-256, a P-384, a P-521 és a P-256K (SECP256K1) elliptikus görbe típusú kulcsokat.

### <a name="cryptographic-protection"></a>Titkosítási védelem

A Key Vault által használt kriptográfiai modulok (a HSM vagy a szoftverek) a FIPS (szövetségi adatfeldolgozási szabványok) hitelesítése. A FIPS-módban való futtatáshoz nem szükséges semmilyen speciális művelet. A HSM által védettként **létrehozott** vagy **importált** kulcsok a HSM-ben lesznek feldolgozva, amely a 2. szintű FIPS 140-2-es szintre van érvényesítve. A szoftver által védettként **létrehozott** vagy **importált** kulcsok feldolgozása az 1. szintű FIPS 140-2-es szintre ellenőrzött titkosítási modulokon belül történik. További információ: [kulcsok és kulcsok típusai](#keys-and-key-types).

###  <a name="ec-algorithms"></a>EK-algoritmusok
 A következő algoritmus-azonosítók támogatottak a Key Vault EC és EC-HSM kulcsaival. 

#### <a name="curve-types"></a>Görbe típusai

-   **P-256** – a (z) p-256-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.
-   **P-256K** – a másodperces görbe SECP256K1, amely [a 2. másodpercben van meghatározva: Javasolt elliptikus görbe tartományi paraméterek](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – a (z) p-384-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.
-   **P-521** – a (z) p-521-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.

#### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **ES256** – ECDSA a P-256 görbével létrehozott SHA-256 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** – ECDSA a P-256K által létrehozott SHA-256 kivonatokhoz és kulcsokhoz. Ez az algoritmus a normalizálás függőben van.
-   **ES384** – ECDSA a P-384 görbével létrehozott SHA-384 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** – ECDSA a P-521 görbével létrehozott SHA-512 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>RSA-algoritmusok  
 A következő algoritmus-azonosítók támogatottak az RSA és az RSA-HSM kulcsok esetében Key Vaultban.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁS/VISSZAFEJTÉS

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] key encryption  
-   **RSA-OAEP** -RSAES az optimális aszimmetrikus titkosítási kitöltés (OAEP) [RFC3447] használatával az a. 2.1. szakaszban az RFC 3447 által meghatározott alapértelmezett paraméterekkel. Ezek az alapértelmezett paraméterek az SHA-1 kivonatoló függvényét használják, és a MGF1 maszk generálási funkciója az SHA-1.  

#### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **RS256** -RSASSA-PKCS-V1_5 az SHA-256 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-256 értékkel kell kiszámítani, és 32 bájt hosszúságú kell lennie.  
-   **RS384** -RSASSA-PKCS-V1_5 az SHA-384 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-384 értékkel kell kiszámítani, és 48 bájt hosszúságú kell lennie.  
-   **RS512** -RSASSA-PKCS-V1_5 az SHA-512 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-512 értékkel kell kiszámítani, és 64 bájt hosszúságú kell lennie.  
-   **RSNULL** – tekintse meg a következőt: [RFC2437], speciális használati eset az egyes TLS-forgatókönyvek engedélyezéséhez.  

###  <a name="key-operations"></a>Legfontosabb műveletek

Key Vault a következő műveleteket támogatja a legfontosabb objektumokon:  

-   **Létrehozás**: Lehetővé teszi, hogy az ügyfél Key Vault hozzon létre egy kulcsot. A kulcs értékét Key Vault és tárolja a rendszer, és az nem jelenik meg a-ügyfél számára. Az aszimmetrikus kulcsok a Key Vaultben hozhatók létre.  
-   **Importálás**: Lehetővé teszi, hogy az ügyfél egy meglévő kulcsot importáljon Key Vaultba. Az aszimmetrikus kulcsok a JWK-konstrukción belül számos különböző csomagolási módszer használatával importálhatók Key Vaultba. 
-   **Frissítés**: Lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel módosítsa a korábban a Key Vault belül tárolt kulcs metaadatait (fő attribútumait).  
-   **Törlés**: Lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel törölje a kulcsot a Key Vaultból.  
-   **Lista**: Lehetővé teszi az ügyfél számára az adott Key Vault összes kulcsának listázását.  
-   **Verziók listázása**: Lehetővé teszi az ügyfél számára egy adott kulcs összes verziójának listázását egy adott Key Vaultban.  
-   **Beolvasás**: Lehetővé teszi, hogy az ügyfél lekérje egy adott kulcs nyilvános részeit egy Key Vault.  
-   **Biztonsági mentés**: Kulcs exportálása védett űrlapon.  
-   **Visszaállítás**: Egy előzőleg mentett kulcs importálása.  

További tudnivalókért tekintse [meg a Key Vault REST API dokumentációjában található főbb műveleteket](/rest/api/keyvault).  

Miután létrehozta a kulcsot a Key Vaultban, a következő titkosítási műveletek végezhetők el a kulcs használatával:  

-   **Aláírás és ellenőrzés**: Szigorúan ez a művelet "aláírási kivonat" vagy "hash ellenőrzése", mivel a Key Vault nem támogatja a tartalom kivonatolását az aláírás létrehozása részeként. Az alkalmazásoknak a helyileg aláírandó adatokat kell megadniuk, majd Key Vault aláírni a kivonatot. Az aláírt kivonatok ellenőrzése olyan alkalmazások kényelmi művelete, amelyek esetleg nem férnek hozzá a [nyilvános] kulcsfontosságú anyagokhoz. A legjobb teljesítmény érdekében ellenőrizze, hogy a műveletek helyileg történnek-e.  
-   **Kulcs titkosítása/csomagolása**: Key Vault tárolt kulcs egy másik kulcs, jellemzően szimmetrikus tartalom-titkosítási kulcs (CEK) elleni védelemre használható. Ha a kulcs Key Vault aszimmetrikus, a rendszer a kulcs titkosítását használja. Például az RSA-OAEP és a WRAPKEY/UNWRAPKEY műveletek egyenértékűek a TITKOSÍTÁSsal/VISSZAFEJTÉSsel. Ha a kulcs Key Vault szimmetrikus, a rendszer a kulcs-körbefuttatást használja. Például: AES-KW. A WRAPKEY művelet olyan alkalmazások számára használható, amelyek esetleg nem férnek hozzá a [nyilvános] kulcshoz. Az alkalmazások legjobb teljesítményéhez helyileg kell végrehajtani a WRAPKEY műveleteket.  
-   **Titkosítás és visszafejtés**: Key Vault tárolt kulcs egyetlen adatblokk titkosítására és visszafejtésére használható. A blokk méretét a kulcs típusa és a kiválasztott titkosítási algoritmus határozza meg. A titkosító művelet kényelmi célokat szolgál olyan alkalmazások számára, amelyek esetleg nem férnek hozzá a [nyilvános] kulcshoz. A legjobb teljesítmény érdekében a titkosítási műveleteket helyileg kell végrehajtani.  

Habár az aszimmetrikus kulcsokat használó WRAPKEY/UNWRAPKEY feleslegesnek tűnhet (mivel a művelet a TITKOSÍTÁShoz/VISSZAFEJTÉShez egyenértékű), a különböző műveletek használata fontos. A különbségtétel a művelet szemantikai és engedélyezési elkülönítését, valamint a szolgáltatás által támogatott más típusú kulcsok egységességét biztosítja.  

A Key Vault nem támogatja az EXPORTÁLÁSi műveleteket. Ha a kulcsot kiépítik a rendszerbe, azt nem lehet kibontani, vagy a kulcs lényegét módosítani. Előfordulhat azonban, hogy a Key Vault felhasználóinak más használati esetekhez, például a törlésük után is szükségük van a kulcsra. Ebben az esetben a biztonsági mentési és VISSZAÁLLÍTÁSi műveleteket a kulcs védett formában való exportálására/importálására használhatják. A biztonsági mentési művelet által létrehozott kulcsok nem használhatók Key Vaulton kívül. Azt is megteheti, hogy az IMPORTÁLÁSi művelet több Key Vault példányon is használható.  

A felhasználók a JWK objektum key_ops tulajdonságának használatával korlátozhatják az Key Vault által támogatott titkosítási műveleteket.  

A JWK-objektumokkal kapcsolatos további információkért lásd a [JSON webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)című témakört.  

###  <a name="key-attributes"></a>Fő attribútumok

A kulcs anyagán kívül a következő attribútumok is megadhatók. JSON-kérelemben az attribútumok kulcsszó és a kapcsos zárójelek ({"}") szükségesek, még akkor is, ha nincsenek megadva attribútumok.  

- *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Megadja, hogy a kulcs engedélyezve van-e, és használható-e titkosítási műveletekhez. Az *engedélyezett* attribútum a *NBF* és az *exp*együttes használata esetén használatos. Ha a *NBF* és az *exp*közötti művelet történik, akkor csak akkor lesz *engedélyezve* , ha a beállítás értéke **true (igaz**). Az *NBF* / *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat.
- *nbf*: IntDate, nem kötelező, alapértelmezés szerint most. A *NBF* (nem előtte) attribútum azt az időpontot határozza meg, ameddig a kulcs nem használható a titkosítási műveletekhez, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat. A *NBF* attribútum feldolgozásához az aktuális dátumnak és időpontnak a *NBF* attribútumban felsorolt nem korábbi dátummal és időponttal kell rendelkeznie. Előfordulhat, hogy a Key Vault némi mozgásteret biztosít, amely általában legfeljebb néhány percet vesz igénybe, hogy az óra döntse. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *exp*: IntDate, nem kötelező, az alapértelmezett érték a "Forever". Az *exp* (lejárati idő) attribútum azt a lejárati időt határozza meg, amely után a kulcs nem használható a titkosítási művelethez, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat. Az *exp* attribútum feldolgozásához szükséges, hogy az aktuális dátumnak és időpontnak az *exp* attribútumban felsorolt lejárati dátum/idő előtt kell lennie. Előfordulhat, hogy a Key Vault némi mozgásteret biztosít, amely jellemzően nem csupán néhány percet vesz igénybe, hogy az óra döntse. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  

További írásvédett attribútumok is szerepelnek a legfontosabb attribútumokat tartalmazó válaszokban:  

- *Létrehozva*: IntDate, nem kötelező. A *létrehozott* attribútum azt jelzi, hogy mikor jött létre a kulcs ezen verziója. Az érték null értékű az attribútum hozzáadása előtt létrehozott kulcsok esetében. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A *frissített* attribútum azt jelzi, hogy a kulcs ezen verziója frissítve lett-e. Az érték null értékű azon kulcsok esetében, amelyek utolsó frissítése az attribútum hozzáadása előtt történt. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  

További információ a IntDate és az egyéb adattípusokról: [adattípusok](#data-types)  

#### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A *NBF* / *exp* ablakon kívül még nem érvényes és lejárt kulcsok is használhatók a **visszafejtéshez**, a **kicsomagoláshoz**és a műveletek **ellenőrzéséhez** (nem ad vissza 403, tiltott). A még nem érvényes állapot használatának indoklása az éles használat előtt a kulcs tesztelésének engedélyezése. A lejárt állapot használatának indoklása a helyreállítási műveletek engedélyezése a kulcs érvényessége után létrehozott adatokon. Emellett letilthatja a hozzáférést egy kulcshoz Key Vault szabályzatok használatával, vagy az *engedélyezett* kulcs attribútum **hamis értékre**való frissítésével.

Az adattípusokkal kapcsolatos további információkért lásd az [adattípusokat](#data-types)ismertető témakört.

További információ a többi lehetséges attribútumról: JSON- [webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Kulcs Címkék

További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakterből és 256 karakterből állhat.  

>[!Note]
>A címkék a hívó által olvashatók, ha rendelkeznek a *listával* , vagy *kapnak* engedélyt az adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványok) számára.

###  <a name="key-access-control"></a>Kulcshozzáférés-vezérlés

A Key Vault által felügyelt kulcsok hozzáférés-vezérlését a kulcsok tárolójának Key Vault szintjén kell megadnia. A kulcsok hozzáférés-vezérlési házirendje különbözik az azonos Key Vault található titkos kódok hozzáférés-vezérlési házirendjétől. A felhasználók létrehozhatnak egy vagy több tárolót a kulcsok tárolására, és szükségesek a forgatókönyvek megfelelő szegmentálásának és a kulcsok kezelésének fenntartásához. A kulcsok hozzáférés-vezérlése független a titkok hozzáférés-vezérlésével.  

A következő engedélyek adhatók meg felhasználónkénti vagy egyszerű szolgáltatás alapján a tárolóban található kulcsok hozzáférés-vezérlési bejegyzésében. Ezek az engedélyek szorosan tükrözik a Key objektumon engedélyezett műveleteket.  Egy egyszerű szolgáltatáshoz való hozzáférés biztosítása a Key vaultban egy egyszeri művelet, amely minden Azure-előfizetésnél azonos marad. Azt is megteheti, hogy tetszőleges számú tanúsítványt telepít. 

- A kulcskezelő műveletekhez szükséges engedélyek
  - *beolvasás*: Olvassa el a kulcs nyilvános részét, valamint annak attribútumait
  - *lista*: A Key vaultban tárolt kulcsok kulcsainak vagy verzióinak listázása
  - *frissítés*: Kulcs attribútumainak frissítése
  - *Létrehozás*: Új kulcsok létrehozása
  - *Importálás*: Kulcs importálása Key vaultba
  - *Törlés*: A Key objektum törlése
  - *helyreállítás*: Törölt kulcs helyreállítása
  - *biztonsági mentés*: Key Vault-kulcs biztonsági mentése
  - *visszaállítás*: Biztonsági másolatban lévő kulcs visszaállítása kulcstartóra

- Titkosítási műveletekhez szükséges engedélyek
  - *Visszafejtés*: A kulcs használata a bájtok sorrendjének feloldásához
  - *titkosítás*: A kulcs használatával tetszőleges bájtos sorozatot biztosíthat.
  - *unwrapKey*: A kulcs használata a burkolt szimmetrikus kulcsok feloldásához
  - *wrapKey*: A kulcs használata szimmetrikus kulcs védelemmel való ellátásához
  - *ellenőrzés*: A kulcs használata a kivonatoló ellenőrzéséhez  
  - *aláírás*: A kulcs használata a kivonatok aláírására
    
- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: Törölt kulcs végleges törlése (véglegesen törölve)

A kulcsok használatával kapcsolatos további információkért tekintse meg [a Key Vault REST API-referenciával](/rest/api/keyvault)foglalkozó témakörben található főbb műveleteket. Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault titkok 

### <a name="working-with-secrets"></a>A titkok használata

Fejlesztői szempontból Key Vault API-k elfogadják és visszaadják a titkos értékeket karakterláncként. Belsőleg Key Vault a titkokat az oktettek (8 bites bájtok) sorozatának megfelelően tárolja és kezeli, és a 25k maximális mérete (bájt). A Key Vault szolgáltatás nem biztosít szemantikai adatokat a titkokhoz. Csupán elfogadja az adatot, titkosítja, tárolja, és visszaadja a titkos azonosítót ("id"). Az azonosító segítségével később is lekérheti a titkos kulcsot.  

A szigorúan bizalmas adatok esetében az ügyfeleknek az adatok védelmének további rétegeit kell figyelembe venniük. Az adattitkosítás egy külön védelmi kulccsal történik, a tárolás előtt Key Vault egy példa.  

A Key Vault a Secrets (contentType) mezőt is támogatja. Az ügyfelek megadhatják a titkos kód tartalomtípusát, hogy segítséget nyújtsanak a titkos adatokat a beolvasás során. A mező maximális hossza 255 karakter. Nincsenek előre definiált értékek. A javasolt használat a titkos adatok értelmezésére utal. Előfordulhat például, hogy egy implementáció titkos kulcsként tárolja a jelszavakat és a tanúsítványokat, majd ezt a mezőt használja a megkülönböztetéshez. Nincsenek előre definiált értékek.  

### <a name="secret-attributes"></a>Titkos attribútumok

A titkos adatok mellett a következő attribútumok is megadhatók:  

- *exp*: IntDate, nem kötelező, az alapértelmezett érték **örökre**. Az *exp* (lejárati idő) attribútum azt a lejárati időt határozza meg, amely után a titkos adatok nem kérhetők le, kivéve [bizonyos helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű, mivel a Key Vault szolgáltatás felhasználóit nem használja fel, mert egy adott titok nem használható. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.   
- *nbf*: IntDate, nem kötelező, alapértelmezés szerint **most**. A *NBF* (nem korábban) attribútum azt az időpontot határozza meg, ameddig a titkos adatokat nem lehet lekérni, kivéve [bizonyos helyzetekben](#date-time-controlled-operations). Ez a mező csak **tájékoztató** jellegű. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie. 
- *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Ez az attribútum határozza meg, hogy a titkos adatot lehet-e lekérni. Az enabled attribútum a *NBF* és az *exp* együttes használata esetén használatos, ha a *NBF* és az *exp*közötti művelet történik, akkor csak akkor lesz engedélyezve, ha a beállítás értéke **true (igaz**). A *NBF* és az *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva, kivéve [bizonyos helyzetekben](#date-time-controlled-operations).  

A titkos attribútumokat tartalmazó válaszokban további írásvédett attribútumok is szerepelnek:  

- *Létrehozva*: IntDate, nem kötelező. A létrehozott attribútum azt jelzi, hogy a titkos kulcs ezen verzióját hozta-e létre. Ez az érték null értékű az attribútum hozzáadása előtt létrehozott titkok esetében. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy a titkos kulcs ezen verziója frissítve lett-e. Ez az érték null értékű azoknál a titkoknál, amelyeket az attribútum hozzáadása előtt utoljára frissítettek. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.

#### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A titkos **beolvasási** művelet a *NBF* / *exp* ablakon kívül még nem érvényes és lejárt titkokat fog működni. A titkos kód **lekérési** műveletének meghívása tesztelési célokra használható. A lejárt titkos kód beolvasása **(beolvasása**) helyreállítási műveletekhez használható.

Az adattípusokkal kapcsolatos további információkért lásd az [adattípusokat](#data-types)ismertető témakört.  

### <a name="secret-access-control"></a>Titkoskulcs-hozzáférés vezérlése

A Key Vaultban felügyelt titkok Access Control az adott titkokat tartalmazó Key Vault szintjén vannak megadva. A titkos kulcsok hozzáférés-vezérlési szabályzata különbözik az azonos Key Vault található kulcsok hozzáférés-vezérlési házirendjétől. A felhasználók egy vagy több tárolót hozhatnak létre a titkos kulcsok megőrzése érdekében, és a forgatókönyvek megfelelő szegmentálásához és a titkok kezeléséhez szükségesek.   

A következő engedélyek használhatók a tár Secrets hozzáférés-vezérlési bejegyzésében, valamint a titkos objektumon engedélyezett műveletek részletes tükrözéséhez:  

- A titkos felügyeleti műveletekhez szükséges engedélyek
  - *beolvasás*: Titkos kód beolvasása  
  - *lista*: Egy Key Vault tárolt titkos kulcs titkainak vagy verzióinak listázása  
  - *beállítás*: Titkos kód létrehozása  
  - *Törlés*: Titkos kulcs törlése  
  - *helyreállítás*: Törölt titkos kód helyreállítása
  - *biztonsági mentés*: Titkos kulcs biztonsági mentése a kulcstartóban
  - *visszaállítás*: Biztonsági másolatból álló titkos kód visszaállítása kulcstartóra

- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: Törölt titkos kód kiürítése (végleges törlése)

A titkokkal kapcsolatos további információkért tekintse meg [a Key Vault REST API-referenciában található titkos műveletek](/rest/api/keyvault)című témakört. Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Titkos Címkék  
További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakterből és 256 karakterből állhat.  

>[!Note]
>A címkék a hívó által olvashatók, ha rendelkeznek a *listával* , vagy *kapnak* engedélyt az adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványok) számára.

## <a name="key-vault-certificates"></a>Tanúsítványok Key Vault

Key Vault tanúsítványok támogatása biztosítja a x509-tanúsítványok kezelését, valamint a következő viselkedéseket:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa egy Key Vault létrehozási folyamaton vagy egy meglévő tanúsítvány importálásán keresztül hozzon létre tanúsítványt. Magában foglalja az önaláírt és a hitelesítésszolgáltató által generált tanúsítványokat is.
-   Lehetővé teszi, hogy a Key Vault tanúsítvány tulajdonosa a X509-tanúsítványok biztonságos tárolását és kezelését a titkos kulcsokkal való interakció nélkül implementálja.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa olyan házirendet hozzon létre, amely a tanúsítvány életciklusának kezeléséhez Key Vault irányítja.  
-   Lehetővé teszi a tanúsítvány tulajdonosai számára, hogy kapcsolattartási adatokat szolgáltassanak a lejárat és a tanúsítvány megújításának életciklusával kapcsolatos eseményekről.  
-   Támogatja az automatikus megújítást a kiválasztott kiállítókkal – Key Vault partneri X509-szolgáltatók/hitelesítésszolgáltatók.

>[!Note]
>A nem partnerrel rendelkező szolgáltatók/hatóságok is engedélyezettek, de nem támogatják az automatikus megújítási funkciót.

### <a name="composition-of-a-certificate"></a>Tanúsítvány összetétele

Key Vault-tanúsítvány létrehozásakor a rendszer megcímezhető kulcsot és titkos kulcsot is létrehoz ugyanazzal a névvel. A Key Vault kulcs lehetővé teszi a kulcsfontosságú műveleteket, és a Key Vault titkos kulcs lehetővé teszi a tanúsítvány értékének beolvasását titkos fájlként. A Key Vault tanúsítvány A nyilvános x509 tanúsítvány-metaadatokat is tartalmaz.  

A tanúsítványok azonosítója és verziója hasonló a kulcsok és a titkos kódokhoz. Az Key Vault-tanúsítvány verziószámával létrehozott címezhető kulcs és titok adott verziója elérhető a Key Vault-tanúsítvány válaszában.
 
![A tanúsítványok összetett objektumok](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportálható vagy nem exportálható kulcs

Key Vault-tanúsítvány létrehozásakor a megcímezhető titkos kulcsból a PFX vagy PEM formátumban kérhető le. A tanúsítvány létrehozásához használt házirendnek jeleznie kell, hogy a kulcs exportálható. Ha a házirend nem exportálható állapotot jelez, akkor a titkos kulcs nem része az értéknek, amikor a rendszer titokként kéri le.  

A címezhető kulcs nagyobb jelentőséggel bír a nem exportálható KV-tanúsítványok esetén. A megcímezhető KV-kulcs műveletei a KV tanúsítvány létrehozásához használt KV-os tanúsítvány-házirend *kulcshasználat* mezőjéből vannak leképezve.  

Két típusú kulcs támogatott: *RSA* vagy *RSA HSM* tanúsítványokkal. Az exportálhatóként csak az RSA-mel engedélyezett, az RSA HSM nem támogatja.  

### <a name="certificate-attributes-and-tags"></a>Tanúsítvány attribútumai és címkéi

A tanúsítvány metaadatainak, a megcímezhető kulcs és a címezhető titok mellett egy Key Vault tanúsítvány is tartalmaz attribútumokat és címkéket.  

#### <a name="attributes"></a>Attribútumok

A tanúsítvány attribútumai a megcímezhető kulcs és titkos kód attribútumaira vannak tükrözve, amikor létrejön a KV-os tanúsítvány.  

A Key Vault tanúsítványa a következő tulajdonságokkal rendelkezik:  

-   *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Megadható annak jelzése, hogy a tanúsítvány adatai titkos fájlként, vagy kulcsként működnek-e. A *NBF* és az *exp* együttes használata esetén is használatos, ha a *NBF* és az *exp*közötti művelet történik, és csak akkor lesz engedélyezve, ha a beállítás értéke TRUE (igaz). A *NBF* és az *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva.  

A válaszban további írásvédett attribútumok is szerepelnek:

-   *Létrehozva*: IntDate: azt jelzi, hogy mikor jött létre a tanúsítvány ezen verziója.  
-   *frissítve*: IntDate: azt jelzi, hogy mikor frissítették a tanúsítvány ezen verzióját.  
-   *exp*: IntDate: az x509-tanúsítvány lejárati dátumának értékét tartalmazza.  
-   *nbf*: IntDate: a x509-tanúsítvány dátumának értékét tartalmazza.  

> [!Note] 
> Ha egy Key Vault-tanúsítvány lejár, az megcímezhető kulcs, és a titkos kód inműködőképes lesz.  

#### <a name="tags"></a>Tags

 Az ügyfél a kulcsok és titkok címkéhez hasonlóan a kulcs érték párok szótárát adta meg.  

 > [!Note]
> A címkék a hívó által olvashatók, ha rendelkeznek a *listával* , vagy *kapnak* engedélyt az adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványok) számára.

### <a name="certificate-policy"></a>Tanúsítvány-házirend

A tanúsítvány-szabályzat a Key Vault-tanúsítványok életciklusának létrehozásával és kezelésével kapcsolatos információkat tartalmaz. Ha egy titkos kulccsal rendelkező tanúsítványt importál a kulcstartóba, a rendszer létrehoz egy alapértelmezett szabályzatot az x509-tanúsítvány beolvasásával.  

Ha egy Key Vault-tanúsítvány teljesen létre lett hozva, a szabályzatot meg kell adni. A házirend meghatározza, hogyan kell létrehozni ezt a Key Vault-tanúsítvány verzióját vagy a következő Key Vault-tanúsítvány verzióját. A szabályzat létrehozása után a későbbi verziókhoz nem szükséges egymást követő létrehozási művelet. Egy Key Vault-tanúsítvány összes verziójához csak egy példány van.  

A tanúsítvány-házirend magas szinten a következő információkat tartalmazza:  

-   X509 tanúsítvány tulajdonságai: A tulajdonos nevét, a tulajdonos alternatív neveit, valamint a x509 létrehozásához használt egyéb tulajdonságokat tartalmazza.  
-   Kulcs tulajdonságai: a kulcs típusát, a kulcs hosszát, az exportálható és a kulcs újrafelhasználására szolgáló mezőket tartalmazza. Ezek a mezők a Key vaultot tájékoztatják a kulcsok létrehozásáról.  
-   Titkos kód tulajdonságai: olyan titkos tulajdonságokat tartalmaz, mint például a címezhető titok tartalomtípusa a titkos érték létrehozásához, a tanúsítvány titkosként való lekéréséhez.  
-   Élettartam-műveletek: a KV-os tanúsítvány élettartamának műveleteit tartalmazza. Minden élettartam-művelet tartalmazza a következőket:  

     - Trigger: a lejárat vagy az élettartam százalékában megadott napokon keresztül van megadva  

     - Művelet: Művelettípus megadása – *emailContacts* vagy *autorenew*  

-   Kibocsátó A tanúsítvány kiállítójának a x509-tanúsítványok kiállításához használt paraméterei.  
-   Házirend-attribútumok: a Szabályzathoz társított attribútumokat tartalmazza.  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 Key Vault használati leképezéshez

A következő táblázat a x509-használati szabályzat hozzárendelését mutatja be egy Key Vault tanúsítvány létrehozása során létrehozott kulcs hatékony működéséhez.

|**X509-használat jelzői**|**Key Vault Key Ops**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|DataEncipherment|titkosítás, visszafejtés| – |
|DecipherOnly|visszafejteni| –  |
|DigitalSignature|aláírás, ellenőrzés| Key Vault alapértelmezett beállítás a tanúsítvány létrehozási idejének használati specifikációja nélkül | 
|EncipherOnly|encrypt| – |
|KeyCertSign|aláírás, ellenőrzés|–|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault alapértelmezett beállítás a tanúsítvány létrehozási idejének használati specifikációja nélkül | 
|Letagadhatatlanság nyújtására|aláírás, ellenőrzés| – |
|crlsign|aláírás, ellenőrzés| – |

### <a name="certificate-issuer"></a>Tanúsítvány kiállítója

A Key Vault Certificate objektum olyan konfigurációt tartalmaz, amely a kiválasztott tanúsítvány-kiállító szolgáltatóval való kommunikációra szolgál a x509-tanúsítványok megrendeléséhez.  

-   Key Vault partnerek az SSL-tanúsítványok következő tanúsítvány-kiállítói szolgáltatói által

|**Szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|A nyilvános felhőben és Azure Governmentban a Key Vault szolgáltatás összes helyén támogatott|
|GlobalSign|A nyilvános felhőben és Azure Governmentban a Key Vault szolgáltatás összes helyén támogatott|

Ahhoz, hogy a tanúsítvány kiállítója egy Key Vaulton legyen létrehozva, az 1. és a 2. előfeltételként szükséges lépések végrehajtása után sikeresen el kell végezni a műveletet.  

1. Beléptetés a HITELESÍTÉSSZOLGÁLTATÓI szolgáltatókba  

    -   A vállalati rendszergazdának be kell jelentkeznie a vállalatnál (pl. Contoso) legalább egy HITELESÍTÉSSZOLGÁLTATÓI szolgáltatóval.  

2. A rendszergazda az SSL-tanúsítványok regisztrálásához (és megújításához) Key Vault kérő hitelesítő adatokat hoz létre.  

    -   A Key vaultban a szolgáltató kiállító objektumának létrehozásához használandó konfigurációt adja meg.  

A kiállítói objektumok tanúsítvány-portálról történő létrehozásával kapcsolatos további információkért tekintse meg a [Key Vault tanúsítványok blogját](https://aka.ms/kvcertsblog) .  

Key Vault lehetővé teszi több kiállító objektum létrehozását különböző kiállítói szolgáltatói konfigurációval. A kiállítói objektum létrehozása után a neve hivatkozhat egy vagy több tanúsítvány-házirendre. A kiállító objektumra való hivatkozás arra utasítja a Key Vault, hogy a kiállító objektumban megadott konfigurációt használja, amikor a tanúsítvány létrehozásakor és megújításakor a x509-tanúsítványt kéri a CA-szolgáltatótól.  

A kiállító objektumok a tárolóban jönnek létre, és csak a KV tanúsítványokkal használhatók ugyanabban a tárolóban.  

### <a name="certificate-contacts"></a>Tanúsítványhoz tartozó névjegyek

A tanúsítványhoz tartozó névjegyek kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartama eseményei által aktivált értesítések küldéséhez. A kapcsolattartási adatokat a Key Vault összes tanúsítványa megosztja. A Key Vault bármelyik tanúsítványa esetében értesítést küld a rendszer az adott esemény összes megadott ügyfelének.  

Ha a tanúsítvány házirendje automatikus megújításra van beállítva, akkor a rendszer értesítést küld a következő eseményekről.  

- A tanúsítvány megújítása előtt
- A tanúsítvány megújítása után, ha a tanúsítvány sikeres megújítása megtörtént, vagy ha hiba történt, a tanúsítvány manuális megújítását kell megkövetelni.  

  Ha egy manuálisan megújított tanúsítvány-házirend (csak e-mail-cím), akkor a rendszer értesítést küld, amikor a tanúsítvány megújítására kerül sor.  

### <a name="certificate-access-control"></a>Tanúsítvány Access Control

 A tanúsítványok hozzáférés-vezérlését Key Vault kezeli, és a tanúsítványokat tartalmazó Key Vault nyújtja. A tanúsítványok hozzáférés-vezérlési házirendje különbözik az azonos Key Vault található kulcsok és titkok hozzáférés-vezérlési házirendjeitől. A felhasználók egy vagy több tárolót hozhatnak létre a tanúsítványok tárolására, a forgatókönyvek megfelelő szegmentálásának és a tanúsítványok kezelésének fenntartása érdekében.  

 A következő engedélyek a Key Vault Secrets hozzáférés-vezérlési bejegyzésében, és a titkos objektumon engedélyezett műveletek részletes tükrözése céljából használhatók:  

- Tanúsítványkezelő műveletekhez szükséges engedélyek
  - *beolvasás*: A tanúsítvány aktuális verziójának vagy bármely verziójának beolvasása 
  - *lista*: A tanúsítvány aktuális tanúsítványainak vagy verzióinak listázása  
  - *frissítés*: Tanúsítvány frissítése
  - *Létrehozás*: Key Vault tanúsítvány létrehozása
  - *Importálás*: Tanúsítvány anyagának importálása Key Vault-tanúsítványba
  - *Törlés*: Tanúsítvány, a szabályzat és az összes verzió törlése  
  - *helyreállítás*: Törölt tanúsítvány helyreállítása
  - *biztonsági mentés*: A Key vaultban lévő tanúsítvány biztonsági mentése
  - *visszaállítás*: Biztonsági másolatban lévő tanúsítvány visszaállítása kulcstartóba
  - *managecontacts*: Key Vault-tanúsítványok kapcsolatainak kezelése  
  - *manageissuers*: Key Vault hitelesítésszolgáltatók/kibocsátók kezelése
  - *getissuers*: Tanúsítvány hitelesítő szerveinek/kiállítóinak beszerzése
  - *listissuers*: A tanúsítvány szerveinek/kiállítóinak listázása  
  - *setissuers*: Key Vault tanúsítvány szerveinek/kiállítóinak létrehozása vagy frissítése  
  - *deleteissuers*: Key Vault tanúsítvány hatóságainak/kiállítóinak törlése  
 
- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: Törölt tanúsítvány törlése (végleges törlése)

További információkért tekintse meg a [tanúsítványok műveleteit a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Azure Storage-fiók kulcsainak kezelése

A Key Vault az Azure Storage-fiók kulcsait tudja kezelni:

- Belsőleg az Azure Storage-fiókkal Key Vault listázhatja (szinkronizálhatja) a kulcsokat. 
- Key Vault a kulcsok rendszeres újragenerálása (elforgatása).
- A rendszer soha nem adja vissza a kulcs értékeit a hívónak válaszul.
- Key Vault a Storage-fiókok és a klasszikus Storage-fiókok kulcsait kezeli.

További információ: [Azure Key Vault Storage-fiók kulcsainak](key-vault-ovw-storage-keys.md) használata

### <a name="storage-account-access-control"></a>Storage-fiók hozzáférés-vezérlése

A következő engedélyek használhatók, ha egy felhasználó vagy egy alkalmazás egy felügyelt Storage-fiók műveleteinek elvégzését engedélyezi:  

- A felügyelt Storage-fiók és az SaS-definíciós műveletek engedélyei
  - *beolvasás*: A Storage-fiókkal kapcsolatos információk beolvasása 
  - *lista*: Key Vault által kezelt Storage-fiókok listázása
  - *frissítés*: Storage-fiók frissítése
  - *Törlés*: Tárfiók törlése  
  - *helyreállítás*: Törölt Storage-fiók helyreállítása
  - *biztonsági mentés*: Storage-fiók biztonsági mentése
  - *visszaállítás*: Biztonsági másolatból álló Storage-fiók visszaállítása Key Vault
  - *beállítás*: Storage-fiók létrehozása vagy frissítése
  - *regeneratekey*: Megadott kulcs értékének újralétrehozása egy Storage-fiókhoz
  - *getsas*: A Storage-fiókhoz tartozó SAS-definícióval kapcsolatos információk beolvasása
  - *listsas*: Storage-fiókhoz tartozó Storage SAS-definíciók listázása
  - *deletesas*: SAS-definíció törlése egy Storage-fiókból
  - *setsas*: Új SAS-definíció/-attribútumok létrehozása vagy frissítése egy Storage-fiókhoz

- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: Felügyelt Storage-fiók kiürítése (végleges törlése)

További információ: a [Storage-fiók műveletei a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Lásd még:

- [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](/azure/key-vault/key-vault-developers-guide)
