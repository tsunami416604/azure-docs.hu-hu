Ha már nincs szüksége egy virtuális géphez (VM-hez) csatolt adatlemezre, könnyedén leválaszthatja. Amikor leválaszt egy lemezt a virtuális gépről, a lemezt nem távolítja el a tárolóból. Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  

> [!NOTE]
> Az Azure-ban a virtuális gépek különböző lemeztípusokat használnak – operációsrendszer-lemezt, helyi ideiglenes lemezt és opcionális adatlemezeket. További információ: [A lemezek és virtuális merevlemezek ismertetése](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az operációsrendszer-lemezeket csak akkor választhatja le, ha a virtuális gépet is törli.

## <a name="find-the-disk"></a>A lemez megkeresése
Mielőtt leválaszthatna egy lemezt egy virtuális gépről, meg kell keresnie a LUN számot, amely a leválasztani kívánt lemez azonosítója. Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure CLI-t, és [csatlakozzon Azure-előfizetéséhez](../articles/xplat-cli-connect.md). Bizonyosodjon meg róla, Azure szolgáltatásfelügyelet módban van-e (`azure config mode asm`).
2. Azonosítsa, hogy mely lemezek vannak a virtuális gépéhez csatolva. Az alábbi példa felsorolja a `myVM` nevű virtuális gép lemezeit:

    ```azurecli
    azure vm disk list myVM
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Jegyezze fel a leválasztani kívánt lemez LUN-ját (**logikaiegység-szám**).

## <a name="remove-operating-system-references-to-the-disk"></a>A lemezre mutató operációs rendszerbeli hivatkozások eltávolítása
Mielőtt leválasztaná a lemezt a Linux-vendégről, meg kell győződnie arról, hogy a lemez egyik partíciója sincs használatban. Győződjön meg arról, hogy az operációs rendszer nem kísérli meg újból csatlakoztatni őket az újraindítás után. Ezek a lépések visszavonják a lemez [csatolásakor](../articles/virtual-machines/linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) valószínűleg létrehozott konfigurációt.

1. Az `lsscsi` paranccsal derítse fel a lemez azonosítóját. Az `lsscsi` a `yum install lsscsi` (Red Hat-alapú disztribúciókon) vagy az `apt-get install lsscsi` (Debian-alapú disztribúciókon) használatával telepíthető. A LUN számmal megtalálhatja a keresett lemezazonosítót. Minden sorban a rekord utolsó száma a LUN szám. A következő `lsscsi`-példában a LUN 0 a következőnek felel meg: */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Az `fdisk -l <disk>` paranccsal fedezze fel a leválasztani kívánt lemezzel társított partíciókat. Az alábbi példa a `/dev/sdc` kimenetét mutatja be:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Válassza le a lemezhez felsorolt összes partíciót. Az alábbi példa leválasztja a következőt: `/dev/sdc1`

    ```bash
    sudo umount /dev/sdc1
    ```

4. A `blkid` paranccsal derítse fel az összes partíció UUID-jét. A kimenet a következő példához hasonló:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Távolítsa el az eszközútvonalakkal vagy az összes partíció UUID-ivel társított **/etc/fstab** fájlban lévő bejegyzéseket a lemez leválasztásához.  Ebben a példában a bejegyzések a következők lehetnek:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    vagy
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>A lemez leválasztása
Miután megtalálta a lemez LUN számát és eltávolította az operációs rendszer hivatkozásait, készen áll a lemez leválasztására:

1. A következő parancs futtatásával válassza le a kiválasztott lemezt a virtuális gépről: `azure vm disk detach
   <virtual-machine-name> <LUN>`. Az alábbi példa leválasztja a `0` LUN számú lemezt a `myVM` nevű virtuális gépről:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. A `azure vm disk list` ismételt futtatásával ellenőrizheti, hogy leválasztotta-e a lemezt. Az alábbi példa ellenőrzi a `myVM` nevű virtuális gépet:
   
    ```azurecli
    azure vm disk list myVM
    ```

    A kimenet a következő példához hasonló, amelyen látható, hogy az adatlemez már nincs csatolva:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

A leválasztott lemez a tárolóban marad, de más nincs virtuális géphez csatlakoztatva.

