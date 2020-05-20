---
title: A Windows leállási hibáinak hibaelhárítása – címtárszolgáltatás-Inicializálási hiba
description: Hárítsa el azokat a problémákat, amelyekben egy Active Directory tartományvezérlő virtuális gépe (VM) az Azure-ban beragadt egy hurkot, amely azt jelzi, hogy újra kell indítani.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665137"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>A Windows leállási hibáinak hibaelhárítása – címtárszolgáltatás-Inicializálási hiba

Ez a cikk olyan problémák megoldását ismerteti, amelyekben egy Active Directory tartományvezérlő virtuális gép (VM) az Azure-ban, hurkot tartalmaz, és azt állítja be, hogy újra kell indítani.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, a képernyőfelvétel azt mutatja, hogy a virtuális gépnek egy hiba miatt újra kell indítania a **0XC00002E1** a Windows Server 2008 R2 rendszerben, vagy a Windows Server 2012 vagy újabb verzió **0xC00002E2** .

![A Windows Server 2012 indítási képernyőjének állapota "a számítógép hibát észlelt, és újra kell indítani. Most gyűjtünk néhány hibaüzenetet, majd újraindulunk. "](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Ok

Az **0xC00002E2** hibakód a **STATUS_DS_INIT_FAILURE**, a hibakód pedig az **STATUS_DS_CANT_STARTt** **jelképezi** . Mindkét hiba akkor fordul elő, ha probléma van a címtárszolgáltatás szolgáltatással.

Ahogy az operációs rendszer elindul, a helyi biztonsági hitelesítő kiszolgáló (**Lsass. exe**) automatikusan újraindul, amely hitelesíti a felhasználói bejelentkezéseket. A hitelesítés nem lehetséges, ha a virtuális gépen futó operációs rendszer olyan tartományvezérlő, amely nem rendelkezik írási/olvasási hozzáféréssel a helyi Active Directory adatbázisához. **Active Directory (ad)** elérésének hiánya miatt az Lsass. exe nem tud hitelesíteni, és az operációs rendszer újraindítására kényszerül.

Ezt a hibát a következő feltételek bármelyike okozhatja:

- Nincs hozzáférése a helyi AD-adatbázist (NTDS) tároló lemezhez **. DIT**).
- A helyi AD-adatbázist (NTDS) tároló lemez DIT) elfogyott a szabad terület.
- A helyi AD-adatbázis (NTDS). DIT) fájl hiányzik.
- A virtuális gépnek több lemeze van, és a Tárolóhálózati (SAN) házirend helytelenül van konfigurálva. A TÁROLÓHÁLÓZATI házirend nem a **ONLINEALL**értékre van állítva, és a nem operációsrendszer-lemezek kapcsolat nélküli módban vannak csatolva a Lemezkezelés eszközben.
- A helyi AD-adatbázis (NTDS). DIT) fájl sérült.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Szabad terület a lemezen.
1. Győződjön meg arról, hogy az AD-adatbázist tartalmazó meghajtó csatlakoztatva van.
1. Címtárszolgáltatások helyreállító módjának engedélyezése.
1. **Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
1. Hozza létre újra a virtuális gépet.
1. Konfigurálja újra a TÁROLÓHÁLÓZATI házirendet.

> [!NOTE]
> Ha ezt a hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="free-up-space-on-disk"></a>Szabadítson fel lemezterületet a lemezen

Mivel a lemez most már egy javító virtuális géphez van csatolva, ellenőrizze, hogy a Active Directory belső adatbázisát tároló lemez elegendő lemezterülettel rendelkezik-e a megfelelő végrehajtáshoz.

1. Győződjön meg arról, hogy a lemez megtelt-e, és kattintson a jobb gombbal a meghajtóra, majd válassza a **Tulajdonságok parancsot**.
1. Ha a lemez 300 MB-nál kevesebb szabad területtel rendelkezik, [bontsa ki azt legfeljebb 1 TB](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)-ra a PowerShell használatával.
1. Ha a lemez elérte a felhasznált lemezterület 1 TB-át, hajtson végre egy Lemezkarbantartó-karbantartási műveletet.

   1. A PowerShell használatával [válassza le az adatlemezt](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell) a hibás virtuális gépről.
   1. A hibás virtuális gépről való leválasztás után [csatlakoztassa az adatlemezt](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm) egy működő virtuális géphez.
   1. A [Lemezkarbantartó eszközzel](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) szabadítson fel további helyet.

1. Nem **kötelező** – Ha további tárhelyre van szükség, nyisson meg egy cmd-példányt, és írja be a `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` parancsot a detöredezettség a meghajtón való végrehajtásához:

  * A parancsban cserélje le az `<LETTER ASSIGNED TO THE OS DISK>` operációsrendszer-lemez betűjét. Ha például a lemez betűjele `F:` , akkor a parancs a következő lesz: `defrag F: /u /x /g` .

  * A töredezettség szintjétől függően a detöredezettség eltarthat egy óráig.

Ha van elég hely a lemezen, folytassa a következő feladattal.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Győződjön meg arról, hogy a Active Directory adatbázist tartalmazó meghajtó csatlakoztatva van

