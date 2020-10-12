---
title: Az Azure-beli virtuális gép vendég operációs rendszerének tűzfala hibásan van konfigurálva | Microsoft Docs
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja a távoli Azure-beli virtuális gépeken a soros konzol vagy a kapcsolat nélküli módszer használatával a helytelenül konfigurált vendég operációs rendszer tűzfalát.
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
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422536"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Helytelenül van konfigurálva az Azure-beli virtuális gép vendég operációs rendszerének tűzfala

Ez a cikk bemutatja, hogyan lehet elhárítani a nem konfigurált vendég operációs rendszer tűzfalát az Azure-beli virtuális gépen.

## <a name="symptoms"></a>Hibajelenségek

1.  A virtuális gép (VM) üdvözlő képernyőjén látható, hogy a virtuális gép teljesen be van töltve.

2.  A vendég operációs rendszer konfigurálásának módjától függően előfordulhat, hogy egy vagy több hálózati forgalom nem éri el a virtuális gépet.

## <a name="cause"></a>Ok

A vendégrendszer tűzfalának helytelen konfigurációja blokkolja a virtuális gép valamilyen hálózati forgalmát.

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma elhárításához használja a soros konzolt, vagy [javítsa ki a virtuális gépet](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) úgy, hogy a virtuális gép rendszerlemezét egy helyreállítási virtuális géphez csatolja.

## <a name="online-mitigations"></a>Online enyhítés

Kapcsolódjon a [soros konzolhoz, és nyisson meg egy PowerShell-példányt](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a következő Azure-cikk "az offline virtuális gép kijavítása" című szakaszában:

 [Belső hiba jelentkezik, ha távoli asztalon keresztül próbál csatlakozni az Azure-beli virtuális géphez](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

A következő szabályok módosíthatók úgy, hogy engedélyezzék a virtuális gép hozzáférését (RDP-n keresztül), vagy egyszerűbb hibaelhárítási élményt nyújtson:

*   Távoli asztal (TCP, bejövő): Ez a szabványos szabály, amely elsődleges hozzáférést biztosít a virtuális géphez az RDP az Azure-ban való engedélyezésével.

*   Rendszerfelügyeleti webszolgáltatások (HTTP, bejövő): Ez a szabály lehetővé teszi, hogy a PowerShell használatával kapcsolódjon a virtuális géphez. az Azure-ban ez a fajta hozzáférés lehetővé teszi a távoli parancsfájlok parancsfájlkezelési aspektusának használatát, valamint a hibaelhárítást.

*   Fájl-és nyomtatómegosztás (SMB, bejövő): Ez a szabály engedélyezi a hálózati megosztás elérését hibaelhárítási lehetőségként.

*   Fájl-és nyomtatómegosztás (ECHO Request-ICMPv4-in): Ez a szabály lehetővé teszi a virtuális gép pingelését.

A soros konzol hozzáférési példányában a tűzfalszabály aktuális állapotát kérdezheti le.

*   Lekérdezés a megjelenítendő név paraméter használatával:

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

*   Ha úgy látja, hogy a szabály le van tiltva, akkor a következő parancs futtatásával engedélyezheti:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Hibaelhárításhoz a tűzfal profiljait kikapcsolhatja:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Ha ezt a tűzfalat helyesen állítja be, a hibaelhárítás befejezése után engedélyezze újra a tűzfalat.

    > [!Note]
    > A módosítás alkalmazásához nem kell újraindítani a virtuális gépet.

*   Próbálkozzon újra a virtuális gép RDP-kapcsolaton keresztüli kapcsolódásával.

### <a name="offline-mitigations"></a>Offline megoldás

1.  A tűzfalszabályok engedélyezéséhez vagy letiltásához tekintse [meg a tűzfalszabály engedélyezése vagy letiltása Azure-beli virtuális gép vendég operációs rendszeren](enable-disable-firewall-rule-guest-os.md)című témakört.

2.  Győződjön meg arról, hogy a [vendég operációs rendszer tűzfala blokkolja a bejövő forgalom forgatókönyvét](guest-os-firewall-blocking-inbound-traffic.md).

3.  Ha még mindig kétséges, hogy a tűzfal blokkolja-e a hozzáférést, tekintse meg [a vendég operációs rendszer tűzfalának letiltását az Azure virtuális gépen](disable-guest-os-firewall-windows.md), majd a megfelelő szabályok használatával engedélyezze újra a vendégrendszer tűzfalát.

