---
title: A távoli asztal gyakran bontja a kapcsolatot az Azure Virtuálisgépben| Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el a távoli asztal gyakori lekapcsolódásainak elhárítását az Azure virtuális gépben.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918223"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>A távoli asztal kapcsolata gyakran megszakad az Azure-beli virtuális gépen

Ez a cikk bemutatja, hogyan háríthatja el az Azure virtuális géppel (VM) a Távoli asztali protokoll RDP-n keresztül történő gyakori lekapcsolódások hibáit.


## <a name="symptom"></a>Hibajelenség

A munkamenetek során időszakos RDP-kapcsolati problémákkal szembesül. Kezdetben csatlakozhat a virtuális géphez, de a kapcsolat megszakad.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha az RDP-figyelő helytelenül van konfigurálva. Ez a probléma általában egy egyéni lemezképet használó virtuális számítógépen fordul elő.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép [operációsrendszer-lemezéről](../windows/snapshot-copy-managed-disk.md) biztonsági másolatként. 

A probléma elhárításához használja [a](#repair-the-vm-offline) Soros-vezérlőt, vagy javítsa ki a virtuális gép offline állapotban lévő operációs rendszerének csatlakoztatásával a virtuális gép operációs rendszerlemezét egy helyreállítási virtuális géphez.

### <a name="serial-control"></a>Soros vezérlés

1. Csatlakozzon a [Soros konzolhoz, és nyissa meg a CMD-példányt.](./serial-console-windows.md) Ezután futtassa a következő parancsokat az RDP-konfigurációk alaphelyzetbe állításához. Ha a soros konzol nincs engedélyezve a virtuális gép, folytassa a következő lépéssel.
2. Csökkentse az RDP biztonsági réteget 0-ra. Ebben a beállításban a kiszolgáló és az ügyfél közötti kommunikáció a natív RDP-titkosítást használja.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Csökkentse a titkosítási szintet a minimális beállításra, hogy az örökölt RDP-ügyfelek csatlakozni.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Állítsa be az RDP-t az ügyfélszámítógép felhasználói konfigurációjának betöltésére.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Az RDP Keep-Alive vezérlő engedélyezése:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Az RDP Újracsatlakozás vezérlő beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Az RDP-munkamenet idővezérlőjének beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Állítsa be az RDP lekapcsolási idő vezérlőt: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Az RDP-kapcsolat idővezérlőjének beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Az RDP-munkamenet tétlenidő-vezérlőjének beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Állítsa be a "Maximális egyidejű kapcsolatok korlátozása" vezérlőt:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Indítsa újra a virtuális gép, és próbálja meg újra csatlakozni az RDP használatával.

### <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Miután az operációs rendszer lemeze csatlakozik a helyreállítási virtuális géphez, győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3. A csatolt operációsrendszer-lemezen keresse meg a **\windows\system32\config** mappát. Másolja a mappában lévő összes fájlt biztonsági másolatként, ha visszaállításra van szükség.
4. Indítsa el a Rendszerleíróadatbázis-szerkesztőt (regedit.exe).
5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a > **Fájlbetöltő hive parancsot:** **File**
6. Tallózással keresse meg a csatolt operációsrendszer-lemez **\windows\system32\config\SYSTEM** mappáját. A struktúra nevéhez írja be a **BROKENSYSTEM**. Az új beállításstruktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg. Ezután töltse be a szoftverstruktúrát **\windows\system32\config\SOFTWARE** a **HKEY_LOCAL_MACHINE** kulcs alá. A kaptárszoftver nevéhez írja be a **BROKENSOFTWARE**. 
7. Nyisson meg egy rendszergazdai jogú parancssori ablakot (**Rendszergazdaként futtatása**), és futtasson parancsokat az RDP-konfigurációk alaphelyzetbe állításához szükséges további lépésekben. 
8. Csökkentse az RDP biztonsági réteget 0-ra, hogy a kiszolgáló és az ügyfél közötti kommunikáció a natív RDP-titkosítást használja:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Csökkentse a titkosítási szintet a minimális beállításra, hogy az örökölt RDP-ügyfelek csatlakozhassanak:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Állítsa be az RDP-t az ügyfélgép felhasználói konfigurációjának betöltésére.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Az RDP Keep-Alive vezérlő engedélyezése:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Az RDP Újracsatlakozás vezérlő beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Az RDP-munkamenet idővezérlőjének beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Állítsa be az RDP lekapcsolási idő vezérlőt:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Az RDP-kapcsolat idővezérlőjének beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. RdP-munkamenet tétlen időszabályozásának beállítása: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Állítsa be a "Maximális egyidejű kapcsolatok korlátozása" vezérlőt:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Indítsa újra a virtuális gép, és próbálja meg újra csatlakozni az RDP használatával.

## <a name="need-help"></a>Segítségre van szüksége? 
Vegye fel a kapcsolatot az ügyfélszolgálattal. Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.





