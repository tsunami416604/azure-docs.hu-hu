---
title: Létrehozása és kezelése Python használatával Azure-beli Windows virtuális gép |} A Microsoft Docs
description: Megtanulhatja, hogyan hozhat létre és kezelhet egy Windows virtuális Gépet az Azure-ban a Python használata.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: zarhoads
ms.openlocfilehash: c1fc12bfe57edf34701d8f1f93ca18298be29160
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470268"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Létrehozása és kezelése Windows virtuális gépek az Azure-ban a Python használatával

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrásokat kell. Ez a cikk ismerteti a létrehozását, kezelését és Python használatával Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * Csomagok telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Körülbelül 20 perc alatt elvégezheti ezeket a lépéseket vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **Python fejlesztési** a számítási feladatok lapján, és kattintson a **telepítése**. A fentieket összegezve láthatja, hogy **Python 3 64-bit (3.6.0)** automatikusan ki van jelölve az Ön számára. Ha már telepítette a Visual Studio, a Python munkaterhelés, a Visual Studio indítója használatával adhat hozzá.
2. Miután telepítette és elindította a Visual Studióban, kattintson a **fájl** > **új** > **projekt**.
3. Kattintson a **sablonok** > **Python** > **Python-alkalmazás**, adja meg *myPythonProject* neve: a projekt, válassza ki a projekt helyét, és kattintson **OK**.

## <a name="install-packages"></a>Csomagok telepítése

1. A Megoldáskezelőben alatt *myPythonProject*, kattintson a jobb gombbal **Python-környezetek**, majd válassza ki **virtuális környezet hozzáadása**.
2. A virtuális környezet hozzáadása képernyőn fogadja el az alapértelmezett *env*, ügyeljen arra, hogy *Python 3.6-os (64 bites)* az alapszintű értelmezőt van kiválasztva, és kattintson **létrehozás** .
3. Kattintson a jobb gombbal a *env* környezetben létrehozott, kattintson a **Python-csomag telepítése**, adja meg *azure* a keresőmezőbe, és nyomja le az ENTER billentyűt.

