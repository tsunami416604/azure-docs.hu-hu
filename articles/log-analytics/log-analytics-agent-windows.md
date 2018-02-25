---
title: "Windows rendszerű számítógépek csatlakozzon az Azure Naplóelemzés szolgáltatáshoz |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan Windows rendszerű számítógépeken tárolt többi felhőt vagy a helyszíni és a Microsoft Monitoring Agent (MMA) szolgáltatáshoz kapcsolódni."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: magoedte
ms.openlocfilehash: 3bb023cfd94c7b87550d692101d30f922de80bf9
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows számítógépek csatlakoztatása a Log Analytics szolgáltatás az Azure-ban

Ahhoz, hogy a figyelheti és kezelheti a virtuális gépek vagy fizikai számítógép a helyi adatközpontban, illetve más felhőalapú környezetben a Naplóelemzési, kell a Microsoft Monitoring Agent (MMA) telepítheti és konfigurálhatja azt, hogy egy vagy több Naplóelemzési munkaterület.  Az ügynök az Azure Automation is támogatja a hibrid forgatókönyv-feldolgozói szerepkör.  

A figyelt Windows-számítógépen az ügynök van megadva, a Microsoft Monitoring Agent szolgáltatást. A Microsoft Monitoring Agent szolgáltatást a naplófájlok és a Windows Eseménynapló teljesítményadatokat és egyéb telemetriai eseményeit gyűjti. Akkor is, ha az ügynök nem képes kommunikálni a Log Analytics szolgáltatással, és jelentéseket küldeni, az ügynök továbbra is fut, és az összegyűjtött adatokat a figyelt számítógép lemezén várólisták. Ha a kapcsolat helyreállt, a Microsoft Monitoring Agent szolgáltatást gyűjtött adatokat küld a szolgáltatás.

Az ügynök az alábbi módszerek egyikének használatával telepíthetők. A legtöbb telepítések ezen módszerek kombinálhatja lehet telepíteni a számítógépek, más-más részhalmazához.  Az egyes módszerek használatával részletei a cikk későbbi részében is szerepelnek.

* Manuális telepítés. A telepítő a számítógépen a parancssorból, a telepítővarázslóval manuálisan fut, vagy telepíthető egy meglévő szoftver terjesztési eszköz használatával.
* Azure Automation-Célállapotkonfiguráció (DSC). A DSC használata az Azure Automationben parancsprogrammal a környezetében már telepített Windows rendszerű számítógépeken.  
* PowerShell-parancsfájlt.
* Resource Manager-sablon virtuális gépek helyszíni Windows Azure-készletben.  

