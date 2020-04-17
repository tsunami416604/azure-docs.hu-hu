---
title: Windows virtuális gép létrehozása és kezelése az Azure-ban a Python használatával
description: Ismerje meg, hogyan hozhat létre és kezelhet Windows-virtuális gépeket az Azure-ban.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 670c369e25639c859f6a8d8b3c65e329b5cf7f04
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458181"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Windows-virtuális gépek létrehozása és kezelése az Azure-ban a Python használatával

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) szüksége van több támogató Azure-erőforrások. Ez a cikk a virtuális gép erőforrásainak létrehozása, kezelése és törlése a Python használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * Csomagok telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezeket a lépéseket körülbelül 20 perc alatt kell megtennie.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)alkalmazást. Válassza a **Python-fejlesztés** lehetőséget a Számítási feladatok lapon, majd kattintson a **Telepítés gombra.** Az összegzésben láthatja, hogy a **Python 3 64 bites (3.6.0)** automatikusan kiválasztódik az Ön számára. Ha már telepítette a Visual Studio alkalmazást, hozzáadhatja a Python-számítási feladatot a Visual Studio-megnyitó indítójával.
2. A Visual Studio telepítése és elindítása után kattintson az**Új** > **projekt** **fájlja** > gombra.
3. Kattintson **a Sablonok** > **Python** > **Python Alkalmazás**elemre, írja be a *myPythonProject* értéket a projekt nevéhez, válassza ki a projekt helyét, majd kattintson az **OK**gombra.

## <a name="install-packages"></a>Csomagok telepítése

1. A Solution Explorer ben a *myPythonProject*csoportban kattintson a jobb gombbal **a Python-környezetek**elemre, majd válassza **a Virtuális környezet hozzáadása**parancsot.
2. A Virtuális környezet hozzáadása képernyőn fogadja el az *env*alapértelmezett nevét, győződjön meg arról, hogy a *Python 3.6 (64 bites)* van kiválasztva az alapértelmezőhöz, majd kattintson a **Create gombra.**
3. Kattintson a jobb gombbal a létrehozott *env* környezetre, kattintson a **Python-csomag telepítése parancsra,** írja be az *azure-t* a keresőmezőbe, majd nyomja le az Enter billentyűt.

Látnia kell a kimeneti ablakokban, hogy az azure-csomagok telepítése sikeresen megtörtént. 

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lépés megkezdése előtt győződjön meg arról, hogy rendelkezik [egy Active Directory egyszerű szolgáltatással.](../../active-directory/develop/howto-create-service-principal-portal.md) Az alkalmazásazonosítót, a hitelesítési kulcsot és a bérlői azonosítót is rögzítenie kell egy későbbi lépésben.

1. Nyissa *meg myPythonProject.py* létrehozott fájlt, majd adja hozzá ezt a kódot az alkalmazás futtatásához:

    ```python
    if __name__ == "__main__":
    ```

2. A szükséges kód importálásához adja hozzá ezeket a nyilatkozatokat a .py fájl tetejéhez:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Ezután a .py fájlban adja hozzá a változókat az importálási utasítások után a kódban használt közös értékek megadásához:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Cserélje le **az előfizetés-azonosítót** az előfizetésazonosítóra.

4. A kérelmekhez szükséges Active Directory-hitelesítő adatok létrehozásához adja hozzá ezt a függvényt a .py fájl változói után:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Cserélje le **az alkalmazásazonosítót**, **a hitelesítési kulcsot**és a **bérlői azonosítót** azokkal az értékekkel, amelyeket korábban az Azure Active Directory egyszerű szolgáltatásának létrehozásakor gyűjtött.

5. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Erőforrások létrehozása
 
### <a name="initialize-management-clients"></a>Felügyeleti ügyfelek inicializálása

Felügyeleti ügyfelek szükségesek az erőforrások létrehozásához és kezeléséhez az Azure-beli Python SDK használatával. A felügyeleti ügyfelek létrehozásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás alá:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>A virtuális gép és a támogató erőforrások létrehozása

Minden erőforrásnak [erőforráscsoportban](../../azure-resource-manager/management/overview.md)kell lennie.

1. Erőforráscsoport létrehozásához adja hozzá ezt a függvényt a .py fájl változói után:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[A rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

1. Rendelkezésre állási készlet létrehozásához adja hozzá ezt a függvényt a .py fájlban lévő változók után:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) szükséges.

