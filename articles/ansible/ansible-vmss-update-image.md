---
title: Oktatóanyag – Azure-beli virtuálisgép-méretezési csoportok egyéni rendszerképének frissítése a Ansible használatával
description: Megtudhatja, hogyan frissítheti a virtuálisgép-méretezési csoportokat az Azure-ban egyéni rendszerképpel a Ansible használatával
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 3b7baffe6ce0fadbac2dd56b9c8296c80546fa72
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241334"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Oktatóanyag: az Azure virtuálisgép-méretezési csoportok egyéni rendszerképének frissítése a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

A virtuális gép üzembe helyezése után konfigurálja a virtuális gépet az alkalmazás által igényelt szoftverrel. Ennek a konfigurációs feladatnak az egyes virtuális gépeken való használata helyett egyéni rendszerképet hozhat létre. Az egyéni rendszerkép egy meglévő virtuális gép pillanatképe, amely tartalmazza a telepített szoftvereket. [Méretezési csoport konfigurálásakor](./ansible-create-configure-vmss.md)meg kell adnia a méretezési csoport virtuális gépei számára használni kívánt rendszerképet. Egyéni rendszerkép használatával minden virtuálisgép-példány azonos módon van konfigurálva az alkalmazáshoz. Időnként előfordulhat, hogy frissítenie kell a méretezési csoport egyéni rendszerképét. Ez a feladat az oktatóanyag középpontjában áll.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két virtuális gép konfigurálása a HTTPD segítségével
> * Egyéni rendszerkép létrehozása meglévő virtuális gépről
> * Méretezési csoport létrehozása rendszerképből
> * Az egyéni rendszerkép frissítése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Két virtuális gép konfigurálása

Az ebben a szakaszban található forgatókönyv-kód két olyan virtuális gépet hoz létre, amelyeken a HTTPD is telepítve van. 

Az egyes virtuális gépekhez tartozó `index.html` oldalon egy teszt sztring jelenik meg:

* Az első virtuális gép a `Image A` értéket jeleníti meg
* A második virtuális gép a `Image B` értéket jeleníti meg

Ez a karakterlánc az egyes virtuális gépek különböző szoftverekkel való konfigurálását jelenti.

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) forgatókönyvet, és mentse a `create_vms.yml` értékre.
* Hozzon létre egy `create_vms.yml` nevű új fájlt, és másolja bele a következő tartalomba:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` paranccsal, és cserélje le az `myrg` értéket az erőforráscsoport nevére:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

A forgatókönyv @no__t – 0 szakaszában az `ansible-playbook` parancs az egyes virtuális gépek IP-címét fogja kinyomtatni. Másolja ezeket az IP-címeket későbbi használatra.

![Virtuális gépek IP-címei](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Kapcsolódás a két virtuális géphez

Ebben a szakaszban minden virtuális géphez csatlakozik. Ahogy az előző szakaszban is említettük, a `Image A` és a `Image B` karakterláncok két különböző konfigurációval rendelkező virtuális gépet utánoznak.

Az előző szakasz IP-címeinek használatával csatlakozhat mindkét virtuális géphez:

![Képernyőkép az A virtuális gépről](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Képernyőkép a B virtuális gépről](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Lemezképek létrehozása az egyes virtuális gépekről

Ekkor két, némileg eltérő konfigurációval rendelkező virtuális gép van (a `index.html` fájl).

Az ebben a szakaszban található forgatókönyv-kód minden virtuális géphez létrehoz egy egyéni rendszerképet:

* @no__t – 0 – a virtuális géphez létrehozott egyéni rendszerkép, amely a kezdőlapján a `Image A` értéket jeleníti meg.
* @no__t – 0 – a virtuális géphez létrehozott egyéni rendszerkép, amely a kezdőlapján a `Image B` értéket jeleníti meg.

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) forgatókönyvet, és mentse a `capture-images.yml` értékre.
* Hozzon létre egy `capture-images.yml` nevű új fájlt, és másolja bele a következő tartalomba:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Futtassa a forgatókönyvet a `ansible-playbook` paranccsal, és cserélje le az `myrg` értéket az erőforráscsoport nevére:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Méretezési csoport létrehozása az A rendszerkép használatával

Ebben a szakaszban egy, a következő Azure-erőforrások konfigurálására szolgáló forgatókönyvt használunk:

* Nyilvános IP-cím
* Load Balancer
* Méretezési csoport, amely a `image_vmforimageA` értékre hivatkozik

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) forgatókönyvet, és mentse a `create-vmss.yml` értékre.
* Hozzon létre egy `create-vmss.yml` nevű új fájlt, és másolja bele a következő tartalomba: "

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` paranccsal, és cserélje le az `myrg` értéket az erőforráscsoport nevére:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

A forgatókönyv @no__t – 0 szakasza miatt a `ansible-playbook` parancs a méretezési csoport IP-címét fogja kinyomtatni. Másolja ezt az IP-címet későbbi használatra.

![Nyilvános IP-cím](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Kapcsolódás a méretezési csoporthoz

Ebben a szakaszban a méretezési csoporthoz csatlakozik. 

Az előző szakasz IP-címének használatával kapcsolódjon a méretezési csoporthoz.

Ahogy az előző szakaszban is említettük, a `Image A` és a `Image B` karakterláncok két különböző konfigurációval rendelkező virtuális gépet utánoznak.

A méretezési csoport a `image_vmforimageA` nevű egyéni rendszerképre hivatkozik. A `image_vmforimageA` egyéni rendszerkép olyan virtuális gépről lett létrehozva, amelynek a kezdőlapján a `Image A` látható.

Ennek eredményeképpen megjelenik egy `Image A` oldalt megjelenítő Kezdőlap:

![A méretezési csoport az első virtuális géphez van társítva.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Hagyja nyitva a böngészőablakot, és folytassa a következő szakasszal.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Egyéni rendszerkép módosítása a méretezési csoport és a frissítési példányok között

Az ebben a szakaszban szereplő forgatókönyv-kód a méretezési csoport rendszerképét (@no__t – 0 – `image_vmforimageB` értékre módosítja. A méretezési csoport által telepített összes aktuális virtuális gép is frissül.

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) forgatókönyvet, és mentse a `update-vmss-image.yml` értékre.
* Hozzon létre egy `update-vmss-image.yml` nevű új fájlt, és másolja bele a következő tartalomba:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Futtassa a forgatókönyvet a `ansible-playbook` paranccsal, és cserélje le az `myrg` értéket az erőforráscsoport nevére:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Térjen vissza a böngészőhöz, és frissítse az oldalt. 

Láthatja, hogy a virtuális gép alapjául szolgáló egyéni rendszerkép frissült.

![A méretezési csoport a második virtuális géphez van társítva](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`-ként:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible)