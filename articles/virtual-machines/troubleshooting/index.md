---
layout: LandingPage
description: Megtudhatja, hogyan háríthatja el a virtuális gépek üzembe helyezésével kapcsolatos hibákat.
title: Az Azure Virtual Machines hibaelhárítása – dokumentáció | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: ed1873fa8e74b2af5734411dc80aeb9089b61a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080455"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Azure-beli virtuális gépek hibaelhárítása

## <a name="tools-for-troubleshooting"></a>Hibaelhárítási eszközök

- [Soros konzol](serial-console-windows.md)
- [Rendszerindítási diagnosztika](boot-diagnostics.md)
- [Windows rendszerű virtuális gép: Csatlakoztassa az operációsrendszer-lemezt egy másik virtuális géphez a hibaelhárításhoz.](troubleshoot-recovery-disks-portal-windows.md)
- [Linux rendszerű virtuális gép: Csatlakoztassa az operációsrendszer-lemezt egy másik virtuális géphez a hibaelhárításhoz.](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Nem lehet csatlakozni a virtuális géphez

### <a name="windows"></a>Windows

**Gyakran használt megoldások**

- [Az RDP alaphelyzetbe állítása](reset-rdp.md)
- [Az RDP hibaelhárítása](troubleshoot-rdp-connection.md)
- [Az RDP részletes hibaelhárítása](detailed-troubleshoot-rdp.md)

**RDP-hibák**

- [Nincs licenckiszolgáló](troubleshoot-rdp-no-license-server.md)
- [Belső ](Troubleshoot-rdp-internal-error.md)
- [hitelesítési hibák](troubleshoot-authentication-error-rdp-vm.md)
- [Konkrét hibák elhárítása](troubleshoot-specific-rdp-errors.md)

**Virtuális gép rendszerindítási hibái**

* [BitLocker rendszerindítási hibái](troubleshoot-bitlocker-boot-error.md) 
* [A Windows „A fájlrendszer ellenőrzése” üzenetet jeleníti meg a rendszerindítás során](troubleshoot-check-disk-boot-error.md)
* [Kék képernyős hibák](troubleshoot-common-blue-screen-error.md)
* [A virtuális gép indítása elakadt „A Windows előkészítése.](troubleshoot-vm-boot-configure-update.md)
* [EGY KULCSFONTOSSÁGÚ SZOLGÁLTATÁS MEGHIÚSULT” hibával a kék képernyőn](troubleshoot-critical-service-failed-boot-error.md)
* [Újraindítási hurokkal kapcsolatos probléma](troubleshoot-reboot-loop.md)
* [A virtuális gép indítása elakadt a Windows frissítési szakaszánál](troubleshoot-stuck-updating-boot-error.md)
* [A virtuális gép csökkentett üzemmódban indul el](troubleshoot-rdp-safe-mode.md)

**Egyéb**
- [Kapcsolat nélküli üzemmódban működő Windows rendszerű virtuális gép jelszavának alaphelyzetbe állítása](reset-local-password-without-agent.md)
- [Hálózati adapter visszaállítása hibás konfigurációt követően](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Az SSH hibaelhárítása](troubleshoot-ssh-connection.md)
- [Az SSH részletes hibaelhárítása](detailed-troubleshoot-ssh-connection.md)
- [Gyakori hibaüzenetek](error-messages.md)
- [Kapcsolat nélküli üzemmódban működő Linux rendszerű virtuális gép jelszavának alaphelyzetbe állítása](reset-password.md)

## <a name="vm-deployment-issues"></a>Virtuális gép üzembehelyezési problémái

- [Foglalási hibák](allocation-failure.md)
- Virtuális gép ismételt üzembe helyezése
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Üzemelő példányok hibaelhárítása
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Az eszköznevek módosultak](troubleshoot-device-names-problems.md)
- [A Windows rendszerű virtuálisgép-ügynök offline telepítése](install-vm-agent-offline.md)
- [Virtuális gép újraindítása vagy átméretezése](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Virtuális gép teljesítményével kapcsolatos problémák
- [Virtuális gépek teljesítményproblémái](performance-diagnostics.md)
- Windows
    - [A PerfInsights használata](how-to-use-perfinsights.md)
    - [Teljesítménydiagnosztikai bővítmény](performance-diagnostics-vm-extension.md)
- Linux
    - [A PerfInsights használata](how-to-use-perfinsights-linux.md)