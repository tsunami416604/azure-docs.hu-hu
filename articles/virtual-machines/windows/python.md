---
title: Windows rendszerű virtuális gép létrehozása és kezelése az Azure-ban a Python használatával
description: Útmutató a Windows rendszerű virtuális gépek Azure-ban való létrehozásához és kezeléséhez a Python használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: tracking-python
ms.openlocfilehash: 5bddb1d141b461eec794de0849b6234c6a5c6102
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284455"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Windows rendszerű virtuális gépek létrehozása és kezelése az Azure-ban a Python használatával

Egy [Azure-beli virtuális gépnek](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrásra van szüksége. Ez a cikk a VM-erőforrások létrehozását, kezelését és törlését ismerteti a Python használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * Csomagok telepítése
> * Hitelesítő adatok létrehozása
> * Az erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezek a lépések körülbelül 20 percet vesznek igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Visual studiót](/visualstudio/install/install-visual-studio). Válassza a **Python-fejlesztés** elemet a munkaterhelések lapon, majd kattintson a **telepítés**gombra. Az összegzésben láthatja, hogy a **Python 3 64-bit (3.6.0)** automatikusan ki van választva. Ha már telepítette a Visual studiót, a Python munkaterhelést a Visual Studio Launcher használatával adhatja hozzá.
2. A Visual Studio telepítése és elindítása után kattintson a **fájl**  >  **új**  >  **projekt**elemre.
3. Kattintson a **sablonok**  >  **Python**  >  **Python-alkalmazás**elemre, írja be a *myPythonProject* nevet a projekt neveként, válassza ki a projekt helyét, majd kattintson **az OK**gombra.

## <a name="install-packages"></a>Csomagok telepítése

1. Megoldáskezelő a *myPythonProject*alatt kattintson a jobb gombbal a **Python-környezetek**elemre, majd válassza a **virtuális környezet hozzáadása**elemet.
2. A virtuális környezet hozzáadása képernyőn fogadja el az *env*alapértelmezett nevét, győződjön meg arról, hogy a *Python 3,6 (64-bit)* van kiválasztva az alapértelmező számára, majd kattintson a **Létrehozás**gombra.
3. Kattintson a jobb gombbal a létrehozott *env* -környezetre, kattintson a **Python-csomag telepítése**parancsra, írja be az *Azure* kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

A kimeneti ablakokban látnia kell, hogy az Azure-csomagok telepítése sikeres volt. 

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lépés elkezdése előtt győződjön meg arról, hogy rendelkezik [Active Directory egyszerű szolgáltatással](../../active-directory/develop/howto-create-service-principal-portal.md). Az alkalmazás AZONOSÍTÓját, a hitelesítési kulcsot és a bérlő AZONOSÍTÓját is rögzíteni kell egy későbbi lépésben.

1. Nyissa meg a létrehozott *myPythonProject.py* fájlt, majd adja hozzá ezt a kódot az alkalmazás futtatásának engedélyezéséhez:

    ```python
    if __name__ == "__main__":
    ```

2. A szükséges kód importálásához adja hozzá a következő utasításokat a. a. a. a fájl elejéhez:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. A. a. a fájl mellett adja hozzá a változókat az importálási utasítások után a kódban használt általános értékek megadásához:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Cserélje le az **előfizetés-azonosítót** az előfizetés azonosítójára.

4. A kérések elvégzéséhez szükséges Active Directory hitelesítő adatok létrehozásához adja hozzá ezt a függvényt a. a. file-ban található változók után:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Cserélje le az **Application-ID**, a **Authentication-Key**és a **bérlői azonosító** értékét azokra az értékekre, amelyeket korábban a Azure Active Directory egyszerű szolgáltatás létrehozásakor gyűjtött.

5. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Az erőforrások létrehozása
 
### <a name="initialize-management-clients"></a>Felügyeleti ügyfelek inicializálása

Az Azure-beli Python SDK használatával az erőforrások létrehozásához és kezeléséhez felügyeleti ügyfelek szükségesek. A felügyeleti ügyfelek létrehozásához adja hozzá ezt a kódot az **IF** utasításban a. file.

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

Az összes erőforrást egy [erőforráscsoporthoz](../../azure-resource-manager/management/overview.md)kell foglalni.

1. Erőforráscsoport létrehozásához adja hozzá ezt a függvényt a. a. file-fájl változói után:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

A [rendelkezésre állási](tutorial-availability-sets.md) csoportok megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

