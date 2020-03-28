---
title: Oktatóanyag – Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokba az Azure-ban az Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja az Ansible segítségével az Azure virtuálisgép-méretezési csoportjait, és hogyan helyezheti üzembe az alkalmazást a méretezési csoporton
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940858"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokba az Azure-ban az Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Az Azure-beli virtuális gépek egy csoportjának gazdagépadatainak lekérése
> * A mintaalkalmazás klónozása és létrehozása
> * A JRE (Java Runtime Environment) telepítése méretezési csoportra
> * A Java-alkalmazás központi telepítése méretezési készletre

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **A git** - [git](https://git-scm.com) az oktatóanyagban használt Java minta letöltésére szolgál.
- **Java SE Development Kit (JDK)** – A [JDK](https://aka.ms/azure-jdks) a Java-mintaprojekt létrehozásához szükséges.
- **Az Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) a minta Java-projekt létrehozásához használatos.

## <a name="get-host-information"></a>Gazdagép információinak lekérése

Ebben a szakaszban a forgatókönyv-kód lekéri a virtuális gépek egy csoportjának gazdagépadatait. A kód leveszi a nyilvános IP-címeket és a terheléselosztót `scalesethosts` egy adott erőforráscsoporton belül, és létrehoz egy készletben megnevezett gazdagépcsoportot.

Mentse a következő mintaforgatókönyvet `get-hosts-tasks.yml` néven:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Alkalmazás előkészítése üzembe helyezéshez

Ebben a szakaszban a `git` forgatókönyv-kód a GitHubjava mintaprojektjének klónozására és a projekt létrehozásához használja. 

Mentse a következő forgatókönyvet `app.yml` néven:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Futtassa az Ansible-mintaforgatókönyvet az alábbi paranccsal:

  ```bash
  ansible-playbook app.yml
  ```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Az alkalmazás telepítése méretezési készletre

Az ebben a szakaszban található forgatókönyvkód a következőkre szolgál:

* Telepítse a JRE-t egy nevű gazdagépcsoportra`saclesethosts`
* A Java-alkalmazás telepítése egy nevű gazdagépcsoportra`saclesethosts`

A mintaforgatókönyv kétféleképpen szerezhető be:

* [Töltse le a forgatókönyvet,](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) és mentse el `vmss-setup-deploy.yml`.
* Hozzon létre `vmss-setup-deploy.yml` egy új fájl nevű és másolja be a következő tartalmat:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:

* A `vars` szakaszban cserélje `{{ admin_password }}` le a helyőrzőt a saját jelszavára.
* Az ssh kapcsolat típusának jelszavakkal való használatához telepítse az sshpass programot:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    Centos:

    ```bash
    yum install sshpass
    ```

* Bizonyos környezetekben előfordulhat, hogy hibaüzenet et használ egy SSH-jelszó használata a kulcs helyett. Ha ez a hibaüzenet jelenik meg, letilthatja az `/etc/ansible/ansible.cfg` `~/.ansible.cfg`állomáskulcs-ellenőrzést, ha a következő sort adja hozzá a vagy:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Futtassa a forgatókönyvet az alábbi paranccsal:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Az ansible-playbook parancs futtatásának kimenete azt jelzi, hogy a minta Java alkalmazás telepítve van a méretezési csoport gazdacsoportjába:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Az eredmények ellenőrzése

Ellenőrizze a munka eredményeit a méretezési készlet terheléselosztó URL-címére navigálva:

![Az Azure-ban méretezési készletben futó Java-alkalmazás.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Automatikus skálázású virtuálisgép-méretezési csoportok az Azure-ban az Ansible használatával](./ansible-auto-scale-vmss.md)