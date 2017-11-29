---
title: "Az Azure Automation DSC Szolgáltatásban a Site Recovery mobilitási szolgáltatás üzembe helyezése |} Microsoft Docs"
description: "Azure Automation DSC használata az Azure Site Recovery mobilitási szolgáltatás és az Azure agent automatikus telepítése a VMware virtuális gép és a fizikai kiszolgáló replikálás az Azure-bA"
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: krnese
ms.openlocfilehash: 118a2e775ae3d036f58989d9778104e372e8c701
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Telepíteni a mobilitási szolgáltatást a replikáció a virtuális gépet az Azure Automation DSC Szolgáltatásban
Az Operations Management Suite azt biztosít egy átfogó biztonsági mentési és vész-helyreállítási megoldást, amely az üzletmenet folytonosságát biztosító terve részeként is használhatja.

A Hyper-V együtt út indította Hyper-V-replika használatával. De azt kibővítette a heterogén telepítő támogatásához, mert a felhő ügyfelek több hipervizorok és platformok.

Futtat VMware munkaterhelések és/vagy fizikai kiszolgálók ma, ha a felügyeleti kiszolgáló futtatja az Azure Site Recovery-összetevőket a kommunikáció és replikálás az Azure-ral, kezeléséhez, a cél Azure esetén a környezetben.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>A Site Recovery mobilitási szolgáltatás telepítését Automation DSC
Kezdjük gyors részletes információkat a felügyeleti kiszolgáló funkciója végrehajtásával.

A felügyeleti kiszolgáló számos kiszolgálói szerepkört futtatja. Ezek a szerepkörök egyik *konfigurációs*, amely kommunikáció koordinálását, és kezeli az adatokat replikálás és helyreállítási folyamatok.

Emellett a *folyamat* szerepkör replikációs átjáróként. Ezt a szerepkört kap replikációs adatokat védett forrásgépek, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, és küld egy Azure storage-fiókot. A funkciók a folyamat szerepkör egyik is védett gépek leküldéssel telepíteni a mobilitási szolgáltatást, és végezze el a VMware virtuális gépek automatikus észlelése.

Ha a feladat-visszavétel, az Azure-ból a *fő* szerepkör Ez a művelet részeként fogja kezelni a replikációs adatokat.

A védett gépek azt támaszkodhat a *mobilitásiszolgáltatás*. Ez az összetevő van telepítve minden gépen (VMware virtuális gép vagy fizikai kiszolgálón), amely az Azure-bA replikálni kívánt. A gépen végbemenő adatírásokat, és továbbítja őket a felügyeleti kiszolgáló (folyamat szerepkör).

Üzleti folytonosság kezelése, fontos a munkaterhelések, az infrastruktúra és az érintett összetevők megismerése. Majd is megfelel a követelményeknek a helyreállítási idő célkitűzése (RTO) és a helyreállítási időkorlát (RPO). Ebben a környezetben a mobilitási szolgáltatás a kulcs annak biztosításához, hogy a munkaterhelések védelmének módon teheti meg.

Igen, hogyan, egy optimalizált módon biztosíthatja, hogy rendelkezik-e súgó néhány Operations Management Suite összetevői egy megbízható védett telepítése?

Ez a cikk bemutatja, hogyan használhatók az Azure Automation szükséges konfiguráló (DSC), és a hely helyreállítását követően annak érdekében, hogy:

* A mobilitási szolgáltatás és az Azure Virtuálisgép-ügynök a védeni kívánt Windows gépek vannak telepítve.
* A mobilitási szolgáltatás és az Azure Virtuálisgép-ügynök mindig futnak, amikor Azure a replikációs cél.

## <a name="prerequisites"></a>Előfeltételek
* A tárház tárolja a szükséges telepítés
* A tárház tárolja a szükséges jelszót regisztrálása a management Server kiszolgálón

  > [!NOTE]
  > Minden felügyeleti kiszolgáló létrejön egy egyedi jelszót. Ha több felügyeleti kiszolgálót telepíteni kívánja, akkor győződjön meg arról, hogy a helyes jelszót a passphrase.txt fájl tárolja.
  >
  >
