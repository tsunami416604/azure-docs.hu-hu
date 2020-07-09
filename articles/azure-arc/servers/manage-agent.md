---
title: Az Azure arc for Servers (előzetes verzió) ügynökének kezelése
description: Ez a cikk azokat a különböző felügyeleti feladatokat ismerteti, amelyeket általában a kiszolgálók csatlakoztatott számítógép-ügynökének Azure-ív életciklusa során fog elvégezni.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 086491484592c8015bdb519bb498e73da3836f6f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103908"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>A csatlakoztatott gép ügynökének kezelése és karbantartása

Az Azure arc for Servers (előzetes verzió) csatlakoztatott gépi ügynök Windows vagy Linux rendszerre való kezdeti üzembe helyezését követően újra kell konfigurálnia az ügynököt, frissítenie vagy eltávolítania a számítógépről, ha elérte a nyugdíjazási szakaszt az életciklusában. Ezeket a rutin karbantartási feladatokat manuálisan vagy Automation használatával egyszerűen kezelheti, ami csökkenti a működési hibákat és a költségeket is.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Windows és a Linux rendszerhez készült Azure-beli csatlakoztatott gépi ügynök a követelményektől függően manuálisan vagy automatikusan is frissíthető a legújabb kiadásra. Az alábbi táblázat az ügynök frissítésének végrehajtásához támogatott metódusokat ismerteti.

