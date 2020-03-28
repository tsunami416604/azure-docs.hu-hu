---
title: Oktatóanyag – Kubenet-hálózat konfigurálása az Azure Kubernetes szolgáltatásban (AKS) az Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja az Ansible használatával a kubenet-hálózatszolgáltatást az Azure Kubernetes-szolgáltatás (AKS) fürtjében
keywords: ansible, azúr, devops, bash, cloudshell, ötletekbõl, aks, konténer, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156891"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: Kubenet-hálózat konfigurálása az Azure Kubernetes szolgáltatásban (AKS) az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AKS használatával a következő hálózati modellek használatával telepíthet fürtöt:

- [Kubenet-hálózatkezelés](/azure/aks/configure-kubenet) – A hálózati erőforrások általában az AKS-fürt telepítésekor jönnek létre és konfigurálnak.
- [Azure Container Networking Interface (CNI) hálózati](/azure/aks/configure-azure-cni) – AKS-fürt csatlakozik a meglévő virtuális hálózati erőforrások és konfigurációk.

Az Alkalmazások AKS-ben való hálózattal kapcsolatos további tudnivalókért olvassa el az [AKS-alkalmazások hálózati fogalmait.](/azure/aks/concepts-network)

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * Az Azure kubenet hálózatkonfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Ebben a szakaszban a forgatókönyv-kód a következő Azure-erőforrásokat hozza létre:

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

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózatban

Ebben a szakaszban a forgatókönyv-kód létrehoz egy AKS-fürtöt egy virtuális hálózaton belül. 

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

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- A `azure_rm_aks_version` támogatott verzió megkereséséhez használja a modult.
- Az `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.
- A `network_profile` kubenet hálózati beépülő modul tulajdonságait határozza meg.
- Az `service_cidr` az AKS-fürt belső szolgáltatásainak IP-címhez rendelésére szolgál. Ennek az IP-címtartománynak olyan címterületnek kell lennie, amelyet a hálózat más részein nem használnak. 
- A `dns_service_ip` címnek a szolgáltatás IP-címtartományának ".10" címének kell lennie.
- A `pod_cidr` kell egy nagy címtér, amely nem használatos máshol a hálózati környezetben. A címtartománynak elég nagynak kell lennie ahhoz, hogy elférjen a csomópontok száma, amely várhatóan felskálázási. A fürt telepítése után ez a címtartomány nem módosítható.
- A pod IP-címtartománya /24 címterület hozzárendelésére szolgál a fürt minden csomópontjéhez. A következő példában `pod_cidr` a 192.168.0.0/16-os csomópont az első 192.168.0.0/24 csomópontot, a második csomópontot 192.168.1.0/24, a harmadik csomópontot pedig 192.168.2.0/24.
- A fürt méretezése vagy frissítése, az Azure továbbra is hozzárendelegy pod IP-címtartományminden új csomóponthoz.
- A forgatókönyv `ssh_key` betöltődik `~/.ssh/id_rsa.pub`. Ha módosítja, használja az egysoros formátumot - kezdve az "ssh-rsa" (idézőjelek nélkül).
- A `client_id` `client_secret` és az `~/.azure/credentials`értékek betöltődnek a programból, amely az alapértelmezett hitelesítő adatok fájlja. Ezeket az értékeket beállíthatja a szolgáltatásnévhez, vagy betöltheti ezeket az értékeket a környezeti változókból:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>A hálózati erőforrások társítása

AKS-fürt létrehozásakor létrejön egy hálózati biztonsági csoport és útvonaltábla. Ezeket az erőforrásokat az AKS kezeli, és a szolgáltatások létrehozásakor és elérhetővé teszi. Társítsa a hálózati biztonsági csoportot és az útvonaltáblát a virtuális hálózati alhálózathoz az alábbiak szerint. 

Mentse a következő `associate.yml`forgatókönyvet a módon.

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

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- Az `node_resource_group` az erőforráscsoport neve, amelyben az AKS-csomópontok létrejönnek.
- Az `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.


## <a name="run-the-sample-playbook"></a>A mintaforgatókönyv futtatása

Ez a szakasz a jelen cikkben létrehozott feladatokat meghívja teljes mintaforgatókönyvet sorolja fel. 

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

- A `resource_group` kulcs esetében `aksansibletest` módosítsa az értéket az erőforráscsoport nevére.
- A `name` kulcs esetében `aksansibletest` módosítsa az értéket az AKS-névre.
- A `Location` kulcs esetében `eastus` módosítsa az értéket az erőforráscsoport helyére.

Futtassa a teljes `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook aks-kubenet.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket jelenít meg:

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

Mentse a következő `cleanup.yml`kódot:

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

A `vars` szakaszban cserélje `{{ resource_group_name }}` le a helyőrzőt az erőforráscsoport nevére.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag – Konfigurálja az Azure Container Networking Interface (CNI) hálózati hálózatát az AKS-ben az Ansible használatával](./ansible-aks-configure-cni-networking.md)