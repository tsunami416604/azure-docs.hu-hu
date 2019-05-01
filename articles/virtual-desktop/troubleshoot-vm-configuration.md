---
title: Bérlő és a gazdagépcsoport készlet létrehozásakor, a Windows virtuális asztal – Azure
description: Kapcsolatos problémák megoldását, amikor egy bérlő és a munkamenet gazdagép virtuális gép (VM) konfigurálja a Windows virtuális asztali környezetben.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928122"
---
# <a name="tenant-and-host-pool-creation"></a>Bérlő és a gazdagép készlet létrehozása

Ez a cikk segítségével problémák merültek fel a való konfigurálásakor a Windows virtuális asztal munkamenetben gazdagép virtuális gépek (VM).

## <a name="provide-feedback"></a>Visszajelzés küldése

A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai.

## <a name="vms-are-not-joined-to-the-domain"></a>A tartományhoz nem csatlakozó virtuális gépek

Ha a virtuális gépek csatlakoztatása a tartományhoz problémákat tapasztal, kövesse az alábbi utasításokat.

- Csatlakoztassa a virtuális Gépet, a folyamat segítségével manuálisan [egy Windows Servert futtató virtuális gép csatlakoztatása felügyelt tartományokhoz](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) használatával vagy a [tartományhoz való csatlakozás sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Próbálja ki a pingelés parancssori paranccsal futtathatja virtuális gépen a tartomány nevét.
- Tekintse át a tartományhoz való csatlakozás hibaüzenetek a [hibaelhárítási illesztési hibaüzenetek](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Hiba: Helytelen hitelesítő adatokat

**OK:** Hiba történt, amikor az az Azure Resource Manager sablon kapcsolat javítja a hitelesítő adatok megadott elgépelte.

**Javítás:** Kövesse az alábbi utasításokat, javítsa ki a hitelesítő adatokat.

1. Adja hozzá manuálisan a virtuális gépek tartományhoz.
2. Ismételt üzembe helyezése után már visszaigazolt hitelesítő adatokat. Lásd: [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Virtuális gépek csatlakoztathatja egy tartományhoz, a sablon használatával [AD-tartományhoz csatlakozik a meglévő Windows virtuális gép](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Hiba: Felhasználói bevitel Várakozás időtúllépése

**OK:** A tartományhoz való csatlakozás végrehajtásához használt fiók a többtényezős hitelesítés (MFA) lehet.

**Javítás:** Kövesse az alábbi utasításokat a tartományhoz való csatlakozás végrehajtásához.

1. Ideiglenesen távolítsa el az MFA a fiókhoz.
2. A szolgáltatás fiók használata.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Hiba: A kiépítés során használt fióknak nincs engedélye a művelet végrehajtásához

**OK:** Az éppen használt fiók nem rendelkezik engedélyekkel virtuális gépek csatlakoztatása a tartományhoz, megfelelőségi és előírások miatt.

**Javítás:** Kövesse az alábbi utasításokat.

1. A Rendszergazdák csoport tagja olyan fiókot használjon.
2. Adja meg a szükséges engedélyeket, az éppen használt fiók.

### <a name="error-domain-name-doesnt-resolve"></a>Hiba: Tartománynév nem oldja meg

**1. ok:** Egy erőforráscsoport, a virtuális hálózat (VNET), ahol megtalálható a tartományhoz nem tartozó virtuális gépek találhatók.

**1 javítás:** Hozzon létre virtuális hálózatok közötti társviszony a virtuális hálózathoz, ahol a virtuális gépek kiépített és a tartományvezérlő (DC) futtató virtuális hálózat között. Lásd: [hozzon létre egy virtuális hálózati társviszony - erőforrás-kezelő, a különböző előfizetésekben](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**2. ok:** AadService (AADS) használatakor a DNS-bejegyzések nincs beállítva.

**2 Javítás:** Tartományi szolgáltatások beállítása, lásd: [engedélyezése az Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows virtuális asztal ügynök és a Windows virtuális asztal rendszertöltő nincs telepítve

Az ajánlott módszer a virtuális gépek üzembe helyezése az Azure Resource Manager használ **létrehozásával és kiépítésével Windows virtuális asztali állomás készlet** sablont. A sablon automatikusan telepíti a Windows virtuális asztal ügynök és a Windows virtuális asztal ügynök rendszertöltő.

Kövesse ezeket az utasításokat annak ellenőrzéséhez, hogy az összetevők telepítési és hibaüzeneteket.

1. Ellenőrizze, hogy a két összetevő van telepítve ellenőrzésével **Vezérlőpult** > **programok** > **programok és szolgáltatások**. Ha **Windows virtuális asztal ügynök** és **Windows virtuális asztal ügynök rendszertöltő** nem láthatók, a fentiek nincsenek telepítve a virtuális gépen.
2. Nyissa meg **fájlkezelő** , és keresse meg **C:\Windows\Temp\scriptlogs.log**. A fájl hiányzik, akkor ez azt jelzi, hogy a PowerShell DSC, amely a két összetevő telepítése nem volt képes a megadott biztonsági környezetében futnak.
3. Ha a fájl **C:\Windows\Temp\scriptlogs.log** jelen, nyissa meg és hibaüzeneteket.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Hiba: Windows virtuális asztal ügynök és a Windows virtuális asztal ügynök rendszertöltő hiányoznak. C:\Windows\Temp\scriptlogs.log is hiányzik.

**1. ok:** Az Azure Resource Manager-sablon a megadott hitelesítő adatok helytelen vagy engedélyei megkülönböztették a nem elegendő.

**1 javítás:** Manuálisan adja hozzá a hiányzó összetevőket a virtuális gépek [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**2. ok:** PowerShell DSC képes volt elindításához, és hajtsa végre, de nem sikerült befejezni, mert nem jelentkezzen be a Windows virtuális asztalok és nem szükséges információk beszerzéséhez.

**2 Javítás:** Erősítse meg a következő listán szereplő elemeket.

- Győződjön meg arról, hogy a fiók nem rendelkezik a többtényezős hitelesítés.
- Győződjön meg arról, hogy a bérlő nevét és a bérlő szerepel a Windows virtuális asztal.
- Ellenőrizze, hogy a fióknak legalább a távoli asztali szolgáltatások közreműködői engedélyekkel.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Hiba: A hitelesítés nem sikerült, hiba történt a C:\Windows\Temp\scriptlogs.log

**OK:** PowerShell DSC tudta hajtani, de nem sikerült csatlakozni a virtuális asztali Windows.

**Javítás:** Erősítse meg a következő listán szereplő elemeket.

- Manuálisan regisztrálja a virtuális gépek a virtuális asztali Windows-szolgáltatás.
- Erősítse meg a Windows virtuális asztal való csatlakozáshoz használt fiók jogosult a gazdagép-címkészletek létrehozása a bérlőn.
- Erősítse meg a fiók nem rendelkezik a többtényezős hitelesítés.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows virtuális asztal ügynök nincs Regisztrálás a virtuális asztali Windows-szolgáltatás

A Windows virtuális asztal ügynök első telepítésekor a munkamenet üzemeltethet virtuális gépet (vagy manuálisan vagy keresztül az Azure Resource Manager-sablon és a PowerShell DSC), egy regisztrációs tokent biztosít. Az alábbi szakasz ismerteti a hibaelhárítási problémák a Windows virtuális asztal ügynök és a jogkivonat érvényes.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Hiba: A Get-RdsSessionHost parancsmag mezőjénél állapota állapota nem érhető el

![Get-RdsSessionHost parancsmag állapotát jeleníti meg, nem érhető el.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**OK:** Az ügynök nem tud magát egy új verzióra szeretne frissíteni.

**Javítás:** Kövesse ezeket az utasításokat az ügynököt manuálisan frissíteni.

1. Töltse le a munkamenet virtuális gazdagép-ügynök új verziója.
2. A Feladatkezelő indítása, és a szolgáltatás lap a RDAgentBootLoader szolgáltatás leállításához.
3. Futtassa a telepítőt, a virtuális asztal-ügynök Windows új verziójának.
4. Ha a rendszer kéri a regisztrációs jogkivonatot, távolítsa el az INVALID_TOKEN bejegyzést, majd kattintson a Tovább gombra (új jogkivonat nem szükséges).
5. A telepítés varázsló befejezéséhez.
6. Nyissa meg a Feladatkezelőt, és indítsa el a RDAgentBootLoader szolgáltatást.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Hiba:  Windows virtuális asztal ügynök bejegyzést a beállításjegyzékben IsRegistered jeleníti meg a 0 érték

**OK:** Regisztrációs jogkivonat lejárt vagy lejárati értéke 999999 lett létrehozva.

**Javítás:** Kövesse ezeket az utasításokat az ügynök beállításjegyzék hiba elhárításához.

1. Ha már van egy regisztrációs jogkivonatot, távolítsa el a Remove-RDSRegistrationInfo.
2. Az Rds-NewRegistrationInfo új jogkivonat létrehozása.
3. Győződjön meg arról, hogy 72 - ExpriationHours paraméter értéke (a maximális értéknél 99999 közötti érték).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Hiba: Virtuális asztali Windows-ügynök nem reporting szívverést, Get-RdsSessionHost futtatásakor

**1. ok:** RDAgentBootLoader szolgáltatás le lett állítva.

**1 javítás:** A Feladatkezelő indítása, és a szolgáltatás lapon jelentések RDAgentBootLoader szolgáltatás leállított állapotba, ha a szolgáltatás elindítása.

**2. ok:** 443-as port zárva.

**2 Javítás:** Kövesse az alábbi utasításokat a 443-as portot.

1. Erősítse meg a 443-as port meg nyitva a PSPing eszköz letöltésével [Sysinternals eszközök](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Telepítse a PSPing a munkamenetgazda az ügynököt futtató virtuális Gépet.
3. Nyissa meg a parancssort rendszergazdaként, és adja ki az alábbi parancsot:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Győződjön meg arról, hogy a PSPing kapott információkat visszaküldi a RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>A Windows Virtual Desktop egymás mellett hibával kapcsolatos hibák elhárítása

A Windows Virtual Desktop egymás mellett verem automatikusan telepítve van a Windows Server 2019. A Microsoft Installer (MSI) segítségével a párhuzamos verem telepítése a Microsoft Windows Server 2016 vagy Windows Server 2012 R2. A Microsoft Windows 10 esetén a Windows Virtual Desktop egymás mellett verem engedélyezve van a **enablesxstackrs.ps1**.

Három fő módja a párhuzamos verem lekérdezi telepítve vagy engedélyezve a munkamenet gazdagépre a virtuális gépek készletté van:

- Az Azure Resource Manager **létrehozásával és kiépítésével új Windows virtuális asztali állomás készlet** sablon
- Által éppen tartalmazza, és a fő rendszerkép engedélyezve
- Telepítve vagy engedélyezve manuálisan az egyes virtuális Gépeken (vagy a bővítmények/PowerShell használatával)

Ha a Windows Virtual Desktop egymás mellett hibával problémákat tapasztal, írja be a **qwinsta** parancsot a parancssorból, győződjön meg arról, hogy a párhuzamos verem telepítve vagy engedélyezve.

A kimenet a **qwinsta** felsorolja **rdp-sxs** a kimenetben, ha a párhuzamos verem telepítve és engedélyezve van.

![Párhuzamos verem telepítve vagy engedélyezve van az rdp-sxs a kimenetben állapottal qwinsta.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Vizsgálja meg az alábbi beállításjegyzék-bejegyzések, és győződjön meg arról, hogy azok az értékek egyeznek. Ha bejegyzéskulcsok hiányoznak, vagy értékek nem egyeznek, kövesse a [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) való telepítse újra a párhuzamos vermet.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Hiba: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE hibakód.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**OK:** A párhuzamos verem a munkamenetgazda virtuális gép nincs telepítve.

**Javítás:** Kövesse az alábbi utasításokat a párhuzamos verem telepítése a munkamenetgazda virtuális Gépet.

1. Távoli asztal protokoll (RDP) használatával közvetlenül a helyi rendszergazdaként munkamenetgazda virtuális gép kaphat.
2. Töltse le és importálja [a Windows virtuális asztal PowerShell modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.
3. Telepítés az egymás melletti stack használatával [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Hogyan háríthatja el a Windows Virtual Desktop egymás mellett verem egy hibásan működik

Nem ismert, amelyek miatt a párhuzamos verem hibás működésével esetekben:

- Nem követi a megfelelő sorrendben lépést ahhoz, hogy a párhuzamos verem
- Az automatikus frissítés a Windows 10-es fokozott sokoldalú lemez (holland)
- A távoli asztali munkamenetgazda (RDSH) szerepkör hiányzik
- Enablesxsstackrc.ps1 több alkalommal fut
- Futó enablesxsstackrc.ps1 egy fiókot, amely nem rendelkezik helyi rendszergazdai jogosultságokat

A jelen szakaszban található útmutatásokat segítségével távolítsa el a Windows Virtual Desktop egymás mellett stack. Miután eltávolítja a párhuzamos vermet, lépjen az "A virtuális gép regisztrálása a Windows virtuális asztali állomás készlettel" az [gazdagép-készlet létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) az egymás melletti verem újratelepítése.

A szervizelés futtatásához használt virtuális Gépet a virtuális Gépet, a hibásan egymás mellett hibával alhálózat és a tartomány kell lennie.

Kövesse az alábbi utasításokat, szervizelés futtatásához az alhálózat és a tartomány:

1. A standard szintű Remote Desktop Protocol (RDP) csatlakozhat a virtuális gép, ahol javítást alkalmazza.
2. Töltse le a PsExec https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Bontsa ki a letöltött fájlt.
4. Indítsa el a parancssort helyi rendszergazdaként.
5. Keresse meg a mappát, ahol PsExec kicsomagolt volt.
6. A parancssorból a következő paranccsal:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname a gép neve, a virtuális gépről a nem megfelelően működő párhuzamos vermet a.

7. Elfogadom gombra kattintva fogadja el a PsExec licencszerződést.

    ![Szoftver licenc szerződés képernyőképe.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Ez a párbeszédpanel csak az első futtatásakor PsExec jelennek meg.

8. A parancssori munkamenetben nyitja meg a virtuális gépen a hibás egymás mellett hibával, miután qwinsta futtassa, és győződjön meg arról, hogy rendelkezésre áll-e az rdp-sxs nevű elem. Ha nem, nem jelenik meg a egy párhuzamos verem megtalálható a virtuális Gépen, ezért a probléma a párhuzamos verem nem kötődik.

    ![Rendszergazdai parancssorból](media/AdministratorCommandPrompt.png)

9. Futtassa a következő parancsot, amelyben szerepelni fog a virtuális Gépet a hibás párhuzamos verem a telepített Microsoft összetevőit.

    ```cmd
        wmic product get name
    ```

10. Futtassa az alábbi parancsot a termékek neveit a fenti lépést.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Távolítsa el az összes olyan termékhez, indítsa el a "Távoli asztallal."

12. Az összes virtuális asztali Windows-összetevők eltávolítása után kövesse az utasításokat az operációs rendszer:

13. Ha az operációs rendszer Windows Server, indítsa újra a virtuális gép, amely a (vagy az Azure Portalon vagy a PsExec eszköz) nem megfelelően működő egymás mellett stack rendelkezett.

Ha az operációs rendszer a Microsoft Windows 10-es, folytassa az alábbi utasításokat:

14. PsExec futtató gépről nyissa meg a Fájlkezelőt, és a virtuális gép a malfunctioned egymás mellett hibával rendszermeghajtóján disablesxsstackrc.ps1 másolja.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname a gép neve, a virtuális gépről a nem megfelelően működő párhuzamos vermet a.

15. Az ajánlott eljárás: a PsExec eszköz indítsa el a Powershellt, és lépjen abba a mappába, az előző lépésben és disablesxsstackrc.ps1 futtassa. Másik lehetőségként futtassa a következő parancsmagokat:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. A parancsmagok végrehajtása után fut, indítsa újra a virtuális Gépet, a hibás egymás mellett hibával.

## <a name="next-steps"></a>További lépések

- Hibaelhárítási Windows virtuális asztal és a kiterjesztés nyomon követi az áttekintést lásd: [hibaelhárítási áttekintése, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- Windows virtuális asztali környezetben egy bérlő és a gazdagép-készlet létrehozása során problémák hibaelhárítása: [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md).
- Egy virtuális gépet (VM) konfigurálása a Windows virtuális asztal során problémák hibaelhárítása: [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- Windows virtuális asztali kapcsolatok problémáinak hibaelhárítása: [távoli asztali kapcsolatok](troubleshoot-client-connection.md).
- A virtuális asztal Windows PowerShell-lel kapcsolatos problémák elhárításához lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Nyissa meg a hibaelhárítás az oktatóanyagot, tekintse meg [oktatóanyag: Resource Manager-sablon üzemelő példányok hibaelhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).