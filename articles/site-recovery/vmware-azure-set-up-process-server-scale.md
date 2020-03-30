---
title: Kibővített folyamatkiszolgáló beállítása a VMware virtuális gépek és a fizikai kiszolgálók vészhelyreállítása során az Azure Site Recovery szolgáltatással | Microsoft Dokumentumok"
description: Ez a cikk ismerteti, hogyan állíthat be horizontális felskálázási folyamat kiszolgáló a VMware virtuális gépek és a fizikai kiszolgálók vészhelyreállítása során.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257146"
---
# <a name="scale-with-additional-process-servers"></a>Méretezés további folyamatkiszolgálókkal

Alapértelmezés szerint, ha a Site [Recovery](site-recovery-overview.md)használatával piszkotmásolja a VMware virtuális gépeket vagy a fizikai kiszolgálókat az Azure-ba, a konfigurációs kiszolgálón egy folyamatkiszolgáló van telepítve, és a Site Recovery és a helyszíni infrastruktúra közötti adatátvitel koordinálására szolgál. A kapacitás növeléséhez és a replikáció központi telepítésének kiszélesedéséhez további önálló folyamatkiszolgálókat is hozzáadhat. Ez a cikk a kibővített folyamatkiszolgáló beállítását ismerteti.

## <a name="before-you-start"></a>Előkészületek

### <a name="capacity-planning"></a>Kapacitástervezés

Győződjön meg arról, hogy végrehajtotta a [VMware replikációkapacitás-tervezését.](site-recovery-plan-capacity-vmware.md) Ez segít annak azonosításában, hogy hogyan és mikor kell további folyamatkiszolgálókat telepítenie.

A 9.24-es verziótól az új replikációk folyamatkiszolgálójának kiválasztásakor útmutatást ad hozzá. A folyamatkiszolgáló bizonyos feltételek alapján kifogástalan, figyelmeztetés és kritikus jelöléssel lesz ellátva. A folyamatkiszolgáló állapotát befolyásoló különböző forgatókönyvek megértéséhez tekintse át a [folyamatkiszolgálóriasztásait.](vmware-physical-azure-monitor-process-server.md#process-server-alerts)

> [!NOTE]
> A klónozott Process Server-összetevő használata nem támogatott. Kövesse az ebben a cikkben leírt lépéseket az egyes PS horizontális felskálázás.

### <a name="sizing-requirements"></a>Méretezési követelmények 

Ellenőrizze a táblázatban összefoglalt méretezési követelményeket. Általában, ha a központi telepítés több mint 200 forrásgépek, vagy ha van egy teljes napi lemorzsolódási sebesség több mint 2 TB, további folyamatkiszolgálók a forgalom kezelésére.

| **További folyamatkiszolgáló** | **A lemez méretének gyorsítótára** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- |
|4 vCPU (2 foglalat * \@ 2 mag 2,5 GHz), 8 GB memória |300 GB |250 GB vagy kevesebb |85 vagy kevesebb gép replikálására. |
|8 vCPU (2 foglalat * \@ 4 mag 2,5 GHz), 12 GB memória |600 GB |250 GB–1 TB |Replikálja között 85-150 gépek. |
|12 vCPU (2 foglalat * \@ 6 mag 2,5 GHz) 24 GB memória |1 TB |1 TB és 2 TB között |Replikálja között 150-225 gépek. |

Ahol minden védett forrásgép 3, egyenként 100 GB-os lemezzel van konfigurálva.

### <a name="prerequisites"></a>Előfeltételek

A további folyamatkiszolgáló előfeltételeit az alábbi táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Telepítési fájl letöltése

Töltse le a folyamatkiszolgáló telepítési fájlját az alábbiak szerint:

1. Jelentkezzen be az Azure Portalon, és keresse meg a Recovery Services-tallózó.
2. Nyissa **meg a Site Recovery Infrastructure** > **VMWare és fizikai gépek** > **konfigurációs kiszolgálóit** (a VMware & fizikai gépek esetén).
3. Válassza ki a konfigurációs kiszolgálót a kiszolgáló részleteinek részletezéséhez. Ezután kattintson **a + Process Server gombra.**
4. A **Folyamatkiszolgáló** >  hozzáadása: Válassza ki a**folyamatkiszolgáló üzembe helyezésének helyét,** és válassza a Kibővített **folyamatkiszolgáló helyszíni telepítése lehetőséget.**

   ![Kiszolgálók hozzáadása lap](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kattintson **a Microsoft Azure Site Recovery egyesített beállításának letöltése**elemre. Ezzel letölti a telepítőfájl legújabb verzióját.

   > [!WARNING]
   > A folyamatkiszolgáló telepítési verziójának meg kell egyeznie a konfigurációs kiszolgáló futtatott verziójával, vagy korábbinak kell lennie annál. A verziókompatibilitás biztosításának egyszerű módja, ha ugyanazt a telepítőt használja, amelyet legutóbb a konfigurációs kiszolgáló telepítéséhez vagy frissítéséhez használt.

## <a name="install-from-the-ui"></a>Telepítés a felhasználói felületről

Telepítse az alábbiak szerint. A kiszolgáló beállítása után a forrásgépeket áttelepíti azok használatához.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Telepítés a parancssorból

Telepítés a következő parancs futtatásával:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Ahol a parancssori paraméterek a következők:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Példa:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Proxybeállítási fájl létrehozása

Ha proxyt kell beállítania, a ProxySettingsFilePath paraméter egy fájlt vesz fel bemenetként. A fájlt az alábbiak szerint hozhatja létre, és átadhatja bemeneti ProxySettingsFilePath paraméterként.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>További lépések
További információ a [folyamatkiszolgáló beállításainak kezeléséről](vmware-azure-manage-process-server.md)
