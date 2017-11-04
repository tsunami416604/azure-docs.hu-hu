---
title: "Létrehozását és kezelését egy Windows virtuális Gépet az Azure-ban, Python |} Microsoft Docs"
description: "További Python használandó létrehozása és kezelése a Windows Azure-ban."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: davidmu
ms.openlocfilehash: bb777d41570d7b1dc97402d532519488912948e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Létrehozása és kezelése Windows-alapú virtuális gépek az Azure-ban, Python

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrások kell. Ez a cikk ismerteti a létrehozása, kezelése és Python Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * Csomagok telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

A lépések elvégzéséhez körülbelül 20 percet vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **Python fejlesztői** a munkaterhelések lapot, és kattintson a **telepítése**. Az összegzés láthatja, hogy **Python 3 64 bites (3.6.0)** automatikusan ki van jelölve meg. Ha már telepítette a Visual Studio, a Python munkaterhelés, a Visual Studio indítója használatával adhat hozzá.
2. Miután telepíti, és a Visual Studio indítja, kattintson a **fájl** > **új** > **projekt**.
3. Kattintson a **sablonok** > **Python** > **Python alkalmazás**, adja meg *myPythonProject* a projekt nevét, válassza ki a projekt helyét, és kattintson **OK**.

## <a name="install-packages"></a>Csomagok telepítése

1. A Megoldáskezelőben a *myPythonProject*, kattintson a jobb gombbal **Python-környezetek**, majd válassza ki **virtuális környezet hozzáadása**.
2. A virtuális környezet hozzáadása képernyőn fogadja el az alapértelmezett *env*, győződjön meg arról, hogy *Python 3.6 (64 bites)* az alapszintű értelmezőt van jelölve, és kattintson a **létrehozása**.
3. Kattintson a jobb gombbal a *env* környezetben létrehozott, kattintson a **Python-csomag telepítése**, adja meg *azure* a keresőmezőbe, majd nyomja le az ENTER billentyűt.

Meg kell jelennie a kimeneti Windows a azure csomagok telepítése sikeresen megtörtént. 

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Ez a lépés megkezdése előtt győződjön meg arról, hogy rendelkezik egy [Active Directory szolgáltatás egyszerű](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Is rögzíteni kell az alkalmazás Azonosítóját, a hitelesítési kulcs és a bérlő azonosítója, amelyekre szüksége van egy későbbi lépésben.

1. Nyissa meg *myPythonProject.py* fájl jött létre, és adja meg a kódot, hogy az alkalmazás futtatásához engedélyezése:

    ```python
    if __name__ == "__main__":
    ```

2. Importálja a szükséges kódot, vegye fel ezeket az utasításokat a .py fájl elejéhez:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Ezután .py fájl hozzáadása után az importálási utasítást, hogy adja meg a gyakori értékek a kódban használt változók:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Cserélje le **előfizetés-azonosító** az előfizetés-azonosítóval.

4. Az Active Directory hitelesítő adatait hozhatják létre, meg kell győződnie kérelmeket, a függvény hozzáadása után a változók a .py fájl:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Cserélje le **alkalmazásazonosító**, **hitelesítési kulcs**, és **bérlőazonosító** létrehozása után az Azure Active Directory szolgáltatás egyszerű gyűjtött értékekkel.

5. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Erőforrások létrehozása
 
### <a name="initialize-management-clients"></a>Felügyeleti ügyfelek inicializálása

Felügyeleti ügyfelek létrehozásához és a Python SDK használatával az Azure-erőforrások kezeléséhez szükségesek. Hozzon létre a felügyeleti ügyfeleket, vegye fel ezt a kódot a a **Ha** .py fájl majd végén utasítást:

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

### <a name="create-the-vm-and-supporting-resources"></a>A virtuális gép és az azt támogató erőforrások létrehozása

Minden erőforrás tartalmaznia kell egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md).

1. Hozzon létre egy erőforráscsoportot, a változók a .py fájl után adja hozzá ezt a funkciót:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépeket, amelyet az alkalmazás karbantartása.

1. Egy rendelkezésre állási csoport létrehozása, vegye fel ezt a funkciót után a .py fájl a változók:
   
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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) kommunikálni a virtuális gép van szükség.

1. Egy nyilvános IP-címet a virtuális gép létrehozásához, vegye fel ezt a funkciót után a .py fájl a változók:

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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Az alhálózat szerepelnie kell egy virtuális gépet egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

