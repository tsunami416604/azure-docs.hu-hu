---
title: Titkosított logikai kötetek felügyeleti lemezek átméretezése Azure Disk Encryption
description: Ez a cikk az ADE titkosított lemezek logikai kötetek kezelésével történő átméretezésére vonatkozó utasításokat tartalmazza.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343968"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>A Azure Disk Encryption-mel titkosított logikai kötet-felügyeleti eszközök átméretezése

Ez a cikk lépésről lépésre bemutatja, hogyan méretezheti át az ADE titkosított adatlemezeket a logikai kötetek kezelése (LVM) használatával Linux rendszeren, több forgatókönyvre alkalmazható.

A folyamat a következő környezetekre vonatkozik:

- Linux-disztribúciók
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure Disk Encryption Single-pass bővítmény
- Azure Disk Encryption Dual-pass bővítmény

## <a name="considerations"></a>Megfontolandó szempontok

Ez a dokumentum azt feltételezi, hogy:

1. Létezik egy meglévő LVM-konfiguráció.
   
   Az LVM Linux rendszerű virtuális gépen való konfigurálásával kapcsolatos további információkért olvassa el az [LVM konfigurálása](configure-lvm.md) linuxos virtuális gépen című témakört.

2. A lemezek már titkosítva vannak a Azure Disk Encryption az LVM [konfigurálása a crypt](how-to-configure-lvm-raid-on-crypt.md) -ben az LVM-on-Crypt konfigurálásával kapcsolatos információkért.

3. Ezeket a példákat a szükséges Linux-és LVM-szakértelemmel látja el.

4. Tudomásul veszi, hogy az [eszközök neveivel kapcsolatos problémák elhárítása](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)során az Azure-beli adatlemezek használatára vonatkozó javaslat a/dev/disk/scsi1/elérési útjának használata.

## <a name="scenarios"></a>Forgatókönyvek

A jelen cikkben ismertetett eljárások a következő helyzetekben érvényesek:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Hagyományos LVM-és LVM-titkosítási konfigurációk esetén

- Logikai kötet kibővítése, ha rendelkezésre áll egy szabad terület a VG-ben

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Hagyományos LVM titkosítás esetén (a logikai kötetek titkosítva vannak, nem a teljes lemez)

- Egy hagyományos LVM-kötet kiterjesztése új PV-t hozzáadva
- A hagyományos LVM-kötetek kiterjesztése egy meglévő PV-re

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Az LVM-on-Crypt (ajánlott módszer esetén a teljes lemez titkosítva van, nem csak a logikai kötet)

- Az LVM kiterjesztése a crypt-kötetre új PV hozzáadásával
- Az LVM kiterjesztése egy meglévő PV-t átméretezni a crypt köteten

> [!NOTE]
> A hagyományos LVM titkosítás és az LVM-on-Crypt használata ugyanazon a virtuális gépen nem ajánlott.

> [!NOTE]
> Ezek a példák a lemezek, a fizikai kötetek, a kötetek csoportjai, a logikai kötetek, a fájlrendszerek, az UUID-EK és a csatolási meglévő neveit használják, a környezetnek megfelelően kell lecserélnie a példákban megadott értékeket.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Logikai kötet kibővítése, ha rendelkezésre áll egy szabad terület a VG-ben

A logikai kötetek átméretezésére használt hagyományos módszer a nem titkosított lemezekre, a hagyományos LVM titkosított kötetekre és az LVM-on-Crypt konfigurációra is alkalmazható.

