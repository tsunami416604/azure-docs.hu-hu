---
title: Az Azure igazolási jogcímek készletei
description: Az Azure-igazolás igénylési készlete.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: afe2cf288cd4a15091e8278309b3ecf74a2d35a4
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572748"
---
# <a name="claim-sets"></a>Jogcímkészletek

A Microsoft Azure igazolást használó enklávék igazolása során létrehozott jogcímek az alábbi kategóriákba oszthatók:

- **Bejövő jogcímek**: Microsoft Azure igazolások által generált jogcímek az igazolási bizonyítékok elemzése után, és a szabályzatok szerzői által az engedélyezési szabályok definiálására használhatók egy egyéni házirendben

- **Kimenő jogcímek**: az Azure-igazolás által generált jogcímek és az igazolási jogkivonatban az összes jogcímet tartalmazza

- **Tulajdonság jogcímei**: az Azure-igazolás által kimenetként létrehozott jogcímek. Tartalmazza az igazolási jogkivonat tulajdonságait jelképező jogcímeket, például a jelentés kódolását, a jelentés érvényességi időtartamát stb.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Gyakori bejövő jogcímek az összes igazolási típus között

Az alábbi jogcímeket az Azure igazolása hozza létre, és az engedélyezési szabályok definiálására használható egy egyéni szabályzatban:
- **x-MS-ver**: JWT séma verziója (várhatóan "1,0")
- **x-MS-igazolás-típus**: az igazolás típusát jelképező karakterlánc-érték 
- **x-MS-Policy-hash**: az Azure igazolási értékelési szabályzatának kivonata BASE64URL-ként kiszámítva (sha256 (UTF8 (BASE64URL (Policy Text))))
- **x-MS-Policy-aláíró**: JSON-objektum "jwk" taggal, amely a szabályzat aláírására használt ügyfelet jelöli, amikor az ügyfél feltölt egy aláírt szabályzatot

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím 
--- | --- 
ver | x-MS-ver
póló | x-MS-igazolás-típus
Maa – policyHash | x-MS-Policy-hash
policy_hash | x-MS-Policy-hash
policy_signer | x-MS-Policy-aláíró

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Általános kimenő jogcímek az összes igazolási típus között

