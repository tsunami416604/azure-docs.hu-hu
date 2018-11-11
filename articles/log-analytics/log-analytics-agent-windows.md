---
title: Windows-számítógépek csatlakoztatása az Azure Log Analytics |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan csatlakozhat az egyéb felhőkben vagy a helyszínen a Log Analytics-a Microsoft Monitoring Agent (MMA) üzemeltetett Windows-számítógépek.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a16230b6f51f0ce93f4a9bf53591abbcd6b4bd3b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283682"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows-számítógépek csatlakoztatása a Log Analytics szolgáltatás az Azure-ban

Figyelheti és kezelheti a virtuális gépek vagy fizikai számítógépek a helyi adatközpontban vagy egyéb felhőalapú környezetben a Log Analytics használatával, meg kell a Microsoft Monitoring Agent (MMA) telepítse és konfigurálja azt, hogy egy vagy több Log Analytics-munkaterületek.  Az ügynök az Azure Automation is támogatja a hibrid forgatókönyv-feldolgozói szerepkör.  

A felügyelt Windows-számítógépeken az ügynök szerepel a Microsoft Monitoring Agent szolgáltatást. A Microsoft Monitoring Agent szolgáltatást a naplófájlok és a Windows eseménynaplót, teljesítményadatokat és egyéb telemetriát eseményeket gyűjti. Akkor is, ha az ügynök nem tud kommunikálni a Log Analytics szolgáltatással, és jelentéseket küldeni, az ügynök továbbra is fut, és az összegyűjtött adatok a figyelt számítógép lemezén üzenetsorok. Ha a kapcsolat helyreáll, a Microsoft Monitoring Agent szolgáltatást összegyűjtött adatokat küld a szolgáltatás.

Az ügynök a következő módszerek valamelyikével telepíthetők. A legtöbb esetben ezek a módszerek kombinációját használatával szükség szerint telepítse a számítógépeken, más-más részhalmazához.  Az egyes módszerek használatával kapcsolatban a cikk későbbi részében találhatók.

