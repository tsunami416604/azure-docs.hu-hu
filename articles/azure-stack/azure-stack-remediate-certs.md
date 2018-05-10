---
title: Tanúsítványproblémák javítása Azure verem |} Microsoft Docs
description: Az Azure verem készültségi ellenőrző használatával tekintse át, és javíthatja a tanúsítvánnyal kapcsolatos hibákat.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Gyakori problémák megoldásához Azure verem PKI-tanúsítványok
A cikkben szereplő információk segítségével megértéséhez, valamint az Azure verem PKI-tanúsítványok gyakori problémák megoldásában. Problémákat is felderítheti, ha az Azure verem készültségi ellenőrző eszköz [Azure verem PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md). Az eszköz ellenőrzi, hogy győződjön meg arról, hogy a tanúsítvány nyilvános kulcsokra épülő infrastruktúra követelményeinek teljesítése érdekében az Azure Alkalmazásveremben üzembe és Azure verem titkos elforgatási, és naplózza az eredményeket egy [report.json fájl](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Olvassa el a PFX
**Figyelmeztetés** -jelszó csak a tanúsítványban lévő titkos adatokat védi.  
**Szervizelés** -javasoljuk, hogy az opcionális beállítás a PFX-fájlok exportálását **tanúsítvány védelmének engedélyezése**.  

**Hiba** -PFX-fájl érvénytelen.  
**Szervizelés** -újra exportálja a tanúsítványt, a lépések [előkészítése Azure verem PKI tanúsítványainak központi telepítési](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Aláírási algoritmus
**Hiba** -aláírási algoritmus SHA1.    
**Szervizelés** -aláírási kérelem létrehozása újragenerálja a tanúsítvány-aláírási kérelem (CSR) az aláírási algoritmus az SHA-256 Azure verem tanúsítványok kövesse a lépéseket. Küldje el a CSR-t a adja ki újra a tanúsítványt a hitelesítésszolgáltatótól.

## <a name="private-key"></a>Titkos kulcs
**Hiba** – a titkos kulcs hiányzik, vagy nem tartalmaz a helyi gép attribútumot.  
**Szervizelés** – a számítógépről, amely létrehozta a CSR újra exportálja a tanúsítványt, előkészítése Azure verem PKI-tanúsítványok központi telepítés lépéseit használva. Ezek a lépések az alábbiak exportálása a helyi számítógép tanúsítványtárolójába.

## <a name="certificate-chain"></a>Tanúsítványlánc
**Hiba** -tanúsítványlánc nincs kész.  
**Szervizelés** -tanúsítványok tartalmaznia kell egy teljes tanúsítványláncot.  Újra exportálja a tanúsítványt, a lépések [előkészítése Azure verem PKI tanúsítványainak központi telepítési](azure-stack-prepare-pki-certs.md) válassza ki, **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.**

## <a name="dns-names"></a>DNS-nevek
**Hiba** -DNSNameList a tanúsítvány nem tartalmaz az Azure verem szolgáltatási végpont neve, vagy érvényes helyettesítő egyezés.  Helyettesítő karakteres megegyezik a bal szélső névtér a DNS-név csak érvényesek. Például _*. region.domain.com_ csak *portal.region.domain.com*, nem _*. table.region.domain.com_.  
**Szervizelés** -aláírási kérelem létrehozása újragenerálja a megfelelő DNS-névvel rendelkező CSR Azure verem tanúsítványok a lépések segítségével támogatja az Azure-verem végpontok. Küldje el újra a CSR egy hitelesítésszolgáltatónak, és kövesse a lépéseket [előkészítése Azure verem PKI tanúsítványainak központi telepítési](azure-stack-prepare-pki-certs.md) exportálja a tanúsítványt a számítógépről, amely a CSR jön létre.  

## <a name="key-usage"></a>Kulcshasználat
**Hiba** - kulcshasználat digitális aláírás hiányzik, vagy kulcstitkosítás, orEnhanced kulcshasználat hiányzik a kiszolgáló hitelesítése vagy ügyfél-hitelesítéshez.  
**Szervizelés** -az alábbi témakörben található lépésekkel [Azure verem-aláírási kérelem létrehozása tanúsítványok](azure-stack-get-pki-certs.md) újragenerálja a CSR a megfelelő kulcshasználat attribútumokkal.  Küldje el újra a CSR-t a hitelesítésszolgáltatótól, és győződjön meg arról, hogy a tanúsítványsablon nem felülírása a kulcshasználat a kérelemben.

## <a name="key-size"></a>Kulcsméret
**Hiba** -kulcs mérete kisebb, mint 2048-ra    
**Szervizelés** -az alábbi témakörben található lépésekkel [Azure verem-aláírási kérelem létrehozása tanúsítványok](azure-stack-get-pki-certs.md) újragenerálja a CSR megfelelő kulcs hossza (2048), és küldje el a CSR-t a hitelesítésszolgáltatótól.

## <a name="chain-order"></a>Lánc sorrendje
**Hiba** – a tanúsítványlánc sorrendje nem megfelelő.  
**Szervizelés** -újra exportálja a tanúsítványt, a lépések [előkészítése Azure verem PKI tanúsítványainak központi telepítési](azure-stack-prepare-pki-certs.md) válassza ki, **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.** Győződjön meg arról, hogy csak a levéltanúsítvány az exportált van kiválasztva. 

## <a name="other-certificates"></a>Más tanúsítványok
**Hiba** – a PFX-csomag tartalmaz tanúsítványokat, amelyek nem a levél tanúsítványt vagy tanúsítványláncot részét.  
**Szervizelés** -újra exportálja a tanúsítványt, a lépések [előkészítése Azure verem PKI tanúsítványainak központi telepítési](azure-stack-prepare-pki-certs.md), válassza ki, **minden tanúsítvány belefoglalása a tanúsítványláncba, ha lehetséges.** Győződjön meg arról, hogy csak a levéltanúsítvány az exportált van kiválasztva.

## <a name="fix-common-packaging-issues"></a>Közös csomagolás kapcsolatos problémák megoldása
A AzsReadinessChecker importálhatja és exportálhatja a megoldásához csomagolás gyakori problémákat, beleértve a PFX-fájlba: 
 - *Titkos kulcs* helyi gép attribútum hiányzik.
 - *Tanúsítványlánc* hiányos vagy hibás. (A helyi számítógépen kell tartalmaznia a tanúsítványlánc, ha a PFX-csomag nem.) 
 - *Más tanúsítványok*.
Azonban a AzsReadinessChecker nem segít, ha hozható létre egy új CSR, és adja ki újra a tanúsítványt kell. 

### <a name="prerequisites"></a>Előfeltételek
A következő előfeltételeknek kell teljesülniük a számítógépen az eszköz futtatásához: 
 - Windows 10 vagy Windows Server 2016, az internetkapcsolat.
 - PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancs, és tekintse át a *fő* verziója és *kisebb* verziók.

   > `$PSVersionTable.PSVersion`
 - Konfigurálása [PowerShell Azure verem](azure-stack-powershell-install.md). 
 - Töltse le a legújabb verzióját [Microsoft Azure verem készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz.

### <a name="import-and-export-an-existing-pfx-file"></a>Importálni és exportálni egy meglévő PFX-fájlt
1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazda PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítése  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. A PowerShell-parancssorból futtassa az alábbi a PFX-jelszót. Cserélje le *PFXpassword* a tényleges jelszóval. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. A PowerShell-parancssorból futtassa a következő új PFX-fájl exportálását.
   - A *- PfxPath*, dolgozunk a PFX-fájl elérési útját adja meg.  A következő példa az elérési út *.\certificates\ssl.pfx*.
   - A *- ExportPFXPath*, adja meg a helyét és az exportált PFX-fájl nevét.  A következő példa az elérési út *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Az eszköz befejezése után tekintse át a kimenetet a sikeres: ![eredmények](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>További lépések
[További tudnivalók az Azure-verem biztonsági](azure-stack-rotate-secrets.md)