---
title: Oktatóanyag – az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja a kubenet hálózatkezelést az Azure Kubernetes Service (ak) fürtben a Ansible használatával
keywords: Ansible, Azure, devops, bash, cloudshellben, ötletekbõl, AK, tároló, AK, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156897"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AK-t használva a következő hálózati modellekkel telepíthet fürtöt:

- [Kubenet hálózatkezelés](/azure/aks/configure-kubenet) – a hálózati erőforrások általában az AK-fürt üzembe helyezésekor jönnek létre és konfigurálhatók.
- Az [Azure CNI Networking](/azure/aks/configure-azure-cni) -AK-fürt a meglévő virtuális hálózati (VNET) erőforrásokhoz és konfigurációkhoz van csatlakoztatva.

További információ az alkalmazásokkal való hálózatkezelésről az AK-ban: az AK-beli [alkalmazások hálózati fogalmai](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * Az Azure CNI hálózatkezelésének konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Virtuális hálózat létrehozása
- Alhálózat létrehozása a virtuális hálózaton belül

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

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Hozzon létre egy AK-fürtöt egy virtuális hálózaton belül.

Mentse a következő forgatókönyvet `aks.yml` néven:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
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
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- A támogatott verzió megkereséséhez használja a `azure_rm_aks_version` modult.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.
- A forgatókönyv betölti `ssh_key`t a `~/.ssh/id_rsa.pub`ból. Ha módosítja, használja az egysoros formátumot – az "SSH-RSA" kezdetű értékkel (idézőjelek nélkül).
- A `client_id` és `client_secret` értékek betöltődik a `~/.azure/credentials`ból, amely az alapértelmezett hitelesítőadat-fájl. Ezeket az értékeket beállíthatja az egyszerű szolgáltatásnév számára, vagy betöltheti ezeket az értékeket a környezeti változókból:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Az ebben a szakaszban ismertetett példa az oktatóanyagban bemutatott különböző szolgáltatások tesztelésére szolgál.

Mentse a következő forgatókönyvet `aks-azure-cni.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- Módosítsa a `aksansibletest` értéket az erőforráscsoport nevére.
- Módosítsa a `aksansibletest` értéket az AK-névre.
- Módosítsa a `eastus` értéket az erőforráscsoport helyére.

Futtassa a forgatókönyvt a Ansible-ötletekbõl paranccsal:

```bash
ansible-playbook aks-azure-cni.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Törölje a `vars` szakaszban említett erőforráscsoportot.

Mentse a következő forgatókönyvet `cleanup.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- Cserélje le az `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.
- A megadott erőforráscsoport összes erőforrása törölve lesz.

Futtassa a forgatókönyvt a Ansible-ötletekbõl paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Active Directory konfigurálása az AK-ban a Ansible használatával](./ansible-aks-configure-rbac.md)