1. Nyisson meg egy rendszergazda jogú CMD-példányt, és futtassa a következő parancsokat:

   1. Beállításjegyzékbeli fájl betöltése:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      A megjelölés `f:` feltételezi, hogy a lemez meghajtó `F:` . Használja az operációsrendszer-lemezt tartalmazó meghajtó betűjelét.

   1. Határozza meg az NTDS meghajtóbetűjelét és mappáját **. DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Beállításjegyzék-fájl eltávolítása:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. A Azure Portal használatával ellenőrizze, hogy a meghajtón, ahol az NTDS. A DIT beállítása megtörténik, a rendszer hozzáadja a virtuális géphez.
1. A Lemezkezelés konzoljának a vendég operációs rendszerből való használatával ellenőrizze, hogy az NTDS-t tartalmazó lemez. A DIT online állapotban van.
   1. A Lemezkezelés eszköz megtalálható a **felügyeleti eszközök > számítógép-kezelés > tárolóban**, vagy a parancs használatával érhető el `diskmgmt.msc` egy cmd-példányban.
1. Ha a lemez nincs csatlakoztatva a virtuális géphez, csatlakoztassa újra az adatlemezt a probléma megoldásához.

   Ha a lemez szabályosan lett csatolva, folytassa a következő feladattal.

### <a name="enable-directory-services-restore-mode"></a>Címtárszolgáltatások helyreállító módjának engedélyezése

Állítsa be úgy a virtuális gépet, hogy a címtárszolgáltatások **helyreállító módjának (címtárszolgáltatás** -javító mód) módban induljon el az NTDS meglétének ellenőrzésének mellőzése érdekében. DIT-fájl a rendszerindítás során.

1. A folytatás előtt győződjön meg arról, hogy végrehajtotta az előző feladatokat, hogy a lemezt egy javító virtuális géphez csatolja, és meghatározta, hogy melyik lemez az NTDS. A DIT-fájl helye:.
1. Ha emelt szintű CMD-példányt használ, sorolja fel az adott áruház rendszerindítási partíciójának adatait, és keresse meg az azonosítót az aktív partícióról:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Cserélje le az `< Drive Letter >` t az előző lépésekben meghatározott betűre.

   ![A képernyőfelvétel egy emelt szintű CMD-példányt mutat be a "bcdedit/Store <meghajtóbetűjel>: \boot\bcd/enum" parancs beírása után, amely az azonosítóval rendelkező Windows rendszerindítási kezelőt jeleníti meg.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Engedélyezze a `safeboot DsRepair` jelzőt a rendszerindítási partíción:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Cserélje le az `< Drive Letter >` és az `< Identifier >` értéket az előző lépésekben meghatározott értékekre.

1. Kérdezze le újra a rendszerindítási beállításokat, hogy a módosítás megfelelően be legyen állítva.

   ![A képernyőképen egy emelt szintű CMD-példány jelenik meg, miután engedélyezte a safeboot DsRepair jelzőt.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriaképek gyűjtését

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt egy rendszergazda jogú parancssor-munkamenet (Futtatás rendszergazdaként) megnyitásával, és futtassa a következő parancsokat.

1. A soros konzol engedélyezése:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe legalább a virtuális gép memóriájának méretétől (RAM) egyenlő.

  1. Ha nincs elég hely az operációsrendszer-lemezen, módosítsa a memóriakép fájljának helyét, és tekintse meg, hogy a virtuális géphez csatolt minden olyan adatlemezhez, amely elegendő szabad hellyel rendelkezik.

     A hely módosításához cserélje le az `%SystemRoot%` adatlemez meghajtóbetűjelét (például `F:` :) a következő parancsokra.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Az operációs rendszer memóriaképének engedélyezéséhez a következő konfiguráció javasolt:

  **Sérült operációsrendszer-lemez betöltése**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Engedélyezés a ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Engedélyezés a ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Sérült operációsrendszer-lemez eltávolítása**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

1. A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

### <a name="reconfigure-the-storage-area-network-policy"></a>A tárterület hálózati házirendjének újrakonfigurálása

1. A Címtárszolgáltatások helyreállító módjában az egyetlen elérhető felhasználó a helyreállítási rendszergazda, amelyet a virtuális gép tartományvezérlővé való előléptetése során használt. Minden más felhasználó hitelesítési hibát jelez.

   1. Ha nem áll rendelkezésre más tartományvezérlő, helyileg kell bejelentkeznie `.\administrator` a vagy `machinename\administrator` a és a Címtárszolgáltatások helyreállító módjának jelszavával.

1. Állítsa be a TÁROLÓHÁLÓZATI házirendet úgy, hogy az összes lemez online állapotú legyen.

   1. Nyisson meg egy rendszergazda jogú CMD-példányt, és írja be a parancsot `DISKPART` .
   1. A lemezek listájának lekérdezése.

      `DISKPART> list disk`

   1. Adja meg a következő parancsokat az online állapotba hozni kívánt lemez kiválasztásához és a TÁROLÓHÁLÓZATI házirend módosításához:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. A probléma javítása után ellenőrizze, hogy a jelző el lett-e `DsRepair safeboot` távolítva:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Indítsa újra a virtuális gépet.

   > [!NOTE]
   > Ha a virtuális gépet áttelepítette a helyszíni rendszerből, és több tartományvezérlőt szeretne áttelepíteni az Azure-ba, érdemes figyelembe vennie az alábbi cikk lépéseit, hogy megakadályozza a problémát a jövőbeli áttelepítésekben:
   >
   > [Meglévő helyszíni Hyper-V-tartományvezérlők feltöltése az Azure-ba Azure PowerShell használatával](https://support.microsoft.com/help/2904015)
