---
title: Az Azure Arc kezelése kiszolgálókhoz (előzetes verziójú) ügynökhöz
description: Ez a cikk ismerteti a különböző felügyeleti feladatokat, amelyekáltalában az Azure Arc a kiszolgálók csatlakoztatott gép ügynöke általában végrehajtani.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308975"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>A csatlakoztatott számítógép ügynökének kezelése és karbantartása

Az Azure Arc üzembe helyezése után a kiszolgálók (előzetes verzió) Csatlakoztatott gép ügynök Windows vagy Linux, előfordulhat, hogy újra kell konfigurálnia az ügynök, frissítse, vagy távolítsa el a számítógépről, ha elérte a kivonási szakaszban az életciklusa. Ezeket a rutinkarbantartási feladatokat egyszerűen kezelheti manuálisan vagy automatizálással, ami csökkenti a működési hibákat és a költségeket.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Windows és Linux Azure Connected Machine ügynök manuálisan vagy automatikusan frissíthető a legújabb kiadásra a követelményektől függően. Az alábbi táblázat az ügynökfrissítés végrehajtásához támogatott módszereket ismerteti.

| Operációs rendszer | Frissítési módszer |
|------------------|----------------|
| Windows | Manuálisan<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper között](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows-ügynök

Ha windowsos gépen szeretné frissíteni az ügynököt a legújabb verzióra, az ügynök elérhető a Microsoft Update szolgáltatásból, és a meglévő szoftverfrissítés-kezelési folyamattal telepíthető. Manuálisan is futtatható a parancssorból, egy parancsfájlból vagy más automatizálási megoldásból, vagy a felhasználói felület varázslójából a `AzureConnectedMachine.msi`végrehajtással. 

> [!NOTE]
> * Az ügynök frissítéséhez rendszergazdai engedélyekkel kell *rendelkeznie.*
> * A manuális frissítéshez először le kell töltenie és át kell másolnia a Telepítő csomagot a célkiszolgáló natérébe vagy egy megosztott hálózati mappába. 

Ha nem ismeri a Windows Installer csomagok parancssori beállításait, tekintse át az [Msiexec szabványos parancssori beállításait](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) és az [Msiexec parancssori kapcsolókat.](https://docs.microsoft.com/windows/win32/msi/command-line-options)

#### <a name="to-upgrade-using-the-setup-wizard"></a>Frissítés a Telepítő varázslóval

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az **AzureConnectedMachineAgent.msi** fájl végrehajtása a telepítővarázsló elindításához.

A telepítővarázsló felderíti, hogy létezik-e korábbi verzió, majd automatikusan végrehajtja az ügynök frissítését. A frissítés befejezéseután a telepítővarázsló automatikusan bezárul.

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök csendes frissítéséhez és a mappában lévő `C:\Support\Logs` telepítési naplófájl létrehozásához futtassa a következő parancsot.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux ügynök

Az ügynök frissítése egy Linux gépen a legújabb verzióra, két parancsot foglal magában. Egy parancs a helyi csomagindex frissítésére az adattárakból elérhető legújabb csomagok listájával, és egy parancs a helyi csomag frissítéséhez. 

> [!NOTE]
> Az ügynök frissítéséhez *root* hozzáférési engedélyekkel vagy olyan fiókkal kell rendelkeznie, amely a Sudo használatával emelt szintű jogosultságokkal rendelkezik.

#### <a name="upgrade-ubuntu"></a>Az Ubuntu frissítése

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    apt update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    apt upgrade
    ```

Az [apt](https://help.ubuntu.com/lts/serverguide/apt.html) parancs lépései, például a csomagok telepítése `/var/log/dpkg.log` és eltávolítása, a naplófájlba kerülnek.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Frissítés Red Hat / CentOS / Amazon Linux

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    yum check-update
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    yum update
    ```

A [yum](https://access.redhat.com/articles/yum-cheat-sheet) parancs lépései, például a csomagok telepítése `/var/log/yum.log` és eltávolítása a naplófájlba kerülnek. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise frissítése

1. Ha frissíteni szeretné a helyi csomagindexet az adattárakban végrehajtott legújabb módosításokkal, futtassa a következő parancsot:

    ```bash
    zypper refresh
    ```

2. A rendszer frissítéséhez futtassa a következő parancsot:

    ```bash
    zypper update
    ```

A [zypper](https://en.opensuse.org/Portal:Zypper) parancs műveletek, például a csomagok telepítése és `/var/log/zypper.log` eltávolítása a naplófájlba kerülnek. 

## <a name="about-the-azcmagent-tool"></a>Az Azcmagent eszköz

Az Azcmagent eszköz (Azcmagent.exe) konfigurálja az Azure Arc kiszolgálók (előzetes verzió) Csatlakoztatott gép ügynök a telepítés során, vagy módosítsa a kezdeti konfigurációaz ügynök a telepítés után. Az Azcmagent.exe parancssori paramétereket biztosít az ügynök testreszabásához és állapotának megtekintéséhez:

* **Csatlakozás** – A gép csatlakoztatása az Azure Archoz

* **Kapcsolat bontása** – A gép leválasztása az Azure Arcról

* **Újracsatlakozás** – Leválasztott gép újracsatlakoztatása az Azure Archoz

* **Megjelenítés** – Az ügynök állapotának és konfigurációs tulajdonságainak megtekintése (Erőforráscsoport neve, Előfizetés-azonosító, verzió stb.), amelyek segíthetnek az ügynökkel kapcsolatos probléma elhárításában.

* **-h vagy --help** - A rendelkezésre álló parancssori paraméterek megjelenítése

    Ha például részletes súgót szeretne látni `azcmagent reconnect -h`az **Újracsatlakozás** paraméterhez, írja be a következőt: . 

* **-v vagy --verbose** - Részletes naplózás engedélyezése

A **Csatlakozás**, **a Kapcsolat bontása**és az **Újracsatlakozás** műveletet interaktív bejelentkezés közben manuálisan is elvégezheti, vagy automatizálhatja ugyanazt a egyszerű szolgáltatást, amelyet több ügynök vagy egy Microsoft-identitásplatform-hozzáférési [jogkivonat](../../active-directory/develop/access-tokens.md)használatával használt. Ha nem egy egyszerű szolgáltatás használatával regisztrálja a gépet az Azure Arc kiszolgálók (előzetes verzió), tekintse meg a következő [cikket](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) egy egyszerű szolgáltatás létrehozásához.

### <a name="connect"></a>Kapcsolódás

Ez a paraméter egy erőforrást ad meg az Azure Resource Managerben, amely a gépet képviseli az Azure-ban. Az erőforrás a megadott előfizetési és erőforráscsoportban van, és a gép adatai `--location` a beállítás által megadott Azure-régióban tárolódnak. Az alapértelmezett erőforrásnév a számítógép állomásneve, ha nincs megadva.

A rendszer által hozzárendelt identitásnak megfelelő tanúsítványt ezután letölti és helyileg tárolja. Ha ez a lépés befejeződött, az Azure Connected Machine metaadat-szolgáltatás és a vendég konfigurációs ügynök szinkronizálása az Azure Arc kiszolgálók (előzetes verzió).

Ha egyszerű szolgáltatáshasználatával szeretne csatlakozni, futtassa a következő parancsot:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Ha hozzáférési jogkivonaton keresztül szeretne csatlakozni, futtassa a következő parancsot:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

A rendszergazda által naplózott hitelesítő adatok (interaktív) használatával való kapcsolatfelvételhez futtassa a következő parancsot:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Leválasztás

Ez a paraméter egy erőforrást ad meg az Azure Resource Managerben, amely a gépet képviseli az Azure-ban. Nem törli az ügynököt a gépről, ezt külön lépésként kell elvégezni. A gép leválasztása után, ha azt szeretné, hogy újra regisztrálja `azcmagent connect` az Azure Arc a kiszolgálók (előzetes verzió), használja, így egy új erőforrás jön létre az Azure-ban.

Ha egyszerű szolgáltatáshasználatával szeretne leválasztani a kapcsolatot, futtassa a következő parancsot:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Ha hozzáférési jogkivonathasználatával szeretne leválasztani a kapcsolatot, futtassa a következő parancsot:

`azcmagent disconnect --access-token <accessToken>`

A rendszergazda által immár bejelentkezett hitelesítő adatokkal (interaktív) való kapcsolat bontásához futtassa a következő parancsot:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Újracsatlakozás

Ez a paraméter újra csatlakoztatja a már regisztrált vagy csatlakoztatott gépet az Azure Arc kiszolgálókhoz (előzetes verzió). Erre akkor lehet szükség, ha a tanúsítvány lejáratához legalább 45 napig ki van kapcsolva a készülék. Ez a paraméter a megadott hitelesítési beállításokat használja az Azure Resource Manager-erőforrásnak megfelelő új hitelesítő adatok lekéréséhez.

Ez a parancs magasabb jogosultságokat igényel, mint az [Azure Connected Machine bevezetési](overview.md#required-permissions) szerepkör.

Ha egyszerű szolgáltatáshasználatával szeretne újra csatlakozni, futtassa a következő parancsot:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Ha hozzáférési jogkivonathasználatával szeretne újra csatlakozni, futtassa a következő parancsot:

`azcmagent reconnect --access-token <accessToken>`

A kiemelt enyelegtel (interaktív) való újracsatlakozáshoz futtassa a következő parancsot:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Az ügynök eltávolítása

Hajtsa végre az alábbi módszerek egyikét a Windows vagy Linux connected machine ügynök eltávolításához a gépről. Az ügynök eltávolítása nem törölje a gép regisztrációját az Arc kiszolgálók (előzetes verzió), ez egy külön folyamat, amelyakkor hajtvégre, ha már nem kell kezelni a gépet az Azure-ban.

### <a name="windows-agent"></a>Windows-ügynök

Mindkét alábbi módszer eltávolítja az ügynököt, de nem távolítja el a *C:\Program Files\AzureConnectedMachineAgent* mappát a számítógépen.

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról

1. A Windows-ügynök számítógépről való eltávolításához tegye a következőket:

    a. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.  
    b. A **Vezérlőpulton**válassza a **Programok és szolgáltatások**lehetőséget.  
    c. A **Programok és szolgáltatások**területen válassza az Azure Connected Machine **Agent**lehetőséget, válassza **az Eltávolítás**lehetőséget, majd az **Igen**lehetőséget.  

    >[!NOTE]
    > Az ügynök telepítővarázslóját az **AzureConnectedMachineAgent.msi** telepítőcsomagra duplán kattintva is futtathatja.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból

Ha manuálisan szeretné eltávolítani az ügynököt a parancssorból, vagy automatikus módszert, például parancsfájlt szeretne használni, a következő példát használhatja. Először le kell kérnie a termékkódot, amely egy GUID, amely az alkalmazáscsomag fő azonosítója az operációs rendszerből. Az eltávolítás az Msiexec.exe parancssorból `msiexec /x {Product Code}`történik .
    
1. Nyissa meg a Rendszerleíróadatbázis-szerkesztőt.

2. A rendszerleíró `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`kulcs alatt keresse meg és másolja a termékkód GUID azonosítóját.

3. Ezután az Msiexec használatával eltávolíthatja az ügynököt az alábbi példák használatával:

   * A parancssori típusból:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Ugyanazokat a lépéseket a PowerShell használatával hajthatja végre:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux ügynök

> [!NOTE]
> Az ügynök eltávolításához *root* hozzáférési engedélyekkel vagy a Sudo használatával emelt szintű jogosultságokkal rendelkező fiókkal kell rendelkeznie.

A Linux-ügynök eltávolításához a használni kívánt parancs a Linux operációs rendszertől függ.

- Ubuntu esetén futtassa a következő parancsot:

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL, CentOS és Amazon Linux esetén futtassa a következő parancsot:

    ```bash
    sudo yum remove azcmagent
    ```

- Az SLES esetében futtassa a következő parancsot:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Gép regisztrációjának megszüntetése

Ha azt tervezi, hogy leállítja a gép kezelését az Azure-beli támogató szolgáltatásokkal, hajtsa végre a következő lépéseket a gép regisztrációjának megszüntetése az Arc kiszolgálókhoz (előzetes verzió). Ezeket a lépéseket a csatlakoztatott számítógép ügynökének a számítógépről való eltávolítása előtt vagy után hajthatja végre.

1. Nyissa meg az Azure Arc kiszolgálókhoz (előzetes verzió) az [Azure Portalon.](https://aka.ms/hybridmachineportal)

2. Jelölje ki a gépet a listában, jelölje ki a három pontot (**...**), majd kattintson a **Törlés gombra.**
