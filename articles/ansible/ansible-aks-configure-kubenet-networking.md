---
title: Oktatóanyag – kubenet hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja a kubenet hálózatkezelést az Azure Kubernetes Service (ak) fürtben a Ansible használatával
keywords: Ansible, Azure, devops, bash, cloudshellben, ötletekbõl, AK, tároló, AK, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/23/2019
ms.openlocfilehash: 67b4eb9e9ee53613ec8b54b2bf8d3bbdb89778c7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881519"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: a kubenet hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AK-t használva a következő hálózati modellekkel telepíthet fürtöt:

- [Kubenet hálózatkezelés](/azure/aks/configure-kubenet) – a hálózati erőforrások általában az AK-fürt üzembe helyezésekor jönnek létre és konfigurálhatók.
- Az [Azure Container Network Interface (CNI) hálózatkezelési](/azure/aks/configure-azure-cni) -AK-fürt a meglévő virtuális hálózati erőforrásokhoz és konfigurációkhoz csatlakozik.

További információ az alkalmazásokkal való hálózatkezelésről az AK-ban: az AK-beli [alkalmazások hálózati fogalmai](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * Az Azure kubenet hálózatkezelésének konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Az ebben a szakaszban található forgatókönyv-kód a következő Azure-erőforrásokat hozza létre:

- Virtuális hálózat
- A virtuális hálózaton belüli alhálózat

Mentse a következő forgatókönyvet `vnet.yml` néven:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AK-fürt létrehozása a virtuális hálózaton

Az ebben a szakaszban szereplő forgatókönyv-kód egy AK-fürtöt hoz létre egy virtuális hálózaton belül. 

Mentse a következő forgatókönyvet `aks.yml` néven:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- Használja `azure_rm_aks_version` modult a támogatott verzió megkereséséhez.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.
- A `network_profile` a kubenet hálózati beépülő moduljának tulajdonságait határozza meg.
- A `service_cidr` az AK-fürt belső szolgáltatásainak IP-címhez való hozzárendelésére szolgál. Ez az IP-címtartomány olyan címterület, amelyet a hálózat máshol nem használ. 
- A `dns_service_ip` címnek a szolgáltatás IP-címtartomány ". 10" címének kell lennie.
- A `pod_cidr`nak olyan nagy címtartománynek kell lennie, amely nem a hálózati környezet más részében van használatban. A címtartománynek elég nagynak kell lennie ahhoz, hogy megfeleljen a felskálázásra várt csomópontok számának. Ez a címtartomány a fürt üzembe helyezése után nem módosítható.
- A pod IP-címtartomány egy/24 címterület hozzárendelésére szolgál a fürt mindegyik csomópontján. A következő példában a 192.168.0.0/16 `pod_cidr` az első csomópontot (192.168.0.0/24), a második csomópontot 192.168.1.0/24, a harmadik pedig a 192.168.2.0/24 csomópontot rendeli hozzá.
- A fürt méretezése vagy frissítése során az Azure továbbra is egy Pod IP-címtartományt rendel minden új csomóponthoz.
- A forgatókönyv betölti `ssh_key`t a `~/.ssh/id_rsa.pub`ból. Ha módosítja, használja az egysoros formátumot – az "SSH-RSA" kezdetű értékkel (idézőjelek nélkül).
- A `client_id` és `client_secret` értékek betöltődik a `~/.azure/credentials`ból, amely az alapértelmezett hitelesítőadat-fájl. Ezeket az értékeket beállíthatja az egyszerű szolgáltatásnév számára, vagy betöltheti ezeket az értékeket a környezeti változókból:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Hálózati erőforrások hozzárendelése

AK-fürt létrehozásakor létrejön egy hálózati biztonsági csoport és egy útválasztási tábla. Ezeket az erőforrásokat az AK felügyeli, és frissülnek a szolgáltatások létrehozásakor és közzétételekor. A következőképpen társítsa a hálózati biztonsági csoportot és az útválasztási táblázatot a virtuális hálózati alhálózatához. 

Mentse a következő forgatókönyvet `associate.yml` néven.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- A `node_resource_group` az az erőforráscsoport-név, amelyben az AK-csomópontok létre lettek hozva.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.


## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ez a szakasz felsorolja az ebben a cikkben létrehozott feladatokat meghívó teljes minta-forgatókönyveket. 

Mentse a következő forgatókönyvet `aks-kubenet.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

A `vars` szakaszban hajtsa végre a következő módosításokat:

- A `resource_group` kulcsnál módosítsa a `aksansibletest` értéket az erőforráscsoport nevére.
- A `name` kulcs esetében módosítsa a `aksansibletest` értéket az AK-névre.
- A `Location` kulcs esetében módosítsa a `eastus` értéket az erőforráscsoport helyére.

Futtassa a teljes forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook aks-kubenet.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket mutat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`-ként:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

A `vars` szakaszban cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag – az Azure Container Network Interface (CNI) hálózatkezelés konfigurálása az AK-ban a Ansible használatával](./ansible-aks-configure-cni-networking.md)