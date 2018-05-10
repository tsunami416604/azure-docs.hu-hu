---
title: Start-AzsReadinessChecker parancsmag-referencia |} Microsoft Docs
description: Az Azure verem készültségi ellenőrző modul PowerShell parancsmag súgóját.
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
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker parancsmag-referencia

A modul: Microsoft.AzureStack.ReadinessChecker

Ez a modul csak egyetlen parancsmag tartalmazza.  Ez a parancsmag egy vagy több központi telepítés előtti vagy előre karbantartási feladatokat végez Azure verem.

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
A **Start-AzsReadinessChecker** parancsmag ellenőrzi a tanúsítványokat, Azure-fiókra, Azure-előfizetések és Azure Active könyvtárak. Futtassa az ellenőrzést Azure verem telepítése előtt, vagy az Azure verem karbantartási műveletek, például a titkos kulcs elforgatás előtt. A parancsmag is használható a tanúsítványokat és opcionálisan PaaS tanúsítványok tanúsítvány-aláírási kérelmek létrehozásához.  Végezetül a parancsmag is csomagolja újra PFX-tanúsítványok közös csomagolás problémák megoldásához.

## <a name="examples"></a>Példák
**Példa: A tanúsítvány-aláírási kérelem létrehozása**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Ebben a példában a Start-AzsReadinessChecker állít elő, több tanúsítvány-aláírási kérelem (CSR) tanúsítványok megfelelő-e egy AD FS Azure Alkalmazásveremben üzembe nevű régióban "Kelet" és "azurestack.contoso.com" külső Tartományneve

**Példa: Tanúsítványok érvényesítésére.**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Ebben a példában a PFX-jelszót a rendszer biztonságosan, és a Start-AzsReadinessChecker ellenőrzi az AAD-telepítéshez olyan a terület neve "Kelet" és "azurestack.contoso.com" külső Tartományneve érvényes tanúsítványok "Tanúsítványok" relatív mappa 

**Példa: Ellenőrizze a tanúsítványok központi telepítésével kapcsolatos adatokat (központi telepítési és támogatás)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
A központi telepítés és a támogatás példában a PFX-jelszót a rendszer biztonságosan, és a Start-AzsReadinessChecker ellenőrzi a tanúsítvány érvényes, ahol identitását, valamint régió és külső teljes Tartományneve pedig olvassa a központi telepítés "Tanúsítványok" relatív mappa a központi telepítési adatok JSON-fájl jön létre a központi telepítéshez. 

**Példa: PaaS tanúsítványok érvényesítésére.**
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

Ebben a példában egy kivonattáblát összeállított útvonalak és PaaS tanúsítványokra a jelszavakat. Tanúsítványok elhagyható. Start-AzsReadinessChecker mindegyik PFX elérési út létezik, és érvényesíti azokat a régió "Kelet" és "azurestack.contoso.com" külső FQDN ellenőrzi.

**Példa: PaaS tanúsítványok központi telepítési adatok ellenőrzéséhez**
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

Ebben a példában egy kivonattáblát összeállított útvonalak és PaaS tanúsítványokra a jelszavakat. Tanúsítványok elhagyható. Start-AzsReadinessChecker mindegyik PFX elérési út létezik-e, és ellenőrzi azokat a régiót, és a külső FQDN olvasni a telepítési adatok JSON-fájl jön létre a központi telepítés ellenőrzi. 

**Példa: Azure-identitás ellenőrzése**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatait a rendszer bekéri biztonságosan, és a Start-AzsReadinessChecker ellenőrzi, hogy az Azure-fiókot és az Azure Active Directory "azurestack.contoso.com" a bérlő címtár nevét az aad-ben telepítési érvényesek


**Példa: A központi telepítésével kapcsolatos adatokat (telepítési támogatása) Azure-identitás ellenőrzése**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Ebben a példában a szolgáltatás-rendszergazdai fiók hitelesítő adatait a rendszer bekéri biztonságosan, és a Start-AzsReadinessChecker ellenőrzi az Azure-fiókot és az Azure Active Directory egy AAD telepítését érvényes ahol AzureCloud és TenantName pedig olvassa a rendszer a központi telepítésével kapcsolatos adatokat JSON-fájl jön létre a központi telepítéshez.


