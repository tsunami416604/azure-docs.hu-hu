---
title: Virtualizálás-alapú biztonság (VBS) protokoll az Azure-igazoláshoz
description: VBS-igazolási protokoll
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91343784"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>Virtualizálás-alapú biztonság (VBS) igazolási protokoll 

Ahhoz, hogy Microsoft Azure igazolást biztosítson, hogy az adatok hitelesek legyenek, a belső vezérlőprogram a hypervisor és a Secure kernel elindításához szükséges megbízhatósági láncot kell létrehoznia. Ahhoz, hogy ez az Azure-tanúsítvány elérhető legyen, a biztonságos enklávéban való megbízhatósági kapcsolat létrehozása előtt igazolnia kell a gép rendszerindítási állapotát. Az operációs rendszert, a Hypervisort és a Secure kernel bináris fájljait a megfelelő hivatalos Microsoft-hatóságoknak kell aláírnia, és azokat biztonságos módon kell konfigurálni. Ha kötött megbízhatósági kapcsolat van a platformmegbízhatósági modul (TPM) és a hypervisor állapota között, akkor a mért rendszerindítási naplóban megadott VBS-IDKS megbíznak. Ezzel ellenőrizheti, hogy egy kulcspár létrejött-e az enklávé és egy olyan igazolási jelentés, amely az adott kulcs megbízhatóságát köti össze, és más jogcímeket, például a biztonsági szintet és a rendszerindítási igazolási tulajdonságokat is tartalmaz.

## <a name="protocol-messages"></a>Protokoll üzenetei

### <a name="init-message"></a>Init-üzenet

#### <a name="direction"></a>Irány

Ügyfél – > Azure-igazolás

#### <a name="payload"></a>Adattartalom

```
{
  "type": "aikcert"
}
```

"type" (ASCII karakterlánc): a kért igazolás típusát jelöli. Jelenleg csak a "aikcert" támogatott.

### <a name="challenge-message"></a>Kérdéses üzenet

#### <a name="direction"></a>Irány

Azure-igazolás – > ügyfél

#### <a name="payload"></a>Adattartalom

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**Challenge** (BASE64URL (oktett)): a szolgáltatás által kiállított véletlenszerű érték.

**service_context** (BASE64URL (oktett)): a szolgáltatás által létrehozott átlátszatlan, titkosított környezet, amely többek között magában foglalja a kihívást és az adott kihívás lejárati idejét.


### <a name="request-message"></a>Kérelem üzenete

#### <a name="direction"></a>Irány

Ügyfél – > Azure-igazolás 

#### <a name="payload"></a>Adattartalom

```
{

  "request": "<JWS>"
  
}
```

**kérelem** (JWS): a kérelem egy JSON web Signature (JWS) struktúrából áll. A JWS által védett fejléc és JWS adattartalom alább látható. Ahogy a JWS struktúrában, a végső érték a következőkből áll:

BASE64URL (UTF8 (JWS Protected header)) | | '.' ||

BASE64URL (JWS hasznos adat) | | '.' ||

BASE64URL (JWS-aláírás)

##### <a name="jws-protected-header"></a>JWS által védett fejléc

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS hasznos adat

A JWS-tartalom alapszintű vagy VBS típusú lehet. Az alapszintű érték akkor használatos, ha a tanúsítvány igazolása nem tartalmaz VBS-adatokat.

Alapszintű példa

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

VBS példa

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (StringOrURI): függő entitás azonosítója. A szolgáltatás használja a számítógép-azonosító jogcím kiszámításához

**rp_data** (BASE64URL (oktettek)): a függő entitás által átadott átlátszatlan adatvesztés. Ezt a függő entitás általában a jelentés frissességének biztosítására használja.

**Challenge** (BASE64URL (oktett)): a szolgáltatás által kiállított véletlenszerű érték

**tpm_att_data**: TPM-hez kapcsolódó igazolási adat

- **srtm_boot_log (BASE64URL (oktettek))**: az SRTM rendszerindítási napló lekérése a (z) log type = TBS_TCGLOG_SRTM_BOOT függvény Tbsi_Get_TCG_Log_Ex

- **srtm_resume_log (BASE64URL (oktettek))**: a függvény Tbsi_Get_TCG_Log_Ex a log type = TBS_TCGLOG_SRTM_RESUME paranccsal folytatja a SRTM.

- **drtm_boot_log (BASE64URL (oktettek))**: az drtm rendszerindítási napló lekérése a (z) log type = TBS_TCGLOG_DRTM_BOOT függvény Tbsi_Get_TCG_Log_Ex

- **drtm_resume_log (BASE64URL (oktettek))**: a függvény Tbsi_Get_TCG_Log_Ex a log type = TBS_TCGLOG_DRTM_RESUME paranccsal folytatja a drtm.

- **aik_cert (BASE64URL (oktettek))**: az AIK X. 509 tanúsítványa, amelyet a (z) tulajdonsággal rendelkező Function NCryptGetProperty adott vissza. NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: az AIK nyilvános része JSON webkulcs (JWK) objektumként van megjelölve (RFC 7517)

- **current_claim (BASE64URL (oktettek))**: az aktuális PCR-állapothoz tartozó igazolási jogcím, amelyet a dwClaimType = NCRYPT_CLAIM_PLATFORM és a (z) NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK paraméterrel rendelkező NCryptCreateClaim függvény ad vissza, amely tartalmazza az összes PCR. A szolgáltatás által eljuttatott kihívást is fel kell használni a jogcím kiszámításához.

- **boot_claim (BASE64URL (bájt))**: az igazolási jogcímet, amelyet a rendszer a (z) dwClaimType = NCRYPT_CLAIM_PLATFORM és NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK paraméterrel rendelkező NCryptCreateClaim függvény által visszaadott rendszerindítási jogcímen visszaadott, az összes PCR

**vbs-jelentés** (BASE64URL (oktettek)): a vbs enklávé igazolási jelentése, amelyet a függvény EnclaveGetAttestationReport adott vissza. A EnclaveData paraméternek a report_signed értékének SHA-512 kivonatának kell lennie (beleértve a nyitó és a záró kapcsos zárójeleket is). A kivonatoló függvény bemenete UTF8 (report_signed)

**attest_key**: az enklávé kulcsának nyilvános része JSON webkulcs (JWK) objektumként van megjelölve (RFC 7517)

**custom_claims**: a szolgáltatásnak eljuttatott egyéni enklávé-jogcímek tömbje, amelyet a szabályzat kiértékel. A jogcím

- **név (karakterlánc)**: a jogcím neve. Ezt a nevet a rendszer hozzáfűzi az igazolási szolgáltatás által meghatározott URL-címhez (az ütközések elkerülése érdekében), az összefűzött karakterlánc pedig a szabályzatban használható jogcím típusa lesz.

- **érték (karakterlánc)**: a jogcím értéke

- **value_type (karakterlánc)**: a jogcím értékének adattípusa

**service_context** (BASE64URL (oktett)): a szolgáltatás által létrehozott átlátszatlan, titkosított környezet, amely többek között magában foglalja a kihívást és az adott kihívás lejárati idejét.

### <a name="report-message"></a>Jelentés üzenete

#### <a name="direction"></a>Irány

Azure-igazolás – > ügyfél

#### <a name="payload"></a>Adattartalom

```
{
  "report": "<JWT>"
}
```

**jelentés** (JWT): az igazolási jelentés JSON web token (JWT) formátumban (RFC 7519).
