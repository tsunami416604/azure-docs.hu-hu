---
title: Hibrid gépek összekötése az Azure-ba a Azure Portal
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc for Servers (előzetes verzió) használatával a Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898598"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hibrid gépek összekötése az Azure-ba a Azure Portal

Az Azure arc for Servers (előzetes verzió) szolgáltatást engedélyezheti egy vagy több Windows-vagy Linux-gépen a környezetben, ha manuálisan hajt végre lépéseket, vagy automatikus módszert használ az általunk biztosított sablon-parancsfájl futtatásával. Ez a szkript automatizálja mindkét ügynök letöltését és telepítését.

Ennek a telepítési módszernek a használatához rendszergazdai jogosultsággal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a rendszergazdai fiók és a Windows rendszeren a helyi Rendszergazdák csoport tagja.

Mielőtt elkezdené, tekintse át az [előfeltételeket](overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="generate-install-script-from-the-azure-portal"></a>Telepítési parancsfájl létrehozása a Azure Portalból

Az Azure arc letöltésének, telepítésének és létrehozásának automatizálására szolgáló parancsfájl a Azure Portal érhető el. A folyamat befejezését az alábbi lépések ismertetik.

1. A böngészőből indítsa el [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. A **gépek – Azure arc** oldalon válassza a **+ Hozzáadás** lehetőséget a bal felső sarokban, vagy válassza a **gép létrehozása – Azure arc** lehetőséget a középső ablaktábla alján. 

3. A **módszer kiválasztása** lapon válassza ki a **gépek hozzáadása az interaktív parancsfájl használatával** **parancsfájl létrehozása**lehetőséget.

4. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja.

    >[!NOTE]
    >Az Azure arc for Servers (előzetes verzió) csak a következő régiókat támogatja:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

5. A **parancsfájl létrehozása** lap **operációs rendszer** legördülő listájában válassza ki azt a megfelelő operációs rendszert, amelyen a parancsfájl futni fog.

6. Ha a gép proxykiszolgálón keresztül kommunikál az internethez, válassza a **következő lehetőséget: proxykiszolgáló >** . A **proxykiszolgáló** lapon adja meg a proxykiszolgáló IP-címét vagy nevét és portszámát, amelyet a gép a proxykiszolgálóhoz való kommunikációhoz használni fog. Adja meg a `http://<proxyURL>:<proxyport>`formátumát követő értéket. Ha elkészült, válassza a **felülvizsgálat + előállítás**lehetőséget.  Ellenkező esetben válassza a **felülvizsgálat + létrehozás** lehetőséget a lépések végrehajtásához.

7. A **felülvizsgálat + előállítás** lapon tekintse át az összegző információkat, majd kattintson a **Letöltés**gombra. Ellenkező esetben, ha módosításokat kell végeznie, válassza az **előző**lehetőséget.

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és ellenőrzése Windows rendszeren

### <a name="install-manually"></a>Manuális telepítés

A csatlakoztatott számítógép-ügynököt manuálisan is telepítheti, ha a letöltés után a Windows Installer telepítőcsomagot futtatja `AzureConnectedMachineAgent.msi`, és átmásolja azt a célkiszolgáló egyik mappájába vagy egy megosztott hálózati mappából. Ha bármilyen lehetőség nélkül futtatja a telepítőcsomagot, elindul egy telepítővarázsló, amelyet követve interaktív módon telepítheti az ügynököt.

>[!NOTE]
>Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságok szükségesek.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, az alábbi részben ismertetett parancsot a proxykiszolgáló rendszerkörnyezeti változójának beállításához `https_proxy`.

A következő táblázat azokat a paramétereket mutatja be, amelyeket az ügynöknek a parancssorból való beállítása támogat.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | A csendes telepítést felhasználói beavatkozás nélkül hajtja végre. |

A telepítőprogramnak a `/?` paraméterrel való futtatásához például írja be a `msiexec.exe /i AzureConnectedMachineAgent.msi /?`értéket.

A csatlakoztatott gépi ügynök fájljai alapértelmezés szerint a *C:\Program Files\AzureConnectedMachineAgent* -ben települnek. Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*a naplózási könyvtár.

### <a name="install-using-scripted-method"></a>Telepítés parancsfájl-metódus használatával

1. Jelentkezzen be a kiszolgálóra.

2. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

3. Váltson át a mappára, vagy ossza meg a parancsfájlt, és hajtsa végre a kiszolgálón a következő parancs futtatásával: `./OnboardingScript.ps1`.

### <a name="configure-agent-proxy-setting"></a>Ügynök proxy beállításának konfigurálása

Futtassa a következő parancsot a proxykiszolgáló környezeti változó beállításához.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>Az ügynök nem támogatja a proxy hitelesítés beállítását ebben az előzetes verzióban.
>

### <a name="configure-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után az ügynököt úgy kell konfigurálnia, hogy az alábbi parancs futtatásával kommunikáljon az Azure arc szolgáltatással:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Az ügynök telepítése és ellenőrzése Linuxon

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB) a Microsoft [Package adattárában](https://packages.microsoft.com/). A [https://aka.ms/azcmagent](https://aka.ms/azcmagent) található rendszerhéj-parancsfájl-köteg `Install_linux_azcmagent.sh` a következő műveleteket hajtja végre:

- Úgy konfigurálja a gazdagépet, hogy letöltse az ügynök csomagját a packages.microsoft.com.
- Telepíti a hibrid erőforrás-szolgáltatói csomagot.
- Szükség esetén az ügynököt a proxy adataival is konfigurálhatja a `--proxy "{proxy-url}:{proxy-port}"` paraméterrel együtt.

A parancsfájl a támogatott és nem támogatott disztribúciók azonosítására szolgáló logikát is tartalmaz, valamint a telepítés végrehajtásához szükséges engedélyek ellenőrzését. 

Az alábbi példa letölti az ügynököt, és telepíti azt a feltételes ellenőrzések végrehajtása nélkül.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

A következő parancsok futtatásával töltheti le és telepítheti az ügynököt, beleértve a `--proxy` paramétert az ügynök konfigurálásához a proxykiszolgálón keresztül történő kommunikációhoz:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után az ügynököt úgy kell konfigurálnia, hogy az alábbi parancs futtatásával kommunikáljon az Azure arc szolgáltatással:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Az Azure arc-kapcsolatok ellenőrzése

Miután elvégezte az ügynök telepítésének és konfigurálásának lépéseit az Azure arc for Servers szolgáltatáshoz való csatlakozáshoz (előzetes verzió), lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e. A Azure Portal megtekintheti a gépeket a [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)meglátogatásával.

![Sikeres előkészítés](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha le szeretne bontani egy gépet az Azure arc for Servers (előzetes verzió) kiszolgálóról, el kell végeznie az alábbi lépéseket.

1. Nyissa meg az Azure arc for Servers (előzetes verzió) lehetőséget a [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)meglátogatásával.

2. Válassza ki a gépet a listában, kattintson az ellipszisre (`...`), majd válassza a **Törlés**lehetőséget.

3. A Windows-ügynök számítógépről való eltávolításához hajtsa végre a következőket:

    1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

    2. A **Vezérlőpulton**válassza a **programok és szolgáltatások**lehetőséget.

    3. A **programok és szolgáltatások**területen válassza **Az Azure Connected Machine Agent ügynököt**, válassza az **Eltávolítás**lehetőséget, majd válassza az **Igen**lehetőséget.

        >[!NOTE]
        >Az ügynök telepítővarázslója a **AzureConnectedMachineAgent. msi** telepítőcsomag dupla kattintással is futtatható.

    Ha parancsfájlt szeretne használni az eltávolításhoz, használja a következő példát, amely lekéri a termékkód lekérését, és az msiexec. exe parancssori `msiexec /x {Product Code}`használatával eltávolítja az ügynököt. Nyissa meg a Beállításszerkesztőt, és keresse meg a `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` beállításkulcsot, és keresse meg a termékkód GUID azonosítóját. Ezután eltávolíthatja az ügynököt az msiexec használatával.

   Az alábbi példa az ügynök eltávolítását mutatja be.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. A Linux-ügynök eltávolításához hajtsa végre a következő parancsot az ügynök eltávolításához.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szabályzat társítása csatlakoztatott gépekhez](../../governance/policy/assign-policy-portal.md)