| Operációs rendszer | Frissítési módszer |
|------------------|----------------|
| Windows | Manuálisan<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [Zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum használatával](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows-ügynök

A Windows rendszerhez készült csatlakoztatott számítógép-ügynök frissítési csomagja a következő helyről érhető el:

* Microsoft Update

* [Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Home.aspx)

* [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.

Az ügynök a szoftverfrissítési felügyeleti folyamat támogatásának számos módszerét követően frissíthető. A Microsoft Updatetól való beszerzésen kívül a parancssorból, egy parancsfájlból vagy más automatizálási megoldásból, illetve a végrehajtásával a felhasználói felület varázslóból manuálisan is letöltheti és futtathatja a parancsot `AzureConnectedMachine.msi` .

> [!NOTE]
> * Az ügynök frissítéséhez *rendszergazdai* engedélyekkel kell rendelkeznie.
> * A manuális frissítéshez először le kell töltenie és át kell másolnia a telepítőcsomagot egy mappába a célkiszolgálón vagy egy megosztott hálózati mappából. 

Ha nem ismeri a Windows Installer csomagok parancssori kapcsolóit, tekintse át az [msiexec standard parancssori kapcsolóit](/windows/win32/msi/standard-installer-command-line-options) és az [msiexec parancssori kapcsolókat](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>A frissítés a telepítővarázsló használatával

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. **AzureConnectedMachineAgent.msi** végrehajtása a telepítővarázsló elindításához.

A telepítővarázsló felfedi, hogy létezik-e korábbi verzió, és hogy automatikusan végrehajtja-e az ügynök frissítését. Ha a frissítés befejeződött, a telepítővarázsló automatikusan bezáródik.

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Ha csendesen szeretné frissíteni az ügynököt, és létre kell hoznia egy telepítési naplófájlt a `C:\Support\Logs` mappában, futtassa a következő parancsot.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-ügynök

Ha egy Linux rendszerű gépen szeretné frissíteni az ügynököt a legújabb verzióra, két parancsot kell magában foglalnia. Egy parancs a helyi csomag indexének frissítésére a Tárházak legújabb elérhető csomagjainak listájával, valamint egy parancs a helyi csomag frissítéséhez.

A legújabb ügynök-csomagot letöltheti a Microsoft [Package adattárból](https://packages.microsoft.com/).

> [!NOTE]
> Az ügynök frissítéséhez *rendszergazdai* jogosultságokkal vagy olyan fiókkal kell rendelkeznie, amely a sudo használatával emelt szintű jogokkal rendelkezik.

#### <a name="upgrade-ubuntu"></a>Ubuntu frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    apt update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    apt upgrade
    ```

Az [apt](https://help.ubuntu.com/lts/serverguide/apt.html) parancs műveleteit, például a csomagok telepítését és eltávolítását a rendszer naplózza a `/var/log/dpkg.log` naplófájlba.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>A Red Hat/CentOS/Amazon Linux frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    yum check-update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    yum update
    ```

A [yum](https://access.redhat.com/articles/yum-cheat-sheet) parancs műveleteit – például a csomagok telepítését és eltávolítását – a rendszer naplózza a `/var/log/yum.log` naplófájlba. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise frissítése

1. Ha frissíteni szeretné a helyi csomag indexét a Tárházak legújabb módosításaival, futtassa a következő parancsot:

    ```bash
    zypper refresh
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    zypper update
    ```

A [Zypper](https://en.opensuse.org/Portal:Zypper) parancs műveleteit, például a csomagok telepítését és eltávolítását, a rendszer naplózza a `/var/log/zypper.log` naplófájlban. 

## <a name="about-the-azcmagent-tool"></a>Tudnivalók a Azcmagent eszközről

A Azcmagent eszköz (Azcmagent.exe) segítségével konfigurálható az Azure arc for Servers (előzetes verzió) csatlakoztatott gépi ügynök a telepítés során, vagy az ügynök kezdeti konfigurációját a telepítés után módosíthatja. A Azcmagent.exe parancssori paramétereket biztosít az ügynök testreszabásához és az állapotának megtekintéséhez:

* **Kapcsolódás** – a gép és az Azure-ív összekapcsolásához

* **Leválasztás** – a gép leválasztása az Azure-ív használatával

* **Újrakapcsolódás** – leválasztott gép újrakapcsolódása az Azure arc-hoz

* **Megjelenítheti** az ügynök állapotát és a hozzá tartozó konfigurációs tulajdonságokat (erőforráscsoport-név, előfizetés-azonosító, verzió stb.), amely segíthet az ügynökkel kapcsolatos problémák elhárításában.

* **-h vagy--help** – megjeleníti az elérhető parancssori paramétereket

    Ha például meg szeretné tekinteni az **újrakapcsolódási** paraméter részletes súgóját, írja be a következőt: `azcmagent reconnect -h` . 

* **-v vagy--verbose** – részletes naplózás engedélyezése

A **csatlakozást**, a **leválasztást**és az **újracsatlakozást** manuálisan is végrehajthatja interaktív módon, vagy automatizálhatja ugyanazzal a szolgáltatással, amelyet több ügynök bevezetéséhez vagy egy Microsoft Identity platform [hozzáférési jogkivonatának](../../active-directory/develop/access-tokens.md)használatával használ. Ha nem használ egyszerű szolgáltatásnevet a számítógép Azure arc for Servers (előzetes verzió) szolgáltatáshoz való regisztrálásához, tekintse meg az alábbi [cikket](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) egy egyszerű szolgáltatásnév létrehozásához.

### <a name="connect"></a>Kapcsolódás

Ez a paraméter egy olyan erőforrást határoz meg Azure Resource Manager, amely a gépet az Azure-ban hozza létre. Az erőforrás a megadott előfizetésben és erőforráscsoporthoz van megadva, és a gép adatait a beállítás által megadott Azure-régióban tárolja a rendszer `--location` . Ha nincs megadva, az alapértelmezett erőforrás neve a gép állomásneve.

A rendszer a gép rendszerszintű identitásához tartozó tanúsítványt letölti és helyileg tárolja. A lépés elvégzése után az Azure Connected Machine Metadata Service és a vendég konfigurációs ügynök megkezdi a szinkronizálást az Azure arc for Servers (előzetes verzió) szolgáltatással.

Egy egyszerű szolgáltatásnév használatával történő kapcsolódáshoz futtassa a következő parancsot:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Hozzáférési jogkivonat használatával történő kapcsolódáshoz futtassa a következő parancsot:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Ha a rendszergazda jogú bejelentkezett hitelesítő adataival szeretne csatlakozni (interaktív), futtassa a következő parancsot:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Leválasztás

Ez a paraméter egy olyan erőforrást határoz meg Azure Resource Manager, amely a gépet jelképezi az Azure-ban. Nem törli az ügynököt a gépről, ezt külön lépésként kell elvégezni. Ha a gép le van választva, ha újra szeretné regisztrálni az Azure arc for Servers szolgáltatásban (előzetes verzió), akkor az `azcmagent connect` Azure-ban létrehoz egy új erőforrást.

Az egyszerű szolgáltatásnév használatával történő leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Hozzáférési jogkivonat használatával történő leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect --access-token <accessToken>`

Az emelt szintű bejelentkezett hitelesítő adatokkal (interaktív) való leválasztáshoz futtassa a következő parancsot:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Újracsatlakozás

Ez a paraméter újracsatlakoztatja a már regisztrált vagy csatlakoztatott gépet az Azure arc for Servers (előzetes verzió) szolgáltatáshoz. Erre akkor lehet szükség, ha a gép ki van kapcsolva legalább 45 nappal, hogy a tanúsítványa lejár. Ez a paraméter a megadott hitelesítési beállítások használatával kéri le az új hitelesítő adatokat, amelyek megfelelnek a gépet jelképező Azure Resource Manager erőforrásnak.

Ehhez a parancshoz magasabb jogosultságok szükségesek, mint az Azure-beli [csatlakoztatott gép](agent-overview.md#required-permissions) bevezetési szerepköre.

Az egyszerű szolgáltatásnév használatával történő újrakapcsolódáshoz futtassa a következő parancsot:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Hozzáférési jogkivonat használatával történő újrakapcsolódáshoz futtassa a következő parancsot:

`azcmagent reconnect --access-token <accessToken>`

Ha újra szeretne csatlakozni a rendszergazda jogú bejelentkezett hitelesítő adataihoz (interaktív), futtassa a következő parancsot:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Az ügynök eltávolítása

Az alábbi módszerek egyikével távolíthatja el a Windows vagy Linux rendszerű csatlakoztatott gépi ügynököt a gépről. Az ügynök eltávolításakor a rendszer nem törli a gép regisztrációját a kiszolgálókon (előzetes verzió), ez egy külön folyamat, amelyet akkor hajt végre, ha már nincs szüksége a gép felügyeletére az Azure-ban.

### <a name="windows-agent"></a>Windows-ügynök

A következő módszerek mindegyike eltávolítja az ügynököt, de nem távolítja el a *C:\Program Files\AzureConnectedMachineAgent* mappát a gépen.

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról

1. A Windows-ügynök számítógépről való eltávolításához tegye a következőket:

    a. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.  
    b. A **Vezérlőpulton**válassza a **programok és szolgáltatások**lehetőséget.  
    c. A **programok és szolgáltatások**területen válassza **Az Azure Connected Machine Agent ügynököt**, válassza az **Eltávolítás**lehetőséget, majd válassza az **Igen**lehetőséget.  

    >[!NOTE]
    > Az ügynök telepítővarázslója úgy is futtatható, ha duplán kattint a **AzureConnectedMachineAgent.msi** telepítőcsomag csomagra.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból

Ha manuálisan szeretné eltávolítani az ügynököt a parancssorból, vagy automatikus metódust (például egy parancsfájlt) szeretne használni, használja az alábbi példát. Először le kell kérnie a termékkód beolvasását, amely az alkalmazáscsomag elsődleges azonosítója, az operációs rendszerből. Az Eltávolítás a Msiexec.exe parancssor használatával történik `msiexec /x {Product Code}` .

1. Nyissa meg a beállításszerkesztőt.

2. A beállításkulcs szakaszban `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` Keresse meg és másolja a termékkód GUID azonosítóját.

3. Ezután az alábbi példákkal távolíthatja el az ügynököt az msiexec használatával:

   * A parancssorból írja be a következőt:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Ugyanezeket a lépéseket a PowerShell használatával is végrehajthatja:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-ügynök

> [!NOTE]
> Az ügynök eltávolításához *rendszergazdai* jogosultságokkal vagy olyan fiókkal kell rendelkeznie, amely a sudo használatával emelt szintű jogokkal rendelkezik.

A Linux-ügynök eltávolításához a használni kívánt parancs a Linux operációs rendszertől függ.

- Ubuntu esetében futtassa a következő parancsot:

    ```bash
    sudo apt purge azcmagent
    ```

- A RHEL, a CentOS és az Amazon Linux esetében futtassa a következő parancsot:

    ```bash
    sudo yum remove azcmagent
    ```

- A SLES futtassa a következő parancsot:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Számítógép regisztrációjának törlése

Ha azt tervezi, hogy leállítja a gép felügyeletét az Azure-ban támogató szolgáltatásokkal, hajtsa végre a következő lépéseket a gép az arc for Servers (előzetes verzió) kiszolgálóval való regisztrációjának megszüntetéséhez. Ezeket a lépéseket megelőzően vagy azt követően is elvégezheti, hogy eltávolította a csatlakoztatott gépi ügynököt a gépről.

1. Nyissa meg az Azure arc for Servers (előzetes verzió) lehetőséget a [Azure Portal](https://aka.ms/hybridmachineportal).

2. Válassza ki a gépet a listából, válassza a három pontot (**..**.), majd válassza a **Törlés**lehetőséget.

## <a name="update-or-remove-proxy-settings"></a>Proxybeállítások frissítése vagy eltávolítása

Ha úgy szeretné konfigurálni az ügynököt, hogy proxykiszolgálón keresztül kommunikáljon a szolgáltatással, vagy távolítsa el ezt a konfigurációt az üzembe helyezés után, vagy használja az alábbi módszerek egyikét a feladat elvégzéséhez.

### <a name="windows"></a>Windows

A proxykiszolgáló környezeti változójának beállításához futtassa a következő parancsot:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Ha úgy szeretné konfigurálni az ügynököt, hogy a proxykiszolgálón keresztül kommunikáljon, futtassa a következő parancsot a proxykiszolgáló környezeti változó eltávolításához és az ügynök szolgáltatás újraindításához:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

A proxykiszolgáló beállításához futtassa a következő parancsot abból a címtárból, amelyet az ügynök telepítési csomagja letöltött:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Ha úgy szeretné konfigurálni az ügynököt, hogy a proxykiszolgáló használatával leállítsa a kommunikációt, futtassa a következő parancsot a proxy konfigurációjának eltávolításához:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és a Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, és az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatásokkal, például a [Azure Security Centerekkel](../../security-center/security-center-intro.md)felügyeli.
