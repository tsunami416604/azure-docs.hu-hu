---
title: A távoli asztal gyakran leválasztja az Azure virtuális Gépen |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a gyakori szétkapcsolások távoli asztal Azure-beli virtuális gépen.
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
ms.openlocfilehash: 7fecf8c5fdafb64f7922054dd2bb9755b0dec031
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881341"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>A távoli asztal gyakran leválasztja az Azure virtuális Gépen

Ez a cikk azt ismerteti, hogyan háríthatók el a távoli asztal protokoll RDP-Kapcsolaton keresztül egy Azure virtuális gép (VM), a gyakori szétkapcsolások).

> [!NOTE] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

## <a name="symptom"></a>Jelenség

RDP-kapcsolatok időszakos problémák során a munkamenetek között. Kezdetben csatlakozhat a virtuális Gépet, de a kapcsolat megszüntetése.

## <a name="cause"></a>Ok

A probléma akkor fordulhat elő, ha az RDP-figyelő helytelenül van konfigurálva. A probléma általában egy virtuális gépen, amely egyedi rendszerképet használ.

## <a name="solution"></a>Megoldás

Ezek a lépések végrehajtása előtt [az operációsrendszer-lemez pillanatfelvételének](../windows/snapshot-copy-managed-disk.md) az érintett virtuális gép biztonsági mentéséhez. 

A probléma elhárításához, soros vezérlőelem használata vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) által a virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolása.

### <a name="serial-control"></a>Soros vezérlő

1. Csatlakozás [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md). Ezután futtassa a következő parancsokat az RDP-konfiguráció alaphelyzetbe állítása. Ha a soros konzol nincs engedélyezve a virtuális Gépen, nyissa meg a következő lépéssel.
2. Az RDP-biztonsági réteg 0-ra csökken. Ezzel a beállítással a kiszolgáló és az ügyfél közötti kommunikációhoz a natív RDP-titkosítást használjon.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Csökkentheti a minimális beállítás lehetővé teszi örökölt RDP-ügyfelek csatlakozni a titkosítási szint.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Állítsa be az RDP-vel töltse be a felhasználói konfigurációt az ügyfélszámítógép.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Lehetővé teszi az RDP Keep-Alive vezérlő:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Állítsa be a RDP újracsatlakozás vezérlő:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Az RDP-munkamenet ideje ellenőrzés beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Az RDP a leválasztás ideje ellenőrzés beállítása: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Az RDP-kapcsolat ideje ellenőrzés beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Az RDP-munkamenet eltöltött időhányad ellenőrzés beállítása:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Állítsa be a "Korlátozza az egyidejű kapcsolatok maximális száma" vezérlő:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Indítsa újra a virtuális Gépet, és próbálja meg újból RDP használatával csatlakozzon hozzá.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Miután az operációsrendszer-lemez csatolva van a helyreállítási virtuális Gépet, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3. A csatolt operációsrendszer-lemez, navigáljon a **\windows\system32\config** mappát. A fájlokat másolja ebbe a mappába biztonsági mentéséhez, abban az esetben egy visszaállítási megadása kötelező.
4. Indítsa el a Beállításszerkesztőt (regedit.exe).
5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. Válassza a menü **fájl** > **a struktúra betöltése**:
6. Keresse meg a **\windows\system32\config\SYSTEM** a csatolt operációsrendszer-lemez egy mappájába. A hive-neveként, írja be a **BROKENSYSTEM**. Az új beállításjegyzék-struktúrát alatt jelenik meg a **HKEY_LOCAL_MACHINE** kulcsot. Majd betölteni a szoftver hive **\windows\system32\config\SOFTWARE** alatt a **HKEY_LOCAL_MACHINE** kulcsot. Adja meg a hive-szoftver neve, **BROKENSOFTWARE**. 
7. Nyisson meg egy rendszergazda jogú parancssori ablakban (**Futtatás rendszergazdaként**), és futtassa a parancsokat a hátralévő lépések során az RDP-konfiguráció alaphelyzetbe állítása. 
8. Csökkentheti az RDP-biztonsági réteg 0, hogy a kiszolgáló és az ügyfél közötti kommunikációhoz a natív RDP-titkosítást használni:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. A minimális beállítás lehetővé teszi örökölt RDP-ügyfelek csatlakozni a titkosítási szint alacsonyabb:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Állítsa be az RDP-vel, az ügyfélszámítógépen a felhasználói konfiguráció betöltése.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Lehetővé teszi az RDP Keep-Alive vezérlő:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Állítsa be a RDP újracsatlakozás vezérlő:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Az RDP-munkamenet ideje ellenőrzés beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Az RDP a leválasztás ideje ellenőrzés beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Az RDP-kapcsolat ideje ellenőrzés beállítása:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Az RDP-munkamenet eltöltött időhányad ellenőrzés beállítása:     REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Állítsa be a "Korlátozza az egyidejű kapcsolatok maximális száma" vezérlő:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Indítsa újra a virtuális Gépet, és próbálja meg újból RDP használatával csatlakozzon hozzá.

## <a name="need-help"></a>Segítség 
Forduljon a támogatási szolgálathoz. Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.





