---
title: Magánhálózati IP-címek konfigurálása virtuális gépek – Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure parancssori felület (CLI) használó virtuális gépek magánhálózati IP-címek.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4f6a40fde23ee70391c5057762f17ce1eb44123
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561128"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Az Azure CLI használatával virtuális gép magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [kezelése a klasszikus üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Azure CLI az alábbi Példaparancsok egy meglévő egyszerű környezetében várható. Ha azt szeretné, akkor jelennek meg ebben a dokumentumban a parancsok futtatásához először hozhat létre a leírt tesztkörnyezet [hozzon létre egy vnetet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Adjon meg egy statikus magánhálózati IP-cím, egy virtuális gép létrehozásakor

Nevű virtuális gép létrehozása *DNS01* a a *előtérbeli* nevű virtuális hálózat alhálózatának *TestVNet* egy statikus magánhálózati IP-címét *192.168.1.101*, teljes az alábbi lépéseket:

1. Ha még nem még, telepítése és konfigurálása a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). 

2. Hozzon létre egy nyilvános IP-címet a virtuális Gépet a [az network public-ip létrehozása](/cli/azure/network/public-ip#az_network_public_ip_create) parancsot. A kimenet után látható lista ismerteti a használt paramétereket.

    > [!NOTE]
    > Azt szeretné, vagy kell használnia az argumentumok a jelen eltérő értékeket, és azt követő lépéseit, attól függően, hogy a környezetben.
   
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

   * `--resource-group`: Az erőforráscsoport, amelyben létrehozza a nyilvános IP-cím nevére.
   * `--name`: A nyilvános IP-cím neve.
   * `--location`: Az azure-régió, amelyben létrehozza a nyilvános IP-cím.

3. Futtassa a [az network nic létrehozása](/cli/azure/network/nic#az_network_nic_create) paranccsal hozzon létre egy hálózati Adaptert statikus magánhálózati IP-Címmel rendelkező. A kimenet után látható lista ismerteti a használt paramétereket. 
   
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

    * `--private-ip-address`: A statikus magánhálózati IP-címet a hálózati adaptereken.
    * `--vnet-name`: A VNet neve, amelyben létrehozza a hálózati adaptert.
    * `--subnet`:, Amelyben létrehozza a hálózati adapteren. az alhálózat neve

4. Futtassa a [azure virtuális gép létrehozása](/cli/azure/vm/nic#az_vm_nic_create) paranccsal hozza létre a virtuális gép nyilvános IP- és korábban létrehozott hálózati adapter használatával. A kimenet után látható lista ismerteti a használt paramétereket.
   
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
   
   Az alapszintű eltérő paraméterek [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) paramétereket.

   * `--nics`: A hálózati Adaptert, amelyhez csatlakozik a virtuális gép neve.
   
Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-cli.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus magánhálózati IP-címadatok egy virtuális gép beolvasása

Futtassa a következő Azure CLI-parancsot megfigyelni a tartozó értékeket *magánhálózati IP-foglalási-method* és *magánhálózati IP-cím*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Várt kimenet:

```json
"192.168.1.101"
```

Megjeleníti az adott IP-adatait a hálózati adapter virtuális gép, lekérdezés kifejezetten a hálózati adapter:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus magánhálózati IP-cím eltávolítása

A hálózati adapter az Azure CLI Azure Resource Manager üzembe helyezések esetében nem lehet eltávolítani egy statikus magánhálózati IP-címet. Tegye a következőket:
- Hozzon létre egy új hálózati Adaptert, amely egy dinamikus IP-címet használ
- Állítsa be a hálózati Adaptert a virtuális gép ne az újonnan létrehozott hálózati adapterhez. 

A hálózati Adaptert a virtuális gép az előző parancsokban használt módosításához kövesse az alábbi lépéseket:

1. Futtassa a **létrehozása az azure network nic** paranccsal hozzon létre egy új hálózati Adaptert dinamikus IP-címkiosztási használatával új IP-címmel. Mivel nincs IP-cím van megadva, a kiosztási módszer van **dinamikus**.

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

2. Futtassa a **azure virtuálisgép-csoportot** parancs használatával váltson a virtuális gép által használt hálózati Adapterhez.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
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
    > Ha a virtuális gép elég nagy legyen egynél több hálózati Adapterrel rendelkezik, futtassa a **törlése az azure network nic** parancs törli a régi hálózati adaptert.

## <a name="next-steps"></a>További lépések

Kezelésével kapcsolatos [IP-címbeállítások](virtual-network-network-interface-addresses.md).