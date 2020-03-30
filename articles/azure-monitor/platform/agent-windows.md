---
title: Windows-számítógépek csatlakoztatása az Azure Monitorhoz | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan kapcsolhatja össze a más felhőkben vagy a helyszínen tárolt Windows-számítógépeket az Azure Monitorhoz a Windows Log Analytics-ügynökkel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21efb16cf519d4bcad520af1c7d8818f36a77218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275034"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Windows-számítógépek csatlakoztatása az Azure Monitorhoz

A virtuális gépek vagy fizikai számítógépek figyeléséhez és kezeléséhez a helyi adatközpontban vagy más felhőkörnyezetben az Azure Monitor segítségével telepítenie kell a Log Analytics-ügynököt (más néven a Microsoft Monitoring Agent (MMA)) és konfigurálnia kell jelentést készíthet egy vagy több Log Analytics-munkaterületnek. Az ügynök is támogatja a hibrid Runbook feldolgozó szerepkör az Azure Automation.  

Egy figyelt Windows-számítógépen az ügynök microsoftfigyelési ügynök szolgáltatásként szerepel. A Microsoft Monitoring Agent szolgáltatás naplófájlokból és Windows-eseménynaplóból, teljesítményadatokból és egyéb telemetriai adatokból gyűjti az eseményeket. Még akkor is, ha az ügynök nem tud kommunikálni az Azure Monitor azt jelenti, hogy az ügynök továbbra is fut, és várólistára helyezi az összegyűjtött adatokat a felügyelt számítógép lemezén. A kapcsolat visszaállításakor a Microsoft Monitoring Agent szolgáltatás elküldi az összegyűjtött adatokat a szolgáltatásnak.

Az ügynök az alábbi módszerek egyikével telepíthető. A legtöbb esetben ezek kombinációját használják, hiszen a különböző számítógépeknél más-más módszerre lehet szükség.  Az egyes módszerek használatával kapcsolatos részleteket a cikk későbbi részében találja meg.

* Manuális telepítés. A telepítő manuálisan fut a számítógépen a telepítő varázsló segítségével, a parancssorból, vagy egy meglévő szoftverterjesztési eszközzel.
* Az Azure Automation kívánt állapotkonfiguráció (DSC). DSC használata az Azure Automationben egy parancsfájlt a Windows számítógépek már telepített a környezetben.  
* PowerShell-parancsfájl.
* Erőforrás-kezelő sablon a windows helyszíni Azure Stack rendszert futtató virtuális gépekhez. 

