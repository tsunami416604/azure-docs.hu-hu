---
title: Bérlői és gazdagépi készlet létrehozása a Windows Virtual Desktopban – Azure
description: Hogyan oldhatók meg a bérlők és a munkamenet-gazdagépek virtuális gépe (VM) Windows rendszerű virtuális asztali környezetben való konfigurálásakor felmerülő problémák.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 03a8e8063f1a66b929311f09bf8e20cd4b951e43
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163302"
---
# <a name="tenant-and-host-pool-creation"></a>Bérlői és gazdagépcsoport létrehozása

Ez a cikk a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek (VM-EK) konfigurálásakor felmerülő problémák elhárítására használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Jelenleg nem veszünk fel támogatási eseteket, amíg a Windows rendszerű virtuális asztal előzetes verzióban érhető el. Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="vms-are-not-joined-to-the-domain"></a>A virtuális gépek nem csatlakoznak a tartományhoz

Kövesse ezeket az utasításokat, ha problémákat tapasztal a virtuális gépek tartományhoz való csatlakoztatásakor.

- Csatlakoztassa manuálisan a virtuális gépet a [Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) vagy a tartományhoz való csatlakozás [sablonjának](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)használatával.
- Próbálja pingelni a tartománynevet a parancssorból a virtuális gépen.
- Tekintse át a tartományhoz való [Csatlakozás hibaüzeneteit](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)a következő témakörben: tartományi csatlakozási hibaüzenetek.

### <a name="error-incorrect-credentials"></a>Hiba: Helytelen hitelesítő adatok

**Okozhat** A hitelesítő adatoknak a Azure Resource Manager sablon felületén megjelenő javításokban való megadásának elírása történt.

