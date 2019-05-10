---
title: Oktatóanyag – alkalmazások telepítése a virtuális gép méretezési csoportok az Azure-ban az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure-beli virtuálisgép-méretezési csoportok és üzembe helyezése a méretezési csoportban az Ansible használatával
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a44fd06ace9b21122f5f4253ac7d9601b54e6b62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231055"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: Alkalmazások telepítése a virtuális gép méretezési csoportok az Azure-ban az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure virtuális gépek gazdagép adatainak beolvasása
> * Klónozza, és állítsa össze a mintaalkalmazás
> * Telepítse a JRE (Java-futtatókörnyezet) egy méretezési csoportot
> * Egy méretezési csoportot a Java-alkalmazás üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - A [git](https://git-scm.com) segítségével letölthető az ebben az oktatóanyagban használt Java-minta.
- **Java SE Development Kit (JDK)** – A [JDK](https://aka.ms/azure-jdks) a Java-mintaprojekt létrehozásához szükséges.
- **Az Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) Java a mintaprojekt összeállításához.

## <a name="get-host-information"></a>Gazdagép információinak lekérése

A forgatókönyv kód ebben a szakaszban egy csoportot a virtuális gépek gazdagép adatait kérdezi le. A kód lekérdezi a nyilvános IP-címek, és terheléselosztó egy adott erőforráscsoporton belül, és létrehoz egy nevű gazdagépcsoport `scalesethosts` a készletben.

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
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Alkalmazás előkészítése üzembe helyezéshez

Ebben a szakaszban a forgatókönyv kód `git` klónozása a Githubról minta Java-projektek és létrehozza a projektet. 

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

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

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

## <a name="deploy-the-application-to-a-scale-set"></a>Egy méretezési csoportot az alkalmazás üzembe helyezése

A forgatókönyv kód ebben a szakaszban a következőkre használható:

* Telepítse a JRE nevű gazdagépcsoport `saclesethosts`
* Nevű gazdagépcsoport számára a Java-alkalmazás üzembe helyezése `saclesethosts`

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) , és mentse a `vmss-setup-deploy.yml`.
* Hozzon létre egy új fájlt `vmss-setup-deploy.yml` és másolja bele a következő tartalommal:

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* Az a `vars` szakaszban, cserélje le a `{{ admin_password }}` helyőrzőt a saját jelszavát.
* Használatához az ssh-jelszavakat, a kapcsolat típusa a sshpass program telepítéséhez:

    Ubuntu rendszeren:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* Bizonyos környezetekben látni egy SSH-jelszó használatával egy kulcs helyett kapcsolatos hiba. Ha a hibaüzenetet kapja, letilthatja a állomás kulcsát adja hozzá a következő sort ellenőrzése `/etc/ansible/ansible.cfg` vagy `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Futtassa a forgatókönyvet az alábbi paranccsal:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Az ansible-forgatókönyvek parancs kimenete azt jelzi, hogy a minta Java-alkalmazás telepítve van, a méretezési csoport:

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

A munkahelyi eredményeinek ellenőrzése az URL-címét a terheléselosztó a méretezési csoporthoz:

![Egy méretezési csoportban futó Java-alkalmazás beállítása az Azure-ban.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Automatikus skálázási virtuálisgép-méretezési csoportok az Azure-ban az Ansible használatával](./ansible-auto-scale-vmss.md)