* A Windows Management Framework (WMF) 5.0-védelmi (előfeltétele annak, hogy Automation DSC) engedélyezni kívánt számítógépeken telepítve van

  > [!NOTE]
  > A DSC-ből a Windows gépeken, amelyek WMF 4.0-s verziójával használni kívánt, szakaszában olvashat [DSC használata a kapcsolat nélküli környezetben](## Use DSC in disconnected environments).
  

A mobilitási szolgáltatást a parancssor használatával is telepíthető, és több argumentumot fogad el. Ezért a bináris fájlok rendelkezik (után azokat a telepítő kibontása), és tárolja őket egy helyen, ahol helyreállíthatók a DSC-konfiguráció használatával.

## <a name="step-1-extract-binaries"></a>1. lépés: Kivonat bináris fájlok
1. Bontsa ki az ehhez a telepítéshez szükséges, hogy keresse meg a felügyeleti kiszolgálón a következő könyvtárra:

    **\Microsoft azure hely Recovery\home\svsystems\pushinstallsvc\repository**

    Ebben a mappában kell: nevű MSI-fájl

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Az alábbi parancs segítségével bontsa ki a telepítő:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Válassza ki az összes fájlt, és küldje el a tömörített mappa.

Most már rendelkezik a bináris fájlokat, amelyekre szüksége van a mobilitási szolgáltatást a telepítés automatizálása Automation DSC használatával.

### <a name="passphrase"></a>Hozzáférési kód
Ezután meg kell határoznia, ahová a tömörített mappából. Egy Azure storage-fiók is használhatja, ahogy újabb, a jelszót, amelyet a telepítő kell tárolni. Az ügynök ezután regisztrálja az a felügyeleti kiszolgáló, a folyamat részeként.

A jelszót, amelyet a felügyeleti kiszolgáló telepítésekor portáltól passphrase.txt szövegfájlba menthető.

Az Azure storage-fiók egy dedikált tárolójára tegyen mind a tömörített mappából, és a jelszót.

![Mappa helye](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Ha szeretné megtartani ezeket a fájlokat egy megosztást a hálózaton, megteheti. Csak akkor kell győződjön meg arról, hogy az Ön által használt később DSC erőforrás hozzáfér, és a telepítés és a jelszót.

## <a name="step-2-create-the-dsc-configuration"></a>2. lépés: A DSC-konfiguráció létrehozása
A telepítő WMF 5.0 függ. A gép sikeresen alkalmazta a konfiguráció keresztül Automation DSC WMF 5.0 kell elhelyezkednie.

A környezetben használja a következő példa DSC-konfiguráció:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
A konfigurációs rendszer tegye a következőket:

* A változók jelzi a konfigurációs Honnan szerezhetők be a bináris fájlok a mobilitási szolgáltatás és az Azure Virtuálisgép-ügynök, honnan szerezhetők be a jelszót, és a kimeneti tárolási helyét.
* A konfigurációs xPSDesiredStateConfiguration DSC erőforrás importálja, hogy használható `xRemoteFile` töltse le a fájlokat a tárházból.
* A konfigurációs hoz létre egy könyvtárat, ahová a bináris fájlok tárolására.
* Az archív erőforrás lesz csomagolja ki a fájlokat a tömörített mappából.
* A csomag telepítési erőforrás a UNIFIEDAGENT a telepíti a mobilitási szolgáltatást. A megadott argumentumokkal telepítő exe-fájl. (A változókat, amelyek az argumentumok összeállítani a környezetnek megfelelően módosítani kell.)
* A csomag AzureAgent erőforrás telepíti az Azure Virtuálisgép-ügynök, ez az ajánlott minden Azure-ban futó virtuális gépen. Az Azure Virtuálisgép-ügynök is lehetővé teszi bővítmények hozzáadása a virtuális gép a feladatátvételt követően.
* A szolgáltatás vagy erőforrást biztosítja, hogy a kapcsolódó mobilitási szolgáltatás és az Azure-szolgáltatások mindig futnak.

Mentse a konfigurációt **ASRMobilityService**.

> [!NOTE]
> Ne felejtse el cserélje le a CSIP a konfiguráció megfelelően a tényleges felügyeleti kiszolgálót, hogy az ügynök megfelelően csatlakoztatva, és a helyes jelszót fogja használni.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>3. lépés: Töltse fel az Automation DSC
A DSC-konfiguráció végzett importálni fogja a szükséges DSC erőforrásmodul (xPSDesiredStateConfiguration), mert kell importálni az automatizálás modult, mielőtt feltölti a DSC-konfiguráció.

Jelentkezzen be az Automation-fiók, keresse meg a **eszközök** > **modulok**, és kattintson a **Tallózás gyűjtemény**.

Itt kereshet a modult, és importálja a fiókjába.

![Modul importálása](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Ha ezzel végzett, a számítógépen nyissa meg az Azure Resource Manager-modulok telepítése esetében, és folytassa az újonnan létrehozott DSC-konfiguráció importálásakor.

### <a name="import-cmdlets"></a>Importálási parancsmagokat
A PowerShellben jelentkezzen be az Azure-előfizetéshez. Módosítsa a parancsmagok segítségével a környezetnek megfelelően, és rögzítheti az automatizálási fiók adatait egy változóban:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Automation DSC konfigurációját feltölteni a következő parancsmag használatával:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>A konfiguráció az Automation DSC-fordítási
Ezt követően kell a konfiguráció az Automation DSC-fordítási, hogy regisztrálja azt csomópontok megkezdése. Érhetők el, amely a következő parancsmagot:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Ez is igénybe vehet néhány percet, mert a konfiguráció az üzemeltett szolgáltatásban, DSC lekérési alapvetően telepít.

Miután a konfiguráció fordítása le az adatok PowerShell (Get-AzureRmAutomationDscCompilationJob) használatával, vagy a a [Azure-portálon](https://portal.azure.com/).

![Feladat beolvasása](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Most már sikeresen közzé, és feltölti a DSC-konfiguráció Automation DSC.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>4. lépés: A bevezetni gépek Automation DSC
> [!NOTE]
> Ez a forgatókönyv befejezése előfeltételeit egyike, hogy a Windows-alapú gépek WMF legújabb verziójára frissít. Töltse le, és a rendszernek a megfelelő verziót telepítse a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Egy metaconfig most létrehozza a DSC, akkor a csomópontra érvényes lesz. A sikeres, szeretné beolvasni a végponti URL-cím és az elsődleges kulcsát a kijelölt Automation-fiók az Azure-ban. Ezek az értékek alapján is megtalálhatja **kulcsok** a a **összes beállítás** az Automation-fiók paneljén.

![Értékek](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Ebben a példában a Site Recovery segítségével védeni kívánt fizikai Windows Server 2012 R2-kiszolgálóval rendelkezik.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Ellenőrizze az összes függőben lévő fájlműveletek nevezze át a beállításjegyzékben
A kiszolgáló társítja a Automation DSC végponthoz megkezdése előtt javasoljuk, hogy ellenőrizze az összes függőben lévő fájlműveletek nevezze át a beállításjegyzékben. A telepítő előfordulhat, hogy tiltják a befejezési miatt függőben lévő újraindítás.

Futtassa az alábbi parancsmagot, ellenőrizze, hogy a kiszolgálón nincs függőben lévő újraindítás:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Ez azt mutatja, üres, ha az OK gombra a folytatáshoz áll. Ha nem, eleget kell tennie a karbantartási időszak alatt a kiszolgáló újraindításával.

A konfiguráció alkalmazása a kiszolgálón, indítsa el a PowerShell integrált parancsfájlkezelési környezet (ISE), és futtassa a következő parancsfájlt. Ez a lényegében egy DSC helyi konfiguráció, amely arra utasítja a helyi Configuration Manager motor regisztrálása az Automation DSC szolgáltatásban, és a megadott konfigurációs (ASRMobilityService.localhost) beolvasása.

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Ezt a konfigurációt, akkor a helyi Configuration Manager motor az Automation DSC regisztrálja magát. Azt is meghatározza a motor hogyan működjön, mit kell tenni, ha a konfigurációs eltéréseket (ApplyAndAutoCorrect), és hogyan azt kell a konfigurálás folytatásához Ha újraindításra szükség.

Ez a parancsfájl futtatása után a csomópont az Automation DSC Szolgáltatásban regisztrálni kell kezdődnie.

![Folyamatban van a csomópont regisztrációs](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Ha vissza az Azure-portálon, láthatja, hogy az újonnan regisztrált csomópont már megjelent a portál.

![A portál regisztrált csomópontja](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

A kiszolgálón futtathatja a következő PowerShell-parancsmag segítségével győződjön meg arról, hogy a csomópont megfelelően van regisztrálva:

```powershell
Get-DscLocalConfigurationManager
```

Miután a konfigurációs lekért, és a a kiszolgálóra alkalmazza, ezt a következő parancsmag futtatásával ellenőrizheti:

```powershell
Get-DscConfigurationStatus
```

A kimeneti jeleníti meg, hogy a kiszolgáló sikeresen hívja elő a konfigurációban:

![Kimenet](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Emellett a mobilitási szolgáltatás telepítő rendelkezik-e a saját naplóban, amely a következő *SystemDrive*\ProgramData\ASRSetupLogs.

Ennyi az egész. Most már sikeresen telepített és regisztrált a mobilitási szolgáltatást a Site Recovery segítségével védeni kívánt számítógépen. A DSC-ből fog győződjön meg arról, hogy a szükséges szolgáltatások mindig futnak.

![Sikeres telepítés](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Miután a felügyeleti kiszolgáló észleli a sikeres telepítés, konfigurálja a védelmet, és a gép replikációs engedélyezése a Site Recovery segítségével.

## <a name="use-dsc-in-disconnected-environments"></a>A DSC használata a kapcsolat nélküli környezetekben
Ha a gépek nem kapcsolódik az internethez, továbbra is támaszkodhat a DSC telepítheti és konfigurálhatja a mobilitási szolgáltatás a védeni kívánt munkaterhelések.

A saját DSC lekérési kiszolgálójával példányosítható lényegében arra, hogy Automation DSC ugyanazokat a képességeket a környezetben. Ez azt jelenti, hogy az ügyfelek fogja lekérni a konfigurációs (regisztráció) után a DSC-végponthoz. Azonban egy másik lehetőség egy manuális leküldése a DSC-konfiguráció a gépek helyileg vagy távolról.

Vegye figyelembe, hogy ebben a példában egy hozzáadott paraméter, a számítógép neveként. A távoli fájlok most már található távoli megosztásra mutat, amely érhető el, a védeni kívánt gépek. A parancsfájl végén ír elő a konfigurációs, és ezután elindítja a DSC-konfiguráció alkalmazása a célszámítógépen.

### <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a xPSDesiredStateConfiguration PowerShell-modul telepítve van-e. Windows-alapú gépek WMF 5.0 futtató a célszámítógépen a következő parancsmag futtatásával telepítheti a xPSDesiredStateConfiguration modul:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Letöltheti, és mentse a modul, abban az esetben kell terjesztenie windowsos gépeken, amelyek WMF 4.0-s verzióját. Futtassa ezt a parancsmagot egy számítógépen, ha jelen-e PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Is WMF 4.0-s, győződjön meg arról, hogy a [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) számítógépeken telepítve van.

A következő konfigurációs windowsos gépeken, amelyek WMF 5.0 és WMF 4.0 lehet leküldeni:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Ha szeretné példányosítani saját DSC lekérési kiszolgálójával, hogy utánozzák funkciója, amelyek Automation DSC lekérheti a vállalati hálózaton, lásd: [DSC lekérési webkiszolgáló beállítása](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Választható lehetőség: Telepítését a DSC-konfiguráció Azure Resource Manager-sablonnal
Ez a cikk fordította hogyan hozhatja létre saját DSC-konfiguráció automatikusan telepíteni a mobilitási szolgáltatás és az Azure Virtuálisgép-ügynök –, és győződjön meg arról, hogy a védeni kívánt gépeken futnak. Azt is, amely egy új vagy meglévő Azure Automation-fiók telepíti ezen DSC-konfiguráció Azure Resource Manager-sablonokban. A sablon bemeneti paraméterek segítségével Automation eszközöket, mely tartalmazni fogja a környezetnek a változókat létrehozni.

A sablon telepítése után egyszerűen hivatkozhat előkészítésére jelen útmutató 4. lépés a gépek.

A sablon fog tegye a következőket:

1. Használjon meglévő Automation-fiókot, vagy hozzon létre egy újat
2. A bemeneti paraméterek igénybe, hogy:
   * ASRRemoteFile--a mobilitási szolgáltatás beállítása tároló helyét
   * ASRPassphrase--a passphrase.txt fájl tároló helyét
   * ASRCSEndpoint – a felügyeleti kiszolgáló IP-címe
3. A xPSDesiredStateConfiguration PowerShell modul importálása
4. Hozzon létre, és a DSC-konfiguráció-fordítási

Az előző lépések a megfelelő sorrendben történik meg, hogy a bevezetése az ügyfélgépek is elindítható.

A sablon üzembe helyezés esetén utasításokkal található [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

A sablon üzembe helyezése a PowerShell használatával:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Következő lépések
A mobilitási szolgáltatás ügynökök telepítése után is [engedélyezze a replikálást](site-recovery-vmware-to-azure.md) a virtuális gépekhez.
