---
title: Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokban az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan konfigurálhat virtuálisgép-méretezési csoportokat, és hogyan helyezhet üzembe alkalmazásokat a virtuálisgép-méretezési csoportokon az Azure-ban az Ansible használatával
ms.service: ansible
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 762c14b5b6e30f6410a8d572d69651c803f079c2
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918086"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokban az Azure-ban az Ansible használatával
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible használatával üzembe helyezheti alkalmazásait az Azure-ban. Ez a cikk bemutatja, hogyan helyezhet gyorsan üzembe Java-alkalmazást egy Azure-beli virtuálisgép-méretezési csoportban (VMSS).  

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Virtuálisgép-méretezési csoport** – Ha még nem rendelkezik virtuálisgép-méretezési csoporttal, akkor [létrehozhat egyet az Ansible használatával](ansible-create-configure-vmss.md). 
- **git** - A [git](https://git-scm.com) segítségével letölthető az ebben az oktatóanyagban használt Java-minta.
- **Java SE Development Kit (JDK)** – A JDK a Java-mintaprojekt létrehozásához szükséges.
- **Apache Maven összeállítási eszközök** – Az [Apache Maven összeállítási eszközök](https://maven.apache.org/download.cgi) a Java-mintaprojekt létrehozásához szükségesek.

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.6-os verziója szükséges. 

## <a name="get-host-information"></a>Gazdagép információinak lekérése

Ez a szakasz bemutatja, hogyan használhatja az Ansible-t Azure-beli virtuálisgép-csoportok gazdagép-információinak lekéréséhez. Az alábbiakban egy Ansible-példaforgatókönyvet láthat. A kód lekéri az adott erőforráscsoportban szereplő nyilvános IP-címeket és a terheléselosztót, majd létrehoz egy **saclesethosts** nevű gazdagépcsoportot a leltárban. 

Mentse a következő mintaforgatókönyvet `get-hosts-tasks.yml` néven: 

  ```yaml
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

Ebben a szakaszban a git segítségével fogja klónozni a Java-mintaprojektet a GitHubról és létrehozni a projektet. Mentse a következő forgatókönyvet `app.yml` néven:

  ```yaml
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

Az ansible-playbook parancs a következőhöz hasonló kimenetet jelenít meg, ahol látható, hogy létrejött a GitHub-ról klónozott mintaalkalmazás:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Az alkalmazás üzembe helyezése a VMSS-en

Az Ansible-forgatókönyvben szereplő alábbi szakasz telepíti a (JRE) Java-futtatókörnyezetet egy **saclesethosts** nevű gazdagépcsoportban, és üzembe helyezi a Java-alkalmazást egy **saclesethosts** nevű gazdagépcsoporton: 

(Módosítsa az `admin_password` értékét a saját jelszavára.)

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Az előző Ansible-mintaforgatókönyvet mentheti `vmss-setup-deploy.yml` néven, vagy [letöltheti a teljes forgatókönyvet](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Ha az ssh kapcsolattípust szeretné használni jelszavakkal, telepítenie kell az sshpass programot. 
  - Ubuntu 16.04 esetén futtassa az `apt-get install sshpass` parancsot.
  - CentOS 7.4 esetén futtassa a `yum install sshpass` parancsot.

A következőhöz hasonló hibaüzenet jelenhet meg: **SSH-jelszó használata kulcs helyett nem lehetséges, mert a gazdagépkulcsok ellenőrzése engedélyezve van, és ezt az sshpass nem támogatja. A gazdagép kezeléséhez adja hozzá a gazdagép ujjlenyomatát a „known_hosts” nevű fájlhoz.** Ha ezt a hibaüzenetet látja, letilthatja a gazdagépkulcsok ellenőrzését, ha az alábbi sort hozzáadja a `/etc/ansible/ansible.cfg` vagy a `~/.ansible.cfg` fájlhoz:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Futtassa a forgatókönyvet az alábbi paranccsal:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Az ansible-playbook parancs kimenete azt jelzi, hogy a Java-mintaalkalmazás a virtuálisgép-méretezési csoport gazdagépcsoportján lett telepítve:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Gratulálunk! Az alkalmazás mostantól fut az Azure-ban. Lépjen a virtuálisgép-méretezési csoport terheléselosztójának URL-címére:

![Egy Azure-beli virtuálisgép-méretezési csoportban futó Java-alkalmazás.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible-mintaforgatókönyv VMSS-hez](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)