---
title: Az Azure igazolási jogcímek készletei
description: Az Azure-igazolás igénylési készlete.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237258"
---
# <a name="claim-sets"></a>Jogcímkészletek

A Microsoft Azure igazolást használó enklávék igazolása során létrehozott jogcímek az alábbi kategóriákba oszthatók:

- **Bejövő jogcímek**: az igazolási bizonyítékok elemzése után Microsoft Azure igazolás által generált jogcímeket.

- **Kimenő jogcímek**: az Azure-igazolás által kimenetként létrehozott jogcímek. Az igazolási jogkivonatban az összes jogcímet tartalmazza.

- **Tulajdonság jogcímei**: az Azure-igazolás által kimenetként létrehozott jogcímek. Tartalmazza az igazolási jogkivonat tulajdonságait jelképező jogcímeket, például a jelentés kódolását, a jelentés érvényességi időtartamát stb.

A JWT RFC által definiált és az Azure-igazolás által a Response objektumban használt jogcímek:

- **"ISS" (kibocsátói) jogcím**: az "ISS" (kibocsátói) jogcím AZONOSÍTJA a JWT kiállító rendszerbiztonsági tag. A jogcím feldolgozása általában alkalmazásspecifikus. Az "ISS" érték egy kis-és nagybetűket megkülönböztető karakterlánc, amely egy StringOrURI értéket tartalmaz.
- **"IAT" (kiállító) jogcím**: a "IAT" (kiállított) jogcím azt az időpontot határozza meg, amikor a JWT kiadták. Ez a jogcím a JWT életkorának meghatározására használható. Az értéknek egy NumericDate értéket tartalmazó számnak kell lennie.
- **"exp" (lejárati idő) jogcím**: az "exp" (lejárati idő) jogcím meghatározza azt a lejárati időt, amely után a JWT nem fogadható el a feldolgozáshoz. Az "exp" jogcím feldolgozásához az aktuális dátumnak és időpontnak az "exp" jogcímben felsorolt lejárati dátumnak és időpontnak kell lennie.

  Megjegyzés: egy 5 perces időkorlátot ad hozzá a probléma időpontjához (IAT).
- **"NBF" (nem korábban) jogcím**: a "NBF" (nem előtt) jogcím azt az időpontot határozza meg, ameddig a JWT nem lesz elfogadva feldolgozásra. A "NBF" jogcím feldolgozásához az aktuális dátumnak és időpontnak a "NBF" jogcímben felsorolt nem korábbi dátummal vagy azzal egyenlőnek kell lennie.
  Megjegyzés: egy 5 perces időkorlátot ad hozzá a probléma időpontjához (IAT).

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Az Azure-igazolás által az SGX ENKLÁVÉHOZ enklávékban kiállított jogcímek

### <a name="incoming-claims"></a>Bejövő jogcímek 

- **$is-hibakereső**: egy logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **$SGX-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **$SGX-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **$product azonosítója**
- **$svn**: az árajánlatban kódolt biztonsági verziószám 
- **$Tee**: enklávé típusa 

### <a name="outgoing-claims"></a>Kimenő jogcímek

- **hibakereső**: egy logikai érték, amely azt jelzi, hogy az enklávéban engedélyezve van-e a hibakeresés
- **SGX enklávéhoz-mrsigner**: az idézőjel "mrsigner" mezőjének hexadecimális kódolású értéke
- **SGX enklávéhoz-mrenclave**: az idézőjel "mrenclave" mezőjének hexadecimális kódolású értéke
- **termék azonosítója**
- **svn**: az árajánlatban kódolt biztonsági verziószám 
- **Tee**: enklávé típusa 
- **Maa-EHD**: az igazolási kérelemben megadott "enklávéban tárolt adatmennyiség" Base64Url kódolt verziója 
- **AAS-EHD**: az igazolási kérelemben megadott "enklávéban tárolt adatmennyiség" Base64Url kódolt verziója 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Az Azure-igazolás által a VBS enklávékban kiállított jogcímek

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
- **enclaveFamilyId**: az enklávé család azonosítójának Base64Url kódolt értékét tartalmazó karakterlánc-érték. Az enklávé elsődleges moduljának családjának azonosítója.
- **enclaveSvn**: Integer érték, amely az enklávé elsődleges moduljának biztonsági verziószámát tartalmazza.
- **enclavePlatformSvn**: Integer érték, amely az enklávét működtető platform biztonsági verziószámát tartalmazza.
- **enclaveFlags**: a enclaveFlags jogcím egy egész érték, amely az enklávé futtatókörnyezeti szabályzatát leíró jelzőket tartalmaz.
  
### <a name="outgoing-claims"></a>Kimenő jogcímek

- **policy_hash**: a BASE64URL által kiszámított sha256-kivonatot tartalmazó karakterlánc-érték (sha256 (BASE64URL) (UTF8 (házirend szövege))).
- **policy_signer**: a JWK tartalmazó karakterlánc-érték, amely a nyilvános kulccsal vagy az aláírt szabályzat fejlécében található tanúsítványlánc.
- **ver (verzió)**: string érték, amely tartalmazza a jelentés verzióját. Jelenleg 1,0.
- **cnf (megerősítő) jogcím**: a "cnf" jogcím azonosítja a rendelkezésére álló kulcs azonosítására szolgáló kulcsot. A megerősítő jogcím a 7800-as számú RFC-fájlban szerepel, és az igazolt enklávé kulcsának nyilvános része, amely JSON-webkulcs (JWK) objektumként (RFC 7517) van meghatározva.
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
