---
title: Windows Virtual Desktop munkamenetgazda – Azure – Problémamegoldás
description: A Windows Virtual Desktop munkamenetgazda virtuális gépek konfigurálásakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127585"
---
# <a name="session-host-virtual-machine-configuration"></a>Munkamenetgazda virtuális gép konfigurációja

Ebből a cikkből elháríthatja a Windows virtual desktop munkamenetgazda virtuális gépek (VM-ek) konfigurálásakor felmerülő problémákat.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows Virtual Desktop Tech Közösség,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) hogy megvitassák a Windows Virtual Desktop szolgáltatás a termék csapat és az aktív közösség tagjai.

## <a name="vms-are-not-joined-to-the-domain"></a>A virtuális gépek nincsenek a tartományhoz csatlakoztatva

Kövesse ezeket az utasításokat, ha problémái vannak a virtuális gépek tartományhoz való csatlakozással.

- Csatlakozzon a virtuális géphez manuálisan a [Windows Server virtuális gép csatlakozása felügyelt tartományhoz](../active-directory-domain-services/join-windows-vm.md) vagy a [tartományillesztési sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)használatával.
- Próbálja meg pingelni a tartománynevet a virtuális gép parancssorából.
- Tekintse át a tartománycsatlakozási hibaüzenetek listáját a [Tartományhoz való csatlakozáshiba elhárítása hibaüzenetekben.](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)

### <a name="error-incorrect-credentials"></a>Hiba: Helytelen hitelesítő adatok

**Ok:** Elírás történt, amikor a hitelesítő adatok meglett adva az Azure Resource Manager sablonfelületének javításai.

**Javítás:** A megoldáshoz tegye meg az alábbi műveletek egyikét.

- Manuálisan adja hozzá a virtuális gépeket egy tartományhoz.
- Telepítse újra a sablont, miután a hitelesítő adatok megerősítést nyertek. Lásd: [Állomáskészlet létrehozása a PowerShell használatával című témakört.](create-host-pools-powershell.md)
- Csatlakozzon virtuális gépekhez egy tartományhoz egy sablon használatával, amely [egy meglévő Windows virtuális gépről AD-tartományba csatlakozik.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

### <a name="error-timeout-waiting-for-user-input"></a>Hiba: Időhívás a felhasználói bevitelre

**Ok:** A tartományhoz való csatlakozás befejezéséhez használt fiók többtényezős hitelesítéssel (MFA) rendelkezhet.

**Javítás:** A megoldáshoz tegye meg az alábbi műveletek egyikét.

- Ideiglenesen távolítsa el az MFA-t a fiókhoz.
- Használjon szolgáltatásfiókot.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Hiba: A kiépítés során használt fiók nem rendelkezik engedéllyel a művelet végrehajtásához.

**Ok:** A használt fiók nem rendelkezik a virtuális gépek csatlakoztatására vonatkozó engedélyekkel a tartománymegfelelőség és a szabályok miatt.

**Javítás:** A megoldáshoz tegye meg az alábbi műveletek egyikét.

- Olyan fiókot használjon, amely a Rendszergazda csoport tagja.
- Adja meg a szükséges engedélyeket a használt fiókhoz.

### <a name="error-domain-name-doesnt-resolve"></a>Hiba: A tartománynév nem oldódik fel

**1. ok:** A virtuális gépek olyan virtuális hálózaton vannak, amely nincs társítva a tartományt tartalmazó virtuális hálózathoz (VNET).

**1. javítás:** Hozzon létre virtuális hálózat-társviszony-létesítési hely és a virtuális hálózat között, ahol a tartományvezérlő (DC) fut. Lásd: [Virtuális hálózati társviszony-létesítés létrehozása – Erőforrás-kezelő, különböző előfizetések.](../virtual-network/create-peering-different-subscriptions.md)

**2. ok:** Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) használatakor a virtuális hálózat dns-kiszolgálóbeállításai nem frissülnek, hogy a felügyelt tartományvezérlőkre mutassanak.