>[!NOTE]
>Az Azure Security Center (ASC) a Microsoft Monitoring Agent (más néven a Log Analytics Windows-ügynök) függ, és telepíti és konfigurálja, hogy jelentse a Log Analytics-munkaterület részeként a központi telepítés. Az ASC tartalmaz egy automatikus kiépítési lehetőséget, amely lehetővé teszi a Log Analytics Windows-ügynök automatikus telepítését az előfizetés összes virtuális gépén, és úgy konfigurálja, hogy egy adott munkaterületnek jelentsen. Erről a beállításról a [Log Analytics-ügynök automatikus kiépítésének engedélyezése](../../security-center/security-center-enable-data-collection.md#auto-provision-mma)című témakörben talál további információt.
>

Ha úgy kell konfigurálnia az ügynököt, hogy egynél több munkaterületnek jelentsen, ez nem hajtható végre a kezdeti telepítés során, csak ezt követően, a Vezérlőpulton vagy a PowerShellben a beállítások frissítésével, a [munkaterület hozzáadása vagy eltávolítása](agent-manage.md#adding-or-removing-a-workspace)című részében leírtak szerint.  

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Windows operációs rendszereket](log-analytics-agent.md#supported-windows-operating-systems) és a [hálózati tűzfalkonfigurációkat](log-analytics-agent.md#network-firewall-requirements) ismertető részt.

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Windows Log Analytics-ügynök telepítése előtt szüksége van a munkaterület-azonosítóra és a kulcsra a Log Analytics-munkaterülethez.  Erre az információra az egyes telepítési módszerek beállítása során van szükség az ügynök megfelelő konfigurálásához és annak biztosításához, hogy sikeresen kommunikáljon az Azure Monitorral az Azure kereskedelmi és az Egyesült Államok kormányzati felhőjében. 

1. Az Azure Portalon keressen és válassza **a Log Analytics-munkaterületeket.**
2. A Log Analytics-munkaterületek listájában válassza ki azt a munkaterületet, amelynek az ügynök jelentési beállítását szeretné konfigurálni.
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![Log Analytics speciális beállításai](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.   
5. Másolja és illessze be kedvenc szerkesztőjét, a **Munkaterület-azonosítót** és az **elsődleges kulcsot.**    
   
## <a name="configure-agent-to-use-tls-12"></a>Ügynök konfigurálása a TLS 1.2 használatára
A [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokoll használatának konfigurálásához a Windows-ügynök és a Log Analytics szolgáltatás közötti kommunikációhoz az alábbi lépéseket követve engedélyezheti az ügynököt a virtuális gépen vagy azt követően.

>[!NOTE]
>Ha windows Server 2008 SP2 x64 rendszerű virtuális gépre konfigurált a TLS 1.2 használatára, először telepítenie kell a következő [SHA-2 kódaláírási támogatási frissítést](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) az alábbi lépések végrehajtása előtt. 
>

1. Keresse meg a következő rendszerleíró alkulcsot: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Alkulcs létrehozása a TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2** **protokollok** csoportban
3. **Hozzon** létre egy ügyfél alkulcsot a korábban létrehozott TLS 1.2 protokollverzió alkulcs alatt. **Például: HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Hozza létre a következő duplaszó értékeket a **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client csoportban:**

    * **Engedélyezve** [Érték = 1]
    * **DisabledByDefault** [Érték = 0]  

Konfigurálja a .NET Framework 4.6-os vagy újabb rendszert a biztonságos titkosítás támogatására, mivel alapértelmezés szerint le van tiltva. Az [erős kriptográfia](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) biztonságosabb hálózati protokollokat használ, például a TLS 1.2 protokollt, és blokkolja a nem biztonságos protokollokat. 

1. Keresse meg a következő rendszerleíró alkulcsot: **\\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft . NETFramework\v4.0.30319**.  
2. Hozza létre a **SchUseStrongCrypto** duplaszó értéket az alkulcs **alatt, 1**értékkel.  
3. Keresse meg a rendszerleíró adatbázis következő alkulcsát: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Hozza létre a **SchUseStrongCrypto** duplaszó értéket az alkulcs **alatt, 1**értékkel. 
5. Indítsa újra a rendszert, hogy a beállítások érvénybe lépjenek. 

## <a name="install-the-agent-using-setup-wizard"></a>Az ügynök telepítése a telepítővarázslóval
A következő lépések telepítik és konfigurálják a Log Analytics-ügynököt az Azure-ban és az Azure Government-felhőben a számítógépen lévő ügynök beállítási varázslójával. Ha azt szeretné megtudni, hogyan konfigurálhatja úgy az ügynököt, hogy jelentsen egy System Center Operations Manager felügyeleti csoportnak is, olvassa [el az Operations Manager ügynök telepítése az Ügynök beállítása varázslóval című témakört.](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)

1. A Log Analytics-munkaterületen a korábban megkeresse a **Windows-kiszolgálók** lapon válassza ki a **Windows-ügynök letöltésének** megfelelő verzióját a Windows operációs rendszer processzorarchitektúrájától függően.   
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.   
6. Az **Azure Log Analytics** lapon végezze el a következőket:
   1. Illessze be az előzőleg kimásolt **Munkaterület-azonosítót** és **Munkaterületkulcsot (Elsődleges kulcs)**.  Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.  Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.  
7. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.<br><br> ![illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Annak ellenőrzéséhez, hogy a Log Analytics jelentést tesz-e, tekintse [át az Ügynök-kapcsolat ellenőrzése a Log Analytics szolgáltatással kapcsolatát.](#verify-agent-connectivity-to-log-analytics) 

## <a name="install-the-agent-using-the-command-line"></a>Az ügynök telepítése a parancssorból
Az ügynök letöltött fájlja önálló telepítőcsomag.  Az ügynök és a segédfájlok telepítőprogramja a csomagban található, és ki kell bontani annak érdekében, hogy a következő példákban látható parancssorból megfelelően lehessen telepíteni.    

>[!NOTE]
>Ha egy ügynök frissíteni szeretne, a Log Analytics-parancsfájl-szolgáltatás API-t kell használnia. További információkért tekintse meg [a Windows és Linux log analytics-ügynök kezelése és karbantartása](agent-manage.md) című témakört.

Az alábbi táblázat kiemeli az ügynök telepítője által támogatott konkrét paramétereket, beleértve az Automation DSC használatával történő üzembe helyezést is.

|MMA-specifikus beállítások                   |Megjegyzések         |
|---------------------------------------|--------------|
| NOAPM=1                               | Nem kötelező megadni. Az ügynök telepítése .NET alkalmazásteljesítmény-figyelés nélkül.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Az ügynök beállítása a munkaterületnek való jelentéshez                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterület-azonosító (guid) a munkaterület hozzáadásához                    |
|OPINSIGHTS_WORKSPACE_KEY               | A munkaterületi kulcs kezdetben a munkaterülettel való hitelesítéshez |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Adja meg azt a felhőkörnyezetet, ahol a munkaterület található <br> 0 = Azure kereskedelmi felhő (alapértelmezett) <br> 1 = Azure-kormányzat |
|OPINSIGHTS_PROXY_URL               | URI a proxy használatához |
|OPINSIGHTS_PROXY_USERNAME               | Felhasználónév hitelesített proxy eléréséhez |
|OPINSIGHTS_PROXY_PASSWORD               | Jelszó hitelesített proxy eléréséhez |

1. Az ügynök telepítőfájljainak kibontásához `MMASetup-<platform>.exe /c` egy rendszergazda jogú parancssorból, és megkéri a fájlok kibontásának elérési útját.  Másik lehetőségként megadhatja az elérési utat `MMASetup-<platform>.exe /c /t:<Full Path>`az argumentumok megadásával.  
2. Ha csendben szeretné telepíteni az ügynököt, és úgy beállítani, hogy jelentsen egy munkaterületnek az Azure kereskedelmi felhőjében, a telepítőfájlok beírásához kibontott mappából: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   vagy konfigurálja az ügynököt, hogy jelentsen az Azure US Government felhőjében, írja be a következőt: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >A *OPINSIGHTS_WORKSPACE_ID* és *OPINSIGHTS_WORKSPACE_KEY* paraméterek karakterláncértékeit idézőjelek közé kell foglalni, hogy a Windows Installer a csomag érvényes opcióinak megfelelően interprit legyen. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Az ügynök telepítése a DSC használatával az Azure Automationben

A következő parancsfájl-példával telepítheti az ügynököt az Azure Automation DSC használatával.   Ha nem rendelkezik Automation-fiókkal, olvassa el az [Azure Automation használatának első lépéseit](/azure/automation/) az Automation DSC használata előtt szükséges Automation-fiók létrehozásának követelményei és lépései.  Ha nem ismeri az Automation DSC-t, olvassa el [Az Automation DSC használata](../../automation/automation-dsc-getting-started.md)című, Első lépések című területet.

A következő példa telepíti az `URI` érték által azonosított 64 bites ügynököt. A 32 bites verziót az URI-érték cseréjével is használhatja. Mindkét verzió URI-i a következők:

- Windows 64 bites ügynök -https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 bites ügynök -https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Ez az eljárás és parancsfájlpélda nem támogatja a Windows rendszerre már telepített ügynök frissítését.

Az ügynökcsomag 32 bites és 64 bites verziói különböző termékkódokkal rendelkeznek, és a kiadott új verziók is egyedi értéket képviselnek.  A termékkód egy guid azonosító, amely egy alkalmazás vagy termék elsődleges azonosítója, és amelyet a Windows Installer **ProductCode** tulajdonság képvisel.  Az `ProductId` **MMAgent.ps1** parancsfájl értékének meg kell egyeznie a 32 vagy 64 bites ügynöktelepítő csomag termékkódjával.

Ha a termékkódot közvetlenül az ügynöktelepítő csomagból szeretné lekérni, az Orca.exe programot a [Windows Installer Developers Windows SDK-összetevőiből](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) használhatja, amely a Windows szoftverfejlesztői készlet összetevője, vagy a PowerShellt a Microsoft Valuable Professional (MVP) által írt [példaparancsfájlt](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) követve használhatja.  Mindkét megközelítésnél először ki kell bontania a **MOMagent.msi** fájlt az MMASetup telepítőcsomagból.  Ez az első lépés elején, az [Ügynök telepítése parancssorból](#install-the-agent-using-the-command-line)című szakaszban látható.  

1. Importálja az xPSDesiredStateConfiguration DSC modult az Azure Automationbe. [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration)  
2.  Hozzon létre Azure Automation változóeszközöket *OPSINSIGHTS_WS_ID* és *OPSINSIGHTS_WS_KEY.* Állítsa be *OPSINSIGHTS_WS_ID* a Log Analytics-munkaterület-azonosítójára, és állítsa be a munkaterület elsődleges kulcsának *OPSINSIGHTS_WS_KEY.*
3.  Másolja a parancsfájlt mmAgent.ps1 fájlba.

    ```powershell
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

4. Frissítse `ProductId` a parancsfájl értékét az ügynöktelepítési csomag legújabb verziójából kinyert termékkóddal a korábban ajánlott módszerekkel. 
5. [Importálja az MMAgent.ps1 konfigurációs parancsfájlt](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) az Automation-fiókba. 
5. [Rendeljen windowsos számítógépet vagy csomópontot](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) a konfigurációhoz. 15 percen belül a csomópont ellenőrzi a konfigurációját, és az ügynök leküldése a csomópontra.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ügynök-kapcsolat ellenőrzése a Log Analytics szolgáltatással

Az ügynök telepítése után ellenőrizze, hogy sikeresen csatlakoztatva van-e, és a jelentéskészítés kétféleképpen valósítható meg.  

A számítógép **Vezérlőpultjában** keresse meg a **Microsoft Monitoring Agent** elemet.  Jelölje ki, és az **Azure Log Analytics** lapon az ügynök nek meg kell jelenítenie egy üzenetet, amely a következőket tartalmazza: A Microsoft Monitoring Agent **sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz.**<br><br> ![MMA kapcsolati állapota a Log Analytics felé](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Egyszerű naplólekérdezést is végrehajthat az Azure Portalon.  

1. Az Azure Portalon keresse meg és válassza a **Figyelő lehetőséget.**
1. Válassza a menü **Naplók parancsát.**
1. A **Naplók** ablaktáblán a lekérdezésmező típusa:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

A visszaadott keresési eredményekben meg kell jelennie a számítógép szívverési rekordjainak, amelyek jelzik, hogy csatlakoztatva van, és jelentést tesz nekik a szolgáltatásnak.   

## <a name="next-steps"></a>További lépések

- Tekintse át [a Windows és Linux Log Analytics-ügynök kezelése és karbantartása](agent-manage.md) című, az ügynök újrakonfigurálásának, frissítésének és eltávolításának megismerése a virtuális gépről című útmutatót.

- Tekintse át [a Windows-ügynök hibaelhárítása](agent-windows-troubleshoot.md) című témakört, ha problémákat tapasztal az ügynök telepítése vagy kezelése során.
