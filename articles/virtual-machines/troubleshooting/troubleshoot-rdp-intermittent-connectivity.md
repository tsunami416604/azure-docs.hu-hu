---
title: Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen | Microsoft Docs
description: Ismerje meg, hogy miként lehet elhárítani az Azure-beli virtuális gépek Távoli asztal gyakori leválasztásait.
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
ms.openlocfilehash: 636973110e11770e33c635e312c86b25110705da
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981348"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen

Ez a cikk azt ismerteti, hogyan lehet az Azure-beli virtuális gépek (VM) gyakori leválasztásait RDP protokoll RDP-n keresztül elhárítani.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/management/deployment-models.md). Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti. Javasoljuk, hogy ezt a modellt a klasszikus üzemi modell használata helyett új központi telepítések esetén használja.

## <a name="symptom"></a>Hibajelenség

A munkamenetek során átmeneti RDP-kapcsolati problémákba ütköznek. Kezdetben csatlakozhat a virtuális géphez, de a kapcsolat elesik.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha az RDP-figyelő helytelenül van konfigurálva. Ez a probléma általában egy egyéni rendszerképet használó virtuális gépen fordul elő.

## <a name="solution"></a>Megoldás

Az alábbi lépések [elvégzése előtt készítsen pillanatképet](../windows/snapshot-copy-managed-disk.md) az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. 

A probléma elhárításához a virtuális gép operációsrendszer-lemezét egy helyreállítási virtuális géphez csatolva csatlakoztassa a soros vezérlőt vagy [javítsa ki a virtuális gépet](#repair-the-vm-offline) .

### <a name="serial-control"></a>Soros vezérlő

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md). Ezután futtassa az alábbi parancsokat az RDP-konfigurációk alaphelyzetbe állításához. Ha a soros konzol nincs engedélyezve a virtuális gépen, folytassa a következő lépéssel.
2. Csökkentse az RDP biztonsági réteget 0-ra. Ezzel a beállítással a kiszolgáló és az ügyfél közötti kommunikáció a natív RDP-titkosítást használja.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Csökkentse a titkosítási szintet a minimális beállításra, hogy az örökölt RDP-ügyfelek csatlakozhassanak.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Állítsa be az RDP-t az ügyfélszámítógép felhasználói konfigurációjának betöltéséhez.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Az RDP Keep-Alive vezérlő engedélyezése:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Az RDP rekapcsolási vezérlő beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Az RDP-munkamenet idővezérlőjének beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Az RDP-leválasztási idő vezérlőelem beállítása: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Adja meg az RDP-kapcsolat időpontjának vezérlőjét:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Az RDP-munkamenet üresjárati időkorlátjának beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. "Az egyidejű kapcsolatok maximális számának korlátozása" vezérlő beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Indítsa újra a virtuális gépet, majd próbálja meg újra az RDP használatával való kapcsolódáshoz.

### <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Miután az operációsrendszer-lemezt csatlakoztatta a helyreállítási virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3. A csatlakoztatott operációsrendszer-lemezen navigáljon a **\Windows\System32\Config** mappára. Másolja a mappában található összes fájlt biztonsági másolatként, ha visszaállításra van szükség.
4. Indítsa el a Beállításszerkesztőt (Regedit. exe).
5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a **fájl** > **betöltési struktúra**:
6. Keresse meg a **\windows\system32\config\SYSTEM** mappát a csatlakoztatott operációsrendszer-lemezen. A struktúra neveként írja be a következőt: **BROKENSYSTEM**. Az új beállításjegyzék-struktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg. Ezután töltse be a szoftver-struktúra **\windows\system32\config\SOFTWARE** a **HKEY_LOCAL_MACHINE** kulcs alatt. A kaptár szoftver neveként írja be a következőt: **BROKENSOFTWARE**. 
7. Nyisson meg egy rendszergazda jogú parancssort (**Futtatás rendszergazdaként**), és futtassa a további lépések parancsait az RDP-konfigurációk alaphelyzetbe állításához. 
8. Csökkentse az RDP biztonsági réteget 0-ra, hogy a kiszolgáló és az ügyfél közötti kommunikáció a natív RDP-titkosítást használja:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Csökkentse a titkosítási szintet a minimális beállításra, hogy az örökölt RDP-ügyfelek csatlakozhassanak:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Állítsa be az RDP-t az ügyfélszámítógép felhasználói konfigurációjának betöltéséhez.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Az RDP Keep-Alive vezérlő engedélyezése:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Az RDP rekapcsolási vezérlő beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Az RDP-munkamenet idővezérlőjének beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Az RDP-leválasztási idő vezérlőelem beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Adja meg az RDP-kapcsolat időpontjának vezérlőjét:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Az RDP-munkamenet üresjárati időkorlátjának beállítása: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp"/v "fInheritMaxIdleTime"/t REG_DWORD/d 1/f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. "Az egyidejű kapcsolatok maximális számának korlátozása" vezérlő beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Indítsa újra a virtuális gépet, majd próbálja meg újra az RDP használatával való kapcsolódáshoz.

## <a name="need-help"></a>Segítség 
Vegye fel a kapcsolatot az ügyfélszolgálattal. Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.