**Példa: Azure regisztráció érvényesítése.**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatokat a rendszer bekéri biztonságosan és a Start-AzsReadinessChecker hajtja végre az adott fiókhoz elvégzett sémaellenőrzésen, és győződjön meg arról, hogy az előfizetés nem használható Azure verem regisztrációs. 


**Példa: A központi telepítésével kapcsolatos adatokat (a telepítési csapat) Azure regisztráció érvényesítése**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Ebben a példában az előfizetés tulajdonosa hitelesítő adatokat a rendszer bekéri biztonságosan és a Start-AzsReadinessChecker hajtja végre a megadott fiók érvényesítése és győződjön meg arról, hogy az előfizetés nem használható Azure verem regisztrációs hol vannak a további részletek a telepítési adatok JSON-fájl jön létre a központi telepítés olvasni.

**Példa: Import/Export PFX-csomag**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Ebben a példában a PFX-jelszót a rendszer biztonságosan. A ssl.pfx fájlját a rendszer importálta a helyi számítógép tanúsítványtárolójába, és ugyanazt a jelszót újból exportálni majd ssl_new.pfx menti.  Ez az eljárás esetén használható tanúsítvány érvényesítése megjelölt, hogy a titkos kulcs nem rendelkezik a helyi számítógép attribútumkészlet, a tanúsítványlánc megszakad, irreleváns tanúsítványok találhatók a PFX vagy a tanúsítványlánc rossz sorrendben.


**Példa: Ellenőrzési jelentés megjelenítése (telepítési támogatása)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Ebben a példában a központi telepítés vagy a támogatási csapat a készültségi jelentést kap az ügyfél (Contoso), és a Contoso végrehajtott érvényesítési végrehajtások állapotának megtekintése a Start-AzsReadinessChecker segítségével.

**Példa: A fürtérvényesítési jelentés összefoglaló tanúsítvány érvényesítése csak (központi telepítési és támogatás) megtekintése**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Ebben a példában a központi telepítés vagy a támogatási csapat ügyfelektől a készültségi jelentést a Contoso és a tanúsítvány érvényesítési végrehajtások Contoso végre összesített állapotának megtekintése a Start-AzsReadinessChecker segítségével.



## <a name="required-parameters"></a>Kötelező paraméter
> -RegionName

Az Azure Alkalmazásveremben üzembe régió nevét adja meg.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

> TELJES-TARTOMÁNYNÉV    

Adja meg az Azure Alkalmazásveremben üzembe külső teljesen minősített Tartományneve, is ExternalFQDN és ExternalDomainName aliasnevet.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |ExternalFQDN, ExternalDomainName |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

 

> -IdentitySystem    

Határozza meg az Azure Alkalmazásveremben üzembe Identitásrendszere érvényes értékeket, az AAD vagy az AD FS, Azure Active Directory és az Active Directory összevonási szolgáltatásokban kulcsattribútumokkal.
|  |  |
|----------------------------|--------------|
|Típus:                       |Karakterlánc        |
|Pozíció:                   |nevű         |
|Alapértelmezett érték:              |None          |
|Érvényes értékek:               |"AAD", "AD FS"  |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)         |
|Helyettesítő karakterek elfogadása: |False (Hamis)         |

> -PfxPassword    

Adja meg a tanúsítvány PFX-fájlok társított jelszót.
|  |  |
|----------------------------|---------|
|Típus:                       |SecureString |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -PaaSCertificates

Adja meg az elérési utak és PaaS tanúsítványok jelszavakat tartalmazó hashtable.
|  |  |
|----------------------------|---------|
|Típus:                       |Hibás |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -DeploymentDataJSONPath

Adja meg az Azure-verem telepítési adatok JSON-konfigurációs fájlt. A fájl központi telepítési jön létre.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -PfxPath

Meghatározza az importálási/exportálási rutin kijavításához szükséges, ez az eszköz a tanúsítvány érvényesítési jelöli problematikus tanúsítvány elérési útját.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -ExportPFXPath  

Megadja a cél elérési útját az eredményül kapott PFX-fájlt az importálási/exportálási rutin.  
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -Tulajdonos

A tanúsítvány kérelem létrehozása egy rendezett szótár tulajdonos határozza meg.
|  |  |
|----------------------------|---------|
|Típus:                       |OrderedDictionary osztály   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RequestType

