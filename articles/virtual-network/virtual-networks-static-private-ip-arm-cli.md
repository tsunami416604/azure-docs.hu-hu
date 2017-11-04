---
title: "Magánhálózati IP-címek konfigurálása virtuális gépek - Azure CLI 2.0 |} Microsoft Docs"
description: "Útmutató az Azure parancssori felület (CLI) 2.0-s használó virtuális gépek magánhálózati IP-címek konfigurálásához."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
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
ms.openlocfilehash: 071156367c1f819a00d31f1d0335e301391fda81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>Az Azure CLI 2.0 használatával virtuális gépek magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók 

A következő CLI-verziók egyikével elvégezheti a feladatot: 

- [Azure CLI 1.0](virtual-networks-static-private-ip-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez 
- [Az Azure CLI 2.0](#specify-a-static-private-ip-address-when-creating-a-vm) -erőforrás felügyeleti telepítési modell (Ez a cikk) a következő generációs parancssori felület

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [statikus magánhálózati IP-cím, a klasszikus üzembe helyezési modellel kezelése](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Az Azure CLI 2.0 Példaparancsok az alábbi már létrehozott egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először leírt tesztkörnyezet felépítéséhez [hozhat létre egy vnetet](virtual-networks-create-vnet-arm-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Adjon meg egy statikus magánhálózati IP-cím, amikor a virtuális gép létrehozása

Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet* statikus magánhálózati IP-címe a *192.168.1.101*, kövesse az alábbi lépéseket:

1. Ha még nem még konfigurál, a legutóbbi [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/#login). 

2. A virtuális Géphez a nyilvános IP-cím létrehozása a [létrehozása az hálózati nyilvános ip-](/cli/azure/network/public-ip#create) parancsot. A kimenet után látható lista ismerteti a használt paramétereket.

    > [!NOTE]
    > Azt szeretné, vagy szeretné használni a különböző érték is az argumentumok ezen és a későbbi lépésekben a környezettől függően.
   
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

   * `--resource-group`: A nyilvános IP-cím létrehozásához az erőforráscsoport neve.
   * `--name`: A nyilvános IP-cím neve.
   * `--location`: Az azure-régió, ahol a nyilvános IP-cím létrehozásához.

3. Futtassa a [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#create) parancs futtatásával hozzon létre egy hálózati Adaptert statikus magánhálózati IP-címe. A kimenet után látható lista ismerteti a használt paramétereket. 
   
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

    * `--private-ip-address`: Statikus magánhálózati IP-cím, az a hálózati adaptert.
    * `--vnet-name`: A VNet neve, amelyen létrehozásához a hálózati adaptert.
    * `--subnet`: Az alhálózat, amihez létre kívánja hozni a hálózati adaptert. a név

4. Futtassa a [azure virtuális gép létrehozása](/cli/azure/vm/nic#create) parancsot a nyilvános IP- és a hálózati adapter a fenti létrehozott virtuális gép létrehozásához. A kimenet után látható lista ismerteti a használt paramétereket.
   
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
   
   A basic más paramétereket [az virtuális gép létrehozása](/cli/azure/vm#create) paraméterek.

   * `--nics`: A hálózati adapter, amely a virtuális gép csatlakozik neve.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus magánhálózati IP-címadatok a virtuális gép beolvasása

A statikus magánhálózati IP-cím, amelyet létrehozott megtekintéséhez futtassa a következő Azure CLI parancsot, és tekintse meg az értékeit *privát IP-foglalási-metódus* és *magánhálózati IP-cím*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Várt kimenet:

```json
"192.168.1.101"
```

Megjeleníti az adott IP-adatait a hálózati adapter ezt a virtuális gépet, lekérdezés kifejezetten a hálózati adapter:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek

A resource manager üzembe helyezések hozzá statikus magánhálózati IP-cím nem távolítható el az Azure parancssori felület a hálózati Adapterhez. A következők szükségesek:
- Hozzon létre egy új hálózati Adaptert, amely egy dinamikus IP-cím használja
- Állítsa be a hálózati Adaptert a virtuális gép ne az újonnan létrehozott hálózati adaptert. 

A hálózati Adaptert a virtuális gép használt a fenti parancsokban módosításához kövesse az alábbi lépéseket.

1. Futtassa a **azure-hálózat hálózati adapter létrehozása** parancs futtatásával hozzon létre egy új hálózati Adaptert dinamikus IP-lefoglalást használatával új IP-címmel. Vegye figyelembe, hogy a megadott IP-cím, mert a kiosztási módszer **dinamikus**.

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

2. Futtassa a **azure virtuálisgép-készlet** parancs futtatásával módosíthatja a virtuális gép által használt hálózati adapter.
   
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
    > Ha a virtuális gép elég nagy ahhoz, hogy több hálózati adapter, futtassa a **azure-hálózat hálózati törlése** parancs törli a régi hálózati adaptert.
   
## <a name="next-steps"></a>Következő lépések
* További tudnivalók [foglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címek.
* További tudnivalók [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címek.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

