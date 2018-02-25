---
title: " Az Azure Site Recovery kibővített folyamat kiszolgáló kezelése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be és felügyelhet egy kibővített Folyamatkiszolgáló az Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="manage-a-scale-out-process-server"></a>Kibővített folyamat kiszolgáló kezelése

Kibővített folyamat kiszolgáló működik a Site Recovery services és a helyszíni infrastruktúra közötti adatátvitel koordinátora. Ez a cikk ismerteti, hogyan állítsa be, konfigurálása és kibővített folyamat kiszolgáló kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek
A következő a javaslatoknak megfelelő hardverre, a szoftverek és a hálózati konfigurációt a kibővített Folyamatkiszolgáló beállításához szükséges.

> [!NOTE]
> [Kapacitástervezés](site-recovery-capacity-planner.md) egy fontos lépés annak érdekében, hogy telepít a kibővített Folyamatkiszolgáló konfigurálása, hogy a csomagok a terhelési követelményeknek. Tudjon meg többet az [jellemzők skálázás kibővített folyamat kiszolgáló](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>A kibővített Folyamatkiszolgáló szoftver letöltése
1. Jelentkezzen be az Azure-portálon, és keresse meg a Recovery Services-tároló.
2. Keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** (a VMware és fizikai gépek).
3. Válassza ki a konfigurációs kiszolgálót részletekbe menően tárhatják fel a konfigurációs kiszolgáló részletei lapon.
4. Kattintson a **+ Folyamatkiszolgáló** gombra.
5. Az a **hozzáadása folyamatkiszolgáló** lapon jelölje be **helyszíni telepítés kibővített Folyamatkiszolgáló** parancsát a **válassza ki, hol szeretné üzembe helyezni a folyamatkiszolgálót** legördülő.

  ![Kiszolgálók lap hozzáadása](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Kattintson a **töltse le a Microsoft Azure Site Recovery az egységes telepítő** töltse le a legújabb verzióját a kibővített Folyamatkiszolgáló telepítése mutató hivatkozást.

  > [!WARNING]
  A kibővített folyamat kiszolgáló verziója korábbi, mint a konfigurációs kiszolgáló verziója a környezetben futó vagy azzal egyenlőnek kell lennie. Egyszerűen verziókompatibilitás ellenőrzése érdekében gondoskodjon arról, hogy telepíteni vagy frissíteni a konfigurációs kiszolgálón mostanában használt azonos telepítő biteket használja.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Telepítése és regisztrálása a kibővített folyamat kiszolgáló grafikus felhasználói felület
Ha a központi telepítés meghaladja a 200 forrásgépek, vagy több, mint 2 TB-os teljes napi változási sebessége horizontális, további folyamat kiszolgálók kezeléséhez a forgalom mennyisége kell.

Ellenőrizze a [vonatkozó javaslatok kiszolgálókhoz folyamat méretezés](#size-recommendations-for-the-process-server), majd kövesse ezeket az utasításokat a folyamatkiszolgáló beállítása. A kiszolgáló beállítása után telepít át forrásgépek rá.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Telepítése és regisztrálása a kibővített Folyamatkiszolgáló parancssori használata

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Példa
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Kibővített Folyamatkiszolgáló telepítő parancssori argumentumokat.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Proxykiszolgáló-beállítások konfigurációs fájl létrehozása
ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. Fájl létrehozása a következő formátum használatával, és adja át bemeneti ProxySettingsFilePath paraméterként.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Kibővített Folyamatkiszolgáló proxy beállításainak módosítása
1. Jelentkezzen be a kibővített Folyamatkiszolgálót.
2. Nyisson meg egy rendszergazda PowerShell parancsablakot.
3. A következő parancsot
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Ezután keresse meg a könyvtár **%PROGRAMDATA%\ASR\Agent** , és futtassa a következő parancsot
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>A kibővített folyamat-kiszolgáló
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Ezután nyisson meg egy rendszergazdai parancssort.
* Keresse meg a könyvtár **%PROGRAMDATA%\ASR\Agent** , és futtassa a parancsot

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>A kibővített folyamat-kiszolgáló frissítése
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Kibővített folyamat kiszolgáló leszerelése
1. Győződjön meg arról, hogy:
  - Konfigurációs kiszolgáló kapcsolat állapota jeleníti meg, mint a **csatlakoztatva** az Azure-portálon
  - Folyamat-kiszolgáló egy továbbra is képesek kommunikálni a konfigurációs kiszolgáló.
2. Rendszergazdaként jelentkezzen be a folyamatkiszolgáló
3. Nyissa meg Vezérlőpult > Program > Programok eltávolítása
4. Távolítsa el a programokat, megadva a következő sorrendben:
  * Microsoft Azure Recovery konfigurációs kiszolgáló/folyamat helykiszolgáló
  * A Microsoft Azure Site helyreállítási konfigurációs kiszolgáló függőségek
  * Microsoft Azure Recovery Services Agent

Is igénybe vehet fel a 15 perc, a Folyamatkiszolgáló törlésre megfelelően az Azure portálon.

  > [!NOTE]
  Ha a folyamatkiszolgáló nem tudja a konfigurációs kiszolgálóval való kommunikációhoz (a portál kapcsolati állapota Disconnected), majd kövesse az alábbi lépések végrehajtásával kiürítése azt a konfigurációs kiszolgálóról.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>A konfigurációs kiszolgálóról a leválasztott kibővített folyamat-kiszolgáló regisztrációjának törlése

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>A kibővített Folyamatkiszolgáló méretezési követelményei

| **További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * @ 2,5 GHz-es 2 mag), 8 GB-os memória |300 GB |250 GB vagy kevesebb |Kisebb vagy 85 gépek replikálása. |
|8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag), 12-GB memória |600 GB |250 GB és 1 TB |Replikálja a 85-150 gépek között. |
|12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) 24-GB memória |1 TB |1 TB to 2 TB |150-225 gépek közti replikálásához. |
