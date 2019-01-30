---
title: Start-AzsReadinessChecker parancsmag-referencia |} A Microsoft Docs
description: PowerShell-parancsmag súgójában talál az Azure Stack készültségi ellenőrző modul.
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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 861784a2d22d15253c61884bfab62667c0560bcd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250640"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker parancsmag-referencia

A modul: Microsoft.AzureStack.ReadinessChecker

Ez a modul csak egyetlen parancsmag tartalmazza.  Ez a parancsmag egy vagy több központi telepítés előtti vagy előre karbantartási műveleteket az Azure Stack hajtja végre.

## <a name="syntax"></a>Szintaxis

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Leírás

A **Start-AzsReadinessChecker** parancsmag ellenőrzi a tanúsítványokat, Azure-fiókok, az Azure-előfizetések és az Azure Active könyvtárak. Azure Stack üzembe helyezése előtt, vagy azt megelőzően karbantartási műveleteket, például a titkos kulcs Elforgatás Azure Stack érvényesítési futtatásához. A parancsmag is használható tanúsítvány-aláírási kérelmeket a tanúsítványokat és opcionálisan a PaaS-tanúsítványok létrehozásához.  Végül a parancsmag is ismételje meg a becsomagolást csomagolási közös problémák javítása a PFX-tanúsítványokat.

## <a name="examples"></a>Példák

### <a name="example-generate-certificate-signing-request"></a>Példa: Tanúsítvány-aláírási kérelem létrehozása

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Ebben a példában a Start-AzsReadinessChecker állít elő, több tanúsítvány-aláírási kérelem (CSR) a megfelelő ADFS Azure Stack üzembe helyezésének nevű régió "keleti régiója" tanúsítványok és a egy külső Tartományneve "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Példa: Tanúsítványok ellenőrzése

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Ebben a példában a PFX-jelszót kell megadnia a biztonságos, és a Start-AzsReadinessChecker ellenőrzi a relatív mappa "Tanúsítványok" tanúsítványok "keleti régiója" és "azurestack.contoso.com" külső Tartományneve régiónévhez együtt AAD telepítési érvényes

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Példa: Az üzembe helyezési adatok (üzembe helyezés és támogatás) tanúsítványok ellenőrzése

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

A központi telepítés és a támogatás a példában a PFX-jelszót kell megadnia a biztonságos és a Start-AzsReadinessChecker relatív mappa "Tanúsítványok" a tanúsítványok érvényes a telepítéshez, ahol identitás, a régiót és a külső FQDN olvassa a rendszer ellenőrzi a üzembe helyezési adatok JSON-fájl jön létre a központi telepítés. 

### <a name="example-validate-paas-certificates"></a>Példa: PaaS-tanúsítványok ellenőrzése

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

Ebben a példában egy kivonattáblát elérési utak és a jelszavakat az egyes PaaS tanúsítvány jön létre. Tanúsítványok elhagyható. Start-AzsReadinessChecker ellenőrzi az egyes PFX elérési út létezik, és ellenőrzi azokat a régiót a keleti"és 'azurestack.contoso.com' külső FQDN.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Példa: PaaS-tanúsítványok központi telepítési adatok ellenőrzése

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Ebben a példában egy kivonattáblát elérési utak és a jelszavakat az egyes PaaS tanúsítvány jön létre. Tanúsítványok elhagyható. Start-AzsReadinessChecker mindegyik PFX elérési út létezik, és ellenőrzi azokat a régió, és a külső FQDN olvasni a telepítési adatok JSON-fájl jön létre a központi telepítés ellenőrzi. 