* Manuális telepítés. A telepítő manuálisan fut a számítógépen a parancssorból a telepítővarázslóval, vagy egy meglévő szoftverterjesztési eszköz segítségével telepítve.
* Az Azure Automation Desired State Configuration (DSC). DSC Azure Automation használatával, egy parancsfájllal már telepítve van az a környezet Windows-számítógépekhez.  
* PowerShell-szkript.
* Windows rendszerű virtuális gépek a Resource Manager-sablon a helyszíni az Azure Stackben.  

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Windows operációs rendszereket](log-analytics-concept-hybrid.md#supported-windows-operating-systems) és a [hálózati tűzfalkonfigurációkat](log-analytics-concept-hybrid.md#network-firewall-requirements) ismertető részt.

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Windowshoz készült Microsoft Monitoring Agent telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára.  Ezt az információt kötelező megfelelően konfigurálni az ügynököt, és a Log Analytics az Azure-ban kereskedelmi és Egyesült Államok kormányának felhője sikeres kommunikációjának biztosításához az egyes telepítési módszerek a telepítés során.  

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet, jelenteni szeretné a az ügynök telepítése.
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![A Log Analytics speciális beállításai](media/log-analytics-agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.   
5. Másolja és illessze be a kedvenc szerkesztőjébe a **munkaterület-Azonosítót** és **elsődleges kulcs**.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Ügynök telepítése varázsló használatával
Az alábbi lépéseket telepítése és konfigurálása az ügynök a Log Analytics az Azure és az Azure Government felhőben a számítógépre a Microsoft Monitoring Agent a telepítővarázsló segítségével.  

1. A Log Analytics rendszeréből munkaterületén, a **Windows kiszolgálók** lapot nyit meg, ha a korábbi, válassza ki a megfelelő **Windows-ügynök letöltése** verziójú, a processzor architektúrájától függően a Windows operációs rendszer.   
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.   
6. Az **Azure Log Analytics** lapon végezze el a következőket:
   1. Illessze be az előzőleg kimásolt **Munkaterület-azonosítót** és **Munkaterületkulcsot (Elsődleges kulcs)**.  Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.  Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.  
7. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.<br><br> ![illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot](media/log-analytics-agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. A Log Analytics szolgáltatásba jelentések megerősítéséhez tekintse át a [ellenőrizheti az ügynök csatlakozását a Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Ügynök telepítése a parancssor használatával
A letöltött fájlt az ügynök a csomag egy önálló telepítés.  A telepítőprogram az ügynök és a fájlokat a csomagban található, és annak érdekében, hogy megfelelően telepítése a parancssorból a következő példákban szemléltetett módon kinyerni kell.    

>[!NOTE]
>Ha azt szeretné, ügynök frissítése, a parancsprogram-kezelési API Log Analytics használatához szüksége. A témakörben [kezelése és fenntartása a Log Analytics-ügynököket Windows és Linux rendszerhez készült](log-analytics-agent-manage.md) találhat további információt.

Az alábbi táblázat a Log Analytics az ügynök, beleértve az Automation DSC használatával telepítésekor a telepítő által támogatott konkrét paraméterei emeli ki.

|Az MMA-specifikus beállítások                   |Megjegyzések         |
|---------------------------------------|--------------|
| NOAPM=1                               | Nem kötelező paraméter. Az ügynök nélkül .NET alkalmazásteljesítmény-figyelési telepíti.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurálása az ügynök számára, hogy egy munkaterületet                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterület-azonosítót (guid) a munkaterület hozzáadása                    |
|OPINSIGHTS_WORKSPACE_KEY               | Kezdetben a munkaterület-hitelesítésre használható munkaterület kulcsa |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Adja meg a felhőalapú környezet, amelyben a munkaterületen található <br> 0 = az azure kereskedelmi felhőben (alapértelmezett) <br> 1 = az azure Government |
|OPINSIGHTS_PROXY_URL               | URI-t a proxy használata |
|OPINSIGHTS_PROXY_USERNAME               | Hitelesített proxyt hozzáféréséhez használt felhasználónév |
|OPINSIGHTS_PROXY_PASSWORD               | Hitelesített proxyt elérésére szolgáló jelszó |

1. Egy rendszergazda jogú parancssorból futtassa az ügynök telepítési fájljait, kibontásához `MMASetup-<platform>.exe /c` és a rendszer felkéri a elérési útján a fájlok kibontásához.  Másik lehetőségként megadhatja az elérési út az argumentumoknak `MMASetup-<platform>.exe /c /t:<Path>`.  
2. Csendes telepítse az ügynököt, és konfigurálja azt, hogy az Azure kereskedelmi felhőben munkaterület mappájában kibontotta a telepítési fájlokat, írja be: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   vagy az ügynök számára, hogy az Azure US Government felhő konfigurálásához írja be: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Ügynök telepítése az Azure Automation DSC használatával

Az alábbi parancsfájl példa használatával telepítse az ügynököt az Azure Automation DSC használatával.   Ha egy Automation-fiók nem rendelkezik, tekintse meg [Ismerkedés az Azure Automation](/azure/automation/) követelményeket és az Automation DSC használata előtt meg kell adni egy Automation-fiók létrehozásának lépései.  Ha nem ismeri az Automation DSC, tekintse át a [Automation DSC – első lépések](../automation/automation-dsc-getting-started.md).

A következő példa telepíti a 64 bites ügynök, azonosítja a `URI` értéket. A 32 bites verziót használhatja, ha az URI értéket. Az URL-címének mindkét verziók a következők:

- Windows 64 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Ez az eljárás és parancsfájl példa nem támogatja az már egy Windows-számítógépre telepített ügynök frissítése.

Az ügynök csomag az 32 bites és 64 bites verziója eltérő termékkódok és kiadott új verziói is rendelkeznek egy egyedi értéket.  A termékkód egy GUID Azonosítót, amely az alkalmazás vagy termék egyszerű azonosítása és a Windows telepítő által jelölt **ProductCode** tulajdonság.  A `ProductId` értékét a **MMAgent.ps1** parancsfájl meg kell egyeznie a kódot a 32 bites vagy 64 bites ügynök telepítőcsomagból.

Közvetlenül az ügynök telepítési csomagból lekérdezheti a termékkód, használhatja a Orca.exe a [Windows SDK összetevői a Windows Installer-fejlesztőknek](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , amely a Windows Software Development Kitet részét képező vagy használata A következő PowerShell- [példaszkript](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) írt által a Microsoft MVP elismert kiváló szakember ().  Mindkét módszert használja, először szüksége kibontásához a **MOMagent.msi** MMASetup telepítőcsomagból fájlt.  Ez a szakasz első lépése a korábbi látható [ügynök telepítése a parancssor használatával](#install-the-agent-using-the-command-line).  

1. Modul importálása a xPSDesiredStateConfiguration DSC [ http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) az Azure Automationbe.  
2.  Hozzon létre az Azure Automation változó adategységek a *OPSINSIGHTS_WS_ID* és *OPSINSIGHTS_WS_KEY*. Állítsa be *OPSINSIGHTS_WS_ID* a Log Analytics-munkaterület Azonosítójára és a set *OPSINSIGHTS_WS_KEY* az elsődleges kulcsot a munkaterületet.
3.  Másolja a szkriptet, és mentse MMAgent.ps1.

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Frissítés a `ProductId` kinyert az ügynök legújabb verzióját a termék kóddal értékét a korábban ajánlott módszerekkel csomag telepítéséhez. 
5. [Importálja a MMAgent.ps1 konfigurációs parancsfájlt](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) az Automation-fiókba. 
5. [Rendeljen hozzá egy Windows-számítógép vagy a csomópont](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) a konfigurációhoz. 15 percen belül a csomópont ellenőrzi annak konfigurációját, és az ügynököt a csomópontra a rendszer továbbítja.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ellenőrizheti az ügynök csatlakozását a Log Analyticsbe

Az ügynök telepítésének befejezése után a annak ellenőrzésére, hogy sikeresen csatlakozott, és a reporting kétféleképpen végezhető.  

A számítógépről a **Vezérlőpult**, keresse meg az elemet **Microsoft Monitoring Agent**.  Válassza ki, és az a **Azure Log Analytics** lapon jelenítsék meg az ügynök a következő üzenetet: **a Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatásban.**<br><br> ![MMA kapcsolati állapota a Log Analytics felé](media/log-analytics-agent-windows/log-analytics-mma-laworkspace-status.png)

Egy egyszerű naplóbeli keresést is elvégezheti az Azure Portalon.  

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.  
2. A Log Analytics munkaterület oldalán válassza ki a cél munkaterületet, és válassza ki a **naplóbeli keresés** csempére. 
2. A naplóbeli keresés panelen írja be a lekérdezést a mező:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Szívverés rekordok a számítógép csatlakoztatva van megjelölve, és a Service reporting megtekintheti a keresési eredmények között adja vissza.   

## <a name="next-steps"></a>További lépések

Felülvizsgálat [kezelése és fenntartása a Log Analytics-ügynököket Windows és Linux rendszerhez készült](log-analytics-agent-manage.md) hogyan kezelheti az ügynököt a gépeken a központi telepítés életciklusa alatt.  
