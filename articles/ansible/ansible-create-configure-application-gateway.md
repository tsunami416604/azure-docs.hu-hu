---
title: Oktatóanyag – webes forgalom kezelése az Azure Application Gateway a Ansible használatával
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Application Gatewayt a webes forgalom kezeléséhez az Ansible használatával
keywords: Ansible, Azure, devops, bash, ötletekbõl, Application Gateway, Load Balancer, webes forgalom
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241889"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Oktatóanyag: webes forgalom kezelése az Azure Application Gateway a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

Az [Azure Application Gateway](/azure/application-gateway/overview) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A forrás IP-címe és portja alapján a hagyományos Load Balancer irányítja a forgalmat egy cél IP-címhez és porthoz. Application Gateway olyan finomabb szintű szabályozást biztosít, ahol a forgalom az URL-cím alapján irányítható. Megadhatja például, hogy ha `images`, az URL-cím elérési útja, a rendszer átirányítja a forgalmat a rendszerképekhez beállított adott kiszolgálókhoz (azaz készlethez).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hálózat beállítása
> * Két Azure-beli tárolópéldány létrehozása HTTPD-rendszerképekkel
> * Alkalmazásátjáró létrehozása, amely működik az Azure-beli tárolópéldányokkal a kiszolgálókészletben

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az ebben a szakaszban található forgatókönyv-kód egy Azure-erőforráscsoportot hoz létre. Az erőforráscsoport egy olyan logikai tároló, amelyben az Azure-erőforrások konfigurálva vannak.  

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

- Az erőforráscsoport neve `myResourceGroup`. Ezt az értéket használja a rendszer az oktatóanyag során.
- Az erőforráscsoport a `eastus` helyen jön létre.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Az ebben a szakaszban található forgatókönyv-kód egy virtuális hálózatot hoz létre, amely lehetővé teszi az Application Gateway számára más erőforrásokkal való kommunikációt.

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* A `vars` szakasz a hálózati erőforrások létrehozásához használt értékeket tartalmazza. 
* Ezeket az értékeket módosítania kell az adott környezetben.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Kiszolgálók létrehozása

Az ebben a szakaszban szereplő forgatókönyv-kód két Azure Container-példányt hoz létre, amelyekben a HTTPD-lemezképek webkiszolgálóként használhatók az Application Gateway számára.  

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az ebben a szakaszban szereplő forgatókönyv-kód egy `myAppGateway` nevű Application Gateway-t hoz létre.  

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* a `appGatewayIP` a `gateway_ip_configurations` blokkban van definiálva. Az átjáró IP-konfigurációjához egy alhálózat-referencia szükséges.
* a `appGatewayBackendPool` a `backend_address_pools` blokkban van definiálva. Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
* a `appGatewayBackendHttpSettings` a `backend_http_settings_collection` blokkban van definiálva. Megadja, hogy a 80-es és a HTTP-protokollon keresztül kommunikálnak.
* a `appGatewayHttpListener` a `backend_http_settings_collection` blokkban van definiálva. Ez az appGatewayBackendPool készlethez társított alapértelmezett figyelő.
* a `appGatewayFrontendIP` a `frontend_ip_configurations` blokkban van definiálva. Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz.
* a `rule1` a `request_routing_rules` blokkban van definiálva. Ez az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook appgw_create.yml
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat.

## <a name="test-the-application-gateway"></a>Az Application Gateway tesztelése

1. Az [erőforráscsoport létrehozása](#create-a-resource-group) szakaszban meg kell adnia egy helyet. Jegyezze fel az értékét.

1. A [hálózati erőforrások létrehozása](#create-network-resources) szakaszban meg kell adnia a tartományt. Jegyezze fel az értékét.

1. A teszt URL-címéhez a következő mintát cserélje le a hely és a tartomány helyére: `http://<domain>.<location>.cloudapp.azure.com`.

1. Keresse meg a teszt URL-címét.

1. Ha az alábbi oldalt látja, az alkalmazásátjáró megfelelően működik.

    ![Működő alkalmazásátjáró sikeres tesztelése](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`-ként:

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](/azure/ansible/)