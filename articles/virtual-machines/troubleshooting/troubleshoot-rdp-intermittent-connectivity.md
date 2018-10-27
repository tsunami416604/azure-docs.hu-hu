---
title: A távoli asztal gyakran leválasztja az Azure virtuális Gépen |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, amelyben a távoli asztal bontja a kapcsolatot gyakran az Azure virtuális Gépen.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: b65cd4d7e6f68f9444ca0264892bcca31cfbe674
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142436"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>A távoli asztal gyakran leválasztja az Azure virtuális Gépen

Ez a cikk bemutatja, hogyan háríthatók el a problémát, amelyben a távoli asztal bontja a kapcsolatot gyakran az Azure virtuális Gépen.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, javasoljuk, hogy az új központi telepítéseknél helyett a klasszikus üzemi modell használatával Resource Manager üzemi modell használatával.

## <a name="symptom"></a>Jelenség 

RDP-kapcsolat időszakos során a munkamenetek között. Csatlakozhat a virtuális Gépet, de a kapcsolat megszüntetése.

## <a name="cause"></a>Ok

A probléma okozhatja az RDP-figyelő helytelenül van konfigurálva. Ez a probléma általában egyéni virtuális gépen történik.

## <a name="solution"></a>Megoldás  

A lépések végrehajtása előtt [az operációsrendszer-lemez pillanatfelvételének](../windows/snapshot-copy-managed-disk.md) az érintett virtuális gép biztonsági mentéséhez. 

A probléma elhárításához, soros vezérlőelem használata vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) által a virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolása.

### <a name="reset-rdp-configurations"></a>RDP-konfiguráció visszaállítása

1. Csatlakozás a [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md), majd futtassa a következő parancsokat az alaphelyzetbe állítása az RDP-konfigurációk szakaszban. Ha a soros konzol nincs engedélyezve a virtuális Gépen, helyezze át a következő lépéssel. 
2. Az RDP-biztonsági réteg 0, alacsonyabb, így a kiszolgáló és az ügyfél közötti kommunikációhoz fogja használni a natív RDP-titkosítást.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Csökkentheti a minimum lehetővé teszi örökölt RDP-ügyfelek csatlakozni a titkosítási szint.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Állítsa be az RDP-vel töltse be a felhasználói konfigurációt az ügyfélszámítógépen.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. RDP Keep-Alive vezérlésének engedélyezése:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. RDP-újracsatlakozás vezérlés beállítása

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. RDP-munkamenet ideje-vezérlés beállítása 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. A leválasztás ideje RDP-vezérlés beállítása 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Állítsa be a vezérlő RDP-kapcsolat ideje:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Állítsa be az RDP-munkamenet tétlenségi idő vezérlő:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
11. Készlet korlátozása az egyidejű kapcsolatok maximális száma:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

12. Indítsa újra a virtuális Gépet, és tekintse meg, ha, is csatlakozhat, RDP használatával.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Miután az operációsrendszer-lemez csatolva van a helyreállítási virtuális Gépet, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3. Keresse meg a **\windows\system32\config** mappát. Másolja az összes fájl ebben a mappában biztonsági mentéséhez, abban az esetben egy visszaállítási megadása kötelező.
4. Indítsa el a Beállításszerkesztőt (regedit.exe).
5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. Válassza a menü **fájl** > **a struktúra betöltése**:
6. Keresse meg a **\windows\system32\config\SYSTEM** a csatolt operációsrendszer-lemez egy mappájába. A hive-neveként, írja be a **BROKENSYSTEM**. Az új beállításjegyzék-struktúrát alatt jelenik meg a **HKEY_LOCAL_MACHINE** kulcsot.
7. Keresse meg a **\windows\system32\config\SOFTWARE** a csatolt operációsrendszer-lemez egy mappájába. Adja meg a hive-szoftver neve, **BROKENSOFTWARE**.
8. Nyisson meg egy emelt szintű parancssor parancssori ablakban (Futtatás rendszergazdaként), majd futtassa a parancsokat a rest lépések az RDP-konfiguráció alaphelyzetbe állítása. 
9. Csökkentheti az RDP-biztonsági réteg 0, ahol a kiszolgáló és az ügyfél közötti kommunikációhoz fogja használni a natív RDP-titkosítást:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
10. A titkosítás szintjét a minimum lehetővé teszi örökölt csatlakozni RDP-ügyfelek alacsonyabb:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
11. Állítsa be az RDP-vel töltse be a felhasználói konfigurációt az ügyfélszámítógépen.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
12. RDP Keep-Alive vezérlésének engedélyezése:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
13. Állítsa be a vezérlő RDP újracsatlakozás:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

14. Állítsa be a vezérlő RDP-munkamenet ideje:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
15. Állítsa be a vezérlő RDP a leválasztás ideje:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
16. Állítsa be a vezérlő RDP-kapcsolat ideje:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
17. RDP-munkamenet tétlenségi idő vezérlő: "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v "fInheritMaxIdleTime" /t REG_DWORD /d 1 /f REG ADD 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
18. Készlet korlátozása az egyidejű kapcsolatok maximális száma:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
19. Indítsa újra a virtuális Gépet, és tekintse meg, ha, is csatlakozhat, RDP használatával.

## <a name="need-help"></a>Segítség 
Forduljon az ügyfélszolgálathoz. Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.





