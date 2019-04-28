---
title: Oktatóanyag – a frissítés az egyéni rendszerképet az Azure virtuálisgép-méretezési csoport állítja be az Ansible-lel |} A Microsoft Docs
description: 'Útmutató: egyéni rendszerképpel rendelkező virtuálisgép-méretezési csoportok az Azure-ban frissíteni az Ansible használatával'
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 8ef0c9c86562519fc3d41a72c022bf2531aa9451
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767242"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Oktatóanyag: Frissítés az egyéni rendszerképet az Azure virtuálisgép-méretezési csoport állítja be, az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Virtuális gép üzembe helyezését követően a szoftver a virtuális gép konfigurálása az alkalmazás igényeinek megfelelően. Ahelyett, hogy a konfigurációs feladat elvégzése az egyes virtuális Gépekhez, létrehozhat egy egyéni rendszerképet. Egyéni rendszerkép egy meglévő virtuális Gépet, amely tartalmazza az összes telepített szoftvert egy pillanatképet. Amikor Ön [konfigurálása egy méretezési csoportot](./ansible-create-configure-vmss.md), akkor adja meg a méretezési csoport virtuális gépeinek esetében használni kívánt lemezkép. Egyéni rendszerkép használatával minden egyes Virtuálisgép-példány azonos módon konfigurálva van az alkalmazás. Egyes esetekben szükség lehet a méretezési egyéni lemezkép frissítése. Ezt a feladatot a lépéseknek az ismertetése, ebben az oktatóanyagban.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két virtuális gépet HTTPD konfigurálása
> * Egyéni rendszerkép létrehozása meglévő virtuális gépből
> * Hozzon létre egy méretezési csoportot egy rendszerképből
> * Az egyéni lemezkép frissítése

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Két virtuális gép konfigurálása

A forgatókönyv kód ebben a szakaszban két virtuális gépet hoz létre HTTPD is telepíthető. 

A `index.html` oldala minden virtuális gép tesztelési karakterláncként jeleníti meg:

* Első virtuális gép értéket jeleníti meg. `Image A`
* Második virtuális gép értéket jeleníti meg. `Image B`

Ez a karakterlánc minden virtuális gép konfigurálása másik szoftverfrissítési utánoznia hivatott.

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) , és mentse a `create_vms.yml`.
* Hozzon létre egy új fájlt `create_vms.yml` és másolja bele a következő tartalommal:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot, és cserélje le `myrg` az az erőforráscsoport neve:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Mert a `debug` a forgatókönyv szakaszait a `ansible-playbook` parancs nyomtatja ki minden virtuális gép IP-címét. Ezen IP-címek későbbi használat érdekében másolja.

![Virtuális gép IP-címek](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>A két virtuális gépekhez

Ebben a szakaszban csatlakozik minden virtuális Géphez. Ahogy korábban már említettük, az előző szakaszban, a karakterláncok `Image A` és `Image B` utánoznia, két különálló virtuális gépek különböző konfigurációival.

Az IP-címeket használja az előző szakaszban, mindkét virtuális gép csatlakozzon:

![Képernyőkép A virtuális gépről](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Képernyőfelvétel: a B virtuális gépről](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Az egyes Virtuálisgép-rendszerképek létrehozása

Ezen a ponton rendelkezik két virtuális gépet a némileg eltérő konfigurációk (azok `index.html` fájlok).

Ebben a szakaszban a forgatókönyv kód minden virtuális géphez hoz létre egy egyéni rendszerképet:

* `image_vmforimageA` – A virtuális gép, amely megjeleníti a létrehozott egyéni rendszerkép `Image A` a kezdőlapon.
* `image_vmforimageB` – A virtuális gép, amely megjeleníti a létrehozott egyéni rendszerkép `Image B` a kezdőlapon.

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) , és mentse a `capture-images.yml`.
* Hozzon létre egy új fájlt `capture-images.yml` és másolja bele a következő tartalommal:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot, és cserélje le `myrg` az az erőforráscsoport neve:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>A rendszerkép használata a méretezési csoport létrehozása

Ebben a szakaszban egy forgatókönyv használható a következő Azure-erőforrások konfigurálása:

* Nyilvános IP-cím
* Terheléselosztó
* Méretezési csoport a hivatkozások `image_vmforimageA`

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) , és mentse a `create-vmss.yml`.
* Hozzon létre egy új fájlt `create-vmss.yml` és másolja bele a következő tartalommal: "

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot, és cserélje le `myrg` az az erőforráscsoport neve:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Mert a `debug` szakasz a forgatókönyvet, a `ansible-playbook` parancs kiírja a méretezési IP-címét. A későbbi használat céljából IP-cím másolásához.

![Nyilvános IP-cím](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>A méretezési csoporthoz kapcsolódni

Ebben a szakaszban csatlakoztatja a méretezési csoporthoz. 

IP-címet használja az előző szakaszban, csatlakozzon a méretezési csoporthoz.

Ahogy korábban már említettük, az előző szakaszban, a karakterláncok `Image A` és `Image B` utánoznia, két különálló virtuális gépek különböző konfigurációival.

A méretezési csoport hivatkozások nevű egyéni rendszerképet `image_vmforimageA`. Egyéni rendszerkép `image_vmforimageA` hozta létre a virtuális gép, amelynek kezdőlapján `Image A`.

Ennek eredményeképpen látja, a kezdőlap megjelenítése `Image A`:

![A méretezési csoporthoz társítva az első virtuális gép.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Hagyja nyitva a böngészőablakot, miközben a következő szakaszban.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Méretezési csoportban lévő egyéni módosítása – kép beállítása és példányok frissítése

Ebben a szakaszban a forgatókönyv kód módosítja a méretezési lemezkép - `image_vmforimageA` való `image_vmforimageB`. Emellett a méretezési készlet által telepített összes aktuális virtuális gép frissülnek.

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) , és mentse a `update-vmss-image.yml`.
* Hozzon létre egy új fájlt `update-vmss-image.yml` és másolja bele a következő tartalommal:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot, és cserélje le `myrg` az az erőforráscsoport neve:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Térjen vissza a böngészőben, és frissítse az oldalt. 

Láthatja, hogy az alapul szolgáló virtuális gép egyéni rendszerkép frissül.

![A méretezési csoporthoz társítva a második virtuális gép](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible)