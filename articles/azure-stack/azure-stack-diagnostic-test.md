---
title: Az Azure Stack érvényesítési eszközzel |} A Microsoft Docs
description: Tudnivalók az Azure Stackben diagnosztikai naplófájlok összegyűjtése.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: eca66bc2e737b0f9a9954cad21a446e82d753f84
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203811"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack rendszerállapotának ellenőrzése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack operátorait szerint képes arra, hogy meghatározza az egészségügyi és állapotát, a rendszer igény szerinti elengedhetetlen. Az Azure Stack érvényesítési eszköz (**Test-AzureStack**), amely lehetővé teszi egy PowerShell-parancsmag futtatása tesztek egy sorozatát hibák azonosítására, ha jelen van a rendszeren. Általában meg kell adnia az eszköz futtatásához a [kiemelt végpontját (EGP)](azure-stack-privileged-endpoint.md) amikor, forduljon a Microsoft támogatási (CSS) egy problémához. Rendszerszintű állapotát és állapotadatokat csak olyan mélységben CSS is gyűjthet és a részletes naplók elemzése, és összpontosítson a terület, ahol a hiba történt, és együttműködnek Önnel a probléma megoldásához.

## <a name="running-the-validation-tool-and-accessing-results"></a>A fürtérvényesítési eszköz futtatása és az eredmények elérése

Ahogy korábban is hangsúlyoztuk, a fürtérvényesítési eszköz futtatása az EGP-n keresztül. Minden teszt adja vissza egy **PASS/sikertelen** állapota a PowerShell-ablakban. Ezenkívül létrejön a HTML részletes jelentés ami később elérhetők során [naplót](azure-stack-diagnostics.md). Itt látható a teljes körű érvényesítési folyamat tesztelési röviden ismerteti: 

1. Hozzáférés a kiemelt végponthoz (EGP). A következő parancsokat egy EGP-munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > -ComputerName AzS-ERCS01 használata az EGP-ASDK gazdaszámítógépen eléréséhez.

2. Miután az EGP, futtassa: 

   ```powershell
   Test-AzureStack
   ```

   Tekintse meg a [paraméter szempontok](azure-stack-diagnostic-test.md#parameter-considerations) és [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszokban további információkat.

3. Ha bármelyik teszteli, hogy a jelentés **sikertelen**futtassa:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   A parancsmag a Test-AzureStack által létrehozott naplók gyűjt. Diagnosztikai naplók kapcsolatos további információkért lásd: [Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md). Nem kell gyűjtését, vagy forduljon a CSS, ha a jelentés tesztek **WARN**.

4. Ha erre a fürtérvényesítési eszköz futtatása a CSS által, a CSS képviselő kérni fogja a naplókat a probléma elhárításához összegyűjtött.

## <a name="tests-available"></a>Rendelkezésre álló tesztek

A fürtérvényesítési eszköz lehetővé teszi a rendszerszintű tesztek egy sorozatát futtatná, és alapszintű cloud forgatókönyvek, amelyek egy elemzést az aktuális állapotát, és megalkotott problémákat a rendszerben.

### <a name="cloud-infrastructure-tests"></a>Felhőbeli infrastruktúra-tesztek

Kis hatású tesztekhez a infrastruktúra szintjén működik, és különféle rendszer összetevőit és funkciók információkat. Tesztek jelenleg a következő kategóriákba vannak csoportosítva:

| Vizsgált kategória                                        | -Argumentumot belefoglalása és - figyelmen kívül hagyása |
| :--------------------------------------------------- | :-------------------------------- |
| Az Azure Stack riasztási összegzése                            | AzsAlertSummary                   |
| Az Azure Stack biztonsági másolatok megosztásáról kisegítő összegzése       | AzsBackupShareAccessibility       |
| Azure Stack Control Plane Summary                    | AzsControlPlane                   |
| Az Azure Stack Defender összegzése                         | AzsDefenderSummary                |
| Az Azure Stack futtató infrastruktúra belső vezérlőprogram összegzése  | AzsHostingInfraFWSummary          |
| Azure Stack Cloud Hosting Infrastructure Summary     | AzsHostingInfraSummary            |
| Azure Stack Cloud Hosting Infrastructure Utilization | AzsHostingInfraUtilization        |
| Az Azure Stack-infrastruktúra-kapacitás                  | AzsInfraCapacity                  |
| Az Azure Stack-infrastruktúra teljesítmény               | AzsInfraPerformance               |
| Az Azure Stack infrastruktúra-szerepkör összefoglalása              | AzsInfraRoleSummary               |
| Az Azure Stack frissítés összegzése                           | AzsInfraUpdateSummary             |
| Az Azure Stack-portál és API-összefoglalót                   | AzsPortalAPISummary               |
| Az Azure Stack skálázási egység Virtuálisgép-eseményeket                     | AzsScaleUnitEvents                |
| Az Azure Stack skálázási egység Virtuálisgép-erőforrások                  | AzsScaleUnitResources             |
| Az Azure Stack SDN-alapú érvényesítés összegzése                   | AzsSDNValidation                  |
| Azure Stack Service Fabric Role Summary              | AzsSFRoleSummary                  |
| Az Azure Stack BMC összegzése                              | AzsStampBMCSummary                |
| Az Azure Stack tárolási szolgáltatások – összefoglalás                 | AzsStorageSvcsSummary             |
| Azure Stack SQL Store Summary                        | AzsStoreSummary                   |
| Az Azure Stack virtuális gép elhelyezése összegzése                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Felhőbeli forgatókönyv tesztek

A fenti infrastruktúra tesztek mellett funkciók ellenőrzéséhez infrastruktúra összetevői között felhőalapú forgatókönyv tesztek futtatására is rendelkezik. Ezek a tesztek futtatását, mert azok olyan erőforrások üzembe helyezésének felhőbeli rendszergazdai hitelesítő adatok szükségesek. 
    > [!NOTE]
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

A fürtérvényesítési eszköz kell megvizsgálni, a következő felhőt helyzetekben:
- Erőforrás-csoport létrehozása   
- Csomag létrehozása              
- Ajánlat létrehozása            
- Tárfiók létrehozása   
- Virtuális gép létrehozása 
- A BLOB storage-művelet   
- Üzenetsor-tárolási művelet  
- TABLE storage művelet  

## <a name="parameter-considerations"></a>A paraméter kapcsolatos szempontok

- A paraméter **lista** használható összes elérhető vizsgálati kategóriák megjelenítéséhez.

- A paraméterek **Belefoglalás** és **figyelmen kívül hagyása** vagy kizárja a teszt kategóriák használható. Tekintse meg a [elérhető tesztek](azure-stack-diagnostic-test.md#tests-available) szakaszban olvashat a következő argumentumokkal használható gyorsírás.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Egy bérlő virtuális gép üzemel egy része a felhőbeli eset teszteli. Használhat **DoNotDeployTenantVm** , tiltsa le a szolgáltatást. 

- Meg kell adnia a **ServiceAdminCredential** felhőalapú forgatókönyv tesztek futtatásához a paraméter a [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszban.

- **BackupSharePath** és **BackupShareCredential** használt történő tesztelésekor a biztonsági mentési infrastruktúra-beállításokat, ahogyan az a [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszban.

- A fürtérvényesítési eszköz is a PowerShell következő általános paramétereket támogatja: Részletes, hibakeresési, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, és OutVariable. További információkért lásd: [általános paraméterek](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Kis példákat

### <a name="run-validation-without-cloud-scenarios"></a>Ellenőrzés nélkül felhőbeli forgatókönyvek futtatása

Futtassa az ellenőrzési eszközt nélkül a **ServiceAdminCredential** paraméter futó felhőalapú forgatókönyv tesztek kihagyása: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Futtassa az ellenőrzést a felhőbeli forgatókönyvek

A fürtérvényesítési eszköz való ellátása a **ServiceAdminCredentials** paraméter alapértelmezés szerint a felhőbeli eset tesztek fut: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Ha szeretné futtatni a csak felhőbeli forgatókönyvek a többi a tesztek futtatása nélkül, használhatja a **Belefoglalás** paraméter ehhez: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Felhő rendszergazdájának felhasználóneve az egyszerű felhasználónév formátumban kell megadni: serviceadmin@contoso.onmicrosoft.com (Az azure AD). Amikor a rendszer kéri, írja be a felhőalapú rendszergazdai fiók jelszavát.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>A rendszer készültségének tesztelése a frissítés vagy gyorsjavítás telepítése előtt a fürtérvényesítési eszköz futtatása

Frissítés vagy gyorsjavítás telepítése előtt futtatnia kell az eszközt, az Azure Stack állapotának ellenőrzéséhez:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Biztonsági mentési infrastruktúra-beállítások tesztelésére a fürtérvényesítési eszköz futtatása

*Mielőtt* konfigurálja az infrastruktúra biztonsági mentését, tesztelje a biztonsági mentési megosztás elérési útja és a hitelesítő adatok használatával a **AzsBackupShareAccessibility** teszteléséhez: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Miután* biztonsági mentés konfigurálása, futtatása **AzsBackupShareAccessibility** ellenőrzése a megosztás érhető el a ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

A konfigurált biztonsági másolatok megosztásáról az új hitelesítő adatok ellenőrzéséhez futtassa: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>További lépések

Azure Stack-diagnosztikai eszközök és a probléma naplózása kapcsolatos további információkért lásd: [Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md).

Hibaelhárítással kapcsolatos további tudnivalókért lásd: [hibáinak elhárítása a Microsoft Azure Stack](azure-stack-troubleshooting.md).