### <a name="example-validate-azure-identity"></a>Példa: Azure-identitás ellenőrzése

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatait kéri a biztonságos, és a Start-AzsReadinessChecker ellenőrzi, hogy az Azure-fiók és az Azure Active Directory bérlői könyvtár nevét a "azurestack.contoso.com" AAD telepítési érvényesek

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Példa: Azure-identitás (üzembe helyezés támogatása) üzembe helyezési adatok ellenőrzése

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatait kéri a biztonságos, és a Start-AzsReadinessChecker ellenőrzi, az Azure-fiók és az Azure Active Directory érvényes aad-ben üzembe helyezésének ahol AzureCloud és TenantName olvassa a rendszer az üzembe helyezési adatokat Az üzembe helyezéshez létrehozott JSON-fájlt.

### <a name="example-validate-azure-registration"></a>Példa: Azure-regisztráció ellenőrzése

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatait kéri a biztonságos és Start-AzsReadinessChecker végez az adott fiókhoz sémaellenőrzésen, és győződjön meg arról, hogy az előfizetés is használható az Azure Stack-regisztráció. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Példa: Üzembe helyezés adatokkal (a telepítési csapat) Azure-regisztráció ellenőrzése

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatait kéri a biztonságos és Start-AzsReadinessChecker végez az adott fiókhoz sémaellenőrzésen, és győződjön meg arról, hogy az előfizetés is használható az Azure Stack-regisztráció hol tárolja a rendszer további részletei a központi telepítési adatok JSON-fájl jön létre a központi telepítés olvasni.

### <a name="example-importexport-pfx-package"></a>Példa: Importálási/exportálási PFX-csomag

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Ebben a példában a PFX-jelszót a rendszer biztonságosan. A ssl.pfx fájl lesznek importálva a helyi gép tanúsítványtárolójába, és ugyanazt a jelszót újra exportálva és ssl_new.pfx elmentve.  Ez az eljárás akkor használatra, ha a tanúsítványok ellenőrzését, hogy a titkos kulcs nem rendelkezik a helyi gép attribútumkészletet, a tanúsítványlánc megszakad, irreleváns tanúsítványok találhatók a PFX vagy a nem megfelelő sorrendben szerepel a tanúsítványlánc megjelölt.

### <a name="example-view-validation-report-deployment-support"></a>Példa: Ellenőrzési jelentés megjelenítése (üzembe helyezés támogatás)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Ebben a példában az üzembe helyezés vagy a támogatási csapat a készültségi jelentést kap a vásárlói (Contoso), és a Start-AzsReadinessChecker használja, az ellenőrzés céljából Contoso végrehajtott művelet állapotának megtekintéséhez.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Példa: Tanúsítvány érvényesítése csak (üzembe helyezés és támogatás) összefoglaló ellenőrzési jelentés megjelenítése

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Ebben a példában az üzembe helyezés vagy a támogatási csapat ügyfelektől a készültségi jelentést a Contoso és a tanúsítvány érvényesítése céljából Contoso végrehajtott összesített állapotának megtekintése a Start-AzsReadinessChecker használatával.

## <a name="required-parameters"></a>Szükséges paraméterek

> -RegionName

Az Azure Stack üzembe helyezés régió nevét adja meg.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

> -FQDN

Adja meg az Azure Stack üzembe helyezés külső teljes Tartományneve, is ExternalFQDN és ExternalDomainName aliasneve.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |ExternalFQDN, ExternalDomainName |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

> -IdentitySystem

Megadja az Azure Stack üzemelő példányához Identitásrendszer érvényes értékek, az AAD vagy az ADFS, Azure Active Directory és az Active Directory összevonási szolgáltatásokat jelölik.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Érvényes értékek:               |'AAD','ADFS'  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

> -PfxPassword

Adja meg a tanúsítvány PFX-fájlok társított jelszót.
|  |  |
|----------------------------|---------|
|Típus:                       |SecureString |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -PaaSCertificates

Az elérési utak és a jelszavakat a PaaS-tanúsítványokat tartalmazó kivonattáblát határozza meg.
|  |  |
|----------------------------|---------|
|Típus:                       |Kivonattábla |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -DeploymentDataJSONPath

Itt adható meg az Azure Stack üzembe helyezési adatok JSON-konfigurációs fájlt. Ez a fájl jön létre a központi telepítés.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -PfxPath