Megtekintheti a kimeneti ablakban, hogy az azure-csomagok sikeresen megtörtént. 

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Ebben a lépésben a Kezdés előtt győződjön meg arról, hogy rendelkezik-e egy [Active Directory egyszerű szolgáltatás](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Egy későbbi lépésben is az Alkalmazásazonosítót, a hitelesítési kulcsot és a bérlő Azonosítóját kell kell rögzíteni.

1. Nyissa meg *myPythonProject.py* fájlt, amely lett létrehozva, és adja hozzá az ezt a kódot, amelyekkel az alkalmazás futtatásához:

    ```python
    if __name__ == "__main__":
    ```

2. Importálja a szükséges kódot, adja hozzá a következő utasításokat a .py fájl elejéhez:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Ezután a .py fájl hozzáadása után az importálási utasítást használja a kód a gyakori értékek megadásához változók:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Cserélje le **előfizetés-azonosító** az előfizetés-azonosítóval.

4. Az Active Directorybeli hitelesítő adatokat igénylő kérelmeket létrehozásához adja hozzá ezt a funkciót a változókat a .py fájl után:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Cserélje le **alkalmazásazonosító**, **hitelesítési kulcs**, és **bérlőazonosító** azokra az értékekre, amelyek korábban gyűjtött, az Azure Active Directory szolgáltatás létrehozásakor egyszerű.

5. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Erőforrások létrehozása
 
### <a name="initialize-management-clients"></a>Az ügyfelek felügyeleti inicializálása

Felügyeleti ügyfeleket az Azure-ban a Python SDK használatával erőforrások létrehozásához és felügyeletéhez szükségesek. A felügyeleti ügyfeleket létrehozásához adja hozzá a kódot alatt a **Ha** .py fájl majd végén utasítást:

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[A rendelkezésre állási csoportok](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépek, amelyet az alkalmazás karbantartása.

1. Hozzon létre egy rendelkezésre állási csoportot, adja hozzá ezt a funkciót a változókat a .py fájl után:
   
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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) kommunikálni a virtuális gép van szükség.

1. A virtuális gép nyilvános IP-cím létrehozásához adja hozzá ezt a funkciót a változókat a .py fájl után:

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Virtuális gép, egy alhálózaton kell lennie egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

1. Virtuális hálózat létrehozása után a változók a .py fájl adja hozzá ezt a funkciót:

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Adjon hozzá egy alhálózatot a virtuális hálózathoz, a változók a .py fájl után adja hozzá ezt a funkciót:
    
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
        
4. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A hálózati adaptert a virtuális hálózaton kommunikálni valamelyik virtuális gépnél.

1. Hozzon létre egy hálózati adaptert, adja hozzá ezt a funkciót a változókat a .py fájl után:

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A támogató erőforrások létrehozása, létrehozhat egy virtuális gépet.

1. A virtuális gép létrehozása után a változók a .py fájl adja hozzá ezt a funkciót:
   
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
    > Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verzióját futtatja. További rendszerképeket kiválasztásával kapcsolatos további tudnivalókért lásd: [és a Windows PowerShell és az Azure CLI Azure-beli virtuálisgép-rendszerképek kiválasztása](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül előfordulhat, hogy szeretne létrehozni a kód ismétlődő vagy bonyolult feladatok automatizálására.

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak lekérése

1. A virtuális gép adatainak lekérése, adja hozzá ezt a funkciót után a változók a .py fájl:

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
2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális gép leállítása és tartsa az összes beállítást, de továbbra is kell fizetnie, vagy egy virtuális gép leállítása és felszabadítása azt. Ha egy virtuális gép fel van szabadítva, az ahhoz kapcsolódó összes erőforrás is felszabadított és számlázási végpontjainak lesznek.

1. Állítsa le a virtuális gép felszabadítása, anélkül, hogy adja hozzá ezt a funkciót után a változók a .py fájl:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Ha szeretné a virtuális gép felszabadítása, módosítsa ezt a kódot power_off meghívásához:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>A virtuális gép elindítása

1. A virtuális gép elindítása után a változók a .py fájl adja hozzá ezt a funkciót:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

Bizonyos szempontokból az üzembe helyezés során a megfelelő méret a virtuális gép kell tekinteni. További információkért lásd: [Virtuálisgép-méretek](sizes.md).

1. A virtuális gép méretének módosítása után a változók a .py fájl adja hozzá ezt a funkciót:

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális Géphez

Virtuális gépek is rendelkezhetnek, egy vagy több [adatlemezek](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tárolt VHD-ként.

1. Szeretne hozzáadni egy adatlemezt a virtuális gépet, a függvény hozzáadása után a változók a .py fájl: 

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

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrások díjkötelesek, mindig célszerű törölje az erőforrást, amely már nincs rá szükség. Ha törölni szeretné a virtuális gépek és a támogató erőforrások, be kell csak törölje az erőforráscsoportot.

1. Az erőforráscsoportot és az összes erőforrás törléséhez adja hozzá ezt a funkciót a változókat a .py fájl után:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához, adja hozzá ezt a kódot alatt a **Ha** .py fájl végén utasítást:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Mentés *myPythonProject.py*.

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Futtassa a konzolalkalmazást, kattintson a **Start** a Visual Studióban.

2. Nyomja meg **Enter** után az egyes erőforrások állapotát adja vissza. Az állapotinformációk látnia kell egy **sikeres** üzembe helyezési állapota. A virtuális gép létrehozása után lehetősége van az Ön által létrehozott összes erőforrást törli. Mielőtt lenyomja **Enter** indítása erőforrás törlése, eltarthat néhány percig, ellenőrizze azok létrehozása az Azure Portalon. Ha az Azure Portalon nyissa meg, akkor előfordulhat, hogy frissíteni a panelt, tekintse meg az új erőforrások.  

    Nagyjából öt perc alatt az a Konzolalkalmazás futtatása teljesen le kell vennie a befejezéshez. Az alkalmazás összes erőforrását előtt fejeződött be, és az erőforráscsoport törlése után több percig is eltarthat.


## <a name="next-steps"></a>További lépések

- Ha problémák merültek fel az üzembe helyezés során, a következő lépésről lásd: [Troubleshooting resource group deployments with Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md) (Erőforráscsoportok üzemelő példányainak hibaelhárítása az Azure Portalon)
- Tudjon meg többet a [Azure Python-kódtár](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