1. Nyilvános IP-cím létrehozásához a virtuális géphez adja hozzá ezt a függvényt a .py fájlváltozói után:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A virtuális gépnek a virtuális [hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatában kell lennie.

1. Virtuális hálózat létrehozásához adja hozzá ezt a függvényt a .py fájlváltozói után:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Ha alhálózatot szeretne hozzáadni a virtuális hálózathoz, adja hozzá ezt a függvényt a .py fájl változói után:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A virtuális gépnek hálózati adapterre van szüksége a virtuális hálózaton való kommunikációhoz.

1. Hálózati adapter létrehozásához adja hozzá ezt a függvényt a .py fájlváltozói után:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Most, hogy létrehozta az összes támogató erőforrást, létrehozhat egy virtuális gépet.

1. A virtuális gép létrehozásához adja hozzá ezt a függvényt a .py fájlváltozói után:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Ez az oktatóanyag a Windows Server operációs rendszer egy verzióját futtató virtuális gépet hoz létre. Ha többet szeretne tudni a többi lemezkép kiválasztásáról, olvassa el a [Navigálás és az Azure virtuálisgép-lemezképek kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával című témakört.
    > 
    > 

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül az ismétlődő vagy összetett feladatok automatizálására kód is létrehozható.

### <a name="get-information-about-the-vm"></a>Információ a virtuális gépről

1. A virtuális géppel kapcsolatos információk kikérése érdekében adja hozzá ezt a függvényt a .py fájl változói után:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fel kell számítania, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Amikor egy virtuális gép felvan szabadítva, a hozzá társított összes erőforrás is felszabadítva lesz, és a számlázás véget ér.

1. Ha a virtuális gépet a felosztás lekötése nélkül szeretné leállítani, adja hozzá ezt a függvényt a .py fájlváltozói után:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Ha fel szeretné osztani a virtuális gépet, módosítsa a power_off hívását erre a kódra:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>A virtuális gép elindítása

1. A virtuális gép elindításához adja hozzá ezt a függvényt a .py fájlváltozói után:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A virtuális gép méretének meghatározásakor a központi telepítés számos aspektusát figyelembe kell venni. További információ: [VM méretek](sizes.md).

1. A virtuális gép méretének módosításához adja hozzá ezt a függvényt a .py fájl változói után:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

A virtuális gépek rendelkezhetnek egy vagy több virtuális merevlemezként tárolt [adatlemezekkel.](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

1. Ha adatlemezt szeretne hozzáadni a virtuális géphez, adja hozzá ezt a függvényt a .py fájlváltozói után: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért díjat számítunk fel, mindig ajánlott törölni azokat az erőforrásokat, amelyekre már nincs szükség. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

1. Az erőforráscsoport és az összes erőforrás törléséhez adja hozzá ezt a függvényt a .py fájl változói után:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. A korábban hozzáadott függvény hívásához adja hozzá ezt a kódot a .py fájl végén található **if** utasítás hoz:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. A *myPythonProject.py*mentése .

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A konzolalkalmazás futtatásához kattintson a **Start** gombra a Visual Studio alkalmazásban.

2. Az egyes erőforrások állapotának visszaadása után nyomja le az **Enter** billentyűt. Az állapotinformációkban egy **sikeres** kiépítési állapotnak kell lennie. A virtuális gép létrehozása után lehetősége van az összes létrehozott erőforrás törlésére. Mielőtt megnyomna **az Enter** billentyűt az erőforrások törlésének megkezdéséhez, eltarthat néhány percig, hogy ellenőrizze azok létrehozását az Azure Portalon. Ha az Azure Portal meg van nyitva, előfordulhat, hogy frissítenie kell a panelt az új erőforrások megtekintéséhez.  

    Körülbelül öt percet vesz igénybe, amíg ez a konzolalkalmazás teljesen elindul az elejétől a végéig. Az alkalmazás befejezése után több percig is eltarthat, amíg az összes erőforrás és az erőforráscsoport törlődik.


## <a name="next-steps"></a>További lépések

- Ha problémák merültek fel az üzembe helyezés során, a következő lépésről lásd: [Troubleshooting resource group deployments with Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md) (Erőforráscsoportok üzemelő példányainak hibaelhárítása az Azure Portalon)
- További információ az [Azure Python-könyvtárról](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

