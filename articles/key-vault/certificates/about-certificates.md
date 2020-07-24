---
title: Tudnivalók Azure Key Vault tanúsítványokról – Azure Key Vault
description: A REST-felület és a tanúsítványok Azure Key Vault áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 74007f5d10d58cf9680d4531304098cabe9b6d8b
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87115675"
---
# <a name="about-azure-key-vault-certificates"></a>Információk az Azure Key Vault-tanúsítványokról

Key Vault tanúsítványok támogatása biztosítja a x509-tanúsítványok kezelését, valamint a következő viselkedéseket:  

-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa egy Key Vault létrehozási folyamaton vagy egy meglévő tanúsítvány importálásán keresztül hozzon létre tanúsítványt. Magában foglalja az önaláírt és a hitelesítésszolgáltató által generált tanúsítványokat is.
-   Lehetővé teszi, hogy a Key Vault tanúsítvány tulajdonosa a X509-tanúsítványok biztonságos tárolását és kezelését a titkos kulcsokkal való interakció nélkül implementálja.  
-   Lehetővé teszi, hogy a tanúsítvány tulajdonosa olyan házirendet hozzon létre, amely a tanúsítvány életciklusának kezeléséhez Key Vault irányítja.  
-   Lehetővé teszi a tanúsítvány tulajdonosai számára, hogy kapcsolattartási adatokat szolgáltassanak a lejárat és a tanúsítvány megújításának életciklusával kapcsolatos eseményekről.  
-   Támogatja az automatikus megújítást a kiválasztott kiállítókkal – Key Vault partneri X509-szolgáltatók/hitelesítésszolgáltatók.

>[!Note]
>A nem partnerrel rendelkező szolgáltatók/hatóságok is engedélyezettek, de nem támogatják az automatikus megújítási funkciót.

## <a name="composition-of-a-certificate"></a>Tanúsítvány összetétele

Key Vault-tanúsítvány létrehozásakor a rendszer megcímezhető kulcsot és titkos kulcsot is létrehoz ugyanazzal a névvel. A Key Vault kulcs lehetővé teszi a kulcsfontosságú műveleteket, és a Key Vault titkos kulcs lehetővé teszi a tanúsítvány értékének beolvasását titkos fájlként. A Key Vault tanúsítvány A nyilvános x509 tanúsítvány-metaadatokat is tartalmaz.  

A tanúsítványok azonosítója és verziója hasonló a kulcsok és a titkos kódokhoz. Az Key Vault-tanúsítvány verziószámával létrehozott címezhető kulcs és titok adott verziója elérhető a Key Vault-tanúsítvány válaszában.
 