A Windows-ügynök telepítése a hálózati és a rendszer követelmények megértése érdekében tekintse át a [Előfeltételek a Windows rendszerű számítógépeken](log-analytics-concept-hybrid.md#prerequisites).

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Windowshoz készült Microsoft Monitoring Agent telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára.  Szükség az egyes telepítési módszerek megfelelően konfigurálni az ügynököt, és képes sikeresen kommunikálni a kereskedelmi Azure Naplóelemzés és az USA szövetségi kormányzatának felhő telepítés során.  

1. Az Azure portálon kattintson **minden szolgáltatás**. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Naplóelemzési munkaterület, jelölje ki az ügynök konfigurálásáról jelenteni szeretné a munkaterületen.
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![A Log Analytics speciális beállításai](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.   
5. Másolja és illessze be a kedvenc szerkesztő a **munkaterület azonosítója** és **elsődleges kulcs**.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Telepítse az ügynököt telepítő varázsló segítségével
Az alábbi lépéseket telepítse, és konfigurálni az ügynököt a Naplóelemzési Azure és az Azure Government felhőben található, a számítógépre a Microsoft Monitoring Agent a telepítővarázsló segítségével.  

1. A napló Analyics munkaterületen a a **Windows kiszolgálók** lap, amelybe korábban, válassza ki a megfelelő **Windows-ügynök letöltése** verzió letöltése attól függően, hogy a processzor architektúrája a Windows operációs rendszer.   
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez (OMS), majd kattintson a **Tovább** gombra.   
6. Az **Azure Log Analytics** lapon végezze el a következőket:
   1. Illessze be az előzőleg kimásolt **Munkaterület-azonosítót** és **Munkaterületkulcsot (Elsődleges kulcs)**.  Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.  Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.  
7. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.<br><br> ![illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Erősítse meg a jelentések szolgáltatáshoz, tekintse át [Naplóelemzési ügynök ellenőrzésére](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Telepítse az ügynököt, a parancssor használatával
Az ügynök a letöltött fájl csomag egy önálló telepítő IExpress létre.  A telepítőprogram az ügynök és a fájlokat a csomagban található, és megfelelően a parancssorból a következő példákban telepítéséhez ki kell.    

>[!NOTE]
>Ha azt szeretné, az ügynök frissítése, hogy szeretné használni, a parancsprogram-kezelési API Naplóelemzési. A témakörben [kezelése és karbantartása a Log Analyticshez ügynök a Windows és Linux](log-analytics-agent-manage.md) további tájékoztatást talál.

A következő táblázat ismerteti a az ügynök, beleértve az Automation DSC használata telepítésekor a telepítő által támogatott Naplóelemzési paraméterekkel.

|MMA vonatkozó beállítások                   |Megjegyzések         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurálása az ügynök számára, hogy a munkaterület                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterületének azonosítóját (guid) a munkaterülethez való hozzáadása                    |
|OPINSIGHTS_WORKSPACE_KEY               | Kezdetben a való hitelesítéshez szükséges a munkaterület kulcsát |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Adja meg a felhőalapú környezetben, ahol a munkaterület <br> 0 = azure kereskedelmi cloud (alapértelmezett) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | A proxy használatára URI |
|OPINSIGHTS_PROXY_USERNAME               | Egy hitelesített proxykiszolgálót eléréséhez felhasználónév |
|OPINSIGHTS_PROXY_PASSWORD               | Egy hitelesített proxykiszolgálót eléréséhez szükséges jelszót. |

1. Egy rendszergazda jogú parancssorból futtassa a telepítési fájlokat, kibontani `extract MMASetup-<platform>.exe` és felszólítja a fájlokat az elérési úthoz.  Másik lehetőségként az elérési utat is megadhat úgy, hogy az argumentumok `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Az IExpress által támogatott parancssori swtiches további információkért lásd: [parancssori kapcsolók a IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) majd frissítse a példa az igényeinek.
2. Csendes telepítse az ügynököt, és konfigurálja azt, hogy az Azure kereskedelmi felhőben munkaterület mappából kibontotta a telepítési fájlokat be: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   vagy az ügynök számára, hogy Azure Amerikai Egyesült államokbeli kormányzati felhő konfigurálásához írja be: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Telepítse az ügynököt az Azure Automation DSC használata

Az alábbi mintaparancsfájl segítségével telepítse az ügynököt, Azure Automation DSC használata.   Ha egy Automation-fiók nem rendelkezik, tekintse meg a [Ismerkedés az Azure Automation](../automation/automation-offering-get-started.md) követelmények és Automation DSC használatához szükséges Automation-fiók létrehozásának lépéseit.  Ha nem ismeri az Automation DSC Szolgáltatásban, tekintse át a [Ismerkedés az Automation DSC](../automation/automation-dsc-getting-started.md).

A következő példa telepíti a 64 bites ügynök azonosítja a `URI` érték. A 32 bites azáltal, hogy az URI azonosítóját is használhatja. Az URI-azonosítók mindkét-verziók a következők:

- A Windows 64 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828603
- A Windows 32 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>A művelet és a parancsfájl például nem támogatja a Windows-számítógép már telepített ügynök frissítéséhez.

Az ügynök csomag 32 bites és 64 bites verziója eltérő termékkódok pedig kiadott új verziói is egyedi értéket.  A termékkód egy GUID, amely egy alkalmazás vagy termék egyszerű azonosítása és a Windows Installer által képviselt **ProductCode** tulajdonság.  A `ProductId value` a a **MMAgent.ps1** parancsfájl meg kell egyeznie a termékkódot a 32 bites vagy 64 bites ügynök telepítőcsomagból.

A kódot közvetlenül az ügynök telepítési csomag lekérdezni, használhatja a Orca.exe a [Windows SDK összetevői a Windows Installer-fejlesztőknek](https://msdn.microsoft.com/library/windows/desktop/aa370834%27v=vs.85%28.aspx) , amely a Windows Software Development Kit összetevője vagy használatával PowerShell következő egy [a példaként megadott parancsfájlt](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) egy Microsoft értékes Professional (MVP) által írt.

1. Modul importálása a xPSDesiredStateConfiguration DSC [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) az Azure Automation.  
2.  Azure Automation változó eszközök létrehozása *OPSINSIGHTS_WS_ID* és *OPSINSIGHTS_WS_KEY*. Állítsa be *OPSINSIGHTS_WS_ID* a Naplóelemzési munkaterület azonosítója és a set *OPSINSIGHTS_WS_KEY* az elsődleges kulcsot a munkaterületet.
3.  Másolja a parancsfájlt, és mentse MMAgent.ps1

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource –ModuleName PSDesiredStateConfiguration

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

4. [Importálja a MMAgent.ps1 konfigurációs parancsfájl](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) be az Automation-fiók. 
5. [Rendelje hozzá egy Windows-számítógépet vagy egy csomópont](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-dsc) konfigurációjával. 15 percen belül a csomópont ellenőrzi a konfigurációját, és az ügynököt a csomópontra kerül.

## <a name="verify-agent-connectivity-to-log-analytics"></a>A Naplóelemzési ügynök kapcsolat ellenőrzése

Ha az ügynök telepítése befejeződött, ellenőrzése, hogy sikeresen csatlakozott, és reporting kétféleképpen végezhető.  

A számítógépről a **Vezérlőpult**, keresse meg az elemet **Microsoft Monitoring Agent**.  Válassza ki azt, és a a **Azure Naplóelemzés (OMS)** lapon, az ügynök megjelenjen-e a következő üzenet: **a Microsoft Monitoring Agent sikeresen csatlakozott-e a Microsoft Operations Management Suite szolgáltatással.**<br><br> ![MMA kapcsolati állapota a Log Analytics felé](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

Egyszerű napló keresést az Azure portálon is elvégezheti.  

1. Az Azure portálon kattintson **minden szolgáltatás**. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.  
2. A Naplóelemzési munkaterület lapon jelölje be a cél munkaterülettől, és válassza a **naplófájl-keresési** csempére. 
2. A naplófájl-keresési panelen, a lekérdezés mező be a következőt:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

A keresési eredmények visszaadott meg kell jelennie a szívverés rögzíti a számítógép jelző van csatlakoztatva, és a szolgáltatás jelentése.   

## <a name="next-steps"></a>További lépések

Felülvizsgálati [kezelése és karbantartása a Log Analyticshez ügynök a Windows és Linux](log-analytics-agent-manage.md) a gépeken telepítési életciklus során kezelésére, az ügynök kapcsolatban további tudnivalókat.  