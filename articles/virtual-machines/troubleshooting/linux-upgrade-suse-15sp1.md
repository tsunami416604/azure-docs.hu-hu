---
title: Azure-beli virtuális gép frissítése SUSE Linux Enterprise Serverról a SUSE 15 SP1-re | Microsoft Docs
description: Ez a cikk általános lépéseket tartalmaz arról, hogyan használható a SUSE Distribution Migration System a SUSE Linux Enterprise Server és az Azure virtuális gépek SUSE 15 SP1 verziójára történő frissítéséhez.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359511"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Azure-beli virtuális gép frissítése SLES 12-SLES 15 SP1-re

Ez a cikk a SUSE Linux Enterprise Server (SLES) 12 és SLES 15 SP1 Azure-beli virtuális gépek (VM) frissítésére vonatkozó általános lépéseket ismerteti. További információ: [a SUSE Distribution Migration System használata](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) és a [SUSE Linux Enterprise Server 15 SP1 frissítési útmutatója](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Támogatott frissítési útvonalak
A jelenlegi SLES-verziónak 12 SP4 vagy 12 SP5 SLES kell lennie, mielőtt továbblépne a SLES 15 SP1-re.

![A támogatott frissítési útvonallal kapcsolatos képernyőkép](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Előfeltételek

- Tervezze meg az áttelepítési tevékenységet a jóváhagyott állásidő ablak szerint. Ennek az az oka, hogy a virtuális gép újraindul az áttelepítés során.
- Az áttelepítési tevékenység előtt készítsen teljes biztonsági másolatot a virtuális gépről.
- Ha a biztonsági mentés nincs konfigurálva, készítsen pillanatképet az operációsrendszer-lemezről.
- [Ellenőrizze, hogy a virtuális gép v1 vagy Generation v2 verziójú-e](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Frissítés SUSE 12 SP4 vagy SP5 rendszerről SUSE 15 SP1 verzióra

1. Telepítse a virtuális gép legújabb csomagját:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. A telepítés befejezése után indítsa újra a virtuális gépet.

3. Ellenőrizze a kernel és az operációs rendszer verzióját. Győződjön meg arról, hogy a verzió a SUSE 12 SP4 vagy a SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Telepítse a **SUSE-Migration-sle15-Activation**. Ha a csomag **SUSE-Migration-sle15-Activate** telepítése megtörténik, a rendszer egy másik csomagot is telepít a **SLES15** . 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. A telepítés befejezése után futtassa a parancsot a `reboot` virtuális gép újraindításához.

6. Nyissa meg a [Azure Portal](https://portal.azure.com), válassza ki a virtuális gépet, majd válassza a **Serial Console**lehetőséget. Látni fogja, hogy a rendszer leáll "újraindítás: újraindítási rendszer". Ez a folyamat körülbelül 15-45 percet vesz igénybe. A 2. generációs virtuális gépek esetében előfordulhat, hogy az "újraindítás: rendszer újraindítása" képernyőn beragadt. Ebben az esetben várjon 45 percet. Ha még mindig nem halad tovább, lépjen a Azure Portal található virtuális gép **Áttekintés** lapjára, állítsa le a virtuális gépet, majd indítsa újra.

     ![A soros konzolon lévő üzenetekkel kapcsolatos képernyőkép.](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. A rendszer új rendszermaggal való újraindítása után a következő üzenet jelenik meg.

     ![A soros konzolon lévő üzenetekkel kapcsolatos képernyőkép a rendszer újraindítása után az új kernelen.](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Ellenőrizze a kernel és az operációs rendszer verziószámát, és ellenőrizze, hogy a rendszer frissítése sikeresen megtörtént-e.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>A virtuális gép létrehozási verziójának keresése

A következő módszerek egyikével ellenőrizhető a generáció verziója:

- A SLES-terminálon futtassa a parancsot `dmidecode | grep -i hyper` . Ha ez egy generációs v1-es virtuális gép, a rendszer nem ad vissza kimenetet. A Generations v2 virtuális gépek esetében a következő kimenet jelenik meg:

     ![A 2. generációs virtuális gépek kimenetét bemutató képernyőkép](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- A [Azure Portal](https://portal.azure.com)nyissa meg a virtuális gép **tulajdonságait**  , majd jelölje be a **virtuális gép létrehozási** mezőjét.
