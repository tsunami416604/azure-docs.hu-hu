---
title: Oktatóanyag – webes forgalom kezelése az Azure Application Gateway az Ansible-lel |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Application Gatewayt a webes forgalom kezeléséhez az Ansible használatával
keywords: az ansible, azure, devops, bash, forgatókönyv, az Alkalmazásátjáró, terheléselosztó-, web forgalom
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3fd16381aba87b711e799835c9f069e9c53a02ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765978"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Oktatóanyag: Webes forgalom kezelése az Azure Application Gateway az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

Az [Azure Application Gateway](/azure/application-gateway/overview) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A forrás IP-cím és port alapján a hagyományos terheléselosztók irányítják a forgalmat a egy cél IP-cím és port. Az Application Gateway lehetővé teszi egy kifinomultabb mértékű forgalom hol lehet továbbítani az URL-cím alapján. Ha például meghatározhat, ha `images` URL-cím elérési út, adatforgalmat egy meghatározott készletének a kiszolgálók (más néven készletek) konfigurált rendszerképeket.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hálózat beállítása
> * Két Azure-beli tárolópéldány létrehozása HTTPD-rendszerképekkel
> * Alkalmazásátjáró létrehozása, amely működik az Azure-beli tárolópéldányokkal a kiszolgálókészletben

## <a name="prerequisites"></a>Előfeltételek

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A forgatókönyv kód ebben a szakaszban egy Azure-erőforráscsoportot hoz létre. Egy erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások konfigurálva vannak.  

Mentse a következő forgatókönyvet `rg.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

- Az erőforráscsoport neve `myResourceGroup`. Ezt az értéket az oktatóanyag során használatos.
- Az erőforrás-csoport ekkor létrejön a `eastus` helyét.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A forgatókönyv kód ebben a szakaszban létrehoz egy virtuális hálózatot az application gateway más erőforrásokkal kommunikálni engedélyezéséhez.

Mentse a következő forgatókönyvet `vnet_create.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* A `vars` szakasz tartalmazza a hálózati erőforrások létrehozásához használt értékeket. 
* Módosítsa ezeket az értékeket az adott környezethez kell.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Kiszolgálók létrehozása

A forgatókönyv kód ebben a szakaszban két az Azure container instances hoz létre HTTPD lemezképek használható webkiszolgálók az application gateway számára.  

Mentse a következő forgatókönyvet `aci_create.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

A forgatókönyv kód ebben a szakaszban létrehoz egy application gateway, nevű `myAppGateway`.  

Mentse a következő forgatókönyvet `appgw_create.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* `appGatewayIP` van definiálva a `gateway_ip_configurations` letiltása. Az átjáró IP-konfigurációjához egy alhálózat-referencia szükséges.
* `appGatewayBackendPool` van definiálva a `backend_address_pools` letiltása. Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
* `appGatewayBackendHttpSettings` van definiálva a `backend_http_settings_collection` letiltása. Azt is meghatározza, hogy a 80-as portot és a egy HTTP-protokoll használt kommunikációhoz.
* `appGatewayHttpListener` van definiálva a `backend_http_settings_collection` letiltása. Ez az appGatewayBackendPool készlethez társított alapértelmezett figyelő.
* `appGatewayFrontendIP` van definiálva a `frontend_ip_configurations` letiltása. Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz.
* `rule1` van definiálva a `request_routing_rules` letiltása. Ez az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook appgw_create.yml
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Az a [hozzon létre egy erőforráscsoportot](#create-a-resource-group) szakaszban adja meg a helyét. Jegyezze fel annak értékét.

1. Az a [hálózati erőforrások létrehozása](#create-network-resources) szakaszban megadhatja a tartományt. Jegyezze fel annak értékét.

1. A teszt URL-címéhez azáltal, hogy a hely és a tartomány a következő mintának: `http://<domain>.<location>.cloudapp.azure.com`.

1. Keresse meg a tesztcélú URL-cím.

1. Ha az alábbi oldalt látja, az alkalmazásátjáró megfelelően működik.

    ![Működő alkalmazásátjáró sikeres tesztelése](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](/azure/ansible/)