**2. javítás:** Az Azure AD DS-t tartalmazó virtuális hálózat DNS-beállításainak frissítéséhez olvassa el [az Azure virtuális hálózat DNS-beállításainak frissítése](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)című témakört.

**3. ok:** A hálózati csatoló DNS-kiszolgálójának beállításai nem a virtuális hálózat megfelelő DNS-kiszolgálójára mutatnak.

**3. javítás:** A [DNS-kiszolgálók módosítása] című részben ismertetett lépéseket követve a feloldandó műveletek egyikét tegye meg.
- Módosítsa a hálózati csatoló DNS-kiszolgálójának beállításait **Egyéni** beállításra a [DNS-kiszolgálók módosítása](../virtual-network/virtual-network-network-interface.md#change-dns-servers) lépésekkel, és adja meg a virtuális hálózat DNS-kiszolgálóinak privát IP-címét.
- Módosítsa a hálózati csatoló DNS-kiszolgálójának beállításait a [DNS-kiszolgálók módosítása](../virtual-network/virtual-network-network-interface.md#change-dns-servers)lépésekkel a **virtuális hálózatról öröklésre,** majd módosítsa a virtuális hálózat DNS-kiszolgálójának beállításait a [DNS-kiszolgálók módosítása](../virtual-network/manage-virtual-network.md#change-dns-servers)lépésekkel.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>A Windows virtuális asztali ügynök és a Windows virtuális asztali rendszerbetöltő nincs telepítve

A virtuális gépek kiépítésének ajánlott módja az Azure Resource Manager létrehozása és kiépítése a **Windows virtuális asztali gazdagépkészlet** sablon használatával. A sablon automatikusan telepíti a Windows virtuális asztali ügynök és a Windows virtuális asztali ügynök rendszertöltőjét.

Az alábbi utasításokat követve ellenőrizze, hogy az összetevők telepítve vannak-e, és ellenőrizze a hibaüzeneteket.

1. Ellenőrizze, hogy a két összetevő telepítve van-e a **Vezérlőpult** > **programok** > és szolgáltatások című segédprogramjának bejelölésével.**Programs and Features** Ha **a Windows virtuális asztali ügynök** és a Windows virtuális asztali ügynök **rendszertöltő** nem látható, akkor nem települnek a virtuális gépre.
2. Nyissa meg **a Fájlkezelőt,** és keresse meg a **C:\Windows\Temp\ScriptLog.log**fájlt. Ha a fájl hiányzik, azt jelzi, hogy a két összetevőt telepített PowerShell DSC nem futtatható a megadott biztonsági környezetben.
3. Ha a **C:\Windows\Temp\ScriptLog.log** fájl jelen van, nyissa meg, és ellenőrizze a hibaüzeneteket.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Hiba: Hiányzik a Windows virtuális asztali ügynök és a Windows virtuális asztali ügynök rendszerbetöltője. C:\A Windows\Temp\ScriptLog.log is hiányzik

**1. ok:** Az Azure Resource Manager-sablon bemenete során megadott hitelesítő adatok helytelenek voltak, vagy az engedélyek nem voltak elegendőek.

**1. javítás:** Manuálisan adja hozzá a hiányzó összetevőket a virtuális gépekhez [a PowerShell használatával létrehozott gazdagépkészlet használatával.](create-host-pools-powershell.md)

**2. ok:** A PowerShell DSC el tudta indítani és végre tudta hajtani, de nem tudta befejezni, mivel nem tud bejelentkezni a Windows virtuális asztalra, és nem tudja beszerezni a szükséges információkat.

**2. javítás:** Erősítse meg az alábbi lista elemeit.

- Győződjön meg arról, hogy a fióknem rendelkezik Többfa-val.
- Ellenőrizze, hogy a bérlő neve pontos-e, és hogy a bérlő létezik-e a Windows virtuális asztalon.
- Ellenőrizze, hogy a fiók rendelkezik-e legalább RDS-közreműködői engedélyekkel.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Hiba: A hitelesítés nem sikerült, hiba a C:\Windows\Temp\ScriptLog.log fájlban

**Ok:** A PowerShell DSC-t sikerült végrehajtani, de nem tudott csatlakozni a Windows virtuális asztalhoz.

**Javítás:** Erősítse meg az alábbi lista elemeit.

- Manuálisan regisztrálja a virtuális gépeket a Windows virtuális asztal szolgáltatásban.
- Ellenőrizze fiók csatlakozásához használt Windows virtuális asztal rendelkezik a bérlő ihost készletek létrehozására vonatkozó jogosultságokkal.
- Erősítse meg, hogy a fióknem rendelkezik többéves hitelesítési hitelesítést.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>A Windows virtuális asztali ügynök nem regisztrál a Windows virtuális asztal szolgáltatással

Amikor a Windows virtuális asztali ügynök először telepítve van a munkamenet gazdagépvirtuális gépein (manuálisan vagy az Azure Resource Manager sablonon és a PowerShell DSC-n keresztül), regisztrációs jogkivonatot biztosít. A következő szakasz a Windows virtuális asztali ügynökre és a jogkivonatra vonatkozó hibaelhárítási problémákat ismerteti.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Hiba: A Get-RdsSessionHost parancsmagban megadott állapot nem érhető el állapotot jelez

![A Get-RdsSessionHost parancsmag nem érhető el állapotot jelenít meg.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ok:** Az ügynök nem tudja frissíteni magát egy új verzióra.

**Javítás:** Kövesse az alábbi utasításokat az ügynök manuális frissítéséhez.

1. Töltse le az ügynök új verzióját a munkamenetgazda virtuális gépén.
2. Indítsa el a Feladatkezelőt, és a Szolgáltatás lapon állítsa le az RDAgentBootLoader szolgáltatást.
3. Futtassa a Windows virtuális asztali ügynök új verziójának telepítőját.
4. Amikor a rendszer kéri a regisztrációs jogkivonatot, távolítsa el a bejegyzést INVALID_TOKEN és nyomja meg a következőt (új jogkivonat nem szükséges).
5. Végezze el a telepítővarázslót.
6. Nyissa meg a Feladatkezelőt, és indítsa el az RDAgentBootLoader szolgáltatást.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Hiba: A Windows Virtual Desktop Agent isregistered rendszerleíró bejegyzése 0 értéket mutat

**Ok:** A regisztrációs jogkivonat lejárt, vagy 999999 lejárati értékkel jött létre.

**Javítás:** Kövesse az alábbi utasításokat az ügynök beállítási hibájának kijavításához.

1. Ha már van regisztrációs jogkivonat, távolítsa el azt az Remove-RDSRegistrationInfo eszközzel.
2. Hozzon létre új jogkivonatot az Rds-NewRegistrationInfo segítségével.
3. Ellenőrizze, hogy a -ExpriationHours paraméter értéke 72 -e (a maximális érték 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Hiba: A Windows virtuális asztali ügynök nem jelent szívverést a Get-RdsSessionHost futtatásakor

**1. ok:** Az RDAgentBootLoader szolgáltatás leállt.

**1. javítás:** Indítsa el a Feladatkezelőt, és ha a Szolgáltatás lap az RDAgentBootLoader szolgáltatás leállított állapotát jelenti, indítsa el a szolgáltatást.

**2. ok:** A 443-as port lezárva lehet.

**2. javítás:** A 443-as port megnyitásához kövesse az alábbi utasításokat.

1. Ellenőrizze port 443 nyitva letöltésével a PSPing eszköz [Sysinternal eszközök](/sysinternals/downloads/psping/).
2. Telepítse a PSPing-et arra a munkamenetgazda-gazdagépre, ahol az ügynök fut.
3. Nyissa meg a parancssort rendszergazdaként, és adja ki az alábbi parancsot:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Ellenőrizze, hogy a PSPing visszakapott-e információkat az RDBrokertől:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>A Windows virtuális asztal egymás melletti veremével kapcsolatos problémák elhárítása

A Windows Virtual Desktop egymás melletti verem automatikusan települ a Windows Server 2019 rendszerrel. Az egymás melletti verem Microsoft Windows Server 2016 vagy Windows Server 2012 R2 rendszerre történő telepítéséhez használja a Microsoft Installer (MSI) rendszert. Microsoft Windows 10 esetén a Windows virtuális asztal egymás melletti verem engedélyezése **enablesxstackrs.ps1**.

Az egymás melletti verem három fő módon telepíthető vagy engedélyezve van a munkamenetgazda-készlet virtuális gépein:

- Az Azure Resource Manager **új Windows virtuális asztali gazdagépkészletsablon létrehozása és kiépítése**
- A főlemezképbe való felvétel és engedélyezés
- Manuálisan telepítve vagy engedélyezve minden virtuális gépen (vagy bővítményekkel/PowerShellnel)

Ha problémákat talál a Windows virtuális asztal egymás melletti veremével kapcsolatban, írja be a **qwinsta parancsot** a parancssorból, és ellenőrizze, hogy az egymás melletti verem telepítve vagy engedélyezve van-e.

A **qwinsta** kimenete **az rdp-sx-eket** listázza a kimenetben, ha az egymás melletti verem telepítve van és engedélyezve van.

![Egymás melletti verem telepítve vagy engedélyezve qwinsta szerepel rdp-sxs a kimenetben.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Vizsgálja meg az alább felsorolt rendszerleíró bejegyzéseket, és ellenőrizze, hogy értékeik egyeznek-e. Ha a beállításkulcsok hiányoznak, vagy az értékek nem egyeznek, kövesse a [PowerShell alkalmazással rendelkező gazdakészlet létrehozása](create-host-pools-powershell.md) című témakör utasításait az egymás melletti verem újratelepítéséről.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Hiba: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE hibakódot.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ok:** Az egymás melletti verem nincs telepítve a munkamenet gazdagépén.

**Javítás:** Kövesse ezeket az utasításokat az egymás melletti verem telepítéséhez a munkamenetgazda-gazdagépen.

1. A Távoli asztali protokoll (RDP) segítségével közvetlenül a munkamenetgazda virtuális gépébe juthat helyi rendszergazdaként.
2. Töltse le és importálja [a Windows Virtual Desktop PowerShell-modult,](/powershell/windows-virtual-desktop/overview/) amelyet a PowerShell-munkamenetben használhat, ha még nem tette meg, majd futtassa ezt a parancsmast a fiókba való bejelentkezéshez:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Telepítse az egymás melletti verem telepítését [a PowerShell használatával létrehozott gazdagépkészlet használatával.](create-host-pools-powershell.md)

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>A hibásan működő Windows virtuális asztal egymás melletti vermének javítása

Vannak olyan ismert körülmények, amelyek az egymás melletti verem hibás működését okozhatják:

- Nem követi a helyes sorrendben a lépéseket, hogy az egymás melletti verem
- Automatikus frissítés a Windows 10 továbbfejlesztett sokoldalú lemezre (EVD)
- Hiányzik a Távoli asztali munkamenetgazda (RDSH) szerepkör
- Futás enablesxsstackrc.ps1 többször
- Az enablesxsstackrc.ps1 futtatása helyi rendszergazdai jogosultságokkal nem rendelkező fiókban

Az ebben a szakaszban található utasítások segítséget nyújtanak a Windows virtual desktop egymás melletti verem eltávolításában. Miután eltávolította az egymás melletti verem, nyissa meg a "Regisztrálja a virtuális gépet a Windows virtuális asztali gazdakészlet" [a Hozzon létre egy gazdakészlet et PowerShell](create-host-pools-powershell.md) újra az egymás melletti verem.

A szervizelés futtatásához használt virtuális gépnek ugyanazon az alhálózaton és tartományon kell lennie, mint a hibásan működő egymás melletti veremben lévő virtuális gépnek.

A szervizelés ugyanazon alhálózatról és tartományból történő futtatásához kövesse az alábbi utasításokat:

1. Csatlakozzon a szabványos Távoli asztali protokoll (RDP) a virtuális gép, ahonnan javítás lesz alkalmazva.
2. Letöltés PsExec https://docs.microsoft.com/sysinternals/downloads/psexeca .
3. Csomagolja ki a letöltött fájlt.
4. Indítsa el a parancssort helyi rendszergazdaként.
5. Keresse meg azt a mappát, ahol a PsExec ki volt csomagolva.
6. A parancssorból használja a következő parancsot:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname a gép neve a virtuális gép a hibásan működő egymás melletti verem.

7. Fogadja el a PsExec licencszerződést a Megegyezés gombra kattintva.

    ![Szoftverlicenc-szerződés képernyőképe.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Ez a párbeszédpanel csak a PsExec első futtatásakor jelenik meg.

8. Miután a parancssori munkamenet megnyílik a virtuális gép en a hibásan működő egymás melletti verem, futtassa qwinsta és ellenőrizze, hogy egy rdp-sxs nevű bejegyzés érhető el. Ha nem, egy egymás melletti verem nem jelenik meg a virtuális gép, így a probléma nem kötődik az egymás melletti verem.

    ![Rendszergazda parancssor](media/AdministratorCommandPrompt.png)

9. Futtassa a következő parancsot, amely felsorolja a virtuális gépre telepített Microsoft-összetevőket a hibásan működő egymás melletti veremkel.

    ```cmd
        wmic product get name
    ```

10. Futtassa az alábbi parancsot a fenti lépésből származó terméknevekkel.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Távolítsa el az összes olyan terméket, amely a "Távoli asztal" kezdetű.

12. A Windows virtuális asztal összes összetevőjének eltávolítása után kövesse az operációs rendszerre vonatkozó utasításokat:

13. Ha az operációs rendszer A Windows Server, indítsa újra a virtuális gép, amely a hibásan működő egymás melletti verem (akár az Azure Portalon, vagy a PsExec eszköz).

Ha az operációs rendszer Microsoft Windows 10, folytassa az alábbi utasításokat:

14. A PsExec szolgáltatást futtató virtuális gépről nyissa meg a Fájlkezelőt, és másolja a disablesxsstackrc.ps1 fájlt a virtuális gép rendszermeghajtójára a hibásan működő egymás melletti veremkel.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname a gép neve a virtuális gép a hibásan működő egymás melletti verem.

15. Az ajánlott folyamat: a PsExec eszközből indítsa el a PowerShellt, és keresse meg a mappát az előző lépésből, és futtassa a disablesxsstackrc.ps1 parancsot. Másik lehetőségként a következő parancsmagokat is futtathatja:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Ha a parancsmagok futása befejeződött, indítsa újra a virtuális gép a hibásan működő egymás melletti verem.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>A Távoli asztali licencelési mód nincs konfigurálva

Ha rendszergazdai fiókkal jelentkezik be a Windows 10 Enterprise többmunkamenetes munkamenetébe, előfordulhat, hogy a következő üzenet jelenik meg: "A távoli asztali licencelési mód nincs beállítva, a Távoli asztali szolgáltatások x napon belül leáll. A Csatlakozásszervező kiszolgálón a Kiszolgálókezelő segítségével adja meg a Távoli asztal licencelési módját."

Ha az időkorlát lejár, egy hibaüzenet jelenik meg, amely szerint: "A távoli munkamenet megszakadt, mert a számítógéphez nem állnak rendelkezésre távoli asztali ügyféllicencek."

Ha a fenti üzenetek bármelyikét látja, az azt jelenti, hogy a lemezképen nincs telepítve a legújabb Windows-frissítések, vagy hogy a Távoli asztal licencelési módját csoportházirenddel állítja be. A következő szakaszokban ismertetett lépéseket követve ellenőrizze a csoportházirend-beállítást, azonosítsa a Windows 10 Enterprise többmunkamenetes verzióját, és telepítse a megfelelő frissítést.  

>[!NOTE]
>A Windows virtuális asztal csak akkor igényel RDS-ügyféllicencet (CAL), ha a gazdakészlet Windows Server munkamenet-állomásokat tartalmaz. A távoli asztali ügyféllicenc-licenc konfigurálásáról a [Távoli asztali szolgáltatások telepítésének licencelése ügyféllicencekkel](/windows-server/remote/remote-desktop-services/rds-client-access-license/)című témakörben olvashat.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Az Asztal távoli licencelési módjának csoportházirend-beállításának letiltása

Ellenőrizze a csoportházirend-beállítást a Csoportházirend-szerkesztő virtuális gépben való megnyitásával és a **Felügyeleti sablonok ra** > navigálva**a Windows-összetevők** > távoli asztali**szolgáltatások** > **távoli asztali munkamenetgazda-kiszolgálólicencelése:** > **Licensing** > Állítsa be a Távoli asztali**licencelési módot**. Ha a **csoportházirend-beállítás engedélyezve**van, módosítsa **a Letiltott**beállítást. Ha már le van tiltva, akkor hagyja úgy, ahogy van.

>[!NOTE]
>Ha csoportházirendet állít be a tartományon keresztül, tiltsa le ezt a beállítást a Windows 10 Enterprise többmunkamenetes virtuális gépeket célzó házirendeken.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Annak meghatározása, hogy a Windows 10 Enterprise többmunkamenetes verzióját használja

Annak ellenőrzése, hogy a Windows 10 Enterprise melyik verziójával rendelkezik:

1. Jelentkezzen be a rendszergazdai fiókjával.
2. Írja be a "Be" szót a Start menü melletti keresősávba.
3. Válassza **a Számítógép – beadat**.
4. Ellenőrizze a "Verzió" melletti számot. A számnak "1809" vagy "1903" számnak kell lennie, ahogy az az alábbi képen látható.

    ![Képernyőkép a Windows specifikációs ablakáról. A verziószám kék színnel van kiemelve.](media/windows-specifications.png)

Most, hogy már tudja a verziószámát, ugorjon előre a megfelelő szakaszra.

### <a name="version-1809"></a>1809-es verzió

Ha a verziószám "1809" szó, telepítse [a KB4516077 kb.](https://support.microsoft.com/help/4516077)

### <a name="version-1903"></a>1903-as verzió

Telepítse újra a gazdaoperációs rendszert a Windows 10 1903-as verziójának legújabb verziójával az Azure Galleryből.

## <a name="next-steps"></a>További lépések

- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A bérlői és gazdagépkészlet Windows virtuális asztali környezetben való létrehozása során felmerülő problémák elhárításához olvassa el a [Bérlő és a gazdakészlet létrehozása című témakört.](troubleshoot-set-up-issues.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A Windows Virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításáról a Windows Virtuális asztal szolgáltatás kapcsolatai című [témakörben található.](troubleshoot-service-connection.md)
- A Távoli asztali ügyfelekkel kapcsolatos problémák elhárításáról [a Távoli asztali ügyfél – Problémaeltetés – problémamegoldás](troubleshoot-client.md)
- A PowerShell Windows virtuális asztallal való használatakor felmerülő problémák elhárításáról a [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)című témakörben található.
- A szolgáltatásról a [Windows virtuális asztali környezet című témakörben](environment-setup.md)olvashat bővebben.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- A naplózási műveletekről a [Naplózási műveletek az Erőforrás-kezelővel (Audit operations with Resource Manager) témakörben](../azure-resource-manager/management/view-activity-logs.md)olvashat.
- A központi telepítés során fellépő hibák meghatározására irányuló műveletekről a [Telepítési műveletek megtekintése című témakörben](../azure-resource-manager/templates/deployment-history.md)olvashat.
