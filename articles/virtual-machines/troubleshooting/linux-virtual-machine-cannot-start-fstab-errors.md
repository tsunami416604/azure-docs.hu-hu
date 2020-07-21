---
title: Linux rendszerű virtuális gép hibáinak elhárítása az fstab-hibák miatt | Microsoft Docs
description: Ismerteti, hogy a Linux rendszerű virtuális gépek miért nem indíthatók el, és hogyan oldható meg a probléma.
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
ms.openlocfilehash: cf27a842d37e96c82370e9b9b81763c8a5d1f7c9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509052"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Linuxos virtuális gép hibáinak elhárítása az fstab-hibák miatt

Nem lehet biztonságos rendszerhéj-(SSH-) kapcsolaton keresztül csatlakozni egy Azure-beli linuxos virtuális géphez (VM). A [Azure Portal](https://portal.azure.com/) [rendszerindítási diagnosztika](./boot-diagnostics.md) funkciójának futtatásakor az alábbi példákhoz hasonló bejegyzéseket láthat:

## <a name="examples"></a>Példák

A következő példák a lehetséges hibákra mutatnak.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>1. példa: a lemez az univerzálisan egyedi azonosító (UUID) helyett az SCSI-AZONOSÍTÓval van csatlakoztatva.

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>2. példa: nem csatolt eszköz hiányzik a CentOS-ről

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>3. példa: A virtuális gép nem indítható el egy fstab-konfiguráció miatt, vagy mert a lemez már nincs csatlakoztatva

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>4. példa: A soros napló bejegyzései helytelen UUID-t mutatnak.

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

Ez a probléma akkor fordulhat elő, ha a fájlrendszeri tábla (fstab) szintaxisa helytelen, vagy ha az "/etc/fstab" fájl egyik bejegyzéséhez hozzárendelt szükséges adatlemez nincs csatlakoztatva a virtuális géphez.

## <a name="resolution"></a>Feloldás

A probléma megoldásához indítsa el a virtuális gépet vészhelyzeti módban az Azure Virtual Machines soros konzoljának használatával. Ezután használja az eszközt a fájlrendszer kijavításához. Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a [virtuális gép kijavítása offline](#repair-the-vm-offline) szakaszra.

## <a name="use-the-serial-console"></a>A soros konzol használata

### <a name="using-single-user-mode"></a>Egyfelhasználós mód használata

1. Kapcsolódjon [a soros konzolhoz](./serial-console-linux.md).
2. A soros konzol használata az egyfelhasználós mód [egyfelhasználós üzemmódba](../linux/serial-console-grub-single-user-mode.md) való elvégzéséhez
3. Ha a virtuális gép egy egyszeri felhasználói módba lett indítva. Az fstab-fájl megnyitásához használja a kedvenc szövegszerkesztőjét. 

   ```
   # nano /etc/fstab
   ```

4. Tekintse át a felsorolt fájlrendszerek listáját. Az fstab fájl minden sora a virtuális gép indításakor csatlakoztatott állományrendszert jelöl. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a Man fstab parancsot. A kezdési hibák elhárításához tekintse át az egyes vonalakat, hogy ellenőrizze, helyes-e a szerkezet és a tartalom.

   > [!Note]
   > * Az egyes vonalak mezői tabulátorral vagy szóközzel vannak elválasztva. Az üres sorok figyelmen kívül lesznek hagyva. Azok a sorok, amelyeknek a száma (#) az első karakter, a megjegyzések. A kommentált sorok az fstab fájlban maradhatnak, de nem lesznek feldolgozva. Javasoljuk, hogy a sorok eltávolítása helyett olyan fstab-sorokat fűzzenek, amelyekről nem biztos benne.
   > * A virtuális gép helyreállításához és elindításához a fájlrendszer partícióinak az egyetlen szükséges partíciónak kell lenniük. Előfordulhat, hogy a virtuális gép az alkalmazással kapcsolatos hibákat észlel a további kommentált partíciókkal kapcsolatban. A virtuális gépnek azonban a további partíciók nélkül kell kezdődnie. Később elvégezheti a megjegyzésekben szereplő sorok hozzáfűzését.
   > * Javasoljuk, hogy az adatlemezeket az Azure-beli virtuális gépeken a fájlrendszer partíciójának UUID-fájljának használatával csatlakoztassa. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot.
   > * A nem sikertelen beállítással meggyőződhet arról, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a fájlrendszer nem létezik az indításkor. Azt javasoljuk, hogy az fstab fájlon a nem sikertelen beállítást használva engedélyezze az indítást, hogy a virtuális gép elindításához nem szükséges partíciókban lévő hibák után folytassa a hibákat.

5. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

6. Mentse a módosításokat az fstab-fájlba.

7. Indítsa újra a virtuális gépet az alábbi parancs használatával.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Használhatja a "CTRL + x" parancsot is, amely újraindítja a virtuális gépet is.


8. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie egy bash-kérést a portálon. Győződjön meg arról, hogy tud-e csatlakozni a virtuális géphez.

### <a name="using-root-password"></a>Root password használata

1. Kapcsolódjon [a soros konzolhoz](./serial-console-linux.md).
2. Helyi felhasználó és jelszó használatával jelentkezzen be a rendszerbe.

   > [!Note]
   > A soros konzolon nem használhat SSH-kulcsot a rendszerbe való bejelentkezéshez.

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

4. Kapcsolódjon a virtuális géphez a legfelső szintű jelszó (Red Hat-alapú virtuális gépek) használatával.

5. Az fstab-fájl megnyitásához használja a kedvenc szövegszerkesztőjét. A lemez csatlakoztatása után futtassa a következő parancsot a nano számára:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Tekintse át a felsorolt fájlrendszerek listáját. Az fstab fájl minden sora a virtuális gép indításakor csatlakoztatott állományrendszert jelöl. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a Man fstab parancsot. A kezdési hibák elhárításához tekintse át az egyes vonalakat, hogy ellenőrizze, helyes-e a szerkezet és a tartalom.

   > [!Note]
   > * Az egyes vonalak mezői tabulátorral vagy szóközzel vannak elválasztva. Az üres sorok figyelmen kívül lesznek hagyva. Azok a sorok, amelyeknek a száma (#) az első karakter, a megjegyzések. A kommentált sorok az fstab fájlban maradhatnak, de nem lesznek feldolgozva. Javasoljuk, hogy a sorok eltávolítása helyett olyan fstab-sorokat fűzzenek, amelyekről nem biztos benne.
   > * A virtuális gép helyreállításához és elindításához a fájlrendszer partícióinak az egyetlen szükséges partíciónak kell lenniük. Előfordulhat, hogy a virtuális gép az alkalmazással kapcsolatos hibákat észlel a további kommentált partíciókkal kapcsolatban. A virtuális gépnek azonban a további partíciók nélkül kell kezdődnie. Később elvégezheti a megjegyzésekben szereplő sorok hozzáfűzését.
   > * Javasoljuk, hogy az adatlemezeket az Azure-beli virtuális gépeken a fájlrendszer partíciójának UUID-fájljának használatával csatlakoztassa. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot.
   > * A nem sikertelen beállítással meggyőződhet arról, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a fájlrendszer nem létezik az indításkor. Azt javasoljuk, hogy az fstab fájlon a nem sikertelen beállítást használva engedélyezze az indítást, hogy a virtuális gép elindításához nem szükséges partíciókban lévő hibák után folytassa a hibákat.

7. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

8. Mentse a módosításokat az fstab-fájlba.

9. Indítsa újra a virtuális gépet.

10. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie egy bash-kérést a portálon. Győződjön meg arról, hogy tud-e csatlakozni a virtuális géphez.

11. Ellenőrizze a csatlakoztatási pontokat, amikor a Mount – a parancs futtatásával teszteli az fstab-módosításokat. Ha nincsenek hibák, a csatlakoztatási pontoknak jónak kell lenniük.

## <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

1. Csatlakoztassa a virtuális gép rendszerlemezét adatlemezként egy helyreállítási virtuális géphez (bármilyen működő Linux rendszerű virtuális géphez). Ehhez használhatja a [CLI-parancsokat](./troubleshoot-recovery-disks-linux.md) , vagy automatizálhatja a helyreállítási virtuális gép beállítását a [virtuális gép javítási parancsainak](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)használatával.

2. Miután a rendszerlemezt adatlemezként csatlakoztatta a helyreállítási virtuális gépen, készítsen biztonsági másolatot az fstab-fájlról a módosítások végrehajtása előtt, majd kövesse a következő lépéseket az fstab-fájl kijavítani.

3. Keresse meg azt a hibát, amely azt jelzi, hogy a lemez nincs csatlakoztatva. A következő példában a rendszer olyan lemezt próbált csatolni, amely már nem volt jelen:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Kapcsolódjon a virtuális géphez a legfelső szintű jelszó (Red Hat-alapú virtuális gépek) használatával.

5. Az fstab-fájl megnyitásához használja a kedvenc szövegszerkesztőjét. A lemez csatlakoztatása után futtassa a következő parancsot a Nano-hoz. Győződjön meg arról, hogy a csatlakoztatott lemezen található fstab-fájlon dolgozik, nem pedig a mentési virtuális gépen lévő fstab-fájlon.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Tekintse át a felsorolt fájlrendszerek listáját. Az fstab fájl minden sora a virtuális gép indításakor csatlakoztatott állományrendszert jelöl. Az fstab fájl szintaxisával kapcsolatos további információkért futtassa a Man fstab parancsot. A kezdési hibák elhárításához tekintse át az egyes vonalakat, hogy ellenőrizze, helyes-e a szerkezet és a tartalom.

   > [!Note]
   > * Az egyes vonalak mezői tabulátorral vagy szóközzel vannak elválasztva. Az üres sorok figyelmen kívül lesznek hagyva. Azok a sorok, amelyeknek a száma (#) az első karakter, a megjegyzések. A kommentált sorok az fstab fájlban maradhatnak, de nem lesznek feldolgozva. Javasoljuk, hogy a sorok eltávolítása helyett olyan fstab-sorokat fűzzenek, amelyekről nem biztos benne.
   > * A virtuális gép helyreállításához és elindításához a fájlrendszer partícióinak az egyetlen szükséges partíciónak kell lenniük. Előfordulhat, hogy a virtuális gép az alkalmazással kapcsolatos hibákat észlel a további kommentált partíciókkal kapcsolatban. A virtuális gépnek azonban a további partíciók nélkül kell kezdődnie. Később elvégezheti a megjegyzésekben szereplő sorok hozzáfűzését.
   > * Javasoljuk, hogy az adatlemezeket az Azure-beli virtuális gépeken a fájlrendszer partíciójának UUID-fájljának használatával csatlakoztassa. Futtassa például a következő parancsot:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * A fájlrendszer UUID azonosítójának meghatározásához futtassa a blkid parancsot. A szintaxissal kapcsolatos további információkért futtassa a Man blkid parancsot. Figyelje meg, hogy a helyreállítani kívánt lemez most már egy új virtuális gépre van csatlakoztatva. Bár az UUID-EK konzisztensek, az eszköz partíciós azonosítói (például "/dev/sda1") eltérnek a virtuális gépen. A nem rendszervirtuális merevlemezen található eredeti, nem rendszerszintű virtuális gép fájlrendszer-partíciói a [CLI-parancsok használatával](./troubleshoot-recovery-disks-linux.md)nem érhetők el a helyreállítási virtuális gép számára.
   > * A nem sikertelen beállítással meggyőződhet arról, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a fájlrendszer nem létezik az indításkor. Azt javasoljuk, hogy az fstab fájlon a nem sikertelen beállítást használva engedélyezze az indítást, hogy a virtuális gép elindításához nem szükséges partíciókban lévő hibák után folytassa a hibákat.

7. Módosítsa vagy véleményezze az fstab fájl helytelen vagy szükségtelen sorait, hogy a virtuális gép megfelelően induljon el.

8. Mentse a módosításokat az fstab-fájlba.

9. Indítsa újra a virtuális gépet, vagy hozza létre újra az eredeti virtuális gépet.

10. Ha a bejegyzések megjegyzése vagy javítása sikeres volt, a rendszernek el kell érnie egy bash-kérést a portálon. Győződjön meg arról, hogy tud-e csatlakozni a virtuális géphez.

11. Ellenőrizze a csatlakoztatási pontokat, amikor a Mount – a parancs futtatásával teszteli az fstab-módosításokat. Ha nincsenek hibák, a csatlakoztatási pontoknak jónak kell lenniük.

12. Válassza le és válassza le az eredeti virtuális merevlemezt, majd hozzon létre egy virtuális gépet az eredeti rendszerlemezről. Ehhez használhatja a [CLI-parancsokat](troubleshoot-recovery-disks-linux.md) vagy a [virtuális gép javítási parancsait](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , ha a helyreállítási virtuális gép létrehozásához használta őket.

13. Miután újra létrehozta a virtuális gépet, és SSH-n keresztül tud csatlakozni hozzá, hajtsa végre a következő műveleteket:
    * Tekintse át a helyreállítás során megváltoztatott vagy kommentált fstab-sorokat.
    * Győződjön meg arról, hogy az UUID-t és a nem megfelelő beállítást használja.
    * A virtuális gép újraindítása előtt tesztelje az fstab-módosításokat. Ehhez használja a következő parancsot:``$ sudo mount -a``
    * Hozzon létre egy további másolatot a helyesbített fstab-fájlról a jövőbeli helyreállítási forgatókönyvekben való használathoz.

## <a name="next-steps"></a>Következő lépések

* [Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez az Azure CLI 2,0-vel való csatlakoztatásával](./troubleshoot-recovery-disks-linux.md)
* [Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez csatolásával a Azure Portal használatával](./troubleshoot-recovery-disks-portal-linux.md)
