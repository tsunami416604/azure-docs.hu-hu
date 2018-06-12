---
title: Állítsa be a folyamatkiszolgáló az Azure-ban a VMware virtuális és fizikai kiszolgálók feladat-visszavétel az Azure Site Recovery |} Microsoft Docs
description: A cikkből megtudhatja, hogyan állíthat be a folyamatkiszolgáló, Azure, a feladat-visszavételi Azure virtuális gépek VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 3e53954341136a293052f9af755515a5552432fe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300847"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>A méretezhetőség érdekében további folyamat kiszolgálók beállítása

Alapértelmezés szerint amikor replikál, VMware virtuális gépek vagy fizikai kiszolgálók Azure használatával [Site Recovery](site-recovery-overview.md), a folyamatkiszolgáló a konfigurációs kiszolgáló gépre van telepítve, és koordinálja a Site Recovery közötti adatátvitelt és a helyszíni infrastruktúrával. Kapacitás és a replikációtelepítés kibővítési növelése érdekében további önálló folyamatot kiszolgálót is hozzáadhat. Ez a cikk ismerteti, hogyan ehhez.

## <a name="before-you-start"></a>Előkészületek

### <a name="capacity-planning"></a>Kapacitástervezés

Győződjön meg arról, hogy végre [kapacitástervezés](site-recovery-plan-capacity-vmware.md) a VMware-replikáció. Ezzel a megoldással azonosításához hogyan és mikor telepítsen további folyamat kiszolgálók.

### <a name="sizing-requirements"></a>Méretezési követelmények 

Ellenőrizze a méretezési követelmények a táblázat tartalmazza. Általánosságban elmondható Ha több mint 200 forrás gépekre a telepítés méretezésére, vagy naponta kavarog egyidejűleg több, mint 2 TB-os aránya teljes, van szüksége további folyamat kiszolgálók kezeléséhez a forgalom mennyisége.

| **További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * @ 2,5 GHz-es 2 mag), 8 GB-os memória |300 GB |250 GB vagy kevesebb |Kisebb vagy 85 gépek replikálása. |
|8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag), 12-GB memória |600 GB |250 GB és 1 TB |Replikálja a 85-150 gépek között. |
|12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) 24-GB memória |1 TB |1 TB-os és 2 TB |150-225 gépek közti replikálásához. |

### <a name="prerequisites"></a>Előfeltételek

További folyamatkiszolgáló előfeltételeit az alábbi táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Telepítőfájljának letöltése

Töltse le a folyamatkiszolgáló telepítőfájlja az alábbiak szerint:

1. Jelentkezzen be az Azure-portálon, és keresse meg a Recovery Services-tároló.
2. Nyissa meg **Site Recovery-infrastruktúra** > **VMWare és fizikai gépek** > **konfigurációs kiszolgálók** (a VMware és fizikai Gépek).
3. Válassza ki a konfigurációs kiszolgálót részletekbe menően tárhatják fel a kiszolgáló adatait. Kattintson a **+ Folyamatkiszolgáló**.
4. A **hozzáadása folyamatkiszolgáló** >  **válassza ki, hol szeretné üzembe helyezni a folyamatkiszolgálót**, jelölje be **helyszíni telepítés kibővített Folyamatkiszolgáló**.

  ![Kiszolgálók lap hozzáadása](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kattintson a **töltse le a Microsoft Azure Site Recovery az egységes telepítő**. Ezzel letölti a legújabb verzióját a telepítőfájlt.

  > [!WARNING]
  A konfigurációs kiszolgáló verziója telepítve fut, a telepítési folyamat verziója ugyanaz legyen, mint, vagy a korábbi, mint. Egyszerűen verziókompatibilitás ellenőrzése érdekében gondoskodjon arról, hogy telepíteni vagy frissíteni a konfigurációs kiszolgáló legutóbb használt azonos telepítőn használja.

## <a name="install-from-the-ui"></a>Telepítse a felhasználói felületen

Telepítse az alábbiak szerint. A kiszolgáló beállítása után telepít át forrásgépek rá.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Telepítse a parancssorból

Telepítse a következő parancs futtatásával:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Ha parancssori kapcsolók a következők:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Példa:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Hozzon létre egy proxy

Ha a proxy beállítása van szüksége, a ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. Az alábbiak szerint hozza létre a fájlt, és adja át bemeneti ProxySettingsFilePath paraméterként.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>További lépések
További tudnivalók [kiszolgálóbeállítások kezeléséhez feldolgozni.](vmware-azure-manage-process-server.md)