1. Hozzon létre egy virtuális hálózatot, adja hozzá ezt a funkciót után a .py fájl a változók:

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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Adjon hozzá egy alhálózatot a virtuális hálózat, adja hozzá ezt a funkciót a változók a .py fájl után:
    
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
        
4. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A virtuális gépek kell a hálózati adaptert a virtuális hálózaton való kommunikációhoz.

1. A hálózati adaptert létrehozni, a függvény hozzáadása után a változók a .py fájl:

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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Most, hogy létrehozta a támogató erőforrásokat, létrehozhat egy virtuális gépet.

1. A virtuális gép létrehozása, a függvény hozzáadása után a változók a .py fájl:
   
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
    > Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verziója. Más rendszerképek kiválasztásáról kapcsolatos további információkért lásd: [keresse meg és válassza ki azokat a Windows PowerShell és az Azure CLI Azure virtuális gép lemezképeket](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa folyamán érdemes lehet indítása, leállítása vagy törlése a virtuális gépek például a felügyeleti feladatok futtatásához. Emellett érdemes bonyolult vagy ismétlődő feladatok automatizálásához kódot létrehozni.

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak beolvasása

1. Ahhoz, hogy a virtuális gép adatait, a függvény hozzáadása után a .py fájl a változók:

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
2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Állítsa le a virtuális gépet és a beállítások megőrzése, de továbbra is azt számlázni, vagy állítsa le a virtuális gépet, és azt felszabadítani. Ha egy virtuális gép fel van szabadítva, vele társított összes erőforrás is felszabadított és számlázási végpontjainak.

1. A virtuális gép leállítása nélkül felszabadítása azt, a függvény hozzáadása után a változók a .py fájl:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Ha a virtuális gép felszabadítása, módosítsa ezt a kódot power_off hívása:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Indítsa el a virtuális Gépet

1. Indítsa el a virtuális gépet, adja hozzá ezt a funkciót a változók a .py fájl után:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>A virtuális gép átméretezésével

Telepítési sok szempontját figyelembe kell venni, amikor eldönti, a virtuális gép méretét. További információkért lásd: [Virtuálisgép-méretek](sizes.md).

1. Ha módosítani szeretné a virtuális gép méretét, vegye fel ezt a funkciót után a .py fájl a változók:

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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális gép

Virtuális gépek is rendelkezhetnek, egy vagy több [adatlemezek](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) merevlemezekként tárolt.

1. Adatlemez hozzáadása a virtuális gép, vegye fel ezt a funkciót után a .py fájl a változók: 

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

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokhoz van szó, ajánlott mindig egy törli az erőforrást, amely már nem szükséges. Ha törölni szeretné a virtuális gépek és a támogató erőforrásokat, meg kell nyitnia csak törölje a csoportot.

1. Az erőforráscsoport és az összes erőforrást törli, a függvény hozzáadása után a változók a .py fájl:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. A korábban hozzáadott függvény hívása, vegye fel ezt a kódot a a **Ha** a .py fájl végén utasítást:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Mentés *myPythonProject.py*.

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Futtassa a konzolalkalmazást, kattintson a **Start** a Visual Studióban.

2. Nyomja le az **Enter** után az egyes erőforrások állapotának ad vissza. Az állapot információkat kell megjelennie a **sikeres** üzembe helyezési állapota. A virtuális gép létrehozása után lehetősége van az Ön által létrehozott összes erőforrást törli. Ahhoz, hogy nyomja le az ENTER **Enter** erőforrások törlése elindításához eltarthat néhány percig, ellenőrizze azok létrehozásáról az Azure portálon. Ha az Azure-portál megnyitásához, lehetséges, hogy frissíteni a panelt új erőforrások megjelenítéséhez.  

    Öt perc a konzol alkalmazás teljesen futtatásához indítás kell vennie a befejezéshez. Miután az alkalmazás összes erőforrását előtt befejeződött, és az erőforráscsoport törlődnek több percig is eltarthat.


## <a name="next-steps"></a>Következő lépések

- Ha problémák merültek fel az üzembe helyezés során, a következő lépésről lásd: [Troubleshooting resource group deployments with Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md) (Erőforráscsoportok üzemelő példányainak hibaelhárítása az Azure Portalon)
- További információ a [Azure Python kódtár](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