1. A rendelkezésre állási csoport létrehozásához adja hozzá ezt a függvényt a. a. fájl változói után:
   
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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/public-ip-addresses.md) szükséges.

1. Ha nyilvános IP-címet szeretne létrehozni a virtuális géphez, adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:

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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A virtuális gépnek egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatán kell lennie.

1. Virtuális hálózat létrehozásához adja hozzá ezt a függvényt a. a. file-fájl változói után:

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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Ha az alhálózatot hozzá szeretné adni a virtuális hálózathoz, adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:
    
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
        
4. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

A virtuális gépnek hálózati adapterre van szüksége a virtuális hálózaton való kommunikációhoz.

1. Hálózati adapter létrehozásához adja hozzá ezt a függvényt a. a. fájl változói után:

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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Most, hogy létrehozta az összes támogatási erőforrást, létrehozhat egy virtuális gépet.

1. A virtuális gép létrehozásához adja hozzá ezt a függvényt a. a. file-fájl változói után:
   
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
    > Ez az oktatóanyag egy olyan virtuális gépet hoz létre, amely a Windows Server operációs rendszer egy verzióját futtatja. További információ a többi rendszerkép kiválasztásáról: [Azure-beli virtuálisgép-rendszerképek navigálása és kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával.
    > 
    > 

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett érdemes lehet kódot létrehozni az ismétlődő vagy összetett feladatok automatizálásához.

### <a name="get-information-about-the-vm"></a>A virtuális géppel kapcsolatos információk lekérése

1. A virtuális géppel kapcsolatos információk lekéréséhez adja hozzá ezt a függvényt a. a. file-fájlhoz tartozó változók után:

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
2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fizetnie kell, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Ha a virtuális gép fel van foglalva, a hozzá társított összes erőforrás fel van foglalva, és a számlázás véget ér.

1. Ha le szeretné állítani a virtuális gépet a felszabadítása nélkül, adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Ha felszabadítani szeretné a virtuális gépet, módosítsa a power_off hívást a következő kódra:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>A virtuális gép elindítása

1. A virtuális gép elindításához adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A központi telepítés számos aspektusát figyelembe kell venni a virtuális gép méretének meghatározásakor. További információ: virtuális gépek [méretei](sizes.md).

1. A virtuális gép méretének módosításához adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:

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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

A virtuális gépekhez egy vagy több VHD-ként tárolt [adatlemez](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tartozhat.

1. Ha adatlemezt szeretne hozzáadni a virtuális géphez, adja hozzá ezt a függvényt a. a. file-fájl változói után: 

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

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért kell fizetnie, mindig érdemes törölni a már nem szükséges erőforrásokat. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

1. Ha törölni szeretné az erőforráscsoportot és az összes erőforrást, adja hozzá ezt a függvényt a. a. reszelő változóhoz tartozó változók után:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. A korábban hozzáadott függvény meghívásához adja hozzá ezt a kódot az **IF** utasításban a. a. reszelő végén:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Mentse a *myPythonProject.py*.

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A konzol alkalmazás futtatásához kattintson a **Start** gombra a Visual Studióban.

2. Az egyes erőforrások állapotának visszaadása után nyomja le az **ENTER** billentyűt. Az állapotadatok között **sikeres** kiépítési állapotot kell látnia. A virtuális gép létrehozása után lehetősége van törölni az összes létrehozott erőforrást. Mielőtt megnyomja az **ENTER** billentyűt az erőforrások törlésének megkezdéséhez, eltarthat néhány percig a létrehozásuk ellenőrzéséhez a Azure Portal. Ha a Azure Portal nyitva van, előfordulhat, hogy frissítenie kell a panelt az új erőforrások megtekintéséhez.  

    Körülbelül öt percet vesz igénybe ahhoz, hogy a konzol alkalmazás teljes körűen fusson az elejétől a végéig. Az alkalmazás befejezését követően több percet is igénybe vehet, mielőtt az összes erőforrást és az erőforráscsoportot törölné.


## <a name="next-steps"></a>További lépések

- Ha problémák merültek fel az üzembe helyezés során, a következő lépésről lásd: [Troubleshooting resource group deployments with Azure Portal](../../azure-resource-manager/templates/deployment-history.md) (Erőforráscsoportok üzemelő példányainak hibaelhárítása az Azure Portalon)
- További információ az [Azure Python-könyvtárról](/python/api/overview/azure/?view=azure-python)