![A tanúsítványok összetett objektumok](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exportálható vagy nem exportálható kulcs

Key Vault-tanúsítvány létrehozásakor a megcímezhető titkos kulcsból a PFX vagy PEM formátumban kérhető le. A tanúsítvány létrehozásához használt házirendnek jeleznie kell, hogy a kulcs exportálható. Ha a házirend nem exportálható állapotot jelez, akkor a titkos kulcs nem része az értéknek, amikor a rendszer titokként kéri le.  

A címezhető kulcs nagyobb jelentőséggel bír a nem exportálható KV-tanúsítványok esetén. A megcímezhető KV-kulcs műveletei a KV tanúsítvány létrehozásához használt KV-os tanúsítvány-házirend *kulcshasználat* mezőjéből vannak leképezve.  

Két típusú kulcs támogatott: *RSA* vagy *RSA HSM* tanúsítványokkal. Az exportálhatóként csak az RSA-mel engedélyezett, az RSA HSM nem támogatja.  

## <a name="certificate-attributes-and-tags"></a>Tanúsítvány attribútumai és címkéi

A tanúsítvány metaadatainak, a megcímezhető kulcs és a címezhető titok mellett egy Key Vault tanúsítvány is tartalmaz attribútumokat és címkéket.  

### <a name="attributes"></a>Attribútumok

A tanúsítvány attribútumai a megcímezhető kulcs és titkos kód attribútumaira vannak tükrözve, amikor létrejön a KV-os tanúsítvány.  

A Key Vault tanúsítványa a következő tulajdonságokkal rendelkezik:  

-   *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Megadható annak jelzése, hogy a tanúsítvány adatai titkos fájlként, vagy kulcsként működnek-e. A *NBF* és az *exp* együttes használata esetén is használatos, ha a *NBF* és az *exp*közötti művelet történik, és csak akkor lesz engedélyezve, ha a beállítás értéke TRUE (igaz). A *NBF* és az *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva.  

A válaszban további írásvédett attribútumok is szerepelnek:

-   *Létrehozva*: IntDate: azt jelzi, hogy mikor jött létre a tanúsítvány ezen verziója.  
-   *frissítve*: IntDate: azt jelzi, hogy a tanúsítvány ezen verziója frissítve lett-e.  
-   *exp*: IntDate: az x509-tanúsítvány lejárati dátumának értékét tartalmazza.  
-   *NBF*: IntDate: a x509-tanúsítvány dátumának értékét tartalmazza.  

> [!Note] 
> Ha egy Key Vault-tanúsítvány lejár, az ahhoz tartozó címezhető kulcs és titkos kulcs nem fog működni.  

### <a name="tags"></a>Címkék

 Az ügyfél a kulcsok és titkok címkéhez hasonlóan a kulcs érték párok szótárát adta meg.  

 > [!Note]
> A címkék a hívó által olvashatók, ha rendelkeznek a *listával* , vagy *kapnak* engedélyt az adott objektumtípus (kulcsok, titkos kódok vagy tanúsítványok) számára.

## <a name="certificate-policy"></a>Tanúsítvány-házirend

A tanúsítvány-szabályzat a Key Vault-tanúsítványok életciklusának létrehozásával és kezelésével kapcsolatos információkat tartalmaz. Ha egy titkos kulccsal rendelkező tanúsítványt importál a kulcstartóba, a rendszer létrehoz egy alapértelmezett szabályzatot az x509-tanúsítvány beolvasásával.  

Ha egy Key Vault-tanúsítvány teljesen létre lett hozva, a szabályzatot meg kell adni. A házirend meghatározza, hogyan kell létrehozni ezt a Key Vault-tanúsítvány verzióját vagy a következő Key Vault-tanúsítvány verzióját. A szabályzat létrehozása után a későbbi verziókhoz nem szükséges egymást követő létrehozási művelet. Egy Key Vault-tanúsítvány összes verziójához csak egy példány van.  

A tanúsítvány-házirend magas szinten a következő adatokat tartalmazza (a definíciók [itt](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0)találhatók):  

-   X509 tanúsítvány tulajdonságai: a tulajdonos nevét, a tulajdonos alternatív neveit, valamint az X509-tanúsítványkérelem létrehozásához használt egyéb tulajdonságokat tartalmazza.  
-   Kulcs tulajdonságai: kulcs típusát, kulcs hosszát, exportálható és ReuseKeyOnRenewal mezőket tartalmaz. Ezek a mezők a Key vaultot tájékoztatják a kulcsok létrehozásáról.  
-   Titkos kód tulajdonságai: olyan titkos tulajdonságokat tartalmaz, mint például a címezhető titok tartalomtípusa a titkos érték létrehozásához, a tanúsítvány titkosként való lekéréséhez.  
-   Élettartam-műveletek: a KV-os tanúsítvány élettartamának műveleteit tartalmazza. Minden élettartam-művelet tartalmazza a következőket:  

     - Trigger: a lejárat vagy az élettartam százalékában megadott napokon keresztül van megadva  

     - Művelet: Művelettípus megadása – *emailContacts* vagy *autorenew*  

-   Kiállító: az x509-tanúsítványok kiállításához használt tanúsítvány kiállítójának paraméterei.  
-   Házirend-attribútumok: a Szabályzathoz társított attribútumokat tartalmazza.  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 Key Vault használati leképezéshez

A következő táblázat a x509-használati szabályzat hozzárendelését mutatja be egy Key Vault tanúsítvány létrehozása során létrehozott kulcs hatékony működéséhez.

|**X509-használat jelzői**|**Key Vault Key Ops**|**Alapértelmezett viselkedés**|
|----------|--------|--------|
|DataEncipherment|titkosítás, visszafejtés| n.a. |
|DecipherOnly|visszafejteni| n.a.  |
|DigitalSignature|aláírás, ellenőrzés| Key Vault alapértelmezett beállítás a tanúsítvány létrehozási idejének használati specifikációja nélkül | 
|EncipherOnly|encrypt| n.a. |
|KeyCertSign|aláírás, ellenőrzés|n.a.|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault alapértelmezett beállítás a tanúsítvány létrehozási idejének használati specifikációja nélkül | 
|Letagadhatatlanság nyújtására|aláírás, ellenőrzés| n.a. |
|crlsign|aláírás, ellenőrzés| n.a. |

## <a name="certificate-issuer"></a>Tanúsítvány kiállítója

A Key Vault Certificate objektum olyan konfigurációt tartalmaz, amely a kiválasztott tanúsítvány-kiállító szolgáltatóval való kommunikációra szolgál a x509-tanúsítványok megrendeléséhez.  

-   Key Vault partnerek a következő tanúsítvány-kiállító szolgáltatókkal a TLS/SSL-tanúsítványokhoz

|**Szolgáltató neve**|**Helyek**|
|----------|--------|
|DigiCert|A nyilvános felhőben és Azure Governmentban a Key Vault szolgáltatás összes helyén támogatott|
|GlobalSign|A nyilvános felhőben és Azure Governmentban a Key Vault szolgáltatás összes helyén támogatott|

Ahhoz, hogy a tanúsítvány kiállítója egy Key Vaulton legyen létrehozva, az 1. és a 2. előfeltételként szükséges lépések végrehajtása után sikeresen el kell végezni a műveletet.  

1. Beléptetés a HITELESÍTÉSSZOLGÁLTATÓI szolgáltatókba  

    -   A vállalati rendszergazdának be kell jelentkeznie a vállalatnál (pl. Contoso) legalább egy HITELESÍTÉSSZOLGÁLTATÓI szolgáltatóval.  

2. A rendszergazda létrehoz egy kérelmező hitelesítő adatokat a Key Vault számára a TLS/SSL-tanúsítványok regisztrálásához (és megújításához)  

    -   A Key vaultban a szolgáltató kiállító objektumának létrehozásához használandó konfigurációt adja meg.  

A kiállítói objektumok tanúsítvány-portálról történő létrehozásával kapcsolatos további információkért tekintse meg a [Key Vault tanúsítványok blogját](https://aka.ms/kvcertsblog) .  

Key Vault lehetővé teszi több kiállító objektum létrehozását különböző kiállítói szolgáltatói konfigurációval. A kiállítói objektum létrehozása után a neve hivatkozhat egy vagy több tanúsítvány-házirendre. A kiállító objektumra való hivatkozás arra utasítja a Key Vault, hogy a kiállító objektumban megadott konfigurációt használja, amikor a tanúsítvány létrehozásakor és megújításakor a x509-tanúsítványt kéri a CA-szolgáltatótól.  

A kiállító objektumok a tárolóban jönnek létre, és csak a KV tanúsítványokkal használhatók ugyanabban a tárolóban.  

## <a name="certificate-contacts"></a>Tanúsítványhoz tartozó névjegyek

A tanúsítványhoz tartozó névjegyek kapcsolattartási adatokat tartalmaznak a tanúsítvány élettartama eseményei által aktivált értesítések küldéséhez. A kapcsolattartási adatokat a Key Vault összes tanúsítványa megosztja. A Key Vault bármelyik tanúsítványa esetében értesítést küld a rendszer az adott esemény összes megadott ügyfelének.  

Ha a tanúsítvány házirendje automatikus megújításra van beállítva, akkor a rendszer értesítést küld a következő eseményekről.  

- A tanúsítvány megújítása előtt
- A tanúsítvány megújítása után, ha a tanúsítvány sikeres megújítása megtörtént, vagy ha hiba történt, a tanúsítvány manuális megújítását kell megkövetelni.  

  Ha egy manuálisan megújított tanúsítvány-házirend (csak e-mail-cím), akkor a rendszer értesítést küld, amikor a tanúsítvány megújítására kerül sor.  

## <a name="certificate-access-control"></a>Tanúsítvány Access Control

 A tanúsítványok hozzáférés-vezérlését a Key Vault kezeli, és az adott tanúsítványokat tartalmazó Key Vault biztosítja. A tanúsítványok hozzáférés-vezérlési házirendje különbözik az azonos Key Vault található kulcsok és titkok hozzáférés-vezérlési házirendjeitől. A felhasználók egy vagy több tárolót hozhatnak létre a tanúsítványok tárolására, a forgatókönyvek megfelelő szegmentálásának és a tanúsítványok kezelésének fenntartása érdekében.  

 A következő engedélyek a Key Vault Secrets hozzáférés-vezérlési bejegyzésében, és a titkos objektumon engedélyezett műveletek részletes tükrözése céljából használhatók:  

- Tanúsítványkezelő műveletekhez szükséges engedélyek
  - *beolvasás*: a tanúsítvány aktuális verziójának vagy bármely verziójának lekérése 
  - *lista*: a tanúsítvány aktuális tanúsítványait vagy verzióit sorolja fel.  
  - *frissítés*: tanúsítvány frissítése
  - *Létrehozás*: Key Vault tanúsítvány létrehozása
  - *Importálás*: tanúsítvány importálása Key Vault tanúsítványba
  - *Törlés*: tanúsítvány törlése, házirendje és minden verziója  
  - *helyreállítás*: törölt tanúsítvány helyreállítása
  - *biztonsági mentés*: tanúsítvány biztonsági mentése egy kulcstartóban
  - *visszaállítás*: biztonsági másolat készítése a tanúsítványról egy kulcstartóra
  - *managecontacts*: Key Vault tanúsítványok kapcsolatainak kezelése  
  - *manageissuers*: Key Vault hitelesítésszolgáltatók/kiállítók kezelése
  - *getissuers*: tanúsítvány hitelesítő szerveinek/kiállítóinak beszerzése
  - *listissuers*: a tanúsítvány hatóságainak/kiállítóinak listázása  
  - *setissuers*: Key Vault tanúsítvány szerveinek/kiállítóinak létrehozása vagy frissítése  
  - *deleteissuers*: Key Vault tanúsítvány hatóságainak/kiállítóinak törlése  
 
- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: törölt tanúsítvány törlése (végleges törlése)

További információkért tekintse meg a [tanúsítványok műveleteit a Key Vault REST API-referenciában](/rest/api/keyvault). Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>További lépések

- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](../general/about-keys-secrets-certificates.md)
- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