**Javítsa ki** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Manuálisan adja hozzá a virtuális gépeket egy tartományhoz.
- A sablon újbóli üzembe helyezése a hitelesítő adatok megerősítése után. Lásd: [állomáslista létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)-lel.
- Csatlakoztassa a virtuális gépeket egy tartományhoz egy olyan sablonnal, amely egy [meglévő Windows-alapú virtuális GÉPET ad tartományhoz csatlakozik](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Hiba: Felhasználói bevitelre való várakozás időtúllépése

**Okozhat** A tartományhoz való csatlakozás végrehajtásához használt fiók többtényezős hitelesítéssel (MFA) rendelkezhet.

**Javítsa ki** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Ideiglenesen távolítsa el az MFA-t a fiókhoz.
- Használjon egy szolgáltatásfiókot.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Hiba: A kiépítés során használt fiók nem rendelkezik a művelet végrehajtásához szükséges engedélyekkel

**Okozhat** A használt fióknak nincs engedélye a virtuális gépek tartományhoz való csatlakoztatására a megfelelőség és a szabályozások miatt.

**Javítsa ki** A megoldáshoz hajtsa végre az alábbi műveletek egyikét.

- Olyan fiókot használjon, amely a rendszergazda csoport tagja.
- Adja meg a szükséges engedélyeket a használt fiókhoz.

### <a name="error-domain-name-doesnt-resolve"></a>Hiba: A tartománynév nem oldható fel

**1. ok:** A virtuális gépek olyan virtuális hálózaton vannak, amely nincs társítva a virtuális hálózathoz (VNET), ahol a tartomány található.

**1. javítás:** Hozzon létre VNET-társítást a virtuális gépek kiépített VNET és a tartományvezérlőt (DC) futtató VNET között. Lásd: [virtuális hálózati társ-erőforrás-kezelő létrehozása, különböző](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)előfizetések.

**2. ok:** Azure Active Directory Domain Services (Azure AD DS) használata esetén a virtuális hálózat nem frissült a DNS-kiszolgáló beállításaival, hogy azok a felügyelt tartományvezérlőkre mutassanak.

**2. javítás:** Az Azure AD DSt tartalmazó virtuális hálózat DNS-beállításainak frissítéséhez tekintse meg [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance#update-dns-settings-for-the-azure-virtual-network)című témakört.

**3. ok:** A hálózati adapter DNS-kiszolgálójának beállításai nem a virtuális hálózaton lévő megfelelő DNS-kiszolgálóra mutatnak.

**3. javítás:** Hajtsa végre a következő műveletek egyikét a megoldáshoz a [DNS-kiszolgálók módosítása] című témakörben leírtak szerint.
- Módosítsa a hálózati adapter DNS-kiszolgálójának beállításait **Egyéni** értékre a [DNS-kiszolgálók módosítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers) lépésekkel, és a virtuális hálózaton lévő DNS-kiszolgálók magánhálózati IP-címeinek megadásával.
- Módosítsa a hálózati adapter DNS-kiszolgálójának beállításait úgy, hogy azok a **virtuális hálózatról öröklik** a [DNS-kiszolgálók módosításának](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers)lépéseit, majd módosítsa a virtuális hálózat DNS-KISZOLGÁLÓJÁNAK beállításait a [DNS-kiszolgálók módosítása](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers)című témakör lépéseit követve.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Nincs telepítve a Windows rendszerű virtuális asztali ügynök és a Windows rendszerű virtuális asztali rendszerindító betöltő

A virtuális gépek üzembe helyezésének ajánlott módja a **Windows rendszerű virtuális asztali alkalmazáskészlet sablonjának létrehozása és kiépítése** Azure Resource Manager. A sablon automatikusan telepíti a Windows rendszerű virtuális asztali ügynököt és a Windows virtuális asztali ügynök rendszerindítási betöltőjét.

Kövesse ezeket az utasításokat az összetevők telepítésének megerősítéséhez és a hibaüzenetek ellenőrzéséhez.

1. Ellenőrizze, hogy a két összetevő telepítve van-e a **Vezérlőpult** > **programok** > **programok és szolgáltatások**elemének ellenőrzésével. Ha a **Windows rendszerű virtuális asztali ügynök** és a **Windows rendszerű virtuális asztali ügynök** rendszerindító betöltőprogramja nem látható, akkor azok nincsenek telepítve a virtuális gépen.
2. Nyissa meg a **fájlkezelőt** , és navigáljon a **C:\Windows\Temp\scriptlogs.log**. Ha a fájl hiányzik, az azt jelzi, hogy a két összetevőt telepítő PowerShell DSC nem tudott futni a megadott biztonsági környezetben.
3. Ha a fájl **C:\Windows\Temp\scriptlogs.log** van, nyissa meg, és ellenőrizze a hibaüzeneteket.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Hiba: A Windows rendszerű virtuális asztali ügynök és a Windows rendszerű virtuális asztali ügynök rendszerindítási betöltője hiányzik. A C:\Windows\Temp\scriptlogs.log is hiányzik

**1. ok:** A Azure Resource Manager sablon bemenete során megadott hitelesítő adatok helytelenek voltak, vagy az engedélyek elégtelenek voltak.

**1. javítás:** Manuálisan adja hozzá a hiányzó összetevőket a virtuális gépekhez a [gazdagép létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)-lel használatával.

**2. ok:** A PowerShell DSC elindítása és végrehajtása sikerült, de nem fejeződött be, mert nem tud bejelentkezni a Windows rendszerű virtuális asztalra, és beszerezni a szükséges információkat.

**2. javítás:** Erősítse meg az alábbi listán szereplő elemeket.

- Győződjön meg arról, hogy a fiók nem rendelkezik MFA-val.
- Győződjön meg arról, hogy a bérlő neve pontos, és a bérlő létezik a Windows rendszerű virtuális asztalon.
- Győződjön meg arról, hogy a fiók legalább RDS közreműködői engedéllyel rendelkezik.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Hiba: A hitelesítés nem sikerült, hiba a C:\Windows\Temp\scriptlogs.log

**Okozhat** A PowerShell DSC végrehajtása sikerült, de nem tudott csatlakozni a Windows rendszerű virtuális asztalhoz.

**Javítsa ki** Erősítse meg az alábbi listán szereplő elemeket.

- Manuálisan regisztrálja a virtuális gépeket a Windows Virtual Desktop szolgáltatással.
- Ellenőrizze, hogy a Windows rendszerű virtuális asztalhoz való csatlakozáshoz használt fiók rendelkezik-e engedéllyel a bérlőn a gazdagépek létrehozásához.
- A fiók megerősítése nem rendelkezik MFA-val.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>A Windows rendszerű virtuális asztali ügynök nem regisztrálja magát a Windows rendszerű virtuális asztali szolgáltatással

Ha a Windows rendszerű virtuális asztali ügynök először van telepítve a munkamenet-gazdagép virtuális gépei számára (manuálisan vagy a Azure Resource Manager sablonon és a PowerShell DSC-n keresztül), akkor egy regisztrációs jogkivonatot biztosít. A következő szakasz a Windows rendszerű virtuális asztali ügynökre és a jogkivonatra vonatkozó hibaelhárítási problémákat ismerteti.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Hiba: A Get-RdsSessionHost parancsmagban bejelentkezett állapot a nem elérhető állapotot jeleníti meg.

![A Get-RdsSessionHost parancsmag a nem elérhető állapotot jeleníti meg.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Okozhat** Az ügynök nem tudja frissíteni magát egy új verzióra.

**Javítsa ki** Az ügynök manuális frissítéséhez kövesse az alábbi utasításokat.

1. Töltse le az ügynök új verzióját a munkamenet-gazda virtuális gépre.
2. Indítsa el a Feladatkezelő eszközt, és a szolgáltatás lapon állítsa le a RDAgentBootLoader szolgáltatást.
3. Futtassa a telepítőt a Windows rendszerű virtuális asztali ügynök új verziójához.
4. Ha a rendszer kéri a regisztrációs tokent, távolítsa el a INVALID_TOKEN bejegyzést, és nyomja meg a Next (új token nem szükséges) elemet.
5. Fejezze be a telepítővarázsló lépéseit.
6. Nyissa meg a Feladatkezelő eszközt, és indítsa el a RDAgentBootLoader szolgáltatást.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Hiba:  A Windows rendszerű virtuális asztali ügynök beállításjegyzékbeli bejegyzésének IsRegistered a 0 értéket jeleníti meg

**Okozhat** A regisztrációs jogkivonat lejárt, vagy a (999999) lejárati értékkel lett létrehozva.

**Javítsa ki** Az ügynök beállításjegyzékbeli hibájának kijavításához kövesse az alábbi utasításokat.

1. Ha már van regisztrációs jogkivonat, távolítsa el a Remove-RDSRegistrationInfo.
2. Új jogkivonat előállítása az RDS-NewRegistrationInfo.
3. Győződjön meg arról, hogy a-ExpriationHours paraméter értéke 72 (a maximális érték a 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Hiba: A Windows rendszerű virtuális asztali ügynök nem jelent szívverést a Get-RdsSessionHost futtatásakor

**1. ok:** A RDAgentBootLoader szolgáltatás le lett állítva.

**1. javítás:** Indítsa el a Feladatkezelőt, és ha a szolgáltatás lap leállított állapotot jelez a RDAgentBootLoader szolgáltatáshoz, indítsa el a szolgáltatást.

**2. ok:** Lehet, hogy a 443-es port be van zárva.

**2. javítás:** A 443-es port megnyitásához kövesse az alábbi utasításokat.

1. Győződjön meg arról, hogy a 443-es port meg van nyitva a PSPing eszköz a [Sysinternal Tools](https://docs.microsoft.com/sysinternals/downloads/psping)webhelyről való letöltés
2. Telepítse a PSPing a munkamenet-gazda virtuális gépre, amelyen az ügynök fut.
3. Nyissa meg a parancssort rendszergazdaként, és adja ki az alábbi parancsot:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Győződjön meg arról, hogy a PSPing adatokat kapott a RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>A Windows rendszerű virtuális asztalok egymás melletti veremével kapcsolatos hibák elhárítása

A Windows rendszerű virtuális asztali párhuzamos verem automatikusan települ a Windows Server 2019-es verzióra. A Microsoft Installer (MSI) használatával telepítse a párhuzamos stacket a Microsoft Windows Server 2016 vagy a Windows Server 2012 R2 rendszerre. A Microsoft Windows 10 rendszerben a Windows rendszerű virtuális asztali párhuzamos verem engedélyezve van a **enablesxstackrs. ps1**használatával.

A munkamenet-gazdagépen futó virtuális gépeken a következő három fő módszert kell telepíteni vagy engedélyezni:

- A Azure Resource Manager **új Windowsos virtuális asztali alkalmazáskészlet-sablon létrehozása és kiépítése**
- A fő rendszerképbe való felvétel és engedélyezés
- Manuálisan telepítve vagy engedélyezve az egyes virtuális gépeken (vagy bővítményekkel/PowerShell-lel)

Ha problémákat tapasztal a Windowsos virtuális asztal egymás melletti veremben, írja be a **qwinsta** parancsot a parancssorból annak megerősítéséhez, hogy a párhuzamos verem telepítve vagy engedélyezve van.

A **qwinsta** kimenete az **RDP-SxS** fogja listázni a kimenetben, ha a párhuzamos verem telepítve van és engedélyezve van.

![Az egymás melletti verem telepítve van vagy engedélyezve van a kimenetben RDP-SxS-ként felsorolt qwinsta.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Vizsgálja meg az alább felsorolt beállításjegyzék-bejegyzéseket, és ellenőrizze, hogy az értékek egyeznek-e. Ha a beállításkulcsok hiányoznak, vagy az értékek nem egyeznek, kövesse a [gazdagép létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) -lel című témakör utasításait a párhuzamos verem újratelepítésének módjáról.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Hiba: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE-hibakód.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Okozhat** A párhuzamos verem nincs telepítve a munkamenet-gazdagép virtuális gépén.

**Javítsa ki** Kövesse ezeket az utasításokat a párhuzamos verem telepítéséhez a munkamenet-gazda virtuális gépen.

1. A RDP protokoll (RDP) használatával közvetlenül a munkamenet-gazda virtuális gépre kerül a helyi rendszergazdaként.
2. Ha még nem tette meg, töltse le és importálja [a Windows rendszerű virtuális asztali PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) -modult, amelyet a PowerShell-munkamenetben szeretne használni, majd futtassa ezt a parancsmagot a fiókjába való bejelentkezéshez:
    
    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```
    
3. Telepítse a párhuzamos veremet a [gazdagép létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)használatával című részből.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>A Windows rendszerű virtuális asztalok egymás melletti veremének kijavítása

Vannak olyan ismert körülmények, amelyek az egymás melletti verem meghibásodását okozhatják:

- Nem követi a lépések helyes sorrendjét a párhuzamos verem engedélyezéséhez
- Automatikus frissítés a Windows 10 továbbfejlesztett sokoldalú lemezére (EVD)
- Hiányzik a Távoli asztal munkamenet-gazdagép (RDSH) szerepkör
- Enablesxsstackrc. ps1 többszöri futtatása
- A enablesxsstackrc. ps1 futtatása olyan fiókban, amely nem rendelkezik helyi rendszergazdai jogosultságokkal

Az ebben a szakaszban található utasítások segítséget nyújtanak a Windows rendszerű virtuális asztal egymás melletti verem eltávolításához. A párhuzamos verem eltávolítása után nyissa meg a "virtuális gép regisztrálása a Windows rendszerű virtuális asztali készlettel" című részt a [gazdagép létrehozása a PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) -lel című részből a párhuzamos verem újratelepítéséhez.

A szervizelés futtatásához használt virtuális gépnek ugyanazon az alhálózaton és tartományban kell lennie, mint a virtuális gépnek, amely a meghibásodott párhuzamos verem.

Az alábbi utasításokat követve futtassa a szervizelést ugyanarról az alhálózatról és tartományról:

1. Kapcsolódjon a standard RDP protokoll (RDP) szolgáltatáshoz a virtuális géphez, ahonnan a javítást alkalmazni fogja.
2. Töltse le a https://docs.microsoft.com/sysinternals/downloads/psexec PsExec a alkalmazásból.
3. Bontsa ki a letöltött fájlt.
4. Indítsa el a parancssort helyi rendszergazdaként.
5. Navigáljon a mappához, ahol a PsExec ki lett csomagolva.
6. A parancssorban használja a következő parancsot:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >A VMname a virtuális gép számítógépneve, amely a meghibásodott párhuzamos verem.

7. Az elfogadás gombra kattintva fogadja el a PsExec-licencszerződést.

    ![Szoftverlicenc-szerződés képernyőképe](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Ez a párbeszédpanel csak a PsExec első futtatásakor fog megjelenni.

8. Miután a parancssor-munkamenet megnyílik a virtuális gépen a meghibásodott párhuzamos verem használatával, futtassa a qwinsta, és győződjön meg arról, hogy az RDP-SxS nevű bejegyzés elérhető. Ha nem, egy párhuzamos verem nem található a virtuális gépen, így a probléma nem a párhuzamos veremhez van kötve.

    ![Rendszergazdai parancssor](media/AdministratorCommandPrompt.png)

9. Futtassa a következő parancsot, amely felsorolja a virtuális gépen telepített Microsoft-összetevőket a meghibásodott párhuzamos verem használatával.

    ```cmd
        wmic product get name
    ```

10. Futtassa az alábbi parancsot a fenti lépésből származó termékek neveivel.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Távolítsa el az összes olyan terméket, amely a következővel kezdődik: "Távoli asztal."

12. A Windows rendszerű virtuális asztali összetevők eltávolítása után kövesse az operációs rendszerének utasításait:

13. Ha az operációs rendszer a Windows Server, indítsa újra azt a virtuális gépet, amely a (Azure Portal vagy a PsExec eszközből származó) hibás működésű párhuzamos veremmel rendelkezik.

Ha az operációs rendszer Microsoft Windows 10, folytassa az alábbi utasításokkal:

14. A PsExec-t futtató virtuális gépen nyissa meg a fájlkezelőt, és másolja a disablesxsstackrc. ps1 fájlt a virtuális gép rendszermeghajtójába a meghibásodott párhuzamos verem használatával.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >A VMname a virtuális gép számítógépneve, amely a meghibásodott párhuzamos verem.

15. Az ajánlott folyamat: a PsExec eszközből indítsa el a PowerShellt, és navigáljon az előző lépésben található mappára, és futtassa a disablesxsstackrc. ps1 parancsot. Azt is megteheti, hogy a következő parancsmagokat futtatja:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Ha a parancsmagok futása befejeződött, indítsa újra a virtuális gépet a meghibásodott párhuzamos verem használatával.

## <a name="remote-licensing-model-is-not-configured"></a>A távoli licencelési modell nincs konfigurálva

Ha rendszergazdai fiókkal jelentkezik be a Windows 10-es nagyvállalati munkamenetbe, előfordulhat, hogy a "Távoli asztal licencelési mód nincs konfigurálva, Távoli asztali szolgáltatások X nap múlva nem fog működni. A Csatlakozáskezelő kiszolgálón a Kiszolgálókezelő használatával adhatja meg a Távoli asztal licencelési módot. " Ha ezt az üzenetet látja, az azt jelenti, hogy a licencelési módot manuálisan kellkonfigurálnia felhasználónként.

A licencelési mód manuális konfigurálása:  

1. Lépjen a **Start menü** keresőmezőbe, majd keresse meg és nyissa meg a **gpedit. msc fájlt** a helyi csoportházirend-szerkesztő eléréséhez. 
2. Ugrás a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > távoli asztali szolgáltatásokTávoliasztal > **munkamenet-gazdagép**  >  **Licencelés**. 
3. Válassza **a távoli asztal licencelési mód beállítása** lehetőséget, ésmódosítsa felhasználónként értékre.

Jelenleg tekintjük meg az értesítési és türelmi idő időtúllépésével kapcsolatos problémákat, és megtervezjük, hogy egy jövőbeli frissítéssel foglalkozzon velük. 

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához lásd: [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- Az előzetes verziójú szolgáltatással kapcsolatos további tudnivalókért lásd: a [Windows rendszerű virtuális asztali előnézet környezete](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- A következő témakörben talál útmutatást [a hibakereséshez: oktatóanyag: Resource Manager-sablonok központi telepítésének](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)hibája.
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
