---
title: Az Azure VM vendég operációs rendszertűzfala helytelenül van konfigurálva | Microsoft dokumentumok
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153898"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Az Azure virtuális gép vendég operációsrendszer-tűzfala helytelenül van konfigurálva

Ez a cikk bemutatja, hogyan javíthatja a helytelenül konfigurált vendégoperációs rendszer tűzfala az Azure virtuális gép.

## <a name="symptoms"></a>Probléma

1.  A virtuális gép (VM) Üdvözlőképernyő azt mutatja, hogy a virtuális gép teljesen be van töltve.

2.  Attól függően, hogy a vendég operációs rendszer van konfigurálva, előfordulhat, hogy néhány hálózati forgalom eléri a virtuális gép.

## <a name="cause"></a>Ok

A vendégrendszer tűzfalának helytelen konfigurálása blokkolhatja a virtuális gép hálózati forgalmának egy részét vagy minden formáját.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma elhárításához használja a soros konzolt, vagy [javítsa ki a virtuális gép offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) állapotba helyezését a virtuális gép rendszerlemezének helyreállítási virtuális géphez csatlakoztatásával.

## <a name="online-mitigations"></a>Online megoldások

Csatlakozzon a [soros konzolhoz, majd nyisson meg egy PowerShell-példányt.](serial-console-windows.md#use-cmd-or-powershell-in-serial-console) Ha a soros konzol nincs engedélyezve a virtuális gép, folytassa a "VM javítása offline" című szakasza a következő Azure-cikk:

 [Belső hiba jelentkezik, ha távoli asztalon keresztül próbál csatlakozni az Azure-beli virtuális géphez](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

A következő szabályok szerkeszthetők a virtuális gép (RDP-n keresztül) elérésének engedélyezéséhez vagy a könnyebb hibaelhárítási élmény biztosításához:

*   Távoli asztal (TCP-In): Ez a szokásos szabály, amely elsődleges hozzáférést biztosít a virtuális gép hez az RDP engedélyezésével az Azure-ban.

*   Windows remote Management (HTTP-In): Ez a szabály lehetővé teszi, hogy a virtuális géphez a PowerShell használatával.

*   Fájl- és nyomtatómegosztás (SMB-in): Ez a szabály hibaelhárítási lehetőségként engedélyezi a hálózati megosztáselérését.

*   Fájl- és nyomtatómegosztás (Visszhangkérés – ICMPv4- in): Ez a szabály lehetővé teszi a virtuális gép pingelését.

A Soros konzol access példányában lekérdezheti a tűzfalszabály aktuális állapotát.

*   Lekérdezés a Megjelenítendő név paraméterként történő használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Lekérdezés az alkalmazás által használt helyi port használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Lekérdezés az alkalmazás által használt helyi IP-cím használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Ha azt látja, hogy a szabály le van tiltva, a következő paranccsal engedélyezheti:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Hibaelhárításhoz kikapcsolhatja a tűzfalprofilokat:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Ha ezt a tűzfal megfelelő beállításához állítja be, a hibaelhárítás befejezése után engedélyezze újra a tűzfalat.

    > [!Note]
    > A módosítás alkalmazásához nem kell újraindítania a virtuális számítógépet.

*   Próbálkozzon újra a virtuális géphez az RDP-n keresztül.

### <a name="offline-mitigations"></a>Kapcsolat nélküli megoldások

1.  A tűzfalszabályok engedélyezéséhez vagy letiltásához olvassa el [a tűzfalszabály engedélyezése vagy letiltása egy Azure virtuális gép vendégoperációs rendszerén.](enable-disable-firewall-rule-guest-os.md)

2.  Ellenőrizze, hogy a [vendég operációsrendszer tűzfala blokkolja-e](guest-os-firewall-blocking-inbound-traffic.md)a bejövő forgalmat .

3.  Ha továbbra is kétségei vannak azzal kapcsolatban, hogy a tűzfal blokkolja-e a hozzáférést, olvassa el [a vendég operációsrendszer-tűzfal letiltása az Azure virtuális gépben](disable-guest-os-firewall-windows.md)című részt, majd engedélyezze újra a vendégrendszer tűzfalát a megfelelő szabályok használatával.

