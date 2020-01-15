---
title: Oktatóanyag – alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokban az Azure-ban a Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja az Azure virtuálisgép-méretezési csoportokat az Ansible használatával, és hogyan helyezhet üzembe alkalmazást a méretezési csoporton
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940858"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokban az Azure-ban a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Gazdagép információinak lekérése Azure-beli virtuális gépek egy csoportjára
> * A minta alkalmazás klónozása és összeállítása
> * A JRE (Java Runtime Environment) telepítése egy méretezési csoporton
> * A Java-alkalmazás üzembe helyezése egy méretezési csoporton

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - A [git](https://git-scm.com) segítségével letölthető az ebben az oktatóanyagban használt Java-minta.
- **Java SE Development Kit (JDK)** – A [JDK](https://aka.ms/azure-jdks) a Java-mintaprojekt létrehozásához szükséges.
- Az **Apache maven** - [Apache Maven](https://maven.apache.org/download.cgi) használatával felépítheti a minta Java-projektet.

## <a name="get-host-information"></a>Gazdagép információinak lekérése

Az ebben a szakaszban található forgatókönyv-kód lekéri a gazdagép adatait a virtuális gépek egy csoportjára. A kód beolvassa a nyilvános IP-címeket és a terheléselosztó egy megadott erőforráscsoporthoz való betöltését, és létrehoz egy `scalesethosts` nevű számítógépcsoportot a leltárban.

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

Az ebben a szakaszban található forgatókönyv-kód `git`t használ egy Java-minta projekt a GitHubról történő klónozásához, és létrehozza a projektet. 

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

## <a name="deploy-the-application-to-a-scale-set"></a>Az alkalmazás üzembe helyezése egy méretezési csoporton

Az ebben a szakaszban szereplő forgatókönyv-kód a következőhöz használható:

* Telepítse a JRE-t egy `saclesethosts` nevű gazdagép-csoportba
* A Java-alkalmazás üzembe helyezése `saclesethosts` nevű gazdagép-csoportba

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) forgatókönyvet, és mentse a `vmss-setup-deploy.yml`ba.
* Hozzon létre egy `vmss-setup-deploy.yml` nevű új fájlt, és másolja bele a következő tartalomba:

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* A `vars` szakaszban cserélje le a `{{ admin_password }}` helyőrzőt a saját jelszavára.
* Az SSH-kapcsolattípus jelszavakkal való használatához telepítse a sshpass programot:

    Ubuntu

    ```bash
    apt-get install sshpass
    ```

    CentOS

    ```bash
    yum install sshpass
    ```

* Bizonyos környezetekben előfordulhat, hogy a kulcs helyett egy SSH-jelszó használatával kapcsolatos hibaüzenet jelenik meg. Ha ezt a hibaüzenetet kapja, letilthatja a gazdagép kulcsának ellenőrzését úgy, hogy hozzáadja a következő sort `/etc/ansible/ansible.cfg` vagy `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Futtassa a forgatókönyvet az alábbi paranccsal:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

A Ansible-ötletekbõl parancs futtatásának kimenete azt jelzi, hogy a minta Java-alkalmazás telepítve lett a méretezési csoport állomásneve:

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

Ellenőrizze a munka eredményeit a méretezési csoport terheléselosztó URL-címére való navigálással:

![A Java-alkalmazás egy méretezési csoporton fut az Azure-ban.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: virtuálisgép-méretezési csoportok autoskálázása az Azure-ban a Ansible használatával](./ansible-auto-scale-vmss.md)