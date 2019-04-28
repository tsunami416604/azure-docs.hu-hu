---
title: Oktatóanyag – az Azure Kubernetes Service (AKS) az Ansible-lel hálózatkezelés kubenet konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan használja az Ansible konfigurálása az Azure Kubernetes Service (AKS)-fürt hálózati kubenet
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, az aks, tároló, az aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 96c280f43aec32fe3d6631108dfd7e0a3f789224
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767291"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: Az Azure Kubernetes Service (AKS) az Ansible-lel hálózatkezelés kubenet konfigurálása

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Aks-beli használatával telepíthet egy fürtöt az alábbi hálózati modellek:

- [Kubenet hálózatkezelés](/azure/aks/configure-kubenet) -hálózati erőforrások általában létrehozza és beállítja, az AKS-fürt üzembe lesz helyezve.
- [Az Azure-tároló hálózati adapter (CNI) hálózatok](/azure/aks/configure-azure-cni) -AKS-fürt csatlakozik-e meglévő virtuális hálózati erőforrásokat és konfigurációkat.

Az aks-ben az alkalmazások hálózatokkal kapcsolatos további információkért lásd: [fogalmak az aks-ben az alkalmazások hálózati](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * Az Azure kubenet hálózatkezelés konfigurálása

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A forgatókönyv kód ebben a szakaszban a következő Azure-erőforrásokat hoz létre:

- Virtuális hálózat
- A virtuális hálózatban lévő alhálózat

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

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózatban

A forgatókönyv kód ebben a szakaszban létrehoz egy AKS-fürt virtuális hálózaton belüli. 

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

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Használat `azure_rm_aks_version` modul támogatott verzió azonosításához.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.
- A `network_profile` beépülő modul kubenet hálózati tulajdonságok meghatározása.
- A `service_cidr` az AKS-fürtöt a belső szolgáltatások hozzárendelése egy IP-címet használja. Az IP-címtartományt, amely máshol nem használja a hálózati címteret kell lennie. 
- A `dns_service_ip` címének kell lennie a ". 10" a szolgáltatás IP-címtartomány-címét.
- A `pod_cidr` kell lennie a nem használt más részében lévő a hálózati környezet nagy címtér. Lehet, hogy a címtartomány elég nagy a várhatóan fel csomópontok számát. Ez a címtartomány nem módosítható, ha a fürt üzembe lesz helyezve.
- A pod IP-címtartomány egy/24 körnek címtér a fürt egyes csomópontjaihoz. A következő példában a `pod_cidr` 192.168.0.0/16, hozzárendeli az első node 192.168.0.0/24, a második csomópont 192.168.1.0/24 és a harmadik csomóponton 192.168.2.0/24.
- A fürt skálázását követve rugalmasan méretezhető, vagy frissítéseket a Azure egy pod IP-címtartományt rendelni minden új csomópont továbbra is.
- A forgatókönyv betölti `ssh_key` a `~/.ssh/id_rsa.pub`. Ha módosít, a "ssh-rsa" kezdetű (idézőjelek nélkül) - egysoros formátumot használja.
- A `client_id` és `client_secret` értékek töltődnek be a `~/.azure/credentials`, azaz az alapértelmezett hitelesítő adatait tartalmazó fájlt. Ezeket az értékeket beállítani, ha a szolgáltatás egyszerű, vagy betölteni ezeket az értékeket a környezeti változókat:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>A hálózati erőforrások társítása

Amikor egy AKS-fürtöt hoz létre, egy hálózati biztonsági csoport és az útválasztási táblázat jönnek létre. Ezeket az erőforrásokat az AKS által kezelt és létrehozásakor és szolgáltatások frissítése. A következő társítsa a hálózati biztonsági csoport és az útválasztási táblázat a virtuális hálózat alhálózatához. 

Mentse a következő forgatókönyv szerint `associate.yml`.

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

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- A `node_resource_group` van az erőforráscsoport neve, amelyben az AKS-csomópontok jönnek létre.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.


## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ez a szakasz felsorolja a teljes minta a forgatókönyvet, amely meghívja a feladatok létrehozása ebben a cikkben. 

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

Az a `vars` szakaszban, a következő módosításokat:

- Az a `resource_group` kulcs, módosítsa a `aksansibletest` értéket az erőforráscsoport nevét.
- Az a `name` kulcs, módosítsa a `aksansibletest` értéket az AKS neve.
- Az a `Location` kulcs, módosítsa a `eastus` értéket az erőforráscsoport helyét.

A teljes forgatókönyv használatával futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook aks-kubenet.yml
```

A forgatókönyv futtatása eredmények jelennek meg a következő kimenet hasonlít:

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

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

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

Az a `vars` szakaszban, cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag – Azure tároló-hálózati adapter (CNI) a hálózatkezelés konfigurálását az aks-ben az Ansible-lel](./ansible-aks-configure-cni-networking.md)