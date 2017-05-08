Ha létrehoz egy távoli asztali kapcsolatot a virtuális géppel, csatlakozhat egy virtuális hálózaton üzembe helyezett virtuális géphez. A legjobb mód arra, hogy először ellenőrizze, hogy tud-e csatlakozni a virtuális géphez, ha egy magánhálózati IP-címet használ a számítógép neve helyett. Ily módon azt teszteli, hogy tud-e csatlakozni, nem azt, hogy a névfeloldás megfelelően van-e konfigurálva.

1. **Keresse meg a magánhálózati IP-címet.** Ha egy virtuális gép magánhálózati IP-címét szeretné megkeresni, vagy tekintse meg a virtuális gép tulajdonságait az Azure Portalon, vagy használja a PowerShellt.

  - Azure Portal – Keresse meg a virtuális gépet az Azure Portalon. Tekintse meg a virtuális gép tulajdonságait. A magánhálózati IP-cím a listában szerepel.

  - PowerShell – A példa segítségével tekintse meg a virtuális gépek listáját és magánhálózati IP-címeket az erőforráscsoportokból. Ezt a példát nem kell használat előtt módosítania.

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **Csatlakozzon a virtuális géphez.** Győződjön meg arról, hogy VPN-kapcsolattal csatlakozik a virtuális hálózathoz. A távoli asztali kapcsolat megnyitásához írja be a tálca keresőmezőjébe az „RDP” vagy a „Távoli asztali kapcsolat” kifejezést, majd válassza a Távoli asztali kapcsolat lehetőséget. A távoli asztali kapcsolatot megnyithatja a PowerShell `mstsc` parancsával is. A távoli asztali kapcsolatban írja be a virtuális gép magánhálózati IP-címét. A további beállításokat a „Beállítások megjelenítése” gombra kattintva módosíthatja. Ha végzett, hozza létre a kapcsolatot.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Egy virtuális géppel létrehozni kívánt RDP-kapcsolat hibaelhárítása

Ha probléma adódik egy virtuális gép VPN-kapcsolaton keresztüli csatlakoztatása során, érdemes ellenőrizni néhány dolgot.

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Ellenőrizze, hogy a virtuális gép magánhálózati IP-címéhez csatlakozik-e.
- Ha tud csatlakozni a virtuális géphez a magánhálózati IP-címmel, de a számítógép nevével nem, ellenőrizze, hogy a DNS-konfiguráció megfelelő-e.
- Tekintse meg a [virtuális gép távoli asztali kapcsolatainak hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) kapcsolatos részt.