Az alábbi, az [IETF JWT](https://tools.ietf.org/html/rfc7519) által definiált jogcímek, amelyeket az Azure igazolása használ a Response objektumban:

- **"kezdeményezés" (JWT ID) jogcím**
- **"ISS" (kiállítói) jogcím**
- **"IAT" (kiadott) jogcím**
- **"exp" (lejárati idő) jogcím**
- **"NBF" (nem korábban) jogcím**

Az [IETF által megevett](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) és az Azure-igazolás által a Response objektumban használt jogcímek:
- **"Alkalmi jogcím" (alkalom)**

## <a name="claims-specific-to-sgx-enclaves"></a>A SGX ENKLÁVÉHOZ enklávés-re vonatkozó jogcímek

### <a name="incoming-claims-specific-to-sgx-attestation"></a>SGX ENKLÁVÉHOZ-igazolásra vonatkozó bejövő jogcímek

Az alábbi jogcímeket a szolgáltatás hozza létre a SGX ENKLÁVÉHOZ igazoláshoz, és az engedélyezési szabályok definiálására használható egy egyéni szabályzatban:
- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>A SGX ENKLÁVÉHOZ-igazolásra vonatkozó kimenő jogcímek

Az alábbi jogcímeket a szolgáltatás hozza létre, és a SGX ENKLÁVÉHOZ-igazoláshoz tartozó Response objektum tartalmazza:
- **x-MS-SGX enklávéhoz-** a-hibakereső: logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **x-MS-SGX enklávéhoz-Product-ID**
- **x-MS-ver**
- **x-MS-SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **x-MS-SGX enklávéhoz-svn**: az idézőjelbe kódolt biztonsági verziószám 
- **x-MS-SGX enklávéhoz-EHD**: az enklávéban tárolt adat a BASE64URL-ként formázott (enklávéban tárolt adat)
- **x-MS-SGX enklávéhoz-biztosíték**: az igazolás végrehajtásához használt biztosítékot leíró JSON-objektum. Az x-MS-SGX enklávéhoz-biztosíték jogcím értéke beágyazott JSON-objektum a következő kulcs/érték párokkal:
    - **qeidcertshash**: a qe-identitás kiállító sha256 értéke
    - **qeidcrlhash**: a qe-identitás kiállítói tanúsítvány-visszavonási listájának sha256 értéke
    - **qeidhash**: a qe sha256 értéke
    - **quotehash**: a kiértékelt idézet sha256 értéke
    - **tcbinfocertshash**: a TCB információ kiállító tanúsítványának sha256 értéke
    - **tcbinfocrlhash**: az sha256 a TCB-adatok kiállító tanúsítványok CRL-listájának értéke
    - **tcbinfohash**: az igazolás végrehajtásához használt biztosítékot leíró JSON-objektum

Az alábbi jogcímek elavultnak tekintendők, de teljes mértékben támogatottak, és a jövőben is szerepelni fognak. A nem elavult jogcím-nevek használata javasolt.

Elavult jogcím | Ajánlott jogcím
--- | --- 
$is – hibakereső | x-MS-SGX enklávéhoz-a-hibakereső
$sgx – mrsigner | x-MS-SGX enklávéhoz-mrsigner
$sgx – mrenclave | x-MS-SGX enklávéhoz-mrenclave
$product azonosítója | x-MS-SGX enklávéhoz-Product-ID
$svn | x-MS-SGX enklávéhoz-svn
$tee | x-MS-igazolás-típus
Maa – EHD | x-MS-SGX enklávéhoz-EHD
AAS – EHD | x-MS-SGX enklávéhoz-EHD
Maa – attestationcollateral | x-MS-SGX enklávéhoz-biztosíték

## <a name="claims-issued-specific-to-trusted-platform-module-tpm-attestation"></a>A platformmegbízhatósági modul-(TPM-) igazoláshoz megadott jogcímek

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Bejövő jogcímek (kimenő jogcímekként is használhatók)

- **aikValidated**: logikai érték, amely információt tartalmaz, ha az igazolási azonosító kulcs (AIK) tanúsítványa ellenőrizve lett vagy nem.
- **aikPubHash**: a Base64-t tartalmazó karakterlánc (sha256 (AIK public key in der Format)).
- **tpmVersion**: Integer érték, amely tartalmazza a PLATFORMMEGBÍZHATÓSÁGI modul (TPM) főverzióját.
- **secureBootEnabled**: logikai érték, amely jelzi, hogy a biztonságos rendszerindítás engedélyezve van-e.
- **iommuEnabled**: logikai érték, amely azt jelzi, hogy engedélyezve van-e a bemeneti-kimeneti memória kezelési egysége (Iommu).
- **bootDebuggingDisabled**: logikai érték, amely azt jelzi, hogy le van-e tiltva a rendszerindítási hibakeresés.
- **notSafeMode**: logikai érték, amely azt jelzi, hogy a Windows nem csökkentett módban fut-e.
- **notWinPE**: logikai érték, amely azt jelzi, hogy a Windows nem a WinPE módban fut-e.
- **vbsEnabled**: logikai érték, amely azt jelzi, hogy a vbs engedélyezve van-e.
- **vbsReportPresent**: logikai érték, amely azt jelzi, hogy elérhető-e a vbs enklávé-jelentés.
- **enclaveAuthorId**: karakterlánc-érték, amely az enklávé szerző azonosítójának Base64Url kódolt értékét tartalmazza – az enklávé elsődleges moduljának szerzői azonosítóját.
- **enclaveImageId**: az enklávé rendszerkép-azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé elsődleges moduljának képazonosítója.
- **enclaveOwnerId**: az enklávé tulajdonos-azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték – az enklávé tulajdonosának azonosítója.
- **enclaveFamilyId**: az ENKLÁVÉ család azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték. Az enklávé elsődleges moduljának családjának azonosítója.
- **enclaveSvn**: Integer érték, amely az enklávé elsődleges moduljának biztonsági verziószámát tartalmazza.
- **enclavePlatformSvn**: Integer érték, amely az enklávét működtető platform biztonsági verziószámát tartalmazza.
- **enclaveFlags**: a enclaveFlags jogcím egy egész érték, amely az enklávé futtatókörnyezeti szabályzatát leíró jelzőket tartalmaz.
  
### <a name="outgoing-claims-specific-to-tpm-attestation"></a>TPM-igazolásra vonatkozó kimenő jogcímek

- **policy_hash**: a BASE64URL által kiszámított sha256-kivonatot tartalmazó karakterlánc-érték (sha256 (BASE64URL) (UTF8 (házirend szövege))).
- **policy_signer**: egy JWK tartalmaz, amely tartalmazza a nyilvános kulcsot, vagy az aláírt szabályzat fejlécében lévő tanúsítványlánc.
- **ver (verzió)**: string érték, amely tartalmazza a jelentés verzióját. Jelenleg 1,0.
- **cnf (megerősítő) jogcím**: a "cnf" jogcím azonosítja a rendelkezésére álló kulcs azonosítására szolgáló kulcsot. A megerősítő jogcímek a 7800-as számú RFC-fájlban szerepelnek, az igazolt enklávé kulcsának nyilvános részét, amely JSON-webkulcs (JWK) objektumként (RFC 7517) van megadva.
- **rp_data (függő entitások)**: a kérelemben megadott, függő entitások által használt, a kérésben szereplő, a függő entitás által a jelentés frissességének garantálása céljából felhasználva.
- **"kezdeményezési" (JWT ID) jogcím**: a "kezdeményezés" (JWT ID) jogcím egyedi azonosítót biztosít a JWT számára. Az azonosító értékét olyan módon rendeli hozzá a rendszer, amely biztosítja, hogy a rendszer az azonos értéket véletlenül egy másik adatobjektumhoz rendelje.

### <a name="property-claims"></a>Tulajdonság jogcímei

- **report_validity_in_minutes**: egy egész számból álló jogcím, amely azt jelzi, hogy mennyi ideig érvényes a jogkivonat.
  - **Alapértelmezett érték (Time)**: egy nap percben.
  - **Maximális érték (idő)**: egy év percben.
- **omit_x5c**: logikai jogcím, amely azt jelzi, hogy az Azure-igazolásnak ki kell-e hagyni a szolgáltatás hitelességének igazolásához használt tanúsítványt. Ha az értéke igaz, a rendszer hozzáadja a x5t az igazolási jogkivonathoz. Ha hamis (alapértelmezett), a rendszer hozzáadja a x5c az igazolási jogkivonathoz.

## <a name="next-steps"></a>További lépések
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)
