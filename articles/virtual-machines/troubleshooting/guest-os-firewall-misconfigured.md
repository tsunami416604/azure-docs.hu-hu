---
title: Az Azure virtuális gép vendég operációs rendszer tűzfala helytelenül van konfigurálva |} A Microsoft Docs
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
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153898"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Az Azure virtuális gép vendég operációs rendszer tűzfala helytelenül van konfigurálva.

Ez a cikk vezeti be, hogyan háríthatja el a helytelenül konfigurált a vendég operációs rendszer tűzfala Azure virtuális gépen.

## <a name="symptoms"></a>Probléma

1.  A virtuális gép (VM) üdvözlő képernyő látható, hogy a virtuális gép teljes betöltéséig.

2.  A vendég operációs rendszer konfigurációjától függően lehetnek bizonyos vagy nem éri el a virtuális gép hálózati forgalmat.

## <a name="cause"></a>Ok

A Virtual Network szolgáltatás hibás, a Vendég rendszer tűzfal blokkolhatja néhány vagy az összes típusú hálózati forgalmat a virtuális géphez.

## <a name="solution"></a>Megoldás

Mielőtt végrehajtaná ezeket a lépéseket, pillanatkép készítése a rendszerlemezt az érintett virtuális gép biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma elhárításához, használja a soros konzol vagy [javítsa ki a virtuális Gépet offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) egy helyreállítási virtuális Géphez a rendszer a virtuális gép lemezének csatolásával.

## <a name="online-mitigations"></a>Online megoldások

Csatlakozás a [soros konzolon, és nyissa meg egy PowerShell-példány](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Ha a soros konzol nincs engedélyezve a virtuális gépen, nyissa meg a "Javítás a virtuális gép offline állapotba helyezi" szakaszában az Azure-cikket:

 [Belső hiba akkor fordul elő, amikor próbál csatlakozni egy Azure virtuális géphez a távoli asztalon keresztül](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

A következő szabályokat szerkesztheti vagy a hozzáférés a virtuális géphez (RDP) keresztül történő engedélyezéséhez vagy a könnyebb hibaelhárítás biztosít:

*   Távoli asztal (TCP, bejövő): Ez a szabványos szabály, amely elsődleges hozzáférést biztosít a virtuális géphez az RDP az Azure-ban való engedélyezésével.

*   Rendszerfelügyeleti webszolgáltatások (HTTP, bejövő): Ez a szabály lehetővé teszi, hogy a PowerShell használatával kapcsolódjon a virtuális géphez. az Azure-ban ez a fajta hozzáférés lehetővé teszi a távoli parancsfájlok parancsfájlkezelési aspektusának használatát, valamint a hibaelhárítást.

*   Fájl-és nyomtatómegosztás (SMB, bejövő): Ez a szabály lehetővé teszi a hálózati megosztás elérését hibaelhárítási lehetőségként.

*   Fájl-és nyomtatómegosztás (ECHO-kérelem – ICMPv4): Ez a szabály lehetővé teszi a virtuális gép pingelését.

A soros konzolhoz példányban lekérdezheti a tűzfalszabályt az aktuális állapotát.

*   Lekérdezési paraméterként a megjelenítési név használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Lekérdezés az alkalmazás által használt helyi Port használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Lekérdezés a az alkalmazás által használt helyi IP-cím használatával:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Ha látja, hogy a szabály le van tiltva, a következő parancs futtatásával engedélyezheti:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Hibaelhárítási, kikapcsolhatja a tűzfalprofilokról:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Ha így tesz, hogy a tűzfal helyesen, engedélyezze újra a tűzfalat a hibaelhárítási befejezése után.

    > [!Note]
    > Nem kell újraindítani a virtuális Gépet a alkalmazni ezt a módosítást.

*   A virtuális gép RDP-Kapcsolaton keresztül csatlakozni, próbálkozzon újra.

### <a name="offline-mitigations"></a>A kapcsolat nélküli megoldások

1.  A tűzfalszabályok engedélyezéséhez vagy letiltásához tekintse [meg a tűzfalszabály engedélyezése vagy letiltása Azure-beli virtuális gép vendég operációs rendszeren](enable-disable-firewall-rule-guest-os.md)című témakört.

2.  Ellenőrizze, hogy vannak-e a a [vendég operációs rendszer tűzfala blokkolja a bejövő forgalom forgatókönyv](guest-os-firewall-blocking-inbound-traffic.md).

3.  Ha továbbra is a bizonytalan, hogy a tűzfal blokkolja a hozzáférést, tekintse meg [tiltsa le a vendég operációs rendszer tűzfala az Azure virtuális Gépen](disable-guest-os-firewall-windows.md), majd újból engedélyezze a a Vendég rendszer tűzfalon a megfelelő szabályok segítségével.