A SAN adja meg a tanúsítványkérelem. Érvényes értékek MultipleCSR, SingleCSR.
- *MultipleCSR* állít elő, több tanúsítvány-kérelmeket, egy, az egyes szolgáltatásokhoz.
- *SingleCSR* hoz létre egy tanúsítványkérelmet az összes szolgáltatáshoz.   

|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |"MultipleCSR", "SingleCSR" |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -OutputRequestPath

Adja meg a célútvonalat kérelem tanúsítványfájlok directory már léteznie kell.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AADServiceAdministrator

Adja meg az Azure Active Directory szolgáltatás rendszergazdája számára Azure verem központi telepítéshez használt.
|  |  |
|----------------------------|---------|
|Típus:                       |PSCredential   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AADDirectoryTenantName

Azure verem központi telepítéshez használt adja meg az Azure Active Directoryban.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -AzureEnvironment

Adja meg az Azure-szolgáltatások a fiókok, könyvtárak és előfizetések tartalmazó használható Azure Alkalmazásveremben üzembe és nyilvántartási példányát.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Érvényes értékek:               |"AzureCloud", "AzureChinaCloud", "AzureGermanCloud" |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RegistrationAccount

Megadja, hogy a regisztrációs regisztrálási Azure verem használható.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -RegistrationSubscriptionID

Adja meg a regisztrációs Azure verem regisztrációjához használt előfizetés-azonosító.
|  |  |
|----------------------------|---------|
|Típus:                       |GUID     |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |None     |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |

> -ReportPath

Készültségi jelentés elérési útját adja meg, alapértelmezés szerint az aktuális könyvtár és az alapértelmezett jelentés neve.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |



## <a name="optional-parameters"></a>Választható paraméterek
> -CertificatePath     

Adja meg az elérési út, amely alatt csak a tanúsítványra szükség tanúsítvány mappák találhatók.

Az Azure Alkalmazásveremben üzembe az Azure Active Directory identitásrendszere szükséges mappák a következők:

ACSBlob, ACSQueue, ACSTable, felügyeleti portál, rendszergazdai ARM, nyilvános, KeyVault, KeyVaultInternal, nyilvános Portal ARM

Azure verem vannak az Active Directory összevonási szolgáltatások identitásrendszere telepítési mappa szükséges:

ACSBlob, ACSQueue, ACSTable, az AD FS, felügyeleti portál, ARM rendszergazdai, ARM nyilvános, Graph, KeyVault, KeyVaultInternal, nyilvános portál


|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |. \Certificates |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |


> -IncludePaaS  

Itt adhatja meg, ha PaaS szolgáltatások/állomásnevek hozzá kell adni a tanúsítvány-kérelemre.


|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |


> -ReportSections        

Meghatározza, hogy csak megjelenítése a jelentés összefoglaló, hogy kihagyja a részletek.
|  |  |
|----------------------------|---------|
|Típus:                       |Karakterlánc   |
|Pozíció:                   |nevű    |
|Alapértelmezett érték:              |Összes      |
|Érvényes értékek:               |"Tanúsítvány", "AzureRegistration", "AzureIdentity", "Feladatok", "All" |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)    |
|Helyettesítő karakterek elfogadása: |False (Hamis)    |


> -Összegzés 

Meghatározza, hogy csak megjelenítése a jelentés összefoglaló, hogy kihagyja a részletek.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |


> -CleanReport  

Eltávolítja az előző és -ellenőrzés előzmények és érvényesítést ír egy új jelentés.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Alias:                    |CF                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |


> -OutputPath    

Készültségi JSON jelentés és a részletes naplófájl mentése egyéni elérési út megadása  Ha az elérési út már nem létezik, az eszköz megkísérli a következő könyvtár létrehozásakor.
|  |  |
|----------------------------|------------------|
|Típus:                       |Karakterlánc            |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |$ENV: TEMP\AzsReadinessChecker  |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |


> -Megerősítése  

A parancsmag futtatása előtt megerősítést kér.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Alias:                    |CF                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |


> -WhatIf  

Azt mutatja meg, hogy mi történne a parancsmag futtatásakor. A parancsmag nem fut.
|  |  |
|----------------------------|------------------|
|Típus:                       |SwitchParameter   |
|Alias:                    |Wi                |
|Pozíció:                   |nevű             |
|Alapértelmezett érték:              |False (Hamis)             |
|Fogadja el a feldolgozási sor beviteli:      |False (Hamis)             |
|Helyettesítő karakterek elfogadása: |False (Hamis)             |

 
