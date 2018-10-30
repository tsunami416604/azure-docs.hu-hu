---
title: Belső hiba akkor fordul elő, ha Ön a távoli asztal az Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg a Microsoft Azure-ban RDP belső kapcsolatos hibák elhárítása. |} A Microsoft Docs
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
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 0576d241b3412697ac0d46e77cdfb416921781cb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215787"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Belső hiba akkor fordul elő, amikor próbál csatlakozni egy Azure virtuális géphez a távoli asztalon keresztül 

Ez a cikk ismerteti, amikor megpróbál kapcsolódni egy virtuális géphez (VM) a Microsoft Azure-ban tapasztalható hiba.
> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell, amely az új központi telepítéseknél helyett a klasszikus üzemi modell használatát javasoljuk. 

## <a name="symptoms"></a>Probléma 

A távoli asztal protokoll (RDP) használatával nem lehet csatlakozni egy Azure virtuális Gépen. A kapcsolatot a "Távoli konfigurálása" szakaszban elakad, vagy a következő hibaüzenet jelenik meg:

- RDP belső hiba 
- Belső hiba történt
- Ez a számítógép nem lehet csatlakozni a távoli számítógéppel. Próbáljon meg újra. Ha a probléma továbbra is fennáll, forduljon a tulajdonosa, a távoli számítógép vagy a hálózati rendszergazda


## <a name="cause"></a>Ok

A probléma a következő okok miatt fordulhat elő:

- A helyi RSA titkosítási kulcsok nem érhetők el.
- A TLS protokoll le van tiltva.
- A tanúsítvány sérült vagy lejárt.

## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma elhárításához, használja a soros konzol vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) által a virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolása.


### <a name="use-serial-control"></a>Soros vezérlőelem használata

Csatlakozás [soros konzolon és a PowerShell-példány megnyitása](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális Gépen, nyissa meg a [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) szakaszban.

#### <a name="step-1-check-the-rdp-port"></a>. Lépés: 1 Ellenőrizze az RDP-port

1. Egy PowerShell-példányban, használja a [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) ellenőrizze, hogy más alkalmazások által használt-e a 8080-as porton:

        Netstat -anob |more
2. Ha Termservice.exe 8080-as portot használ, akkor folytassa a 2. lépés. Ha egy másik szolgáltatás vagy alkalmazás eltérő Termservice.exe 8080-as portot használ, kövesse az alábbi lépéseket:

    A. Állítsa le a szolgáltatást, amely a 3389-szolgáltatást használ: 

        Stop-Service -Name <ServiceName>

    B. Indítsa el a terminálszolgáltatás: 

        Start-Service -Name Termservice

2. Ha az alkalmazás nem lehet leállítani, vagy ez a módszer nem vonatkozik Önre, módosítsa a portot, az RDP-hez:

    A. A port módosítása:

        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice Start-Service -Name Termservice
 
    B. Állítsa be az új port a tűzfalon:

        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    C. [Frissítse a hálózati biztonsági csoportot az új portot](../../virtual-network/security-overview.md) az Azure portal RDP-portjára.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>2. lépés: Az RDP-önaláírt tanúsítvány a megfelelő engedélyek beállítása

1.  Egy PowerShell-példány futtassa a következő parancsokat egyenként az RDP-önaláírt tanúsítvány megújítása:

        Import-Module PKI Set-Location Cert:\LocalMachine $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) Remove-Item -Path $RdpCertThumbprint 

        Stop-Service -Name "SessionEnv" 

        Start-Service -Name "SessionEnv"

2. Ha ez a módszer használatával a tanúsítvány nem tudják megújítani, próbálja meg az RDP-önaláírt tanúsítvány megújítása távolról:

    1. Működő virtuális Gépet, amely kapcsolódik a virtuális gép, amely problémákba ütközik, típus **mmc** a a **futtatása** párbeszédpanel a Microsoft Management Console megnyitásához.
    2. Az a **fájl** menüjében válassza **beépülő modul hozzáadása/eltávolítása**, jelölje be **tanúsítványok**, majd válassza ki **Hozzáadás**.
    3. Válassza ki **számítógépfiókok**válassza **egy másik számítógépre**, majd adja hozzá a hibás virtuális Gépnek az IP-címét.
    4. Nyissa meg a **távoli Desktop\Certificates** mappát, kattintson a jobb gombbal a tanúsítványt, és majd, és válassza ki **törlése**.
    5. A PowerShell-példány a soros konzolból indítsa újra a távoli asztal konfigurálásának szolgáltatást: 

            Stop-Service -Name "SessionEnv" 

            Start-Service -Name "SessionEnv"
3. Állítsa alaphelyzetbe a MachineKeys mappa engedélyeit.
    
        remove-module psreadline icacls 

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r 

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)" 

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)" 

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)" 

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt Restart-Service TermService -Force

4. Indítsa újra a virtuális Gépet, és ismételje meg a távoli asztali kapcsolatot a virtuális gép indítása. Ha a hiba továbbra is fennáll, lépjen a következő lépéssel.

3. lépés: Engedélyezze az összes támogatott TLS-verziók

Az RDP-ügyfelet használja az alapértelmezett protokoll a TLS 1.0. Azonban ez módosítható a TLS 1.1-et az új standard vált. A TLS 1.1 le van tiltva, a virtuális gépen, ha a kapcsolat nem jön létre.
1.  CMD-példányban engedélyezze a TLS protokoll:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f 

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f 

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Módosítások felülírása megakadályozni az AD-házirend, ideiglenesen állítsa le a csoportházirend frissítése:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  A virtuális gép újraindítása, hogy a módosítások életbe léptetéséhez. Ha a probléma megoldódott, futtassa a következő parancsot a csoportházirend újbóli engedélyezése:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Ha a módosítás visszavonásra kerül, az azt jelenti, hogy nincs-e az Active Directory-házirend a vállalati tartomány. Akkor módosítsa az adott házirendnek forduljon elő ismét a probléma elkerülése érdekében.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Miután az operációsrendszer-lemez csatolva van a helyreállítási virtuális Gépet, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

#### <a name="enable-dump-log-and-serial-console"></a>Memóriakép napló és a soros konzol engedélyezése

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssort munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e F. cserélje le ezt a meghajtóbetűjelet, a virtuális gép a megfelelő értékkel.

    ```powershell
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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>MachineKeys mappa engedélyeinek alaphelyzetbe állítása

1. Nyisson meg egy rendszergazda jogú parancssort munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő szkriptet. Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e F. cserélje le ezt a meghajtóbetűjelet, a virtuális gép a megfelelő értékkel.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Engedélyezze a TLS az összes támogatott verzió 

1.  Nyisson meg egy rendszergazda jogú parancssort munkamenetet (**Futtatás rendszergazdaként**), és a Futtatás a következő parancsokat. Az alábbi parancsfájl feltételezi, hogy a csatlakoztatott operációsrendszer-lemez meghajtóbetűjelét van hozzárendelve F. cserélje le ezt a meghajtóbetűjelet, a megfelelő értékkel van a virtuális géphez.
2.  Ellenőrzés, amely a TLS engedélyezve van:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Ha a kulcs nem létezik, vagy annak értéke **0**, a protokoll engedélyezéséhez futtassa az alábbi parancsfájlok:

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
5.  [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md), és ellenőrizze, hogy a probléma megoldódott.



    
 