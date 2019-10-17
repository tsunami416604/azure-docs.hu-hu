---
title: Oktatóanyag – virtuális gép vagy virtuálisgép-méretezési csoport létrehozása az Azure megosztott rendszerkép-katalógusból a Ansible használatával
description: Ismerje meg, hogyan hozhat létre virtuális gépeket vagy virtuálisgép-méretezési csoportokat a Ansible használatával a megosztott képtárban lévő általánosított rendszerkép alapján.
keywords: Ansible, Azure, devops, bash, ötletekbõl, virtuális gép, virtuálisgép-méretezési csoport, megosztott képgyűjtemény
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 814be49c972e444f2a4e4a703501e88fa1272b89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392144"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Oktatóanyag: virtuális gép vagy virtuálisgép-méretezési csoport létrehozása az Azure megosztott rendszerkép-katalógusból a Ansible használatával

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

A [megosztott](/azure/virtual-machines/windows/shared-image-galleries) képkatalógus egy olyan szolgáltatás, amellyel könnyedén kezelheti, megoszthatja és rendszerezheti az egyéni felügyelt képeket. Ez a funkció hasznos lehet olyan forgatókönyvek esetén, ahol sok lemezképet tartanak karban és osztanak meg. Az egyéni lemezképek az előfizetések között és Azure Active Directory bérlők között oszthatók meg. A lemezképek több régióba is replikálhatók a gyorsabb üzembe helyezési skálázás érdekében.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Általánosított virtuális gép és egyéni rendszerkép létrehozása
> * Megosztott Képtár létrehozása
> * Megosztott rendszerkép és rendszerkép-verzió létrehozása
> * Virtuális gép létrehozása az általánosított rendszerkép használatával
> * Virtuálisgép-méretezési csoport létrehozása az általánosított képpel
> * A megosztott képkatalógus, a rendszerkép és a verzió adatainak beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>A minta forgatókönyvek beolvasása

A minta-forgatókönyvek teljes készletét kétféleképpen szerezheti be:

- [Töltse le a SIG mappát](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) , és mentse a helyi gépre.
- Hozzon létre egy új fájlt az egyes szakaszokhoz, és másolja ki a minta-forgatókönyvet.

A `vars.yml` fájl tartalmazza az oktatóanyaghoz tartozó összes minta forgatókönyv által használt változókat. A fájl szerkesztésével egyedi neveket és értékeket adhat meg.

Az első példaként szolgáló `00-prerequisites.yml` az oktatóanyag elvégzéséhez szükséges tudnivalókat hozza létre:
- Egy erőforráscsoport, amely egy olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.
- Egy virtuális hálózat; alhálózat a virtuális gép nyilvános IP-címe és hálózati adaptere.
- A forrásként szolgáló virtuális gép, amely az általánosított rendszerkép létrehozásához használatos.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 00-prerequisites.yml
```

A [Azure Portal](https://portol.azure.com)ellenőrizze a `vars.yml` mezőben megadott erőforráscsoportot az új virtuális gép és a létrehozott különböző erőforrások megtekintéséhez.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>A virtuális gép általánosítása és egyéni rendszerkép létrehozása

A következő forgatókönyv, `01a-create-generalized-image.yml`, általánosítja az előző lépésben létrehozott forrás virtuális gépet, majd létrehoz egy egyéni rendszerképet a alapján.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Ellenőrizze az erőforráscsoportot, és ellenőrizze, hogy `testimagea` megjelenik-e.

## <a name="create-the-shared-image-gallery"></a>A megosztott Képtár létrehozása

A képkatalógus a lemezképek megosztására és kezelésére szolgáló tárház. A `02-create-shared-image-gallery.yml` forgatókönyvben szereplő forgatókönyv-kód egy megosztott képtárat hoz létre az erőforráscsoporthoz.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Ekkor megjelenik egy új katalógus, `myGallery`, az erőforráscsoporthoz.

## <a name="create-a-shared-image-and-image-version"></a>Megosztott rendszerkép és rendszerkép-verzió létrehozása

A következő forgatókönyvben a `03a-create-shared-image-generalized.yml` egy rendszerkép-definíciót és egy rendszerkép-verziót hoz létre.

A képdefiníciók közé tartoznak a rendszerképek típusa (Windows vagy Linux), a kibocsátási megjegyzések, valamint a minimális és a maximális memória-követelmények. A rendszerkép verziója a rendszerkép verziója. A katalógus, a kép definíciója és a lemezkép verziója segít a logikai csoportokba tartozó képek rendszerezésében. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Az erőforráscsoport már rendelkezik rendszerkép-definícióval és a katalógushoz tartozó rendszerkép-verzióval.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Hozzon létre egy virtuális gépet az általánosított rendszerkép alapján

Végül a `04a-create-vm-using-generalized-image.yml` futtatásával hozzon létre egy virtuális gépet az előző lépésben létrehozott általánosított rendszerkép alapján.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Virtuálisgép-méretezési csoport létrehozása az általánosított rendszerkép alapján

Egy virtuálisgép-méretezési csoport is létrehozható az általánosított rendszerkép alapján. Ehhez futtassa a `05a-create-vmss-using-generalized-image.yml` parancsot.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>A katalógussal kapcsolatos információk beolvasása

A katalógussal, a képdefinícióval és a verzióval kapcsolatos információkat a `06-get-info.yml` futtatásával érheti el.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Megosztott rendszerkép törlése

A katalógus erőforrásainak törléséhez tekintse meg a következőt: példa forgatókönyv `07-delete-gallery.yml`. Erőforrások törlése fordított sorrendben. Először törölje a rendszerkép verzióját. Az összes rendszerkép-verzió törlése után törölheti a rendszerkép definícióját. Az összes rendszerkép-definíció törlését követően törölheti a katalógust.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Törölje a korábban létrehozott két erőforrás-csoportot.

Mentse a következő forgatókönyvet `cleanup.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- Cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.
- A két megadott erőforráscsoport összes erőforrása törölve lesz.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)