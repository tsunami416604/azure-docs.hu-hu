## <a name="overview"></a>Áttekintés
Amikor létrehoz egy új virtuális gépet egy erőforráscsoportban egy [Azure Marketplace-ről](https://azure.microsoft.com/marketplace/) származó rendszerképből, az operációs rendszer meghajtójának alapértelmezett mérete 127 GB. Bár hozzáadhat adatlemezeket a virtuális géphez (ezeknek száma a választott termékváltozattól függ), és ajánlott ezekre a kiegészítő lemezekre telepíteni az alkalmazásokat és a processzorigényes számítási feladatokat, az ügyfeleknek gyakran bővíteniük kell az operációs rendszer meghajtóját bizonyos forgatókönyvek támogatásához, mint például a következők:

1. Az operációs rendszer meghajtójára összetevőket telepítő, régebbi alkalmazások támogatása.
2. Nagyobb operációsrendszer-meghajtóval rendelkező fizikai számítógép vagy virtuális gép migrálása a helyszínről.

> [!IMPORTANT]
> Az Azure két különböző üzemi modellel rendelkezik az erőforrások létrehozásához és használatához: Resource Manager-alapú és klasszikus. Ez a cikk a Resource Manager-alapú modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> 
> 

## <a name="resize-the-os-drive"></a>Az operációs rendszer meghajtójának átméretezése
Ez a cikk az operációs rendszer meghajtójának az [Azure Powershell](/powershell/azureps-cmdlets-docs) erőforrás-kezelő moduljaival történő átméretezését ismerteti. Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be a Microsoft Azure-fiókjába erőforrás-kezelő módban, és válassza ki az előfizetését a következő módon:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Íme a pillanat, amire vártunk! Állítsa az operációsrendszer-lemez méretét a kívánt értékre, és frissítse a virtuális gépet a következő módon:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A maximálisan megengedett méret 1023 GB.
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="summary"></a>Összefoglalás
Ebben a cikkben a Powershell Azure Resource Manager-moduljaival bővítettük egy IaaS-beli virtuális gép operációsrendszer-meghajtóját. Az alábbiakban találja a teljes szkriptet:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Következő lépések
Ez a cikk elsősorban a virtuális gép operációsrendszer-lemezének bővítéséről szól, azonban a létrehozott szkript egyetlen sor módosításával a virtuális géphez csatolt adatlemezek bővítésére is használható. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a ```StorageProfile``` ```OSDisk``` objektumát a ```DataDisks``` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Hasonlóképpen hivatkozhat más, a virtuális géphez csatolt adatlemezekre is, akár a fent látható módon egy index használatával, akár a lemez ```Name``` tulajdonságával, az alább látható módon:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Ha arról szeretne tájékozódni, hogyan csatlakoztathat lemezeket egy Azure Resource Manager-alapú virtuális géphez, tekintse meg ezt a [cikket](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

