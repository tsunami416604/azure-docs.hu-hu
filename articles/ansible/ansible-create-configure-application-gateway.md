---
title: Az Azure Application Gatewayen a webes forgalom kezelése Ansible-lel
description: Megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Application Gatewayt a webes forgalom kezeléséhez az Ansible használatával
ms.service: ansible
keywords: az ansible, azure, devops, bash, forgatókönyv, az Alkalmazásátjáró, terheléselosztó-, web forgalom
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 43e8681e5266f113d466a138abeeda77aff1c18b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052257"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Az Azure Application Gatewayen a webes forgalom kezelése Ansible-lel

Az [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát.

Az Ansible használatával automatizálhatja a környezetében található erőforrások üzembe helyezését és konfigurálását. Ebből a cikkből megtudhatja, hogyan hozhat létre alkalmazásátjárót az Ansible használatával. A cikk azt is bemutatja, hogyan kezelheti a forgalmat az átjáró használatával két olyan webkiszolgálón, amelyek az Azure tárolópéldányokban futnak.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * A hálózat beállítása
> * Két Azure-beli tárolópéldány létrehozása HTTPD-rendszerképekkel
> * Alkalmazásátjáró létrehozása, amely működik az Azure-beli tárolópéldányokkal a kiszolgálókészletben

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges. 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot az **eastus** helyen.

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

Mentse a forgatókönyvet *rg.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Ahhoz, hogy az alkalmazásátjáró kommunikálhasson más erőforrásokkal, először hozzon létre egy virtuális hálózatot.

Az alábbi példa létrehoz egy **myVNet** nevű virtuális hálózatot, egy **myAGSubnet** nevű alhálózatot és egy **mydomain** tartománnyal rendelkező **myAGPublicIPAddress** nevű nyilvános IP-címet.

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

Mentse a forgatókönyvet *vnet_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Kiszolgálók létrehozása

Az alábbi példa bemutatja, hogy hozhat létre két Azure-beli tárolópéldányt HTTPD-rendszerképekkel, hogy azokat webkiszolgálóként lehessen használni az alkalmazásátjáróhoz.  

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

Mentse a forgatókönyvet *aci_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az alábbi példa létrehoz egy **myAppGateway** nevű alkalmazásátjárót a háttér, az előtér és a HTTP konfigurációival együtt.  

* Az **appGatewayIP** a **gateway_ip_configurations** blokkban van meghatározva. Az átjáró IP-konfigurációjához egy alhálózat-referencia szükséges.
* Az **appGatewayBackendPool** a **backend_address_pools** blokkban van meghatározva. Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
* Az **appGatewayBackendHttpSettings** a **backend_http_settings_collection** blokkban van meghatározva. Megszabja, hogy a kommunikációhoz a rendszer egy HTTP-protokollt és a 80-as portot használja.
* Az **appGatewayHttpListener** a **backend_http_settings_collection** blokkban van meghatározva. Ez az appGatewayBackendPool készlethez társított alapértelmezett figyelő.
* Az **appGatewayFrontendIP** a **frontend_ip_configurations** blokkban van meghatározva. Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz.
* A **rule1** a **request_routing_rules** blokkban van meghatározva. Ez az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály.

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

Mentse a forgatókönyvet *appgw_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook appgw_create.yml
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A hálózati erőforrások mintaforgatókönyvében létrehozta a **mydomain** nevű tartományt az **eastus** régióban. A böngészőben lépjen a `http://mydomain.eastus.cloudapp.azure.com` oldalra. Ha az alábbi oldalt látja, az alkalmazásátjáró megfelelően működik.

![Működő alkalmazásátjáró sikeres tesztelése](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége, az alábbi kód futtatásával törölheti őket. Ez töröl egy **myResourceGroup** nevű erőforráscsoportot.

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

Mentse a forgatókönyvet *rg_delete.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)
