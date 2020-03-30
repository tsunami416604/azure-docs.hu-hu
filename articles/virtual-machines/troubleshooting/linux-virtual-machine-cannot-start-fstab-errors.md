---
title: Linux virtuális gép indítási problémáinak elhárítása fstab hibák miatt | Microsoft dokumentumok
description: A linuxos virtuális gép indításának okát és a probléma megoldását ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351147"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Linux virtuális gép indítási problémáinak elhárítása fstab hibák miatt

Biztonságos rendszerhéj (SSH) kapcsolat használatával nem tud csatlakozni egy Azure Linux virtuális géphez.You can't connect to a Azure Linux Virtual Machine (VM) using a Secure Shell (SSH) connection. Amikor futtatja a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) funkciót az [Azure Portalon,](https://portal.azure.com/)az alábbi példákhoz hasonló naplóbejegyzéseket láthat:

## <a name="examples"></a>Példák

Az alábbi példák példák a lehetséges hibákra.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>1. példa: Az scsi-azonosító t az univerzálisan egyedi azonosító (UUID) helyett az SCSI-azonosító csatlakoztatja a lemezhez.

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>2. példa: Hiányzik egy nem csatlakoztatott eszköz a CentOS-on

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>3. példa: A virtuális gép nem indítható el fstab helytelen konfiguráció miatt, vagy mert a lemez már nincs csatlakoztatva

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>4. példa: A soros naplóbejegyzés hibás UUID azonosítót jelenít meg

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Ez a probléma akkor fordulhat elő, ha a fájlrendszertábla (fstab) szintaxisa helytelen, vagy ha az "/etc/fstab" fájlban lévő bejegyzéshez leképezett szükséges adatlemez nincs csatolva a virtuális géphez.

## <a name="resolution"></a>Megoldás:

