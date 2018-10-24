---
title: Azure Stack-tanúsítványokkal kapcsolatos problémák javítása |} A Microsoft Docs
description: Az Azure Stack-készültségi ellenőrző használatával tekintse át és a tanúsítvány problémák elhárítására.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 0ebf69dd3436a6b1010d4184b2063317d14547dd
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957633"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Az Azure Stack PKI-tanúsítványokkal kapcsolatos gyakori hibák javítása
Ebben a cikkben található információk segítségével megismerheti és az Azure Stack PKI-tanúsítványokkal kapcsolatos gyakori hibák elhárításához. Problémák deríthet fel, az Azure Stack készültségi ellenőrző eszköz használatakor [ellenőrzése az Azure Stack PKI-tanúsítványokat](azure-stack-validate-pki-certs.md). Az eszköz ellenőrzi, hogy a tanúsítványok nyilvános kulcsokra épülő infrastruktúra egy Azure Stack üzemelő példányához és az Azure Stack titkos rotációja követelményeinek, és naplózza az eredményeket egy [report.json fájl](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>PFX-titkosítás
**Hiba** -PFX-titkosítás nem TripleDES-SHA1.   
**Szervizelési** -exportálása PFX fájlok **TripleDES-SHA1** titkosítást. Ez az összes Windows 10-ügyfelek számára a tanúsítványok beépülő modul a exportálásakor, vagy az Export-PFXCertificate használatával az alapértelmezett. 

## <a name="read-pfx"></a>Olvassa el a PFX
**Figyelmeztetés** – jelszót csak a tanúsítványban lévő titkos adatokat védi.  
**Szervizelési** – javasoljuk, hogy a nem kötelező beállítás az exportált PFX-fájlok **engedélyezése tanúsítvány adatvédelmi**.  

**Hiba** -PFX-fájl érvénytelen.  
**Szervizelési** -újra exportálja a tanúsítványt, a lépések használatával [üzembe helyezés előkészítése az Azure Stack PKI-tanúsítványai](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Aláírási algoritmus
**Hiba** -aláírási algoritmus SHA1.    
**Szervizelési** -aláírási kérelem generációs újragenerálja a tanúsítvány-aláírási kérelem (CSR) az SHA256 aláírási algoritmus az Azure Stack tanúsítványok kövesse a lépéseket. Ezután küldje el újra a CSR-fájl a hitelesítésszolgáltató adja ki újból a tanúsítványt.

## <a name="private-key"></a>Titkos kulcs
**Hiba** – a titkos kulcs hiányzik, vagy a helyi gép attribútum nem tartalmaz.  
**Szervizelési** – arról a számítógépről, a CSR-fájl jön létre újból exportálja a tanúsítványt, készítse elő az Azure Stack PKI-tanúsítványok központi telepítés lépései alapján. Ide tartozik a helyi számítógép tanúsítványtárolójába exportálása.

## <a name="certificate-chain"></a>Tanúsítványlánc
**Hiba** -tanúsítványlánc még nem fejeződött be.  
**Szervizelési** -tanúsítványokat kell tartalmaznia a teljes tanúsítványlánc.  Újra exportálja a tanúsítványt, a lépések használatával [üzembe helyezés előkészítése az Azure Stack PKI-tanúsítványai](azure-stack-prepare-pki-certs.md) , és jelölje be **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.**

## <a name="dns-names"></a>DNS-nevek
**Hiba** -DNSNameList a tanúsítvány nem tartalmaz az Azure Stack szolgáltatás végpont neve, vagy érvényes helyettesítő egyezést.  Helyettesítő karakteres megegyezik a bal szélső névtér a DNS-név csak érvényesek. Ha például _*. region.domain.com_ je platná pouze Pro *portal.region.domain.com*, nem _*. table.region.domain.com_.  
**Szervizelési** -aláírási kérelem generációs újragenerálja a CSR-fájl a megfelelő DNS-névvel rendelkező Azure Stack-tanúsítványok a lépéseket követve támogatja az Azure Stack-végpontok. Küldje el újra a CSR-fájl a hitelesítésszolgáltatótól, és kövesse a lépéseket a [üzembe helyezés előkészítése az Azure Stack PKI-tanúsítványai](azure-stack-prepare-pki-certs.md) exportálja a tanúsítványt a gépen, ami a CSR-fájl jön létre.  

## <a name="key-usage"></a>Kulcshasználat
**Hiba** – kulcshasználat hiányzik a digitális aláírás vagy kulcstitkosítás orEnhanced kulcshasználat hiányzik a hitelesítési kiszolgáló vagy ügyfél-hitelesítéssel.  
**Szervizelési** -szereplő lépések segítségével [Azure Stack-aláírási kérelmet. generációs tanúsítványok](azure-stack-get-pki-certs.md) újragenerálja a CSR-fájl a megfelelő kulcshasználat attribútumokkal.  Küldje el újra a hitelesítésszolgáltatót a CSR-fájl, és ellenőrizze a tanúsítványsablon nem írja felül a kulcshasználat a kérésben.

## <a name="key-size"></a>Kulcsméret
**Hiba** -kulcs mérete kisebb, mint 2048 bites    
**Szervizelési** -szereplő lépések segítségével [Azure Stack-aláírási kérelmet. generációs tanúsítványok](azure-stack-get-pki-certs.md) újragenerálja a CSR-fájl a megfelelő kulcs hossza (2048), és küldje el a hitelesítésszolgáltató a CSR-fájl.

## <a name="chain-order"></a>Lánc sorrendjében
**Hiba** – a tanúsítványlánc sorrendje nem megfelelő.  
**Szervizelési** -újra exportálja a tanúsítványt, a lépések használatával [üzembe helyezés előkészítése az Azure Stack PKI-tanúsítványai](azure-stack-prepare-pki-certs.md) , és jelölje be **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.** Győződjön meg arról, hogy csak a levéltanúsítvány exportálási van-e kiválasztva. 

## <a name="other-certificates"></a>Más tanúsítványok
**Hiba** – a PFX-csomag tartalmaz tanúsítványokat, amelyek nem a levéltanúsítvány vagy a tanúsítványlánc része.  
**Szervizelési** -újra exportálja a tanúsítványt, a lépések használatával [üzembe helyezés előkészítése az Azure Stack PKI-tanúsítványai](azure-stack-prepare-pki-certs.md), és válassza a **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.** Győződjön meg arról, hogy csak a levéltanúsítvány exportálási van-e kiválasztva.

## <a name="fix-common-packaging-issues"></a>Csomagolási kapcsolatos gyakori problémák megoldása
A AzsReadinessChecker egy segítő parancsmag, amely majd a csomagolási gyakori problémák, beleértve a PFX-fájlba exportálhatók és importálhatók javítási AzsPfxCertificate tartalmazza: 
 - *PFX-titkosítás* nem TripleDES-SHA1
 - *Titkos kulcs* helyi gép attribútum hiányzik.
 - *Tanúsítványlánc* hiányos vagy hibás. (A helyi számítógépen kell tartalmaznia a tanúsítványlánc, ha a PFX-csomag nem létezik.) 
 - *Más tanúsítványok*.
 
Javítás-AzsPfxCertificate nem segíthet, ha egy új CSR-fájl létrehozása, és adja ki újból a tanúsítvány szükséges. 

### <a name="prerequisites"></a>Előfeltételek
A következő előfeltételeknek kell teljesülnie a számítógépen eszköz: 
 - Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
 - A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa az alábbi PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók.

   > `$PSVersionTable.PSVersion`
 - Konfigurálása [PowerShell az Azure Stack-](azure-stack-powershell-install.md). 
 - Töltse le a legújabb verzióját [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

### <a name="import-and-export-an-existing-pfx-file"></a>Importálás és exportálás meglévő PFX-fájl
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítése  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. A PowerShell parancssorában futtassa a következőt adja meg a PFX-jelszót. Cserélje le *PFXpassword* az aktuális jelszóval. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. A PowerShell használatával futtassa a következő új PFX-fájlba exportálni.
   - A *- PfxPath*, dolgozunk a PFX-fájl elérési útját.  A következő példa az elérési út *.\certificates\ssl.pfx*.
   - A *- ExportPFXPath*, adja meg a helyét és nevét, a PFX-fájlt az exportálás.  A következő példa az elérési út *.\certificates\ssl_new.pfx*

   > `Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Az eszköz befejezése után tekintse át a kimenet sikeres végrehajtás esetén: 
````PowerShell
Repair-AzsPfxCertificate v1.1809.1005.1 started.
Starting Azure Stack Certificate Import/Export
Importing PFX .\certificates\ssl.pfx into Local Machine Store
Exporting certificate to .\certificates\ssl_new.pfx
Export complete. Removing certificate from the local machine store.
Removal complete.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Repair-AzsPfxCertificate Completed
````

## <a name="next-steps"></a>További lépések
[További információ az Azure Stack-biztonság](azure-stack-rotate-secrets.md)
