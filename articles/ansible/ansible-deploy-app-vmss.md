---
title: Virtuális gép méretezési csoportok az Azure-ban az Ansible-alkalmazások központi telepítése
description: Ismerje meg, hogyan konfigurálhatja egy virtuálisgép-méretezési csoportot, és üzembe helyezése az Azure-beli virtuálisgép-méretezési az Ansible használatával
ms.service: ansible
keywords: az ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoportot, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008852"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Virtuális gép méretezési csoportok az Azure-ban az Ansible-alkalmazások központi telepítése
Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével telepítheti az alkalmazásokat az Azure-bA. Ez a cikk bemutatja, hogyan helyezhet üzembe egy Java-alkalmazás egy Azure-beli virtuálisgép-méretezési csoportot (VMSS).  

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.
- **Az Ansible konfigurálása** - [létrehozása Azure hitelesítő adatait, és az Ansible konfigurálása](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Az Ansible és az Azure Python SDK-modulok** 
  - [7.4 centOS](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Virtuálisgép-méretezési csoport** – Ha még nem rendelkezik virtuálisgép-méretezési csoportot állítja, akkor is [hozzon létre egy virtuálisgép-méretezési csoportot az Ansible](ansible-create-configure-vmss.md). 
- **a git** - [git](https://git-scm.com) szolgál a jelen oktatóanyagban használt Java minta letöltése.
- **Java használata fejlesztői készlet (JDK)** – a JDK segítségével hozhatók létre a minta Java-projektet.
- **Az Apache Maven build tools** – a [Apache Maven build tools](https://maven.apache.org/download.cgi) Java a mintaprojekt összeállításához szolgálnak.

> [!Note]
> Az Ansible 2.6 futtassa a következő szükséges ebben az oktatóanyagban a mintául szolgáló forgatókönyvek. 

## <a name="get-host-information"></a>Gazdagép információinak lekérése

Ez a szakasz bemutatja, hogyan Ansible használatával lekérheti az Azure-beli virtuális gépek csoportjának gazdagépadatok. Alul látható egy minta az Ansible-forgatókönyvek. A kód lekéri a nyilvános IP-címek és load balancer belül megadott erőforráscsoport, és létrehoz egy nevű gazdagépcsoport **saclesethosts** a készletben. 

Mentse a következő példa forgatókönyv, `get-hosts-tasks.yml`: 

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

## <a name="prepare-an-application-for-deployment"></a>Az alkalmazás előkészítése az üzembe helyezés  

Ebben a szakaszban a git a projekt buildjének elkészítéséhez, és klónozza a GitHub-minta Java-projektek használhatja. Mentse a következő forgatókönyv szerint `app.yml`:

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

A minta az Ansible-forgatókönyvek futtatása a következő paranccsal:

  ```bash
  ansible-playbook app.yml
  ```

Az ansible-forgatókönyvek parancs kimenete megjeleníti meg, ahol láthatja, hogy azt beépített a mintaalkalmazás klónozása a Githubról a következőhöz hasonló:

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

## <a name="deploy-the-application-to-vmss"></a>A VMSS-alkalmazás üzembe helyezése

A következő szakaszt az Ansible-forgatókönyvek telepíti a JRE (Java-futtatókörnyezet) nevű gazdagépcsoport **saclesethosts**, és telepíti a Java-alkalmazás nevű gazdagépcsoport **saclesethosts**: 

(Változás a `admin_password` , a saját jelszavát.)

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

Az előző példa Ansible forgatókönyv, mentheti `vmss-setup-deploy.yml`, vagy [töltse le a teljes minta forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Használatához a ssh kapcsolat írja be a jelszavakat, a sshpass programot kell telepíteni. 
  - Ubunto 16.04, futtassa a parancsot `apt-get install sshpass`.
  - CentOS 7.4, futtassa a parancsot `yum install sshpass`.

Előfordulhat, hogy a hasonló hibával találkozik **egy SSH-jelszó helyett egy kulcs használata nem lehetséges Állomáskulcs-ellenőrzés engedélyezve van, mert a sshpass nem támogatja ezt.  Ez a gazdagép ujjlenyomat hozzáadása a known_hosts fájlt, hogy ez a gazdagép kezelése.** Ha ezt a hibát látja, adja hozzá a következő sort vagy ellenőrzése állomáskulcs letilthatja a `/etc/ansible/ansible.cfg` fájl vagy a `~/.ansible.cfg` fájlt:
  ```bash
  [defaults]
  host_key_checking = False
  ```

A forgatókönyv futtatása a következő paranccsal:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Az ansible-forgatókönyvek parancs kimenete azt jelzi, hogy a minta Java-alkalmazás telepítve van a virtuálisgép-méretezési csoport:

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

Congratulation! Az alkalmazás most már fut az Azure-ban. Mostantól válthat az URL-címet a terheléselosztó a virtuális gép méretezési csoporthoz:

![Az Azure-beli virtuális gép méretezési csoportban lévő futó Java-alkalmazás.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Az Ansible minta forgatókönyv az vmss-hez](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)