
További információ a lemezekkel kapcsolatban: [A lemezek és virtuális merevlemezek ismertetése](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Üres lemez csatlakoztatása
1. Nyissa meg az Azure CLI 1.0-át, és [csatlakozzon Azure-előfizetéséhez](../articles/xplat-cli-connect.md). Bizonyosodjon meg róla, Azure szolgáltatásfelügyelet módban van-e (`azure config mode asm`).
2. Írja be az `azure vm disk attach-new` parancsot egy új lemez létrehozásához és csatlakoztatásához az alábbi példában látható módon. A *myVM* karakterlánc helyébe írja a Linux virtuális gép nevét, és adja meg a lemez méretét GB-ban, ami ebben a példában *100 GB*:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Az adatmeghajtó a létrehozása és csatlakoztatása után megjelenik az `azure vm disk list <virtual-machine-name>` kimenetében, amint az az alábbi példában látható:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    A kimenet a következő példához hasonló:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
Meglévő lemez csatlakoztatása esetén rendelkeznie kell egy tárfiókban elérhető .vhd-vel.

1. Nyissa meg az Azure CLI 1.0-át, és [csatlakozzon Azure-előfizetéséhez](../articles/xplat-cli-connect.md). Bizonyosodjon meg róla, Azure szolgáltatásfelügyelet módban van-e (`azure config mode asm`).
2. Ellenőrizze, hogy a csatlakoztatni kívánt VHD fel van-e már töltve az Azure-előfizetésére:
   
    ```azurecli
    azure vm disk list
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Ha nem találja a használni kívánt lemezt, az `azure vm disk create` vagy `azure vm disk upload` parancs használatával feltölthet egy helyi VHD-t is. A következő példa a `disk create` egy lehetséges változatát mutatja be:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Az `azure vm disk upload` használatával pedig feltölthet egy VHD-t egy adott tárfiókra. Az Azure virtuális gépek adatlemezeinek kezelését végző parancsokról bővebben [itt](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) olvashat.

4. Most csatlakoztassa a kívánt VHD-t a virtuális géphez:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   A *myVM* karakterlánc helyébe mindenképp a virtuális gép nevét, a *myVHD* helyébe pedig a kívánt VHD-t írja.

5. Az `azure vm disk list <virtual-machine-name>` paranccsal ellenőrizheti, hogy a lemez csatlakozik-e a virtuális géphez:
   
    ```azurecli
    azure vm disk list myVM
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Az adatlemezek csatlakoztatását követően be kell jelentkeznie a virtuális gépre, és inicializálnia kell a lemezt, hogy a virtuális gép használhassa azt tárolásra (a lemez inicializálásának módjával kapcsolatban lásd a következő lépéseket).
> 
> 

