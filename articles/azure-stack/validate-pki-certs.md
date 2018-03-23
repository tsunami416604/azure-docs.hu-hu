---
title: Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványok integrált Azure verem rendszerek telepítés ellenőrzéséhez |} Microsoft Docs
description: Az Azure verem PKI-tanúsítványok integrált Azure verem rendszerekhez ellenőrzését ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b38e3cc45d14645611c0cd804f2bfa66047810f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure verem PKI-tanúsítványok ellenőrzése
A Azure verem tanúsítvány-ellenőrző eszközt, a cikkben a deploymentdata.json fájlt mellékelt ellenőrizni fogja az, hogy az OEM által biztosított a [generált PKI-tanúsítványok](azure-stack-get-pki-certs.md) alkalmasak a központi telepítés előtti. Tanúsítványok kell ellenőriznie az elegendő időt tesztelése, és újra ki bocsátani, ha a szükséges tanúsítványokat. 

A tanúsítvány-ellenőrző eszközt (Certchecker) a következő műveleteket hajtja végre:

- **Olvassa el a PFX**. Ellenőrzi, hogy érvényes PFX-fájl helyes jelszót, és figyelmezteti, ha nyilvános információ nem védi jelszó. 
- **Aláírási algoritmus**. Ellenőrzi, hogy az aláírási algoritmus nincs SHA1 
- **Titkos kulcs**. Ellenőrzi a titkos kulcs található, és exportálja a helyi számítógép attribútummal. 
- **Tanúsítványlánc**. Ellenőrzi tanúsítványlánc tact önaláírt tanúsítványokat is beleértve. 
- **DNS-nevek**. A SAN tartalmaz minden végpontra vonatkozó DNS-neveit, vagy egy támogatása jelen-e helyettesítő ellenőrzi. 
- **Kulcshasználat**. Ellenőrzi, kulcshasználat a digitális aláírás és kulcstitkosítás tartalmaz, és a kibővített kulcshasználat tartalmazza a kiszolgáló hitelesítése és ügyfél-hitelesítéshez. 
- **Kulcsméret**. Ellenőrzi a kulcs mérete pedig 2048 vagy nagyobb 
- **Tanúsítványlánc rendelés**. Ellenőrzi, hogy a más tanúsítványokat, így a lánc sorrendjének helyes-e. 
- **Más tanúsítványok**. Gondoskodjon arról, hogy nincs más tanúsítványok van csomagolva, PFX nem a megfelelő levéltanúsítvány vagy láncában. 
- **Nincs profil**. Ellenőrzi, hogy egy új felhasználó be tudják tölteni a PFX-adatainak nélkül a felhasználói profil betöltése, a csoportosan felügyelt szolgáltatásfiók fiókok viselkedését mimicking tanúsítvány karbantartás során.   

> [!IMPORTANT]
> A PKI-tanúsítvány PFX-fájlt és a jelszó bizalmas adatokat kell kezelni.

## <a name="prerequisites"></a>Előfeltételek
A rendszer Azure Alkalmazásveremben üzembe PKI-tanúsítványok érvényesítése előtt kell felelnie a következő előfeltételek teljesülését:
- CertChecker (a PartnerToolKit \utils\certchecker alatt)
- A következő exportált SSL tanúsítvány(ok) a [utasítások előkészítése](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 vagy Windows Server 2016

## <a name="perform-certificate-validation"></a>Végzehessenek a tanúsítványérvényesítést
Használja ezeket a lépéseket az Azure verem PKI-tanúsítványok ellenőrzése: 

1. Bontsa ki a tartalmát <partnerToolkit>egy új mappába, például \utils\certchecker **c:\certchecker**.

2. Nyissa meg a Powershellt rendszergazdaként, és módosítsa a könyvtárat a certchecker mappába:

  ```powershell
  cd c:\certchecker
  ```
 
3. A tanúsítványok könyvtárstruktúra létrehozása a következő PowerShell-parancsok futtatásával:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Ha az identitásszolgáltató az Azure Alkalmazásveremben üzembe az Azure AD, távolítsa el a **az AD FS** és **Graph** könyvtárak. 

4. Az (oka) t, például az előző lépésben létrehozott megfelelő könyvtárak. hely: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - és így tovább... 

5. Másolás **deploymentdata.json** azokat a **c:\certchecker** könyvtár.

6. A PowerShell-ablakban futtassa az alábbi parancsokat: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. A kimeneti összes tanúsítvány és az összes attribútum be van jelölve OK tartalmaznia kell: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Ismert problémák 
**Jelenség**: Certchecker túl korán kilép, és a következő hibaüzenetet kapja: 
> Meghiúsult

> Részletek: Ez a parancs nem futtatható, a hiba miatt: A könyvtár neve érvénytelen. 

**OK**: certchecker.ps1 például korlátozó mappából, fut, a c:\temp vagy a % temp % 

**Megoldási**: a certchecker eszköz áthelyezése új könyvtárba, például C:\CertChecker 


**Jelenség**: Certchecker figyelmeztetést előtti-1803 használatáról (ahogy a fenti példában a 7. lépés):

> [!WARNING]
> Előre-1803 tanúsítvány szerkezete. A mappa Azure verem 1803 szerkezetének vagy újabb verzió: ACSBlob, ACSQueue, ACSTable ACS mappa helyett. További információ a központi telepítési dokumentációjában talál.

**OK**: így megfelelő központi telepítés előtt 1803 észlelt CertChecker egyetlen ACS-mappa használatát. Azure verem verziójához 1803 és központi telepítések fent a gyökérmappa-szerkezetében ACSTable, ACSQueue, ACSBlob módosításait. Certchecker már frissítve van ez a funkció támogatásához.

**Megoldási**: Ha 1802 üzembe, intézkedés nem szükséges. Ha 1803 üzembe és újabb, cserélje le az ACS ACSTable, ACSQueue, ACSBlob, és az ACS tanúsítvány(ok) másolja a mappákat.

**Jelenség**: kimarad a tesztek

**OK**: CertChecker kihagyja bizonyos teszteket, ha egy függőség nem teljesül:
- Más tanúsítványok kimarad a tanúsítványlánc meghibásodásakor.
- Ha a rendszer kihagyja a profil:
  - Nincs olyan korlátozzák az ideiglenes felhasználó létrehozása, és a powershell futtatásához, hogy a felhasználó biztonsági házirenddel.
  - Titkos kulcs az ellenőrzés sikertelen.

**Megoldási**: eszközök kövessék a teszteket hajtson végre minden tanúsítvány részletei szakaszában.


## <a name="prepare-deployment-script-certificates"></a>Készítse elő a telepítési parancsfájl tanúsítványok 
Utolsó lépésként összes tanúsítvány előkészítése után kell lenniük a központi telepítés gazdagépen megfelelő könyvtárak. A központi telepítés állomáson hozzon létre egy nevű mappát. Tanúsítványok ** és helye az exportált tanúsítványt a megadott almappáiban adatbázisfájlok a [kötelező tanúsítványok](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) szakasz:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> A tanúsítványok csillaggal jelölt * csak van szükség, ha az AD FS használva identity.


## <a name="next-steps"></a>További lépések
[Datacenter identitásintegráció](azure-stack-integrate-identity.md)