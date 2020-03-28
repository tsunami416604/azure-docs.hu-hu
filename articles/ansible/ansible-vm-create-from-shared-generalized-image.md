---
title: Oktatóanyag – Virtuálisgép- vagy virtuálisgép-méretezési csoport létrehozása az Azure megosztott képtárából az Ansible használatával
description: Ismerje meg, hogyan használhatja az Ansible segítségével virtuális gép vagy virtuálisgép-méretezési készlet et egy általános ított képa megosztott képtárban alapuló létrehozása.
keywords: ansible, azúr, devops, bash, ötletekbõl, virtuális gép, virtuális gép méretezési készlet, megosztott képgaléria
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155836"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Oktatóanyag: Hozzon létre egy virtuális gép vagy virtuális gép méretezési készletaz Azure megosztott képtár használatával Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[A Megosztott képtár](/azure/virtual-machines/windows/shared-image-galleries) egy olyan szolgáltatás, amely lehetővé teszi az egyénileg kezelt képek egyszerű kezelését, megosztását és rendszerezését. Ez a funkció olyan esetekben hasznos, ahol sok kép karbantartása és megosztása. Egyéni lemezképek között megoszthatók előfizetések és az Azure Active Directory-bérlők között. A lemezképek több régióba is replikálhatók a gyorsabb üzembe helyezési méretezés érdekében.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Általános virtuális gép és egyéni lemezkép létrehozása
> * Megosztott képtár létrehozása
> * Megosztott kép- és képverzió létrehozása
> * Virtuális gép létrehozása az általános rendszerkép használatával
> * Virtuálisgép-méretezési készlet létrehozása az általános képet használva
> * Információ a megosztott képtárról, a képről és a verziószámról.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>A minta forgatókönyveinek beszereznie

Két módja van, hogy a teljes sor minta forgatókönyvek:

- [Töltse le a SIG mappát,](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) és mentse a helyi számítógépre.
- Hozzon létre egy új fájlt minden szakaszhoz, és másolja a minta forgatókönyvét.

A `vars.yml` fájl tartalmazza az összes minta forgatókönyv által használt változókat ehhez az oktatóanyaghoz. A fájl szerkeszthető, hogy egyedi neveket és értékeket adjon meg.

Az első minta forgatókönyv `00-prerequisites.yml` létrehozza, mi szükséges az oktatóanyag befejezéséhez:
- Egy erőforráscsoport, amely egy logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése.
- Virtuális hálózat; alhálózat; nyilvános IP-cím és hálózati kártya a virtuális gép számára.
- A forrás virtuális gép, amely létrehozásához használt általánosított lemezkép.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 00-prerequisites.yml
```

Az [Azure Portalon](https://portal.azure.com)ellenőrizze a megadott `vars.yml` erőforráscsoportot az új virtuális gép és a létrehozott különböző erőforrások megtekintéséhez.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>A virtuális gép általánosítása és egyéni lemezkép létrehozása

A következő forgatókönyv, `01a-create-generalized-image.yml`általánosítja az előző lépésben létrehozott forrásvirtuális gép, majd hozzon létre egy egyéni lemezképet alapján.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Ellenőrizze az erőforráscsoportot, `testimagea` és győződjön meg arról, hogy megjelenik.

## <a name="create-the-shared-image-gallery"></a>A megosztott képtár létrehozása

A képgaléria a képek megosztására és kezelésére szolgáló tárház. A minta forgatókönyv-kód `02-create-shared-image-gallery.yml` létrehoz egy megosztott képtár az erőforráscsoportban.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Most egy új gyűjtemény `myGallery`jelenik meg az erőforráscsoportban.

## <a name="create-a-shared-image-and-image-version"></a>Megosztott kép- és képverzió létrehozása

A következő forgatókönyv, `03a-create-shared-image-generalized.yml` létrehoz egy kép definícióés egy kép változat.

A képdefiníciók közé tartozik a képtípus (Windows vagy Linux), a kiadási megjegyzések, valamint a minimális és maximális memóriakövetelmények. A képverzió a kép verziója. A galéria, a képdefiníció és a képverzió segítségével logikai csoportokba rendezheti a képeket. 

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Az erőforráscsoport most már rendelkezik egy képdefinícióval és egy képverzióval a katalógushoz.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Virtuális gép létrehozása az általánosított lemezkép alapján

Végül futtassa `04a-create-vm-using-generalized-image.yml` a virtuális gép létrehozásához az előző lépésben létrehozott általánosított lemezkép alapján.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Virtuálisgép-méretezési készletek létrehozása az általánosított lemezkép alapján

Az általánosított lemezkép alapján virtuálisgép-méretezési készletet is létrehozhat. Fuss `05a-create-vmss-using-generalized-image.yml` erre.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Információ a galériáról

A gyűjteményről, a képdefinícióról és a `06-get-info.yml`verzióról a futva kaphat információt.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>A megosztott kép törlése

A galéria erőforrásainak törléséhez tekintse `07-delete-gallery.yml`meg a forgatókönyv-mintát. Erőforrások törlése fordított sorrendben. Először is, ha ki kell hagyni a lemezkép-verziót. A kép összes verziójának törlése után törölheti a képdefiníciót. Az összes képdefiníció törlése után törölheti a gyűjteményt.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- A korábban létrehozott két erőforráscsoport törlése

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

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- Cserélje `{{ resource_group_name }}` le a helyőrzőt az erőforráscsoport nevére.
- A két megadott erőforráscsoporton belüli összes erőforrás törlődik.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)