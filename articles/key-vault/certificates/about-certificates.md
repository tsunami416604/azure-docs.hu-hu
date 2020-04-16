---
title: Az Azure Key Vault-tanúsítványok – Azure Key Vault
description: Az Azure Key Vault REST-felületének és tanúsítványainak áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e9fbe624563ad03880ff1a75efdc2df41b151846
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424278"
---
# <a name="about-azure-key-vault-certificates"></a>Az Azure Key Vault-tanúsítványok –

Az Azure Key Vault lehetővé teszi a Microsoft Azure-alkalmazások és -felhasználók számára a kulcsokra és titkos kulcsokra épülő tanúsítványok tárolását és használatát, valamint egy automatikus megújítási funkció hozzáadását.

A Key Vaultról további általános információt a [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

A következő szakaszok általános információkat nyújtanak a Key Vault szolgáltatás megvalósításában.

### <a name="supporting-standards"></a>Támogató szabványok

A JavaScript objektumjelölés (JSON) és a JavaScript-objektumaláíró és -titkosítási (JOSE) specifikációk fontos háttérinformációk.  

-   [JSON webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON webes titkosítás (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON webes algoritmusok (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámmal rendelkeznek, amikor egy objektum új példányát hozják létre. Minden verzió hoz egy egyedi azonosítót és URL-címet. Amikor egy objektumot először hoznak létre, egyedi verzióazonosítót kap, és az objektum aktuális verziójaként van megjelölve. Az azonos objektumnévvel rendelkező új példány létrehozása egyedi verzióazonosítót ad az új objektumnak, így az lesz az aktuális verzió.  

A Key Vault objektumai az aktuális azonosító vagy egy verzióspecifikus azonosító használatával címezhetők. Ha például egy kulcsot `MasterKey`ad meg a nevével, az aktuális azonosítóval végzett műveletek végrehajtása a rendszer a legújabb elérhető verziót használja. Ha a verzióspecifikus azonosítóval hajt végre műveleteket, a rendszer az objektum adott verzióját használja.  

Az objektumok egyedileg azonosíthatók a Key Vaultban egy URL-cím használatával. A rendszerben nincs két objektum azonos URL-címe, függetlenül a földrajzi helytől. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely azonosítja a Key Vaultot, az objektumtípust, a felhasználó által megadott objektumnevet és az objektumverziót. Az objektumneve nem i. és nem módosítható. Az objektumverziót nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatás egyik kulcstartójának neve.<br /><br /> A Key Vault-neveket a felhasználó választja ki, és globálisan egyediek.<br /><br /> A Key Vault nevének 3-24 karakterből álló karakterláncnak kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-type`|Az objektum típusa, "kulcsok" vagy "titkos kulcsok".|  
|`object-name`|Az `object-name` egy felhasználó által megadott nevet, és egyedinek kell lennie a Key Vault.An is a user provided name for and must be unique within a Key Vault. A névnek 1-127 karakterből kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-version`|A `object-version` rendszer által létrehozott, 32 karakteres karakterlánc-azonosító, amely opcionálisan egy objektum egyedi verziójának címzésére szolgál.|  


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
 
![A tanúsítványok összetett objektumok](../media/azure-key-vault.png)

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

## <a name="see-also"></a>Lásd még:

- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