1. Ellenőrizze a bővíteni kívánt fájlrendszer aktuális méretét:

    ``` bash
    df -h /mountpoint
    ```

    ![forgatókönyv – FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Győződjön meg arról, hogy a VG elegendő területtel rendelkezik az LV növeléséhez

    ``` bash
    vgs
    ```

    ![forgatókönyv – pipa-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Használhatja a "vgdisplay" is

    ``` bash
    vgdisplay vgname
    ```

    ![forgatókönyv – vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Annak meghatározása, hogy mely logikai kötetet kell átméretezni

    ``` bash
    lsblk
    ```

    ![forgatókönyv – lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Az LVM-on-Crypt esetében a különbség ebben a kimenetben látható, ami azt mutatja, hogy a titkosított réteg a teljes lemezre kiterjedő titkosított rétegen található.

    ![forgatókönyv – lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. A logikai kötet méretének megkeresése

    ``` bash
    lvdisplay lvname
    ```

    ![forgatókönyv – lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Az LV méretének növeléséhez használja az "-r" kifejezést a fájlrendszer online átméretezéséhez

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![forgatókönyv – átméretezés – lv](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. A LV és a fájlrendszer új méretének ellenőrzése

    ``` bash
    df -h /mountpoint
    ```

    ![forgatókönyv – bejelentkezés-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Az új méret tükröződik, és az LV és a fájlrendszer sikeres átméretezését jelzi.

7. Az LV-szint módosításának megerősítéséhez ismét ellenőrizheti a LV-adatokat

    ``` bash
    lvdisplay lvname
    ```

    ![forgatókönyv – lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Egy hagyományos LVM-kötet kiterjesztése új PV-t hozzáadva

Akkor alkalmazható, ha új lemezt kell felvennie a kötet csoport méretének növeléséhez.

1. Ellenőrizze a bővíteni kívánt fájlrendszer aktuális méretét:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob – bejelentkezés-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. A fizikai kötet aktuális konfigurációjának ellenőrzése

    ``` bash
    pvs
    ```

    ![scenariob – PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Az aktuális VG-információ keresése

    ``` bash
    vgs
    ```

    ![scenariob – vgs](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Az aktuális lemez listájának keresése

    Az adatlemezeket az/dev/disk/Azure/scsi1/alatti eszközök ellenőrzésével kell azonosítani.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob – scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. A lsblk kimenetének keresése 

    ``` bash
    lsbk
    ```

    ![scenariob – lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Az új lemez csatlakoztatása a virtuális géphez

    Kövesse az alábbi dokumentum 4. lépését.

   - [Lemez csatlakoztatása egy virtuális géphez](attach-disk-portal.md)

7. A lemez csatolása után tekintse meg a lemezek listáját, és figyelje meg az új lemezt.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob – scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob – lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Új PV létrehozása az új adatlemez tetejére

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Ez a módszer a teljes lemezt egy partíció nélküli PV-ként használja, opcionálisan használhatja az "fdisk" parancsot a partíció létrehozásához, majd ezt a partíciót használhatja a "pvcreate" számára.

9. Ellenőrizze, hogy a PV hozzá lett-e adva a PV-listához.

    ``` bash
    pvs
    ```

    ![scenariob – pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. A VG bővítése az új PV hozzáadásával

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-bővítés](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Az új VG-méret keresése

    ``` bash
    vgs
    ```

    ![scenariob – vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. A lsblk használatával azonosíthatja, hogy mely LV-t kell átméretezni

    ``` bash
    lsblk
    ```

    ![scenariob – lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Az LV-méret kiterjesztése a "-r" használatával a fájlrendszer online növeléséhez

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Az új LV és fájlrendszer méretének ellenőrzése

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob – FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Fontos tudni, hogy ha az ADE-t hagyományos LVM-konfigurációkon használják, a titkosított réteg az LV szintjén jön létre, nem pedig a lemez szintjén.

    Ezen a ponton a titkosított réteg ki van bontva az új lemezre.
    A tényleges adatlemez nem rendelkezik titkosítási beállításokkal a platform szintjén, így a titkosítási állapota nem frissül.

    >[!NOTE]
    >Íme néhány ok, amiért az LVM-on-Crypt az ajánlott módszer. 

15. Keresse meg a titkosítási adatokat a portálon:

    ![scenariob – portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Hozzá kell adnia egy új LV-t, és engedélyeznie kell a bővítményt a virtuális gépen a lemez titkosítási beállításainak frissítése érdekében.
    
16. Vegyen fel egy új LV-t, hozzon létre egy állományrendszert, és adja hozzá az/etc/fstab-hez

17. Állítsa be újra a titkosítási bővítményt, hogy az új adatlemez titkosítási beállításait a platform szintjén lehessen lepecsételni.

    Példa:

    parancssori felület

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Keresse meg a titkosítási adatokat a portálon:

    ![scenariob – portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. A titkosítási beállítások frissítése után törölheti az új LV-t, ezért törölnie kell a bejegyzést a hozzá létrehozott/etc/fstab és/etc/crypttab bejegyzésből is.

    ![scenariob-delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. A logikai kötet leválasztása

    ``` bash
    umount /mountpoint
    ```

21. A kötet titkosított rétegének lezárása

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Az LV törlése

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>A hagyományos LVM-kötetek kiterjesztése egy meglévő PV-re

Bizonyos forgatókönyveknek vagy korlátozásoknak egy meglévő lemez átméretezésére van szükségük.

1. A titkosított lemezek azonosítása

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc – scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc – lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. A PV-információk keresése

    ``` bash
    pvs
    ```

    ![scenarioc – PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    A PVs összes területe jelenleg használatban van

3. A VGs információinak megtekintése

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc – vgs](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. A lemezek méretének megadásához az fdisk vagy a lsblk használatával listázhatja a meghajtók méretét

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc – az fdisk engedélyezése](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Azonosította azokat a PVs, amelyekhez társítva van a lsblk-FS LVs, a "lvdisplay" parancs futtatásával is azonosíthatja azt.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Ebben a konkrét esetben mind a 4 adatmeghajtó ugyanannak a VG-nek és egyetlen LV-nek a része, a konfiguráció eltérő lehet ettől a példától.

5. A fájlrendszer aktuális kihasználtságának keresése:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc – keresés – DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Az adatlemez (ek) átméretezése:

    A [Linux kibontására szolgáló lemezekre](expand-disks.md) hivatkozhat (csak a lemez átméretezésére vonatkozik), a portál, a CLI vagy a PowerShell segítségével hajthatja végre ezt a lépést.

    >[!NOTE]
    >Vegye figyelembe, hogy a virtuális lemezek átméretezési műveletei nem hajthatók végre a-t futtató virtuális géppel. Ehhez a lépéshez felszabadítani kell a virtuális gépet

7. Ha a lemezek átméretezése a szükséges értékre történik, indítsa el a virtuális gépet, és az fdisk használatával vizsgálja meg az új méreteket

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc – fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Ebben a konkrét esetben a/dev/SDD az 5G-ról a 20G-re lett méretezve

8. Az aktuális PV-méret ellenõrzése

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc – pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Még ha a lemez átméretezése is megtörtént, a PV továbbra is az előző mérettel rendelkezik.

9. A PV átméretezése

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc – pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. A PV méretének ellenõrzése

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc – pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Alkalmazza ugyanazt az eljárást az összes átméretezni kívánt lemez esetében.

11. A VG információinak megtekintése

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc – vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    A VG most már rendelkezik a LVs való lefoglaláshoz szükséges hellyel

12. Az LV átméretezése

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc – lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. FS méretének keresése

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc – Df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Az LVM-on-Crypt Kötet kiterjesztése egy új PV-t hozzáadva

Ez a módszer szorosan követi az [LVM konfigurálása a crypt](how-to-configure-lvm-raid-on-crypt.md) -ben című témakör lépéseit egy új lemez hozzáadásához és egy LVM-on-Crypt konfigurációban való konfigurálásához.

Ezzel a módszerrel hozzáadhat helyet egy már létező LV-hez, vagy Ehelyett létrehozhat új VGs vagy LVs.

1. A VG jelenlegi méretének ellenőrzése

    ``` bash
    vgdisplay vgname
    ```

    ![forgatókönyv – vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Ellenőrizze a bővíteni kívánt FS-és lv-méretet

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![forgatókönyv – lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![forgatókönyv – FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adjon hozzá egy új adatlemezt a virtuális géphez, és azonosítsa azt.

    A lemez hozzáadása előtt keresse meg a lemezeket

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![forgatókönyv – newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Az új lemez hozzáadása előtt keresse meg a lemezeket

    ``` bash
    lsblk
    ```

    ![forgatókönyv – newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Vegyen fel egy új lemezt a PowerShell, az Azure CLI vagy a Azure Portal használatával. Tekintse meg, hogyan [csatolhat lemezeket](attach-disk-portal.md) a virtuális géphez való hozzáadáshoz.

    Az eszközökhöz tartozó kernel-elnevezési séma után az új meghajtó általában a következő rendelkezésre álló betűhöz lesz rendelve, ebben az esetben az új hozzáadott lemez SDD.

4. Az új lemez hozzáadása után keresse meg a lemezeket

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![forgatókönyv – newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![forgatókönyv – newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Hozzon létre egy fájlrendszert a legutóbb hozzáadott lemez tetején

    A legutóbb hozzáadott lemez megfeleltetése a/dev/disk/Azure/scsi1/társított eszközökhöz

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![forgatókönyv – newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![forgatókönyv – mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Új ideiglenes csatlakoztatási pont létrehozása az új hozzáadott lemezhez

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. A nemrég létrehozott fájlrendszer hozzáadása az/etc/fstab modulhoz

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Az új létrehozott FS csatlakoztatása a Mount-a paranccsal

    ``` bash
    mount -a
    ```

9. Ellenőrizze, hogy az új, hozzáadott FS csatlakoztatva van-e

    ``` bash
    df -h
    ```

    ![átméretezés – LVM-forgatókönyv-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![átméretezés-LVM-forgatókönyv-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Indítsa újra az adatmeghajtók korábban elindított titkosítását.

    Az LVM-on-Crypt esetében javasolt a EncryptFormatAll használata, máskülönben a dupla titkosítás a további lemezek beállításakor is előfordulhat.

    A használattal kapcsolatos információkért lásd: az [LVM konfigurálása a crypt](how-to-configure-lvm-raid-on-crypt.md)-ben.

    Példa:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    A titkosítás befejezésekor megjelenik egy Crypt-réteg az újonnan hozzáadott lemezen.

    ``` bash
    lsblk
    ```

    ![forgatókönyv – lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Az új lemez titkosított rétegének leválasztása

    ``` bash
    umount ${newmount}
    ```

12. Az aktuális PVS-információ keresése

    ``` bash
    pvs
    ```

    ![forgatókönyv – currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Hozzon létre egy PV-t a lemez titkosított rétegének tetején

    Fogadja el az eszköz nevét az előző lsblk parancsból, és adja hozzá a/dev/Mapper az eszköz neve előtt a PV létrehozásához.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![forgatókönyv – pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Megjelenik egy figyelmeztetés arról, hogyan törli a jelenlegi ext4 FS-aláírást, ennek a kérdésnek az y válasza jelenik meg

14. Annak ellenőrzése, hogy az új PV hozzá lett-e adva az LVM-konfigurációhoz

    ``` bash
    pvs
    ```

    ![forgatókönyv – newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adja hozzá az új PV-t ahhoz a VG-hez, amelyet fokoznia kell

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![forgatókönyv – vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. A VG új méretének és szabad területének ellenőrzése

    ``` bash
    vgdisplay vgname
    ```

    ![forgatókönyv – vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Figyelje meg a teljes PE-szám és az ingyenes PE/méret növelését

17. Növelje a LV és a fájlrendszer méretét a lvextend-r kapcsolóval (ebben a példában a teljes szabad terület a VG-ben, és hozzáadja azt az adott logikai kötethez)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![forgatókönyv – lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Az LV méretének ellenőrzése

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![forgatókönyv – lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Az imént átméretezett fájlrendszer méretének ellenőrzése

    ``` bash
    df -h mountpoint
    ```

    ![forgatókönyv – DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Győződjön meg arról, hogy az LVM réteg a titkosított réteg felett lett létrehozva

    ``` bash
    lsblk
    ```

    ![forgatókönyv – lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Ha a lsblk beállítások nélkül szeretné használni a csatlakoztatási pontokat, az eszköz és a logikai kötetek alapján rendezi a csatlakoztatási pontokat, érdemes lehet a lsblk, a-s megfordítani a sort, hogy a csatolási egyszer jelenjen meg, majd a lemezek többször is megjelenjenek.

    ``` bash
    lsblk -fs
    ```

    ![forgatókönyv – lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Az LVM kiterjesztése egy meglévő PV-t átméretezni a crypt köteten

1. A titkosított lemezek azonosítása

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. A PV-adatok megtekintése

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. A VG-információk keresése

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Az LV-adatok keresése

    ``` bash
    lvs
    ```

    ![scenariof – LVS](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. A fájlrendszer kihasználtságának keresése

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. A lemezek méretének ellenőrzéséhez

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Az adatlemez átméretezése

    A [Linux kibontására szolgáló lemezekre](expand-disks.md) hivatkozhat (csak a lemez átméretezésére vonatkozik), a portál, a CLI vagy a PowerShell segítségével hajthatja végre ezt a lépést.

    >[!NOTE]
    >Vegye figyelembe, hogy a virtuális lemezek átméretezési műveletei nem hajthatók végre a-t futtató virtuális géppel. Ehhez a lépéshez felszabadítani kell a virtuális gépet

8. A lemezek méretének ellenőrzéséhez

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Vegye figyelembe, hogy (ebben az esetben) mindkét lemezt 2 GB-ról 4GB-ra méretezi át, azonban az FS, az LV és a PV méret változatlan marad.

9. Az aktuális PV-méret ellenõrzése

    Ne feledje, hogy az LVM-on-Crypt esetében a PV a/dev/Mapper/-eszköz, nem pedig az/dev/SD * eszköz

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof – PVS](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. A PV átméretezése

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-átméretezés – PV](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Az átméretezést követően az mé méretének ellenõrzése

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof – PV](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. A titkosított réteg átméretezése a PV-ben

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Alkalmazza ugyanazt az eljárást az összes átméretezni kívánt lemez esetében.

13. A VG-információk keresése

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof – VG](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    A VG most már rendelkezik a LVs való lefoglaláshoz szükséges hellyel

14. Az LV információinak megtekintése

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof – lv](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Az FS kihasználtságának keresése

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof – FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Az LV átméretezése

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Az-r kapcsoló használatával is végrehajtja az FS átméretezését

17. Az LV információinak megtekintése

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. A fájlrendszer kihasználtságának keresése

    ``` bash
    df -h /mountpoint
    ```

    ![fájlrendszer létrehozása](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Alkalmazza ugyanazt az átméretezési eljárást minden további lv-re, amely megköveteli

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
