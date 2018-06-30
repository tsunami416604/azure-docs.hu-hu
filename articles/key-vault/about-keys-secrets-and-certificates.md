---
title: Kulcsok, a titkos kulcsok és a tanúsítványok
description: REST-felületen és KV fejlesztői részletei – áttekintés
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: dd1bb6117c0360e67783434c980c56b5f6ae7f9f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110228"
---
# <a name="about-keys-secrets-and-certificates"></a>Kulcsok, a titkos kulcsok és a tanúsítványok
Az Azure Key Vault lehetővé teszi, hogy a felhasználók tárolhatják és a Microsoft Azure-környezeten belül titkosítási kulcsok használata. Key Vault több ellenőrzőkulcs-típusok és algoritmusokat támogatja, és lehetővé teszi, hogy értékes kulcsok használata hardveres biztonsági modulok (HSM). Emellett a Key Vault segítségével a felhasználók biztonságosan tárolni a titkos kulcsok. A titkokat korlátozott mérete octet objektumok nem adott szemantikájú. Key Vault is támogatja a tanúsítványok, amelyek kulcsok és titkos épül, és adja hozzá az automatikus megújítási szolgáltatást.

Az Azure Key Vault kapcsolatos további általános információkért lásd: [Mi az Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Key Vault általános részleteit**

-   [Szabványok támogatása](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Adattípusok](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objektumok, azonosítók és versioning](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Kulcsok**

-   [Kulcsok és ellenőrzőkulcs-típusok](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [RSA algoritmusok](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [RSA-HSM algoritmusok](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Titkosítási védelem](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Kulcsú műveletek](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Legfőbb attribútumai](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Kulcsazonosítókat tartalmaz](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Tudnivalók a titkos kulcsok** 

-   [Titkos kulcsok használata](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Titkos attribútumok](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Titkos címkék](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Titkos hozzáférés-vezérlés](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Tanúsítványokkal kapcsolatos**

-   [A tanúsítvány összeállítás](#BKMK_CompositionOfCertificate)  
-   [Tanúsítvány-attribútumok és címkék](#BKMK_CertificateAttributesAndTags)  
-   [Tanúsítványszabályzat](#BKMK_CertificatePolicy)  
-   [Tanúsítvány kiállítója](#BKMK_CertificateIssuer)  
-   [Tanúsítvány kapcsolattartók](#BKMK_CertificateContacts)  
-   [Tanúsítvány hozzáférés-vezérlés](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Key Vault általános részleteit

Az alábbi szakaszok vonatkozó általános információkat kínál a végrehajtása az Azure Key Vault szolgáltatás között.

###  <a name="BKMK_Standards"></a> Szabványok támogatása

A JavaScript Object Notation (JSON) és a JavaScript Object aláírás és titkosítás (JOSE) specifikációk is fontos háttér-információkat.  

-   [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (KMH)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Adattípusok

Tekintse meg a [JOSE specifikációk](#BKMK_Standards) a kulcsokat, titkosítási és aláírási megfelelő az adattípusokat.  

-   **algoritmus** -kulcs művelet, például RSA1_5 egy támogatott algoritmus  
-   **titkosított szöveg-érték** -szöveg oktetben Base64URL használatával kódolt cipher  
-   **kivonatoló értékű** – a kimeneti Base64URL használatával kódolt kivonatoló algoritmus  
-   **kulcs típusú** -egyik támogatott kulcs típusa, például RSA.  
-   **egyszerű szöveges értékű** -egyszerű szöveges oktetben Base64URL használatával kódolt  
-   **aláírás-érték** - kimeneti Base64URL használatával kódolt aláírási algoritmus  
-   **base64URL** -egy Base64URL [RFC4648] kódolású bináris érték  
-   **logikai** -igaz vagy hamis  
-   **Identitás** - azonosító az Azure Active Directory (AAD).  
-   **IntDate** - JSON decimális értéket képviselő másodpercben 1970-01-01T0:0:0Z UTC csak a megadott UTC dátum/idő. Dátum kapcsolatos részleteket lásd a [RFC3339] / alkalommal fordult elő az általános és UTC ebben az esetben.  

###  <a name="BKMK_ObjId"></a> Objektumok, azonosítók és versioning

Az Azure Key Vault tárolt objektumok verziók megőrzése mellett, amikor az objektum egy új példánya jön létre, és minden verzió egyedi azonosítóját és URL-címet. Az objektum létrehozásakor kap egy egyedi azonosító, és az objektum aktuális verziója van megjelölve. Ezzel a névvel objektum egy új példányának létrehozása az új objektumot ad egy egyedi azonosító, és azt válnak a jelenlegi verziójával.  

Az Azure Key Vault objektumok is figyelembe venni, hogy az aktuális azonosítója vagy a verzió azonosítója. Például a "Főkulcsos" nevű kulcsot kap, az aktuális azonosítóval rendelkező műveleteket okoz a rendszer az elérhető legújabb verzióra. A verziófüggő azonosítóval rendelkező műveletet hajt végre azt eredményezi, a rendszer, hogy az objektum adott verzióját használja.  

Objektumok egyedileg azonosítja az Azure Key Vault URL-címet használ, úgy, hogy a két objektum a rendszer, függetlenül attól, földrajzi helyhez, az az URL-CÍMÉRE van belül. Az objektum teljes URL-CÍMÉT az objektumazonosító nevezik, és egy előtag része, amely azonosítja a Key Vault, az objektumtípus áll, a felhasználó által megadott objektum nevét, és egy objektum verziója. Az objektum neve nem a nagybetűk között, és nem módosítható. Az objektum verziója nem tartalmazó azonosítók talál azonosítók nevezzük.  

További információkért lásd: [hitelesítési kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatásban kulcstároló neve.<br /><br /> Key Vault nevét a felhasználó által választott, és a globálisan egyedi.<br /><br /> Kulcstároló neve egy hossza csak tartalmazó karakterlánc 3 – 24 karakter lehet (0-9, a – z, A-Z, és -).|  
|`object-type`|Az objektum, vagy a "kulcsok", vagy a "kulcsok" típusú.|  
|`object-name`|Egy `object-name` felhasználó által megadott nevét, és a kulcstároló belül egyedieknek kell lenniük. A nevének kell lennie egy karakterlánc 1-127 karakternél, amely csak 0-9, a – z, A-Z, és -.|  
|`object-version`|Egy `object-version` van, a rendszer által létrehozott, nem kötelezően használt objektumok egyedi verziójának cím 32 karakter hosszúságú karakterlánc-azonosító.|  

## <a name="key-vault-keys"></a>Key Vault kulcsok

###  <a name="BKMK_KeyTypes"></a> Kulcsok és ellenőrzőkulcs-típusok

Titkosítási kulcsok Azure Key Vault a JSON webes kulcs [JWK] objektumként jelennek meg. Az alap JWK/JWA paramétereknek is bővítve lettek az Azure Key Vault megvalósításához egyedi kulcstípusok engedélyezéséhez, például a hardveres biztonsági MODULT (Thales) szállító adott csomagolási használatával biztonságos szállíthatóságát engedélyezése az Azure Key Vault-kulcsok importálása kulcsok, például, hogy Előfordulhat, hogy csak használni őket az Azure Key Vault HSM.  

A kezdeti Azure Key Vault a kiadás támogatja az RSA-kulcsok csak; későbbi kiadásokban lehetséges, hogy más kulcstípusok például szimmetrikus és az elliptikus görbe támogatja.  

-   **RSA**: 2048 bites RSA-kulcs. Ez az, hogy a "soft" kulcs, Key Vault szoftverfrissítési dolgoz fel, de titkosítása kulcsával. a rendszer, amely egy hardveres biztonsági modulban tárolja. Ügyfelek előfordulhat, hogy egy meglévő RSA-kulcs importálása, vagy kérje meg, hogy az Azure Key Vault hozhat létre egy.  
-   **RSA-HSM**: feldolgozása a hardveres biztonsági MODULT az RSA-kulccsal. RSA-HSM kulcsokhoz az Azure Key Vault HSM biztonsági világot egyikében (nincs elkülönítés fenntartásához geography / biztonsági világ). Ügyfelek előfordulhat, hogy importálja az RSA-kulcs, vagy letölthető formában vagy exportálásával egy kompatibilis HSM-eszközről, vagy kérje meg, hogy az Azure Key Vault hozhat létre egy. Ilyen típusú kulcs hozzáadja a T attribútumot a JWK szerezze be a HSM-kulcs anyagot elvégzéséhez.  

     További információ a földrajzi határokon belül: [Microsoft Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> RSA algoritmusok  
 A következő algoritmus azonosítók az Azure Key Vault RSA-kulcsok használata támogatott.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁSI/VISSZAFEJTÉSI

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] kulcsú titkosítás  
-   **RSA-OAEP típusú** - RSAES optimális aszimmetrikus titkosítási kitöltési (OAEP típusú) [RFC3447] RFC 3447 a szakasz A.2.1 által meghatározott alapértelmezett paraméterek használatával. Alapértelmezett paraméterek az SHA-1 SHA-1 kivonatoló függvényt és MGF1 maszk generációs függvényében használ.  

#### <a name="signverify"></a>BEJELENTKEZÉS/ELLENŐRZÉSE

-   **RS256** - által meghatározott RSASSA-PKCS-v1_5 SHA-256 használatával. A megadott alkalmazás kivonatoló értéke SHA-256 algoritmussal kell számolni, és 32 bájt hosszúságúnak kell lennie.  
-   **RS384** - által meghatározott RSASSA-PKCS-v1_5 SHA-384-et használja. A megadott alkalmazás kivonatoló értéket ki kell számítani az SHA-384-et használ, és 48 bájt hosszúságúnak kell lennie.  
-   **RS512** - által meghatározott RSASSA-PKCS-v1_5 SHA-512 használatával. A megadott alkalmazás kivonatoló értéke SHA-512 használatával kell számolni, és 64 bájt hosszúságúnak kell lennie.  
-   **RSNULL** -lásd: [RFC2437] egy speciális használati eset lehetővé bizonyos TLS forgatókönyveket.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> RSA-HSM algoritmusok  
A következő algoritmus azonosítók az Azure Key Vault RSA-HSM-kulcsok használata támogatott.  

### <a name="BKMK_Cryptographic"></a> Titkosítási védelem

A titkosító modulok által használt Azure Key Vault, hogy hardveres biztonsági modul vagy a szoftver,-e a FIPS használatával érvényesített. Nem kell tennie semmit különleges FIPS-módban való futásra. Ha Ön **létrehozása** vagy **importálása** HSM által védett kulcsokat, kézbesítése garantáltan a hardveres biztonsági modulok a FIPS 140-2 2 vagy magasabb szintű érvényesítve belül feldolgozni. Ha Ön **létrehozása** vagy **importálása** kulcsokat szoftveres védelemmel ellátott akkor kerül sor a titkosítási modulok belül érvényesítve a FIPS 140-2 1 vagy magasabb szintű. További információkért lásd: [kulcsok és kulcstípusok](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>NAPLÓCSONKULÁSI/KICSOMAGOLÁSÁHOZ, A TITKOSÍTÁSI/VISSZAFEJTÉSI

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] kulcsú titkosítás.  
-   **RSA-OAEP típusú** - RSAES optimális aszimmetrikus titkosítási kitöltési (OAEP típusú) [RFC3447] RFC 3447 a szakasz A.2.1 által meghatározott alapértelmezett paraméterek használatával. Alapértelmezett paraméterek az SHA-1 SHA-1 kivonatoló függvényt és MGF1 maszk generációs függvényében használ.  

 #### <a name="signverify"></a>BEJELENTKEZÉS/ELLENŐRZÉSE  

-   **RS256** - által meghatározott RSASSA-PKCS-v1_5 SHA-256 használatával. A megadott alkalmazás kivonatoló értéke SHA-256 algoritmussal kell számolni, és 32 bájt hosszúságúnak kell lennie.  
-   **RS384** - által meghatározott RSASSA-PKCS-v1_5 SHA-384-et használja. A megadott alkalmazás kivonatoló értéket ki kell számítani az SHA-384-et használ, és 48 bájt hosszúságúnak kell lennie.  
-   **RS512** - által meghatározott RSASSA-PKCS-v1_5 SHA-512 használatával. A megadott alkalmazás kivonatoló értéke SHA-512 használatával kell számolni, és 64 bájt hosszúságúnak kell lennie.  
-   RSNULL: Lásd: [RFC2437] egy speciális használati eset lehetővé bizonyos TLS forgatókönyveket.  

###  <a name="BKMK_KeyOperations"></a> Kulcsú műveletek

Az Azure Key Vault támogatja a következő műveletek olyan objektumokon, kulcs:  

-   **Hozzon létre**: lehetővé teszi, hogy egy ügyfél az Azure Key Vault a kulcs létrehozásához. A kulcsnak az értéke, az Azure Key Vault által generált és a rendszer és az ügyfél nem szabadul fel. Aszimmetrikus (és a jövőben elliptikus görbéjű és Symmetric) kulcsok létrehozása az Azure Key Vault.  
-   **Importálás**: lehetővé teszi, hogy egy ügyfél egy meglévő kulcs importálása az Azure Key Vaultba. Aszimmetrikus (és a jövőben elliptikus görbéjű és Symmetric) kulcsok előfordulhat, hogy importálni lehessen az Azure Key Vault használatával számos különböző csomagolás módszer egy JWK szerkezet belül.  
-   **Frissítés**: lehetővé teszi, hogy egy ügyfél és az Azure Key Vault belül korábban tárolt kulcsok társított metaadatokat (kulcsattribútum) módosításához szükséges engedéllyel.  
-   **Törlés**: lehetővé teszi, hogy egy ügyfél egy kulcs törléséhez az Azure Key Vault szükséges engedélyekkel.  
-   **Lista**: lehetővé teszi, hogy egy ügyfél egy adott Azure Key Vault az összes kulcs listázásához.  
-   **Verziók listában**: lehetővé teszi, hogy az ügyfél egy adott kulcs egy adott Azure Key Vault összes verziójának felsorolása.  
-   **Első**: lehetővé teszi, hogy egy ügyfél egy adott kulcs egy Azure Key Vault a nyilvános részének beolvasása.  
-   **Biztonsági mentés**: a kulcs, védett formátumban exportálja.  
-   **Visszaállítás**: egy biztonsági másolatból kulcs importálására.  

További információkért lásd: [műveletek kulcs](/rest/api/keyvault/key-operations).  

A kulcs létrehozása az Azure Key Vault, a következő titkosítási műveleteket hajthatja végre a kulcs használatával:  

-   **Bejelentkezési és ellenőrzés**: szigorúan, a művelet nem "bejelentkezési kivonatoló" vagy "kivonatoló hitelesítés", az Azure Key Vault nem támogatja a kivonatolás tartalom aláírás létrehozásának részeként. Alkalmazások kell az adatok helyben alá kell írni, majd kérik az Azure Key Vault megjelölés a kivonat kivonatoló. Alkalmazás esetében, amely nincs hozzáférése a [nyilvános] kulcsokat tárol; kényelmi műveletként támogatott aláírt kivonatok ellenőrzése azt javasoljuk, hogy a legjobb alkalmazásteljesítmény-, ellenőrizze, hogy műveleteket helyileg.  
-   **Titkosítási kulcs / Alkalmazásburkoló**: az Azure Key Vault szolgáltatókban tárolt kulcsok egy másik kulcsot, általában egy tartalom szimmetrikus titkosítási kulcsot (CEK) védelmére használható. A kulcsot az Azure Key Vault a esetén aszimmetrikus kulcsú titkosítást használ, például RSA-OAEP típusú és WRAPKEY/UNWRAPKEY műveletek megfelelői TITKOSÍTÁSA/VISSZAFEJTÉSE. Ha a kulcsot az Azure Key Vault a szimmetrikus, alkalmazásburkoló kulccsal; Ha például az AES-KW. A WRAPKEY műveletet a könnyebb alkalmazásnál, amely nincs hozzáférése a [nyilvános] kulcsokat tárol; Javasoljuk, hogy az alkalmazás legjobb teljesítmény érdekében WRAPKEY műveleteket helyileg.  
-   **Titkosításához és visszafejtéséhez**: az Azure Key Vault szolgáltatókban tárolt kulcsok titkosítására vagy visszafejtésére egyetlen blokkot az adatok, amelyek méretét határozza meg a kulcs típusa és a kiválasztott titkosítási algoritmus használható. A titkosítás művelet biztosított kényelmi alkalmazásnál, amely nincs hozzáférése a [nyilvános] kulcsokat tárol; Javasoljuk, hogy a legjobb alkalmazásteljesítmény, titkosítják műveletek helyileg kell elvégezni.  

WRAPKEY/UNWRAPKEY aszimmetrikus kulcsok használata felesleges tűnhet, mivel a művelet TITKOSÍTÁSA/VISSZAFEJTÉSE egyenértékű, amíg a különböző műveletek használata tekinthető biztosításához fontos szemantikai és a műveletek és a konzisztencia engedélyezési elkülönítése Ha más kulcstípusok a szolgáltatás támogatja.  

Az Azure Key Vault nem támogatja az EXPORTÁLÁSI műveleteket: kulcs kiépítését követően a rendszer nem sikerült beolvasni vagy módosítani a kulcsokat tárol.  Azonban az Azure Key Vault, akinek szüksége a kulcs más felhasználók eseteinek, vagy törölve lett az Azure Key Vault után használhatják a biztonsági MENTÉSI és VISSZAÁLLÍTÁSI műveletek exportálási/importálási a kulcsot a védett űrlapok. A biztonsági MENTÉSI művelet által létrehozott kulcsai nem használható az Azure Key Vault kívül. Az IMPORTÁLÁSI művelet azt is megteheti, szemben az Azure Key Vault több példány is használható.  

Felhasználók korlátozhatják az Azure Key Vault a JWK objektum key_ops tulajdonságának kulcs alapú támogató titkosítási műveletek.  

JWK objektumokra vonatkozó további információkért lásd: [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Legfőbb attribútumai

A kulcsokat tárol, mellett a következő attribútum is megadható. Egy JSON-kérés attribútumok kulcsszó és az kell használni, a "{" "}", még akkor is, ha nincs megadott attribútumok szükségesek.  

- *engedélyezett*: boolean, nem kötelező, alapértelmezett érték a **igaz**. Megadja, hogy a kulcs engedélyezett és a titkosítási műveletek lehet felhasználni. A *engedélyezett* attribútum együtt használatos *nbf* és *exp*. Ha egy művelet között történik *nbf* és *exp*, akkor lesz csak engedélyezett, ha *engedélyezve* értékre van állítva **igaz**. Kívül a *nbf* / *exp* ablakban automatikusan nem engedélyezettek, kivéve a művelet bizonyos [adott feltételek](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, nem kötelező, alapértelmezett érték a most. A *nbf* (hatálybalépési idő) attribútum azonosítja az idő, ameddig a kulcs nem használható a kriptográfiai műveleteket, kivéve a művelet bizonyos [adott feltételek](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). A feldolgozása a *nbf* attribútum szükséges, hogy az aktuális dátum/idő után kell vagy a nem egyenlő-szereplő dátum/idő előtt a *nbf* attribútum. Az Azure Key Vault rendelkezhetnek néhány kis eltérést általában nem csak néhány percet, hogy figyelembe vegye óra eltérésére. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *Exp*: IntDate, nem kötelező, alapértelmezett érték a "végtelen". A *exp* (lejárati idő) attribútum azonosítja a lejárati idő vagy azt követően, amely nem használja a rendszer kriptográfiai művelet, kivéve a művelet bizonyos [adott feltételek](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). A feldolgozása a *exp* attribútum szükséges, hogy az aktuális dátum/idő, szerepel a lejárati dátum/idő előtt kell-e a *exp* attribútum. Az Azure Key Vault rendelkezhetnek néhány kis eltérést általában nem csak néhány percet, hogy figyelembe vegye óra eltérésére. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

Nincsenek további attribútumait, amely fontos attribútumokat tartalmazó választ meg:  

- *létrehozott*: IntDate, nem kötelező. A *létrehozott* attribútum azt jelöli, ezen verziója a kulcs létrehozásakor. Az értéke null, a kulcsokhoz, ez az attribútum hozzáadása előtt létre. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A *frissített* attribútum azt jelöli, amikor ez a kulcs verziója módosult. Ezt az értéket értéke null, a kulcsok utolsó frissített Ez az attribútum hozzáadása előtt. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  

IntDate és más adattípusok további információkért lásd: [adattípusok](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Dátum-idő ellenőrzött műveletek

Nincs még érvényes, és a lejárt kulcsokkal, ezen kívül a *nbf* / *exp* ablak, így fog működni az **visszafejtéséhez**, **kicsomagolásához** és **ellenőrizze** műveletek (403-as, nem vissza tiltott). A nem még érvényes állapotban a profilkategóriák, hogy a egy kulcs kell tesztelni, mielőtt éles környezetben való használathoz. A lejárt használatával indoklása, hogy lehetővé tegyék a helyreállítási műveletek hozták létre, ha a kulcs nem érvényes adatokat. Is, letilthatja a hozzáférés a Key Vault házirendekkel kulcs, vagy frissíti a *engedélyezett* a kulcsattribútum **hamis**.

Az adatok típusai című cikkben olvashat bővebben, [adattípusok](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Más lehetséges attribútumok további információkért lásd: a [JSON webes kulcs (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Kulcsazonosítókat tartalmaz

Az alkalmazás-specifikus metaadatokat címkék formájában is megadhat. Az Azure Key Vault támogatja legfeljebb 15 címkéket, amelyek mindegyike egy 256 karakter hosszúságú nevet és 256 karakter hosszúságú értéket is rendelkezhetnek.  

>[!Note]
>Címke található a hívó által is olvasható, ha rendelkeznek a *lista* vagy *beolvasása* szóban forgó objektumtípushoz; engedéllyel kulcsokat, titkos kulcsokat vagy tanúsítványokat.

###  <a name="BKMK_KeyAccessControl"></a> Kulcs hozzáférés-vezérlés

A kulcstároló, úgy működik, mint a kulcsok tároló szintjén felügyeli a Key Vault kulcsok hozzáférés-vezérlés valósul meg. Nincs hozzáférés-vezérlési szabályzatok kulcsok, amely nem azonos azzal a hozzáférés-vezérlési házirend titkos kulcsainak azonos a Key Vault. Felhasználók hozhat létre egy vagy több tárolók kulcsok tárolására, és megfelelő Szegmentálás forgatókönyv-és felügyeleti kulcsok szükségesek. Hozzáférés-vezérlés a kulcsok nem függ a titkos kulcsok hozzáférés-vezérlést.  

A következő is adhatók engedélyek, az egy felhasználónként / service egyszerű módon, egy tároló kulcsainak hozzáférés-vezérlési bejegyzés. Ezek az engedélyek szorosan tükrözik a kulcs-objektum engedélyezett műveletek:  

-   *Hozzon létre*: új kulcsok létrehozása
-   *első*: olvassa el a nyilvános részét egy kulcsot, valamint a hozzá tartozó attribútumok
-   *lista*: a kulcsok vagy a key vaultban tárolt kulcs verzióinak listázása
-   *frissítés*: frissítse az attribútumot a kulcs
-   *Törlés*: a kulcs-objektum törlése
-   *bejelentkezési*: a kulcs aláírásához digests használata
-   *Győződjön meg arról*: a billentyűvel emésztett ellenőrzése
-   *wrapKey*: a kulcs használatával a szimmetrikus kulcs védelme
-   *unwrapKey*: a kulcs használatát a védelem becsomagolt szimmetrikus kulcsok
-   *titkosítani*: a kulcsa segítségével egy tetszőleges sorozat bájt védelme
-   *visszafejtés*: a védelem bájt sorozatát kulcs használata
-   *Importálás*: a kulcstároló kulcs importálása
-   *biztonsági mentési*: a kulcstároló kulcs biztonsági mentése
-   *visszaállítás*: kulcstároló kulcsot egy biztonsági másolat visszaállítása
-   *minden*: minden engedély

## <a name="key-vault-secrets"></a>Key Vault titkos kulcsok 

###  <a name="BKMK_WorkingWithSecrets"></a> Titkos kulcsok használata

Az Azure Key Vault titkokat octet sorozatok minden 25 kilobájt maximális mérettel. Az Azure Key Vault szolgáltatás nem ad meg semmilyen szemantikáját titkok; csupán fogadja az adatokat, titkosítja és azt egy titkos azonosítót, az "id", a titkos kulcsot egy későbbi időpontban beolvasandó használható visszaadó tárolja.  

Szigorúan bizalmas adatokhoz, az ügyfelek gondolja át az Azure Key Vault; tárolt adatok védelme további rétegei például előre adatok titkosításával egy külön védelmi kulcs használatával.  

Az Azure Key Vault is támogatja a titkos kulcsok contentType mező. Ügyfelek előfordulhat, hogy adja meg a tartalom típusát, "contentType", amely értelmezéséhez a titkos adatokat, amikor lekéri azt a titkos kulcs. Ez a mező maximális hossza 255 karakter lehet. Nincsenek előre meghatározott értékek. Az ajánlott használati van, mint a titkos adatok értelmezése vonatkozó javaslat. Megvalósítását például tárolhatja a jelszavak és a tanúsítványok titkos kulcsok majd azt jelzi, amely a mező használatával. Nincsenek előre meghatározott értékek.  

###  <a name="BKMK_SecretAttrs"></a> Titkos attribútumok

A titkos adatok mellett a következő attribútumok adható meg:  

- *Exp*: IntDate, nem kötelező, alapértelmezett érték a **végtelen**. A *exp* (lejárati idő) attribútum azonosítja a lejárati idő vagy azt követően, amely a titkos kell nem lehet adatokat beolvasni, kivéve a [helyzeteket](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). A feldolgozása a *exp* attribútum szükséges, hogy az aktuális dátum/idő, szerepel a lejárati dátum/idő előtt kell-e a *exp* attribútum. Az Azure Key Vault rendelkezhetnek néhány kis eltérést általában nem csak néhány percet, hogy figyelembe vegye óra eltérésére. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *NBF*: IntDate, nem kötelező, alapértelmezett érték a **most**. A *nbf* (hatálybalépési idő) attribútum azonosítja az időt, ameddig a titkos adatokat nem kell beolvasni, kivéve a [helyzeteket](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). A feldolgozása a *nbf* attribútum szükséges, hogy az aktuális dátum/idő után kell vagy a nem egyenlő-szereplő dátum/idő előtt a *nbf* attribútum. Az Azure Key Vault rendelkezhetnek néhány kis eltérést általában nem csak néhány percet, hogy figyelembe vegye óra eltérésére. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *engedélyezett*: boolean, nem kötelező, alapértelmezett érték a **igaz**. Ez az attribútum határozza meg, vagy nem lehet beolvasni a titkos adatokat. Az engedélyezett attribútum együtt használatos és *exp* közötti művelet esetén és exp, akkor lesz csak engedélyezett, ha engedélyezve van beállítva **igaz**. Kívül a *nbf* és *exp* ablakban a program automatikusan nem engedélyezett, kivéve a [helyzeteket](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Nincsenek további attribútumait, amely választ, amely tartalmazza a titkos attribútumok meg:  

- *létrehozott*: IntDate, nem kötelező. A létrehozott attribútum azt jelzi, hogy ezen verziója a titkos kulcs létrehozásakor. Az értéke null, a titkos kulcsok Ez az attribútum hozzáadása előtt létre. Az érték egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissített*: IntDate, nem kötelező. A frissített attribútum azt jelzi, hogy a titkos kulcs ezen verziója frissítésekor. Ez az érték értéke null, a titkos kulcsok utolsó frissített Ez az attribútum hozzáadása előtt. Az érték egy IntDate értéket tartalmazó számnak kell lennie.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Dátum-idő ellenőrzött műveletek

A titkos kulcs **beolvasása** művelet nem még érvényes, és a lejárt titkok kívül fog működni a *nbf* / *exp* ablak. A titkos kulcs hívása **beolvasása** művelet, nem még érvényes titkos kulcs, tesztelési célokra használható. Beolvasása (**beolvasása**ing) egy lejárt titkos kulcsot, a helyreállítási műveletekhez használható.

Az adatok típusai című cikkben olvashat bővebben, [adattípusok](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Titkos hozzáférés-vezérlés

Hozzáférés-vezérlés kezelése az Azure Key Vault a titkos kulcsok a kulcstároló, ezeket a titkos kulcsok tárolót funkcionál szintjén valósul meg. Nincs hozzáférés-vezérlési szabályzatok a titkos kulcsok azonos a Key Vault a hozzáférés-vezérlési házirend elkülönül. Felhasználók létrehozhatnak egy vagy több tárolók titkos kulcsok tárolására, és forgatókönyv megfelelő Szegmentálás és titkos kulcsok kezelése fenntartásához szükséges. Hozzáférés-vezérléssel titkos kulcsok hozzáférés-vezérlés függetlenek.  

A következő engedélyeket is használhatók, egy egyszerű neve alapján, egy tároló titkos kulcsok hozzáférés-vezérlési bejegyzés, és szorosan tükrözésének titkos-objektum engedélyezett műveletek:  

-   *Állítsa be*: új titkos kulcsok létrehozása  
-   *első*: olvassa el a titkos kulcs  
-   *lista*: a titkos kulcsok vagy a kulcstároló tárolt titkos kulcs verzióinak listázása  
-   *Törlés*: a titkos kulcs törlése  
-   *minden*: minden engedély  

A titkos kulcsok munkáról bővebben lásd: [titkos műveletek](/rest/api/keyvault/secret-operations).  

###  <a name="BKMK_SecretTags"></a> Titkos címkék  
Az alkalmazás-specifikus metaadatokat címkék formájában is megadhat. Az Azure Key Vault támogatja legfeljebb 15 címkéket, amelyek mindegyike egy 256 karakter hosszúságú nevet és 256 karakter hosszúságú értéket is rendelkezhetnek.  

>[!Note]
>Címke található a hívó által is olvasható, ha rendelkeznek a *lista* vagy *beolvasása* szóban forgó objektumtípushoz; engedéllyel kulcsokat, titkos kulcsokat vagy tanúsítványokat.

## <a name="key-vault-certificates"></a>Kulcstároló-tanúsítványok

Key Vault tanúsítványok támogatás lehetővé teszi a x509 vezetése a tanúsítványok és az alábbiak egyike:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa hozzon létre egy tanúsítványt, vagy a meglévő tanúsítvány importálása a Key Vault létrehozási folyamata keresztül. Mindkét önaláírt és a hitelesítésszolgáltató tanúsítványokat jön létre.
-   Lehetővé teszi, hogy a Key Vault tanúsítvány tulajdonosa biztonságos tárolása és kezelése X509 végrehajtásához tanúsítványok és titkos kulcs adatai közötti interakció nélkül.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosának egy házirendet, amely arra utasítja a Key Vault kezeléséhez az életciklus-tanúsítvány létrehozásához.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonos kapcsolattartási kapcsolatos adatok megadása az értesítéshez életciklus-események lejárati és a tanúsítvány megújítása.  
-   Támogatja az automatikus megújítását a kiválasztott kiállítók - Key Vault partner X509 szolgáltatók tanúsítvány / hitelesítésszolgáltatók.

>[!Note]
>Nem közösen szolgáltatók/hitelesítésszolgáltatók is használhatók, de nem támogatja az automatikus megújítási szolgáltatást.

###  <a name="BKMK_CompositionOfCertificate"></a> A tanúsítvány összeállítás

A Key Vault tanúsítvány jön létre, amikor egy megcímezhető kulcs és a titkos kulcsot is létrejönnek ugyanazzal a névvel. A Key Vault kulcs lehetővé teszi, hogy a kulcs műveleteket, és a Key Vault titkos kulcs lehetővé teszi, hogy a titkos kulcs, a tanúsítvány érték beolvasása. A Key Vault tanúsítvány is tartalmaz nyilvános x509 tanúsítvány-metaadatok.  

Az azonosító és a tanúsítványok verziója hasonlít kulcsok és titkos kulcsok. A Key Vault tanúsítvány válaszul egy megcímezhető kulcs és a titkos kulcsot a Key Vault tanúsítvány verziójával létrehozott egy adott verziójához érhető el.
 
![Cetificates olyan összetett objektumok](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Kulcs exportálható vagy nem exportálható

A Key Vault tanúsítvány jön létre, amikor lekérhető a megcímezhető titokból PFX vagy PEM formátumú a titkos kulccsal, ha a tanúsítvány létrehozásához használt házirend azt mutatták ki, hogy a kulcs exportálható-e. Ha a házirend a Key Vault tanúsítvány létrehozásához használt jelzi a kulcs nem exportálható, majd a titkos kulcs része nem az érték, ha egy titkos kulcsként lekérdezése.  

A megcímezhető kulcs nem exportálható KV tanúsítványokkal több megfelelő válik. A megcímezhető KV kulcs műveletek van leképezve a *keyusage* a KV tanúsítási szabályzat a KV tanúsítvány létrehozásához használt mezőjében.  

Kétféle típusú kulcs támogatottak – *RSA* vagy *RSA HSM* tanúsítványokkal. Exportálható csak engedélyezett az RSA RSA HSM által nem támogatott.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Tanúsítvány-attribútumok és címkék

Mellett tanúsítvány metaadatait, a megcímezhető kulcsot, és egy megcímezhető titkos kulcsot, a Key Vault tanúsítvány azt is tartalmaz, attribútumokat és címkék.  

#### <a name="attributes"></a>Attribútumok

A tanúsítvány attribútumait tükrözi, a attribútumait a megcímezhető kulcsot és titkos kulcs jön létre, amikor KV tanúsítvány jön létre.  

A Key Vault tanúsítvány a következő attribútumokkal rendelkezik:  

-   *engedélyezett*: boolean, nem kötelező, alapértelmezett érték a **igaz**. Ez az attribútum is megadható annak jelzésére, hogy a tanúsítvány adatainak beolvasható titkos vagy működtethető kulcsként. Együtt használatos *nbf* és *exp* művelet esetén közötti *nbf* és *exp*, akkor lesz csak engedélyezett, ha engedélyezve van értéke TRUE. Kívül a *nbf* és *exp* ablakban automatikusan engedélyezettek.  

Nincsenek a válaszban szereplő további írásvédett attribútumok:

-   *létrehozott*: IntDate: azt jelzi, hogy jelen verziójában a tanúsítvány létrehozásakor.  
-   *frissített*: IntDate: azt jelzi, amikor ez a tanúsítvány verziója módosult.  
-   *Exp*: IntDate: tartalmazza a x509 lejárati idejét értékének tanúsítványt.  
-   *NBF*: IntDate: a x509 dátumának értéke tanúsítványt.  

> [!Note] 
> A Key Vault tanúsítvány lejár, ha megcímezhető kulcsa, és a titkos kulcs működésképtelenné válik.  

#### <a name="tags"></a>Címkék

 Ügyfél megadott szótár kulcs érték párok, a kulcsok és titkos kódok hasonló.  

 > [!Note]
> Címke található a hívó által is olvasható, ha rendelkeznek a *lista* vagy *beolvasása* szóban forgó objektumtípushoz; engedéllyel kulcsokat, titkos kulcsokat vagy tanúsítványokat.

###  <a name="BKMK_CertificatePolicy"></a> Tanúsítványszabályzat

Tanúsítvány-házirend létrehozása és kezelése KV tanúsítvány életciklusát információkat tartalmaz. A titkos kulcsot tartalmazó tanúsítvány a key vault való importálásakor, alapértelmezett házirend hozta létre a x509 olvasása tanúsítványt.  

Amikor KV tanúsítvány újként jön létre, egy házirend meg kell adni a Key Vault tájékoztatja a KV tanúsítvány vagy a következő KV tanúsítvány verziója létrehozásával. Ha olyan házirend lett létrehozva, nincs szükség az egymást követő műveletek létrehozása a következő KV tanúsítvány verziók létrehozása.  

Nincs házirend KV tanúsítványt mindegyik verziójában csak egy példánya.  

Magas szinten tanúsítvány-házirend a következőket tartalmazza:  

-   X509 tanúsítvány tulajdonságai: tartalmaz tulajdonos neve, a tulajdonos alternatív neveket stb. egy x509 létrehozásához használt tanúsítványkérelmet.  
-   Kulcstulajdonságok: kulcs típust tartalmaz, kulcshossz, exportálható, és újra felhasználhatja kulcsmezők. Ezek a mezők kérje meg a kulcs létrehozásához hogyan kulcstároló.  
-   Titkos tulajdonságok: tartalomtípusa megcímezhető titkos kulcs lekérése során a titkos kulcs tanúsítványát, a titkos értéke létrehozásához például titkos tulajdonságait tartalmazza.  
-   Élettartam műveletek: a KV tanúsítvány élettartama műveleteket tartalmazza. Minden élettartama művelet tartalmaz:  

     - Eseményindító: nappal a lejárati vagy élettartama span százalékos előtt megadott  

     - Művelet: Adja meg a művelet típusa – *emailContacts* vagy *autoRenew*  

-   Kibocsátó: A tanúsítvány kiállítójának a tanúsítványok kiállításához x509 használatára vonatkozó paraméterek.  
-   Házirend attribútumainak: a házirendhez társított attribútumokat tartalmaz  

#### <a name="x509-to-key-vault-usage-mapping"></a>A Key Vault használatának leképezési X509

A következő táblázat a Key Vault tanúsítvány létrehozása részeként létrehozott kulcs hatékony kulcsú műveletek kulcshasználat házirend x509 leképezése jelöli.

|**X509 kulcshasználat jelzők**|**Key Vault kulcs ops**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|DataEncipherment|titkosít, visszafejtés| – |
|DecipherOnly|visszafejtés| –  |
|DigitalSignature|Ellenőrizze, aláírása| Key Vault alapértelmezett egy tanúsítvány létrehozáskor használati megadása nélkül | 
|EncipherOnly|encrypt| – |
|KeyCertSign|Ellenőrizze, aláírása|–|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault alapértelmezett egy tanúsítvány létrehozáskor használati megadása nélkül | 
|Letagadhatatlanság|Ellenőrizze, aláírása| – |
|crlsign|Ellenőrizze, aláírása| – |

###  <a name="BKMK_CertificateIssuer"></a> Tanúsítvány kiállítója

A Key Vault tanúsítványobjektum való kommunikációhoz egy kijelölt kiállító tanúsítványszolgáltató rendelés x509 tanúsítványok használt konfiguráció tartalmazza.  

-   Key Vault partnereknek a következő tanúsítvány kiállítója szolgáltatók az SSL-tanúsítványok

|**A szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|Támogatja a nyilvános felhőben és Azure Government összes kulcstároló szolgáltatási helyek|
|GlobalSign|Támogatja a nyilvános felhőben és Azure Government összes kulcstároló szolgáltatási helyek|

Mielőtt egy tanúsítvány kiállítója hozhatja létre a kulcstároló, előfeltételként szükséges lépések 1 és 2 kell sikeresen érhető el.  

1. Érheti el a tanúsítvány hitelesítésszolgáltatói (CA) szolgáltatók  

    -   Egy szervezet rendszergazdája a helyi kell a vállalat (például) Contoso) legalább egy hitelesítésszolgáltató-szolgáltatóval.  

2. A rendszergazda létrehozza a hitelesítő adatokat igénylő Key Vault szolgáltatás SSL-tanúsítványok igénylését (és megújítását)  

    -   A szolgáltató kibocsátó objektumot létrehozni a key vaultban használandó konfigurációjának biztosít  

A kibocsátó objektumainak létrehozásakor a Certificates portálról további információkért lásd: a [Key Vault tanúsítványok blog](http://aka.ms/kvcertsblog)  

Key Vault lehetővé teszi, hogy a különböző kibocsátó szolgáltató konfigurálása több kibocsátó objektumok létrehozásához. Egy kiállító objektum létrehozása után a neve egy vagy több tanúsítvány-házirendek lehet hivatkozni. A kibocsátó objektum hivatkozik arra utasítja a Key Vault szolgáltatás a kibocsátó objektumban megadott konfigurációját használja, ha a x509 kérő tanúsítványt a CA-szolgáltató a tanúsítvány létrehozása és a megújítás során.  

Kibocsátó objektumok jönnek létre a tárolóban, és csak akkor használható KV tanúsítványokkal ugyanabban a tárolóban.  

###  <a name="BKMK_CertificateContacts"></a> Tanúsítvány kapcsolattartók

Tanúsítvány névjegyek tartalmazhat elérhetőségi adatait a tanúsítvány élettartamának események által kiváltott értesítések küldéséhez. A kapcsolattartási adatok a kulcstároló kulcsain legyen elosztva a tanúsítványokat. Egy értesítést küld egy eseményt, a key vaultban tanúsítvánnyal az összes megadott ügyfelet.  

Ha egy tanúsítvány-házirend beállítása automatikus megújítási, majd értesítést küld el a következő események.  

-   Mielőtt tanúsítványának megújítása
-   Miután a tanúsítvány megújításához, amely meghatározza, hogy ha a tanúsítvány megújítása sikeres volt, vagy ha hiba történt, hogy manuális megújítása a tanúsítvány.  

 Ha egy tanúsítvány-házirend van beállítva manuálisan megújított (csak e-mailekhez), majd értesítést küld amikor a tanúsítvány megújításához.  

###  <a name="BKMK_CertificateAccessControl"></a> Tanúsítvány hozzáférés-vezérlés

 A tanúsítványok hozzáférés-vezérlés Key Vault kezeli, és a kulcstároló, úgy működik, mint azok a tanúsítványok a tároló szintjén van megadva. Nincs hozzáférés-vezérlési szabályzatok tanúsítványok, amely nem azonos azzal a kulcsok és titkos kulcsainak azonos a Key Vault hozzáférés-vezérlési házirend. Felhasználók hozhat létre egy vagy több tárolók tanúsítványok tárolásához, és megfelelő Szegmentálás forgatókönyv és a felügyeleti tanúsítványok szükségesek.  

 Egy egyszerű módon, a titkos kulcsok hozzáférés-vezérlési bejegyzés a kulcstároló, és szorosan tükör titkos-objektum engedélyezett műveletek használható a következő engedélyekkel:  

-   *első*: lehetővé teszi, hogy az aktuális tanúsítvány vagy bármely verziója tanúsítvány lekérése 
-   *lista*: lehetővé teszi, hogy az aktuális tanúsítványokat vagy a tanúsítvány verzióinak listája  
-   *Törlés*: lehetővé teszi, hogy a tanúsítványt, a házirend-és annak verzióit az összes törlése  
-   *Hozzon létre*: lehetővé teszi a Key Vault-tanúsítvány létrehozásához.  
-   *Importálás*: lehetővé teszi, hogy a tanúsítvány anyag importálása a Key Vault tanúsítvány.  
-   *frissítés*: lehetővé teszi, hogy a tanúsítvány frissítése.  
-   *manageconnects*: lehetővé teszi, hogy a Key Vault tanúsítvány ügyfelek felügyelete  
-   *getissuers*: lehetővé teszi, hogy egy tanúsítványkiállítókat get  
-   *listissuers*: lehetővé teszi, hogy a tanúsítvány tanúsítványkibocsátók listája  
-   *setissuers*: lehetővé teszi, hogy létrehozni vagy frissíteni a Key Vault tanúsítványkiállítók  
-   *deleteissuers*: lehetővé teszi, hogy a Key Vault tanúsítványkiállítók törlése  
-   *minden*: minden engedélyt megad a  

## <a name="additional-information-for-certificates"></a>További információ a tanúsítványok

- [Tanúsítványok és házirendek](/rest/api/keyvault/certificates-and-policies)
- [Tanúsítványkiállítók](/rest/api/keyvault/certificate-issuers)
- [Tanúsítvány kapcsolattartók](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>Lásd még:

- [Hitelesítési kérések és válaszok](authentication-requests-and-responses.md)
- [Kulcstároló-verziók](key-vault-versions.md)
- [Kulcs Vault fejlesztői útmutatója](/azure/key-vault/key-vault-developers-guide)
