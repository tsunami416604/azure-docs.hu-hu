---
title: Privát IP-címek konfigurálása virtuális gépekhez – Azure CLI
description: Ismerje meg, hogyan konfigurálhatja a privát IP-címeket a virtuális gépekhez az Azure parancssori felületén (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199478"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Privát IP-címek konfigurálása virtuális géphez az Azure CLI használatával


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> A következő minta Azure CLI-parancsok egy meglévő egyszerű környezetet várnak. Ha a parancsokat úgy szeretné futtatni, ahogy azok ebben a dokumentumban megjelennek, először hozza létre a vnet létrehozása című dokumentumban leírt [tesztkörnyezetet.](quick-create-cli.md)

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus privát IP-cím megadása virtuális gép létrehozásakor

*Dns01* nevű virtuális gép létrehozásához a *TestVNet* nevű virtuális hálózat *FrontEnd* alhálózatában, *192.168.1.101*statikus privát IP-címével, hajtsa végre az alábbi lépéseket:

1. Ha még nem, telepítse és konfigurálja a legújabb [Azure CLI-t,](/cli/azure/install-azure-cli) és jelentkezzen be egy Azure-fiókba [az az login](/cli/azure/reference-index)használatával.

2. Hozzon létre egy nyilvános IP-t a virtuális gép hez az [az-hálózati nyilvános ip create](/cli/azure/network/public-ip) paranccsal. A kimenet után látható lista ismerteti a használt paramétereket.

    > [!NOTE]
    > Előfordulhat, hogy a környezettől függően különböző értékeket szeretne használni az argumentumaihoz ebben és az azt követő lépésekben.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Várt kimenet:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: Annak az erőforráscsoportnak a neve, amelyben a nyilvános IP-címet létre kell hozni.
   * `--name`: A nyilvános IP neve.
   * `--location`: Azure-régió, amelyben a nyilvános IP létrehozásához.

3. Futtassa az [az hálózati nic create](/cli/azure/network/nic) parancsot statikus privát IP-címvel rendelkező hálózati adapter létrehozásához. A kimenet után látható lista ismerteti a használt paramétereket. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Várt kimenet:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Paraméterek:

    * `--private-ip-address`: A hálózati adapter statikus privát IP-címe.
    * `--vnet-name`: Annak a virtuális hálózatnak a neve, amelyben a hálózati adaptert létre szeretné hozni.
    * `--subnet`: Annak az alhálózatnak a neve, amelyben a hálózati adaptert létre szeretné hozni.

4. Futtassa az [azure vm create](/cli/azure/vm/nic) parancsot a virtuális gép létrehozásához a korábban létrehozott nyilvános IP-cím és hálózati adapter használatával. A kimenet után látható lista ismerteti a használt paramétereket.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Várt kimenet:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Az [alapszintű az vm-től](/cli/azure/vm) eltérő paraméterek létrehozása.

   * `--nics`: Annak a hálózati adapternek a neve, amelyhez a virtuális gép csatlakozik.
   
Javasoljuk, hogy ne rendelje statikusan az Azure virtuális géphez rendelt privát IP-címet egy virtuális gép operációs rendszerén belül, kivéve, ha szükséges, például [ha több IP-címet rendel egy Windows virtuális géphez.](virtual-network-multiple-ip-addresses-cli.md) Ha manuálisan állítja be a privát IP-címet az operációs rendszeren belül, győződjön meg arról, hogy az megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt privát IP-címmel, vagy elveszítheti a virtuális géphez való kapcsolódást. További információ a [privát IP-címbeállításokról.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus személyes IP-címadatok lekérése virtuális géphez

Futtassa a következő Azure CLI parancsot a *privát IP allok-metódus* és a *privát IP-cím*értékeinek megfigyeléséhez:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Várt kimenet:

```json
"192.168.1.101"
```

A virtuális gép hálózati adapterének konkrét IP-adatainak megjelenítéséhez kérdezze le a hálózati adaptert, különösen:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

A kimenet a következőképpen fog kinézni:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Statikus privát IP-cím eltávolítása virtuális gépről

Az Azure Resource Manager-telepítések hez az Azure CLI-ben nem távolíthat el statikus magánhálózati IP-címet a hálózati adapterről. A következőket kell tennie:
- Dinamikus IP-címet használó új hálózati adapter létrehozása
- Állítsa be a hálózati adaptert a virtuális gép az újonnan létrehozott hálózati adapter. 

Az előző parancsokban használt virtuális gép hálózati adapterének módosításához hajtsa végre az alábbi lépéseket:

1. Futtassa az **azure network ncreate** parancsot egy új hálózati adapter létrehozásához dinamikus IP-foglalás használatával egy új IP-címmel. Mivel nincs megadva IP-cím, a foglalási módszer **dinamikus**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Várt kimenet:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Futtassa az **azure vm set** parancsot a virtuális gép által használt hálózati adapter módosításához.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Várt kimenet:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Ha a virtuális gép elég nagy ahhoz, hogy egynél több hálózati adapter, futtassa az **azure network nic delete** parancsot a régi hálózati adapter törléséhez.

## <a name="next-steps"></a>További lépések

További információ az [IP-címbeállítások](virtual-network-network-interface-addresses.md)kezeléséről.
