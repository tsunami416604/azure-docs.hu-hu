---
title: Belső hiba történik, amikor RDP-kapcsolatot hoz létre az Azure virtuális gépekkel | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el az RDP belső hibáit a Microsoft Azure-ban.| Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266922"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Belső hiba jelentkezik, ha távoli asztalon keresztül próbál csatlakozni az Azure-beli virtuális géphez

Ez a cikk egy olyan hibát ismertet, amely a Microsoft Azure virtuális géphez (VM) való csatlakozáskor jelenhet meg.


## <a name="symptoms"></a>Probléma

Nem tud csatlakozni egy Azure virtuális gép a távoli asztali protokoll (RDP) használatával. A kapcsolat elakad a "Távoli konfigurálása" szakaszban, vagy a következő hibaüzenet jelenik meg:

- RDP belső hiba
- Belső hiba történt
- Ez a számítógép nem csatlakoztatható a távoli számítógéphez. Próbálkozzon újra a csatlakozáshoz. Ha a probléma továbbra is fennáll, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához


## <a name="cause"></a>Ok

A probléma a következő okok miatt fordulhat elő:

- A helyi RSA titkosítási kulcsok nem érhetők el.
- A TLS protokoll le van tiltva.
- A tanúsítvány sérült vagy lejárt.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma elhárításához használja a soros konzolt, vagy [javítsa ki a virtuális gép offline](#repair-the-vm-offline) állapotba helyezését a virtuális gép operációs rendszerlemezének helyreállítási virtuális géphez csatlakoztatásával.


### <a name="use-serial-control"></a>Soros vezérlő használata

Csatlakozzon a [Soros konzolhoz, és nyissa meg a PowerShell-példányt.](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
) Ha a soros konzol nincs engedélyezve a virtuális gép, nyissa meg a [javítás a virtuális gép offline](#repair-the-vm-offline) szakaszban.

#### <a name="step-1-check-the-rdp-port"></a>Lépés: 1 Ellenőrizze az RDP portot

1. Egy PowerShell-példányban a [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) segítségével ellenőrizze, hogy a 8080-as portot más alkalmazások használják-e:

        Netstat -anob |more
2. Ha a Termservice.exe 8080-as portot használ, folytassa a 2. Ha a Termservice.exe-től eltérő másik szolgáltatás vagy alkalmazás a 8080-as portot használja, kövesse az alábbi lépéseket:

    1. Állítsa le a 3389 szolgáltatást használó alkalmazás szolgáltatását:

            Stop-Service -Name <ServiceName> -Force

    2. Indítsa el a terminálszolgáltatást:

            Start-Service -Name Termservice

2. Ha az alkalmazás nem állítható le, vagy ha ez a módszer nem vonatkozik Önre, módosítsa az RDP portját:

    1. A port módosítása:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Állítsa be az új port tűzfalát:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Frissítse az új port hálózati biztonsági csoportját](../../virtual-network/security-overview.md) az Azure Portal RDP-portján.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>2. lépés: Állítsa be a megfelelő engedélyeket az RDP önaláírt tanúsítvány

1.  Egy PowerShell-példányban egyenként futtassa a következő parancsokat az RDP önaláírt tanúsítványának megújításához:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Ha ezzel a módszerrel nem tudja megújítani a tanúsítványt, próbálja meg távolról megújítani az RDP-alapú önaláírt tanúsítványt:

    1. Egy működő virtuális gép, amely kapcsolatban áll a problémát tapasztal, írja be az **mmc** a **Futtatás** mezőbe a Microsoft Management Console megnyitásához.
    2. A **Fájl** menüben válassza a **Beépülő modul hozzáadása/eltávolítása menüBen**a **Tanúsítványok**, majd a **Hozzáadás parancsot.**
    3. Válassza **a Számítógépfiókok**lehetőséget, válassza **a Másik számítógép**lehetőséget, majd adja hozzá a problémás virtuális gép IP-címét.
    4. Nyissa meg a **Távoli asztal\Tanúsítványok** mappát, kattintson a jobb gombbal a tanúsítványra, majd válassza a **Törlés parancsot.**
    5. A Serial Console egyik PowerShell-példányában indítsa újra a Távoli asztal konfigurációja szolgáltatást:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Állítsa alaphelyzetbe a MachineKeys mappa engedélyét.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Indítsa újra a virtuális gép, majd próbálja meg egy távoli asztali kapcsolat indítása a virtuális gép. Ha a hiba továbbra is előfordul, folytassa a következő lépéssel.

#### <a name="step-3-enable-all-supported-tls-versions"></a>3. lépés: Az összes támogatott TLS-verzió engedélyezése

Az RDP-ügyfél a TLS 1.0 protokollt használja alapértelmezett protokollként. Ez azonban megváltoztatható TLS 1.1-re, amely az új szabványsá vált. Ha a TLS 1.1 le van tiltva a virtuális gépen, a kapcsolat sikertelen lesz.
1.  CMD-példányban engedélyezze a TLS protokollt:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Ha meg szeretné akadályozni, hogy az AD-házirend felülírja a módosításokat, ideiglenesen állítsa le a csoportházirend frissítését:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Indítsa újra a virtuális gép, hogy a módosítások érvénybe lépnek. Ha a probléma megoldódott, futtassa a következő parancsot a csoportházirend újbóli engedélyezéséhez:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Ha a módosítás visszaáll, az azt jelenti, hogy a vállalati tartományban van egy Active Directory-házirend. Meg kell változtatni, hogy a politika, hogy elkerüljék ezt a problémát előforduló újra.

### <a name="repair-the-vm-offline"></a>A virtuális gép offline javítása

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Miután az operációs rendszer lemeze csatlakozik a helyreállítási virtuális géphez, győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

#### <a name="enable-dump-log-and-serial-console"></a>Memóriaképnapló és soros konzol engedélyezése

A memóriaképnapló és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt.

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le ezt a meghajtóbetűjelet a virtuális gép megfelelő értékével.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>A MachineKeys mappa engedélyének alaphelyzetbe állítása

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa az alábbi parancsprogramot. Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le ezt a meghajtóbetűjelet a virtuális gép megfelelő értékével.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Az összes támogatott TLS-verzió engedélyezése

1.  Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**), és futtassa a következő parancsokat. A következő parancsfájl feltételezi, hogy az illesztőprogram betűjele van rendelve a csatlakoztatott operációs rendszer lemez F. Cserélje le ezt a meghajtóbetűjelet a megfelelő értéket a virtuális gép.
2.  Ellenőrizze, hogy melyik TLS engedélyezve van:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Ha a kulcs nem létezik, vagy értéke **0,** engedélyezze a protokollt a következő parancsfájlok futtatásával:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  NLA engedélyezése:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Válassza le az operációs rendszer lemezét, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md), majd ellenőrizze, hogy a probléma megoldódott.





