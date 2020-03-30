---
title: Fő Virtuális merevlemez-lemezkép előkészítése és testreszabása – Azure
description: Windows virtuális asztal főlemezképének előkészítése, testreszabása és feltöltése az Azure-ba.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127726"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Fő VHD-rendszerkép létrehozása és testreszabása

Ebből a cikkből megtudhatja, hogyan készíthet elő egy fő virtuális merevlemez-lemez (VHD) lemezképet az Azure-ba való feltöltéshez, beleértve a virtuális gépek (VM-ek) létrehozását és a szoftverek telepítését. Ezek az utasítások a Windows virtuális asztal-specifikus konfiguráció, amely használható a szervezet meglévő folyamatok.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A Windows 10 Enterprise többmunkamenetes munkamenet az Azure Képtárban érhető el. A kép testreszabására két lehetőség van.

Az első lehetőség egy virtuális gép (VM) kiépítése az Azure-ban a [Virtuális gép létrehozása felügyelt lemezképből](../virtual-machines/windows/create-vm-generalized-managed.md)című útmutató utasításait követve, majd ugorjon előre a Szoftver előkészítése és telepítése című [területre.](set-up-customize-master-image.md#software-preparation-and-installation)

A második lehetőség az, hogy a lemezkép helyi létrehozásához a lemezkép letöltésével, egy Hyper-V virtuális gép kiépítésével és az igényeinek megfelelőtestreszabással, amelyet a következő szakaszban fedünk le.

### <a name="local-image-creation"></a>Helyi kép létrehozása

Miután letöltötte a lemezképet egy helyi helyre, nyissa meg a **Hyper-V Manager** t, és hozzon létre egy virtuális gép a virtuális merevlemezt másolt. A következő utasítások egy egyszerű verzió, de talál részletesebb utasításokat [Hozzon létre egy virtuális gép Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Virtuális gép létrehozása a másolt virtuális merevlemezt:

1. Nyissa meg az **Új virtuális gép varázslót**.

2. A Létrehozás megadása lapon válassza az **1.**

    ![Képernyőkép a Létrehozás megadása lapról. A "Generation 1" opció van kiválasztva.](media/a41174fd41302a181e46385e1e701975.png)

3. Az Ellenőrzőpont típusa csoportban tiltsa le az ellenőrzőpontokat a jelölőnégyzet bejelölésének kijelölésével.

    ![Képernyőkép az Ellenőrzőpontok lap Ellenőrzőpont-típus szakaszáról.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

A powershellben a következő parancsmag futtatásával is letilthatja az ellenőrzőpontokat.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Rögzített lemez

Ha virtuális gép egy meglévő virtuális merevlemezről hoz létre, alapértelmezés szerint dinamikus lemezt hoz létre. A **Lemez szerkesztése lehetőség** kiválasztásával rögzített lemezre módosítható, ahogy az az alábbi képen látható. További részletes útmutatást a [Windows VHD vagy VHDX feltöltésének előkészítése az Azure-ba című témakörben talál.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

![Képernyőkép a Lemez szerkesztése beállításról.](media/35772414b5a0f81f06f54065561d1414.png)

A következő PowerShell-parancsmag futtatásával is módosíthatja a lemezt rögzített lemezre.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Szoftver előkészítése és telepítése

Ez a rész az FSLogix és a Windows Defender előkészítését és telepítését, valamint az alkalmazások és a lemezkép beállításjegyzékének néhány alapvető beállítási beállítását ismerteti. 

Ha az Office 365 ProPlust és a OneDrive-ot telepíti a virtuális gépére, nyissa meg [az Office telepítése fő virtuális merevlemez-lemezképre](install-office-on-wvd-master-image.md) című témakört, és kövesse az utasításokat az alkalmazások telepítéséhez. Miután végzett, térjen vissza ehhez a cikkhez.

Ha a felhasználóknak bizonyos üzletági alkalmazásokhoz kell hozzáférniük, javasoljuk, hogy telepítse őket a szakasz utasításainak elvégzése után.

### <a name="set-up-user-profile-container-fslogix"></a>Felhasználói profiltároló beállítása (FSLogix)

Ha az FSLogix-tárolót a lemezkép részeként szeretné használni, kövesse a [Profiltároló létrehozása fájlmegosztással](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)című útmutató utasításait. Ezzel a [rövid útmutatóval](/fslogix/configure-cloud-cache-tutorial/)tesztelheti az FSLogix tároló működését.

### <a name="configure-windows-defender"></a>A Windows Defender konfigurálása

Ha a Windows Defender konfigurálva van a virtuális gépben, győződjön meg arról, hogy úgy van beállítva, hogy a melléklet során ne ellenőrizze a VHD és a VHDX-fájlok teljes tartalmát.

Ez a konfiguráció csak eltávolítja a VHD és VHDX fájlok beolvasását a melléklet során, de nem befolyásolja a valós idejű beolvasást.

A Windows Defender Windows Server rendszeren való konfigurálásáról a [Windows Defender víruskizárások konfigurálása Windows Server rendszeren](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)című témakörben talál további tudnivalókat.

Ha többet szeretne tudni arról, hogy miként állíthatja be a Windows Defender programot bizonyos fájlok vizsgálatból való kizárására, olvassa el a [Kizárások konfigurálása és érvényesítése fájlkiterjesztés és mappahely alapján című témakört.](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)

### <a name="disable-automatic-updates"></a>Automatikus frissítések letiltása

Az automatikus frissítések letiltása a helyi csoportházirenden keresztül:

1. Nyissa **meg a\\Helyi\\csoportházirend-szerkesztő felügyeleti sablonjait Windows-összetevők\\A Windows Update**.
2. Kattintson a jobb gombbal **az Automatikus frissítés konfigurálása** elemre, és állítsa **letiltva**értékre.

A következő parancsot parancssorban is futtathatja az Automatikus frissítések szolgáltatás letiltásához.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>A Windows 10 rendszerű számítógépek startelrendezésének megadása (nem kötelező)

A parancs futtatásával adja meg a Windows 10 rendszerű számítógépek start elrendezését.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Időzóna-átirányítás beállítása

Az időzóna-átirányítás csoportházirend-szinten kényszeríthető, mivel az állomáskészlet ben lévő összes virtuális gép ugyanannak a biztonsági csoportnak a része.

Az időzónák átirányítása:

1. Az Active Directory-kiszolgálón nyissa meg a **Csoportházirend kezelése konzolt**.
2. Bontsa ki a tartományt és a csoportházirend-objektumokat.
3. Kattintson a jobb gombbal a csoportházirend-beállításokhoz létrehozott **csoportházirend-objektumra,** és válassza a **Szerkesztés parancsot.**
4. A **Csoportházirend kezelése szerkesztőben**keresse meg a **Számítógép-konfigurációs** > **házirendek** > **felügyeleti sablonok** > **A Windows-összetevők** > távoli asztali**szolgáltatások** > **távoli asztali munkamenetgazda-eszköz** > és**erőforrás-átirányítás című lapban.**
5. Engedélyezze az **Időzóna átirányításának engedélyezése** beállítást.

Ezt a parancsot a főlemezképen is futtathatja az időzónák átirányításához:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Tárolósegéd letiltása

A Windows 10 Enterprise vagy a Windows 10 Enterprise többmunkamenetes munkamenetet használó Windows Virtual Desktop munkamenet-gazdagépek esetében javasoljuk a Tárolósegéd letiltását. A Tárolás segédet a Tárolás menüBen **letilthatja,** ahogy az az alábbi képernyőképen látható:

![Képernyőkép a Tárolás menü beállítások csoportban. A "Tárolási értelem" beállítás ki van kapcsolva.](media/storagesense.png)

A beállításjegyzékben a következő parancs futtatásával is módosíthatja a beállítást:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>További nyelvi támogatás felvétele

Ez a cikk nem ismerteti a nyelvi és regionális támogatás konfigurálását. További információkért tekintse át a következő cikkeket:

- [Nyelvek hozzáadása Windows-lemezképekhez](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Igény szerinti funkciók](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Igény szerinti nyelvi és régiói funkciók (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Egyéb alkalmazások és beállításjegyzék-konfiguráció

Ez a szakasz az alkalmazás és az operációs rendszer konfigurációját ismerteti. Ebben a szakaszban minden konfiguráció a parancssori és regedit eszközökkel végrehajtható rendszerleíró bejegyzéseken keresztül történik.

>[!NOTE]
>A csoportházirend-objektumok (GSO-k) vagy a rendszerleíró adatbázis importálásával a konfigurációban gyakorlati tanácsokat valósíthat meg. A rendszergazda a szervezet követelményei alapján választhat.

A visszajelzési központ telemetriai adatok gyűjtéséhez a Windows 10 Enterprise többmunkamenetes munkamenetén futtassa ezt a parancsot:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

A Watson-összeomlások javításához futtassa a következő parancsot:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Írja be a következő parancsokat a rendszerleíró adatbázis szerkesztőjében az 5k felbontás támogatásának javításához. Az egymás melletti verem engedélyezése előtt futtatnia kell a parancsokat.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>A kép előkészítése az Azure-ba való feltöltéshez

Miután befejezte a konfigurációt, és telepítette az összes alkalmazást, kövesse a [Windows vhd vagy VHDX előkészítése az Azure-ba a](../virtual-machines/windows/prepare-for-upload-vhd-image.md) lemezkép előkészítéséhez című, Windows VHD vagy VHDX alkalmazás előkészítése című útmutató utasításait.

A rendszerkép feltöltésre való előkészítése után győződjön meg arról, hogy a virtuális gép kikapcsolt vagy felszabadított állapotban marad.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Fő lemezkép feltöltése tárfiókba az Azure-ban

Ez a szakasz csak akkor érvényes, ha a főlemezkép helyileg lett létrehozva.

A következő utasítások ismertetik, hogyan töltheti fel a fő lemezképet egy Azure-tárfiókba. Ha még nem rendelkezik Azure-tárfiókkal, kövesse az ebben a [cikkben](/azure/javascript/tutorial-vscode-static-website-node-03) található utasításokat, hogy hozzon létre egyet.

1. Konvertálja a virtuális gép képét (VHD) Rögzített, ha még nem tette meg. Ha nem konvertálja a képet Rögzített re, nem tudja sikeresen létrehozni a képet.

2. Töltse fel a virtuális merevlemezt egy blobtárolóba a tárfiókban. A Storage Explorer eszközzel gyorsan feltöltheti a [fájlokat.](https://azure.microsoft.com/features/storage-explorer/) A Tárolókezelő eszközről a [cikkben olvashat bővebben.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

    ![Képernyőkép a Microsoft Azure Storage Explorer eszköz keresési ablakáról. A ".vhd vagy vhdx fájlok feltöltése lapblobként (ajánlott)" jelölőnégyzet be van jelölve.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ezután nyissa meg az Azure Portalt a böngészőben, és keressen a "Képek" kifejezésre. A keresésnek a Kép létrehozása laphoz kell **vezetnie,** ahogy az a következő képernyőképen látható:

    ![Az Azure Portal Kép létrehozása lapjáról képernyőkép, amely a lemezkép példaértékeivel van kitöltve.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Miután létrehozta a képet, a következő képernyőképen láthatóhoz hasonló értesítést kell látnia:

    ![Képernyőkép a "sikeresen létrehozott kép" értesítésről.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik egy lemezképpel, létrehozhat vagy frissíthet gazdakészleteket. A gazdakészletek létrehozásáról és frissítéséről az alábbi cikkekben olvashat bővebben:

- [Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Oktatóanyag: Gazdagépkészlet létrehozása az Azure Piactérrel](create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)
- [Profiltároló létrehozása gazdagépkészlet számára fájlmegosztás használatával](create-host-pools-user-profile.md)
- [A Windows Virtual Desktop terheléselosztási módjának beállítása](configure-host-pool-load-balancing.md)
