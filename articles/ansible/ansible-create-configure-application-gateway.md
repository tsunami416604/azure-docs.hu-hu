---
title: Webes forgalom kezelése az Azure Application Gatewayjel az Ansible segítségével (Előzetes verzió)
description: Megtudhatja, hogyan hozhat létre és konfigurálhat az Ansible segítségével egy Azure Application Gatewayt a webes forgalma kezelésére
ms.service: ansible
keywords: ansible, azure, devops, bash, forgatókönyv, azure application gateway, load balancer, webes forgalom
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 02b98cb22d897fc9599f6e44ddc57ef4211b0893
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410844"
---
# <a name="manage-web-traffic-with-azure-application-gateway-using-ansible-preview"></a>Webes forgalom kezelése az Azure Application Gatewayjel az Ansible segítségével (Előzetes verzió)
Az [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. 

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat az Ansible használatával egy Azure Application Gatewayt, és hogyan használhatja Azure-tárolópéldányokban futó két webes kiszolgáló forgalmának kezelésére. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hálózat beállítása
> * Két Azure-tárolópéldány létrehozása httpd-rendszerképpel
> * Egy alkalmazásátjáró létrehozása a háttérkészletben a fenti Azure-tárolópéldányokkal


## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges. Az Ansible 2.7-es RC verzióját a `sudo pip install ansible[azure]==2.7.0rc2` parancs futtatásával telepítheti. Az Ansible 2.7-es verziója 2018 októberében jelenik meg. Ezt követően már nem lesz szükséges megadnia a verziót, mert az alapértelmezett verzió a 2.7-es lesz. 

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

Mentse a fenti forgatókönyvet *rg.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 
Ahhoz, hogy az alkalmazásátjáró kommunikálhasson más erőforrásokkal, létre kell hoznia egy virtuális hálózatot. 

Az alábbi példa létrehoz egy **myAGSubnet** nevű alhálózattal rendelkező **myVNet** nevű virtuális hálózatot, és **mydomain** tartományú **myAGPublicIPAddress** nevű nyilvános IP-címet. 

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

Mentse a fenti forgatókönyvet *vnet_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook vnet_create.yml
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása
Ebben a példában httpd rendszerképekkel két Azure-tárolópéldányt hozunk létre, amelyeket az alkalmazásátjáró háttérkiszolgálóiként fogunk használni.  

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

Mentse a fenti forgatókönyvet *aci_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most hozzunk létre egy alkalmazásátjárót. A következő példa létrehoz egy **myAppGateway** nevű alkalmazásátjárót háttéralkalmazás-, előtér- és HTTP-konfigurációval.  

> [!div class="checklist"]
> * A **gateway_ip_configurations** blokkban megadott **appGatewayIP** – Az átjáró IP-konfigurációjához szükséges egy hivatkozás az alhálózatra. 
> * A **backend_address_pools** blokkban megadott **appGatewayBackendPool** – Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel. 
> * A **backend_http_settings_collection** blokkban megadott **appGatewayBackendHttpSettings** – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ. 
> * A **backend_http_settings_collection** blokkban megadott **appGatewayHttpListener** – Az appGatewayBackendPool készlethez társított alapértelmezett figyelő. 
> * A **frontend_ip_configurations** blokkban megadott **appGatewayFrontendIP** – Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz. 
> * A **request_routing_rules** blokkban megadott **rule1** – Az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály. 

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

Mentse el a fenti forgatókönyvet *appgw_create.yml* néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook appgw_create.yml
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat. 

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A fenti forgatókönyvben a hálózati erőforrásokhoz a **mydomain** nevű tartomány az **eastus** régióban lett létrehozva. Ekkor ha megnyitja egy böngészőben a `http://mydomain.eastus.cloudapp.azure.com` címet, akkor az alábbi oldalt kell látnia, amely megerősíti, hogy az Application Gateway megfelelően működik.

![Hozzáférés az Application Gatewayhez](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra nincs szüksége, az alábbi példa futtatásával törölheti őket. Törli a **myResourceGroup** nevű erőforráscsoportot. 

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

Mentse a fenti forgatókönyvet *rg_delete*.yml néven. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-ban](https://docs.microsoft.com/azure/ansible/)