A probléma megoldásához indítsa el a virtuális gépet vészhelyzeti módban az Azure virtuális gépek soros konzoljának használatával. Ezután az eszközzel javítsa ki a fájlrendszert. Ha a soros konzol nincs engedélyezve a virtuális gép, nyissa meg [a virtuális gép offline javítása](#repair-the-vm-offline) szakaszban.

## <a name="use-the-serial-console"></a>A soros konzol használata

### <a name="using-single-user-mode"></a>Egyfelhasználós mód használata

1. Csatlakozzon [a soros konzolhoz](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Soros konzol használata egyfelhasználós [módba történő alkalmazásával egyfelhasználós módban](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. Miután a vm elindult egyfelhasználós módban. Az fstab fájl megnyitásához használja kedvenc szövegszerkesztőjét. 

   ```
   # nano /etc/fstab
   ```

4. Tekintse át a felsorolt fájlrendszereket. Az fstab fájl minden sora egy olyan fájlrendszert jelöl, amely a virtuális gép indításakor van csatlakoztatva. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a man fstab parancsot. A kezdési hiba elhárításához tekintse át az egyes sorokat, és győződjön meg arról, hogy az a szerkezet és a tartalom szempontjából is helyes.

   > [!Note]
   > * Az egyes sorok mezőit tabulátorok vagy szóközök választják el egymástól. Az üres sorokat a program figyelmen kívül hagyja. Azok a sorok, amelyek első karakterként számjellel (#) rendelkeznek, megjegyzések. A megjegyzésekkel kapcsolatos sorok megmaradhatnak az fstab fájlban, de nem lesznek feldolgozva. Azt javasoljuk, hogy a sorok eltávolítása helyett olyan fstab sorokat fűzzön hozzá, amelyekben nem biztos.
   > * Ahhoz, hogy a virtuális gép helyreálljon és elinduljon, a fájlrendszer partíciói legyenek az egyetlen szükséges partíciók. A virtuális gép alkalmazáshibákat tapasztalhat a további megjegyzésekkel kapcsolatos partíciókkal kapcsolatban. Azonban a virtuális gép kell kezdeni a további partíciók nélkül. Később a hozzáfűzött sorok megjegyzését is elhagyhatja.
   > * Azt javasoljuk, hogy az azure-beli virtuális gépeken csatlakoztassa az adatlemezeket a fájlrendszerpartíció UUID használatával. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot.
   > * A failfail beállítás segít győződjön meg arról, hogy a virtuális gép elindul akkor is, ha a fájlrendszer sérült, vagy a fájlrendszer nem létezik indításkor. Azt javasoljuk, hogy használja a failfail opciót az fstab fájlban, hogy engedélyezze az indításfolytatását, miután hibák történtek a virtuális gép indításához nem szükséges partíciókon.

5. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

6. Mentse az fstab fájl módosításait.

7. Indítsa újra a virtuális gépet az alábbi paranccsal.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Használhatja a "ctrl+x" parancsot is, amely szintén újraindítja a vm-et.


8. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie a bash promptot a portálon. Ellenőrizze, hogy tud-e csatlakozni a virtuális géphez.

### <a name="using-root-password"></a>Gyökérjelszó használata

1. Csatlakozzon [a soros konzolhoz](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Jelentkezzen be a rendszerbe helyi felhasználó és jelszó használatával.

   > [!Note]
   > SSH-kulccsal nem jelentkezhet be a rendszerbe a soros konzolon.

3. Keresse meg azt a hibát, amely azt jelzi, hogy a lemez nincs csatlakoztatva. A következő példában a rendszer olyan lemezt próbált csatolni, amely már nem volt jelen:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Csatlakozzon a virtuális géphez a gyökérjelszó (Red Hat-alapú virtuális gépek) használatával.

5. Az fstab fájl megnyitásához használja kedvenc szövegszerkesztőjét. A lemez csatlakoztatása után futtassa a következő parancsot a Nano rendszerre:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Tekintse át a felsorolt fájlrendszereket. Az fstab fájl minden sora egy olyan fájlrendszert jelöl, amely a virtuális gép indításakor van csatlakoztatva. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a man fstab parancsot. A kezdési hiba elhárításához tekintse át az egyes sorokat, és győződjön meg arról, hogy az a szerkezet és a tartalom szempontjából is helyes.

   > [!Note]
   > * Az egyes sorok mezőit tabulátorok vagy szóközök választják el egymástól. Az üres sorokat a program figyelmen kívül hagyja. Azok a sorok, amelyek első karakterként számjellel (#) rendelkeznek, megjegyzések. A megjegyzésekkel kapcsolatos sorok megmaradhatnak az fstab fájlban, de nem lesznek feldolgozva. Azt javasoljuk, hogy a sorok eltávolítása helyett olyan fstab sorokat fűzzön hozzá, amelyekben nem biztos.
   > * Ahhoz, hogy a virtuális gép helyreálljon és elinduljon, a fájlrendszer partíciói legyenek az egyetlen szükséges partíciók. A virtuális gép alkalmazáshibákat tapasztalhat a további megjegyzésekkel kapcsolatos partíciókkal kapcsolatban. Azonban a virtuális gép kell kezdeni a további partíciók nélkül. Később a hozzáfűzött sorok megjegyzését is elhagyhatja.
   > * Azt javasoljuk, hogy az azure-beli virtuális gépeken csatlakoztassa az adatlemezeket a fájlrendszerpartíció UUID használatával. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot.
   > * A failfail beállítás segít győződjön meg arról, hogy a virtuális gép elindul akkor is, ha a fájlrendszer sérült, vagy a fájlrendszer nem létezik indításkor. Azt javasoljuk, hogy használja a failfail opciót az fstab fájlban, hogy engedélyezze az indításfolytatását, miután hibák történtek a virtuális gép indításához nem szükséges partíciókon.

7. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

8. Mentse az fstab fájl módosításait.

9. Indítsa újra a virtuális gépet.

10. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie a bash promptot a portálon. Ellenőrizze, hogy tud-e csatlakozni a virtuális géphez.

11. Ellenőrizze a csatlakoztatási pontokat, amikor bármilyen fstab változást tesztel a csatlakoztatás –a parancs futtatásával. Ha nincsenek hibák, a csatlakoztatási pontoknak jónak kell lenniük.

## <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

1. Csatlakoztassa a virtuális gép rendszerlemezét adatlemezként egy helyreállítási virtuális géphez (bármely működő Linux virtuális géphez). Ehhez használhatja a [CLI parancsokat,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) vagy automatizálhatja a helyreállítási virtuális gép beállítását a [virtuális gép javítási parancsaival.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Miután csatlakoztatta a rendszerlemezt adatlemezként a helyreállítási virtuális géphez, készítsen biztonsági másolatot az fstab fájlról a módosítások végrehajtása előtt, majd kövesse a következő lépéseket az fstab fájl javításához.

3.    Keresse meg azt a hibát, amely azt jelzi, hogy a lemez nem volt csatlakoztatva. A következő példában a rendszer olyan lemezt próbált csatolni, amely már nem volt jelen:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Csatlakozzon a virtuális géphez a gyökérjelszó (Red Hat-alapú virtuális gépek) használatával.

5. Az fstab fájl megnyitásához használja kedvenc szövegszerkesztőjét. A lemez csatlakoztatása után futtassa a következő parancsot a Nano számára. Győződjön meg arról, hogy a csatlakoztatott lemezen található fstab fájlon dolgozik, és nem a mentési virtuális gépen található fstab fájlon.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Tekintse át a felsorolt fájlrendszereket. Az fstab fájl minden sora egy olyan fájlrendszert jelöl, amely a virtuális gép indításakor van csatlakoztatva. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a man fstab parancsot. A kezdési hiba elhárításához tekintse át az egyes sorokat, és győződjön meg arról, hogy az a szerkezet és a tartalom szempontjából is helyes.

   > [!Note]
   > * Az egyes sorok mezőit tabulátorok vagy szóközök választják el egymástól. Az üres sorokat a program figyelmen kívül hagyja. Azok a sorok, amelyek első karakterként számjellel (#) rendelkeznek, megjegyzések. A megjegyzésekkel kapcsolatos sorok megmaradhatnak az fstab fájlban, de nem lesznek feldolgozva. Azt javasoljuk, hogy a sorok eltávolítása helyett olyan fstab sorokat fűzzön hozzá, amelyekben nem biztos.
   > * Ahhoz, hogy a virtuális gép helyreálljon és elinduljon, a fájlrendszer partíciói legyenek az egyetlen szükséges partíciók. A virtuális gép alkalmazáshibákat tapasztalhat a további megjegyzésekkel kapcsolatos partíciókkal kapcsolatban. Azonban a virtuális gép kell kezdeni a további partíciók nélkül. Később a hozzáfűzött sorok megjegyzését is elhagyhatja.
   > * Azt javasoljuk, hogy az azure-beli virtuális gépeken csatlakoztassa az adatlemezeket a fájlrendszerpartíció UUID használatával. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot. Figyelje meg, hogy a lemez, amely helyre kívánja állítani most csatlakoztatva van egy új virtuális gép. Bár az UUID-k konzisztensnek kell lenniük, az eszközpartíció-azonosítók (például "/dev/sda1") különböznek ezen a virtuális gépen. A nem rendszervirtuális merevlemezen található eredeti hibás virtuális gép fájlrendszerpartíciói nem érhetők el a helyreállítási virtuális gép [számára cli-parancsokkal.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)
   > * A failfail beállítás segít győződjön meg arról, hogy a virtuális gép elindul akkor is, ha a fájlrendszer sérült, vagy a fájlrendszer nem létezik indításkor. Azt javasoljuk, hogy használja a failfail opciót az fstab fájlban, hogy engedélyezze az indításfolytatását, miután hibák történtek a virtuális gép indításához nem szükséges partíciókon.

7. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

8. Mentse az fstab fájl módosításait.

9. Indítsa újra a virtuális gépet, vagy építse újra az eredeti virtuális gépet.

10. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie a bash promptot a portálon. Ellenőrizze, hogy tud-e csatlakozni a virtuális géphez.

11. Ellenőrizze a csatlakoztatási pontokat, amikor bármilyen fstab változást tesztel a csatlakoztatás –a parancs futtatásával. Ha nincsenek hibák, a csatlakoztatási pontoknak jónak kell lenniük.

12. Válassza le és válassza le az eredeti virtuális merevlemezt, majd hozzon létre egy virtuális gépet az eredeti rendszerlemezről. Ehhez [használhatja a CLI-parancsokat](troubleshoot-recovery-disks-linux.md) vagy a [virtuális gép javítási parancsait,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) ha a helyreállítási virtuális gép létrehozásához használta őket.

13. Miután újra létrehozta a virtuális gép, és csatlakozhat hozzá az SSH-n keresztül, hajtsa végre a következő műveleteket:
    * Tekintse át a helyreállítás során módosított vagy kommentált fstab sorok at.
    * Győződjön meg arról, hogy az UUID és a failfail opció megfelelően használja.
    * Tesztelje az fstab módosításokat a virtuális gép újraindítása előtt. Ehhez használja a következő parancsot:``$ sudo mount -a``
    * Hozzon létre egy további másolatot a javított fstab fájlról későbbi helyreállítási helyzetekben való használatra.

## <a name="next-steps"></a>További lépések

* [Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure CLI 2.0-s környezetével egy helyreállítási virtuális géphez való csatlakoztatásával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure Portalon keresztül idomuló helyreállítási virtuális géphez való csatlakoztatásával](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

