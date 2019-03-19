---
title: Állítsa be az Azure-ban folyamatkiszolgáló sikertelen vissza a vészhelyreállítás során a VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be a folyamatkiszolgáló az Azure-ban, sikertelen lesz az Azure-ból a helyszíni VMware virtuális gépek és fizikai kiszolgálók vészhelyreállítása során.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mayg
ms.openlocfilehash: e3f6a160f57a4432f91c395a2e0dd664bc8f323d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106542"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Méretezési csoport és további folyamatkiszolgálók feladat-visszavételhez

Alapesetben, amikor replikál a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA [Site Recovery](site-recovery-overview.md), folyamatkiszolgáló a konfigurációs kiszolgáló gépen telepítve van, és koordinálja a Site Recovery közötti adatátvitelt és a helyszíni infrastruktúrával. Kapacitás és a horizontális felskálázás a replikációtelepítés növelése érdekében hozzáadhat további önálló folyamatkiszolgálók. Ez a cikk azt ismerteti, hogyan teheti ezt.

## <a name="before-you-start"></a>Előkészületek

### <a name="capacity-planning"></a>Kapacitástervezés

Győződjön meg arról, hogy végre [kapacitástervezés](site-recovery-plan-capacity-vmware.md) a VMware replikálásához. Ennek segítségével azonosíthatja módjának és további folyamatkiszolgálók üzembe kell helyeznie.

> [!NOTE]
> Klónozott Folyamatkiszolgáló összetevő nem támogatott. Kövesse a jelen cikk egyes PS horizontális felskálázást.

### <a name="sizing-requirements"></a>Méretezési követelményei 

Ellenőrizze a méretezési követelmények a táblázat foglalja össze. Általánosságban elmondható Ha több mint 200 forrásgépek, a telepítés méretezésére rendelkezik, vagy ha több mint 2 TB-os napi adatváltozásának összesen, szüksége további folyamatkiszolgálók kezelése a forgalom mennyisége.

| **További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek** |
| --- | --- | --- | --- |
|4 vcpu-k (2 sockets * 2 mag \@ 2,5 GHz-es), 8 GB memória |300 GB |250 GB vagy kevesebb |Kisebb vagy 85 gépeket replikálni. |
|8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es), 12 GB memória |600 GB |250 GB – 1 TB |Replikálja a 85-150 gépek között. |
|12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) 24 GB memória |1 TB |1 TB-os 2 TB-ig |150-225 gépek között replikálja. |

Ha minden egyes védett forrásgép egyenként 100 GB-os 3 lemezzel van konfigurálva.

### <a name="prerequisites"></a>Előfeltételek

A további folyamatkiszolgálón előfeltételeit az alábbi táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



## <a name="download-installation-file"></a>Telepítési fájl letöltése

A folyamatkiszolgáló telepítőfájlja töltse le a következőképpen:

1. Jelentkezzen be az Azure Portalon, és keresse meg a Recovery Services-tárolóba.
2. Nyissa meg **Site Recovery-infrastruktúra** > **VMware-alapú és fizikai gépek** > **konfigurációs kiszolgálók** (a VMware és fizikai Gépek).
3. Válassza ki a konfigurációs kiszolgáló, a részletek feltárásához. Kattintson a **+ Folyamatkiszolgáló**.
4. A **adja hozzá a folyamatkiszolgáló** >  **válassza ki, hol szeretné üzembe helyezni a folyamatkiszolgálót**válassza **helyszíni üzembe helyezés horizontális felskálázási Folyamatkiszolgáló**.

   ![Kiszolgálók lap hozzáadása](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kattintson a **töltse le a Microsoft Azure Site Recovery egyesített telepítővel**. Ez letölti a legújabb verzióját a telepítőfájlt.

   > [!WARNING]
   > A folyamatkiszolgáló telepítés verziójának egyeznie kell, vagy a korábbi, mint a konfigurációs kiszolgáló verzióját telepítette, futó. Egy egyszerű módja annak biztosítása érdekében a verziókompatibilitás, hogy az azonos, telepíteni vagy frissíteni a konfigurációs kiszolgálóra legutóbb használt telepítővel.

## <a name="install-from-the-ui"></a>Telepítse a felhasználói felületről

Telepítse az alábbiak szerint. Miután beállította a kiszolgálón, telepítse át forrásgépek használhatja azt.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Telepítése a parancssorból

Telepítse a következő parancs futtatásával:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Ha parancssori paraméterek a következők:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Példa:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Hozzon létre egy proxy

Állítsa be a proxy van szüksége, ha a ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. A fájl a következőképpen hozhat létre, és adja át azt ProxySettingsFilePath bemeneti paraméterként.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>További lépések
Ismerje meg [kiszolgálóbeállítások kezeléséhez feldolgozni.](vmware-azure-manage-process-server.md)