Importálási/exportálási rutin megoldásához, ez az eszköz a tanúsítvány érvényesítése aszinkronitást igénylő problémát észlel tanúsítvány elérési útja.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -ExportPFXPath  

Itt adhatja meg az importálási/exportálási rutin eredő PFX-fájljának elérési utat.  
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -Tulajdonos

A tanúsítvány-kérelem létrehozása egy rendezett szótár, az e-mail tárgyát határozza meg.
|  |  |
|----------------------------|---------|
|Típus:                       |OrderedDictionary   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RequestType

A SAN adja meg a tanúsítványkérelem. Érvényes értékek MultipleCSR, SingleCSR.

- *MultipleCSR* állít elő, több tanúsítvány-kérelmeket, egyet az egyes szolgáltatások.
- *SingleCSR* hoz létre egy tanúsítványkérelmet szolgáltatásokhoz.

|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |'MultipleCSR','SingleCSR' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -OutputRequestPath

A cél elérési útját adja meg a tanúsítvány kérést fájlok esetében könyvtár már léteznie kell.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AADServiceAdministrator

Adja meg az Azure Active Directory szolgáltatás rendszergazdáját az Azure Stack üzembe helyezéshez használható.
|  |  |
|----------------------------|---------|
|Típus:                       |PSCredential   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AADDirectoryTenantName

Azure Stack központi telepítéshez használt adja meg az Azure Active Directoryban.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AzureEnvironment

Az Azure-szolgáltatások tartalmazó a fiókok, könyvtárak és az előfizetések Azure Stack üzembe helyezésére és regisztrációjára használható példányát határozza meg.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RegistrationAccount

Megadja, hogy a regisztráció az Azure Stack-regisztráció használandó.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RegistrationSubscriptionID

Itt adhatja meg a regisztrációs előfizetés-azonosító használható az Azure Stack-regisztráció.
|  |  |
|----------------------------|---------|
|Típus:                       |GUID     |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -ReportPath

Alapértelmezés szerint az aktuális címtár és az alapértelmezett a jelentés nevét, adja meg a elérési útja a készültségi jelentést.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

## <a name="optional-parameters"></a>Választható paraméterek

> -CertificatePath

Itt adhatja meg az elérési utat, amelyben csak a tanúsítványra szükség mappákat tanúsítvány jelen.

Azure Active Directory identitáskezelő rendszerbe Azure Stack üzembe helyezéshez szükséges mappák a következők:

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Azure stack-telepítés Active Directory összevonási szolgáltatások identitáskezelő rendszerbe van szükséges a mappa:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |. \Certificates |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -IncludePaaS  

Itt adhatja meg, ha a PaaS szolgáltatások/állomásnevek kell adni a tanúsítvány kérést.

|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

> -ReportSections

Itt adhatja meg, hogy csak az jeleníti meg a jelentés összefoglaló, az áttekinthetőség kedvéért kihagyja részletei.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Érvényes értékek:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -Összefoglaló

Itt adhatja meg, hogy csak az jeleníti meg a jelentés összefoglaló, az áttekinthetőség kedvéért kihagyja részletei.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

> -CleanReport

Eltávolítja az előző végrehajtás és érvényesítési előzmények, és a egy új jelentés ellenőrzések írja.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |CF-hez                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

> -OutputPath

Itt adható meg egyéni elérési út készültségi JSON-jelentés és a részletes naplófájl mentéséhez.  Ha az elérési út már nem létezik, az eszköz megkísérli létrehozni a könyvtárat.

|  |  |
|----------------------------|------------------|
|Típus:                       |Karakterlánc            |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |$ENV: TEMP\AzsReadinessChecker  |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

> -Confirm

A parancsmag futtatása előtt megerősítést kér.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |CF-hez                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

> -WhatIf

Megmutatja, hogy mi történne a parancsmag futtatásakor. A parancsmag nem fut.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Aliasnevek:                    |wi                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Adatcsatorna bemenetének elfogadása:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |
