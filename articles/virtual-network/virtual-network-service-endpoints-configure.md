---
title: "Azure-beli virtuális hálózati szolgáltatásvégpontok konfigurálása | Microsoft Docs"
description: "Ismerje meg, hogyan engedélyezheti és tilthatja le a szolgáltatásvégpontokat a virtuális hálózatán"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: c9c23462f80533a224c3c2ac3658b9630f1798f9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok konfigurálása

A virtuális hálózati (VNet) szolgáltatásvégpontok lehetővé teszik Azure-szolgáltatási erőforrások biztosítását Azure-beli virtuális hálózatoknak úgy, hogy az erőforrások eléréséhez egyáltalán nincs szükség internetkapcsolatra. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és egy Azure-szolgáltatás között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-szolgáltatásokba tartó forgalom mindig a Microsoft Azure gerinchálózatában marad. További információk a [virtuális hálózati szolgáltatásvégpontokról.](virtual-network-service-endpoints-overview.md)

Ez a cikk részletesen ismerteti a szolgáltatásvégpontok engedélyezésének és letiltásának menetét. Miután a végpontok engedélyezve lettek egy Azure-szolgáltatás egy alhálózatán, a virtuális hálózatnak biztosíthatók különböző szolgáltatási erőforrások.

A szolgáltatásvégpontok konfigurálhatók az [Azure Portalon](#azure-portal), az [Azure PowerShell-lel](#azure-powershell), az [Azure parancssori felülettel](#azure-cli), vagy egy Azure Resource Manager-[sablonnal](#resource-manager-template).

>[!NOTE]
Az előzetes verzióban a virtuális hálózatok szolgáltatásvégponti funkciója csak bizonyos régiókban támogatott. A támogatott régiók listája az [Azure-beli virtuális hálózati frissítések](https://azure.microsoft.com/updates/?product=virtual-network) oldalán található.

## <a name="service-endpoint-configuration-overview"></a>Szolgáltatásvégpontok konfigurálásának áttekintése

- Szolgáltatásvégpontok csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon helyezhetők üzembe.

- A szolgáltatásvégpontokat a virtuális hálózat egyes alhálózatain lehet beállítani.

- Az alhálózatokon egy szolgáltatáshoz csak egy szolgáltatásvégpont konfigurálható. A különböző szolgáltatásokhoz (pl.: Azure Storage, Azure SQL) külön-külön szolgáltatásvégpont is konfigurálható.

- A végpontok engedélyezhetők egy új vagy meglévő alhálózaton is.

- A végpontok helyét a rendszer automatikusan konfigurálja. Alapértelmezés szerint a szolgáltatásvégpontok a virtuális hálózat régiójában vannak konfigurálva. Az Azure Storage esetén a regionális feladatátvételi funkciók támogatása érdekében a végpontok automatikusan [Azure-társviszonyban álló régiókhoz](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions) vannak konfigurálva.

  >[!NOTE]
  A virtuális hálózat/alhálózat méretétől függően a szolgáltatásvégpont engedélyezése eltarthat egy kis ideig. Győződjön meg arról, hogy a szolgáltatásvégpontok engedélyezésekor egyetlen kritikus fontosságú feladat sem fut. A szolgáltatásvégpontok az alhálózaton minden NIC-en útvonalakat módosítanak, és bezárhatják a nyitott TCP-kapcsolatokat. 

- A szolgáltatásvégpont hívása akkor ad „sikeres” eredményt, ha a szolgáltatáshoz menő forgalom az alhálózat összes NIC-jén átvált virtuális hálózati magánhálózati IP-címekre.

- Érvényes útvonalak a végpontok konfigurálásának ellenőrzéséhez:

   A szolgáltatásvégpont helyes konfigurálásának ellenőrzéséhez az alhálózat bármely NIC-jén az „érvényes útvonalak” egy új „alapértelmezett” útvonalat mutat, amelynek nextHopType típusa: VirtualNetworkServiceEndpoint, szolgáltatásonként és régiónként. További tudnivalók az [érvényes útvonalak hibaelhárításáról.](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Az érvényes útvonalak csak akkor tekinthetők meg, ha egy vagy több hálózati adapter (NIC) egy, az alhálózaton futó virtuális géphez van konfigurálva és társítva.

## <a name="azure-portal"></a>Azure Portal

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Szolgáltatásvégpont létrehozása egy alhálózaton egy virtuális hálózat létrehozásakor

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/).
Jelentkezzen be az Azure-ba az Azure-fiókjával. Ha nincs Azure-fiókja, regisztráljon egy ingyenes próbaverzióra. A fióknak rendelkeznie kell a megfelelő [engedélyekkel](#provisioning) egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.
2. Kattintson az +Új > Hálózat > Virtuális hálózat > +Hozzáadás parancsra.
3. A Virtuális hálózat létrehozása panelen adja meg a következő értékeket, majd kattintson a Létrehozás parancsra:

Beállítás | Érték
------- | -----
Név    | myVNet
Címtér | 10.0.0.0/16
Alhálózat neve|mySubnet
Alhálózati címtartomány|10.0.0.0/24
Erőforráscsoport|Hagyja bejelölve az Új létrehozása lehetőséget, majd adjon meg egy nevet.
Hely|Bármely támogatott régió, pl.: Kelet-Ausztrália
Előfizetés|Válassza ki előfizetését.
__Szolgáltatásvégpontok__|Engedélyezve
__Szolgáltatások__ | Válasszon az elérhető szolgáltatások közül egyet, vagy akár mindet. Az előzetes verzióban a támogatott szolgáltatások a __„Microsoft.Storage” és a „Microsoft.Sql”__.

Válasszon szolgáltatásokat a végpontokhoz: ![Szolgáltatásvégpontok szolgáltatásainak kiválasztása](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Ellenőrizze a beállítások helyességét, majd kattintson a Létrehozás gombra.

![Szolgáltatásvégpont beállítása](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Az Azure-szolgáltatási erőforrások a virtuális hálózat számára történő biztosításának befejezéséhez kattintson a szolgáltatás dokumentációjára a [Következő lépések](#Next%20Steps) között.


### <a name="validating-service-endpoint-configuration"></a>Szolgáltatásvégpontok konfigurálásának ellenőrzése

A következő lépésekkel ellenőrizze, hogy a szolgáltatásvégpontok konfigurálva vannak-e:

- Az erőforrások között kattintson a Virtuális hálózatok elemre. Keresse meg a virtuális hálózatot.
- Kattintson a virtuális hálózat nevére, és lépjen a Szolgáltatásvégpontok részhez.
- A konfigurált végpontok „Sikeres” állapotúként jelennek meg. Az automatikusan konfigurált helyek is láthatók

![Szolgáltatásvégpont konfigurálásának megerősítése](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Érvényes útvonalak a végpontok konfigurálásának ellenőrzéséhez

Az alhálózat egy hálózati adapteréhez (NIC) tartozó érvényes útvonal megtekintéséhez kattintson az alhálózaton bármelyik NIC-re. A Támogatás és hibaelhárítás alatt kattintson az Érvényes útvonalak elemre. Ha a végpont konfigurálva van, egy új „alapértelmezett” útvonal lesz látható, amelynek céljaként a szolgáltatás címelőtagjai szolgálnak, a nextHopType típusa pedig „VirtualNetworkServiceEndpoint”.

![Szolgáltatásvégpontok érvényes útvonalai](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Szolgáltatásvégpontok beállítása egy virtuális hálózat meglévő alhálózatain

1. Az erőforrások között kattintson a Virtuális hálózatok elemre, és keressen rá bármely meglévő virtuális hálózatra.
2. Kattintson a virtuális hálózat nevére, és lépjen a Szolgáltatásvégpontok részhez.
3. Kattintson a Hozzáadás elemre. Válassza a Szolgáltatás lehetőséget. Egyszerre csak egy szolgáltatáshoz lehet létrehozni végpontot. 
4. Válassza ki az összes alhálózatot, ahol alkalmazni szeretné a végpontot. Kattintson a Hozzáadás elemre.

![Alhálózat szolgáltatásvégpontjainak konfigurálása](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Szolgáltatásvégpont törlése
1. Az erőforrások között kattintson a Virtuális hálózatok elemre. Keressen rá egy meglévő virtuális hálózatra a virtuális hálózatok nevének szűrésével.
2. Kattintson a virtuális hálózat nevére, és lépjen a Szolgáltatásvégpontok részhez.
3. Kattintson a szolgáltatás nevére, majd kattintson a jobb gombbal a szolgáltatásvégpont bejegyzésére.
4. Válassza a Törlés elemet.

![Szolgáltatásvégpont törlése](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Beállítás előfeltételei:

- Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Egy PowerShell-munkamenet megkezdéséhez lépjen a **Start** menüre, írja be a **powershell** kifejezést, majd kattintson a **PowerShell** elemre.
- A PowerShellben jelentkezzen be az Azure-ba a `login-azurermaccount` paranccsal. A fióknak rendelkeznie kell a megfelelő [engedélyekkel](#provisioning) egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

### <a name="get-available-service-endpoints-for-azure-region"></a>Az Azure-régióhoz elérhető szolgáltatásvégpontok beszerzése

Használja az alábbi parancsot a végpontokhoz elérhető, az adott Azure-régióban támogatott szolgáltatások listájának megtekintéséhez.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Kimenet: 
Név | ID (Azonosító) | Típus
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Egy Azure Storage-szolgáltatásvégpont hozzáadása a *mySubnet* alhálózathoz a *myVNet* virtuális hálózat létrehozása közben

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Több szolgáltatást is engedélyezhet, ha vesszővel elválasztva sorolja fel a szolgáltatások nevét.
Például: "Microsoft.Storage", "Microsoft.Sql"

Várt kimenet:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Az Azure-szolgáltatási erőforrások a virtuális hálózat számára történő biztosításának befejezéséhez kattintson a szolgáltatás dokumentációjára a [Következő lépések](#Next%20Steps) között.

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Több szolgáltatásvégpont hozzáadása egy létező alhálózathoz

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Várt kimenet: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Egy alhálózaton konfigurált szolgáltatásvégpontok megjelenítése

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Kimenet:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Szolgáltatásvégpontok törlése egy alhálózaton
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

Beállítás előfeltételei:
- Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI 2.0-s verzió használatának első lépéseit](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) ismertető cikkben talál.
 - A fióknak rendelkeznie kell a megfelelő [engedélyekkel](#provisioning) egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

 A virtuális hálózatokra vonatkozó parancsok teljes listájáért lásd az [Azure CLI virtuális hálózati parancsait.](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>Az Azure-régióhoz elérhető szolgáltatásvégpontok beszerzése

Használja az alábbi parancsot a végpontokhoz elérhető, az adott Azure-régióban (például: „EastUS”) támogatott szolgáltatások listájának megtekintéséhez.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Kimenet:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Egy Azure Storage-szolgáltatásvégpont hozzáadása a *mySubnet* alhálózathoz a *myVNet* virtuális hálózat létrehozása közben

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Több végpont hozzáadása: --service-endpoints Microsoft.Storage Microsoft.Sql

Kimenet:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Az Azure-szolgáltatási erőforrások a virtuális hálózat számára történő biztosításának befejezéséhez kattintson a szolgáltatás dokumentációjára a [Következő lépések](#Next%20Steps) között.

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Több szolgáltatásvégpont hozzáadása egy létező alhálózathoz

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Várt kimenet:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Egy alhálózaton konfigurált szolgáltatásvégpontok megjelenítése

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Szolgáltatásvégpontok törlése egy alhálózaton
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Kimenet: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager-sablon

### <a name="securing-azure-service-resources-to-vnets"></a>Azure-szolgáltatási erőforrások biztosítása virtuális hálózatoknak

A szolgáltatásvégpontokkal biztosíthat bizonyos Azure-erőforrásokat a virtuális hálózata számára.

Töltse le a [Resource Manager-példasablont](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration), amellyel egy tárfiókot biztosíthat egy alhálózatnak egy virtuális hálózaton belül.

A sablon létrehoz egy virtuális hálózatot 2 alhálózattal, mindegyikben egy virtuális géppel, amelyek egy-egy hálózati adapterrel rendelkeznek. Engedélyezi a végpontot az egyik alhálózaton, amelynek biztosít egy tárfiókot is.

A sablonnak a letöltés után módosíthatja bizonyos részeit, a saját forgatókönyvéhez igazítva.

A sablonhoz utasítások is járnak, amelyek alapján üzembe helyezheti a sablont az Azure Portal, a PowerShell vagy az Azure CLI használatával. Győződjön meg arról, hogy rendelkezik a szükséges [engedélyekkel](#provisioning) a végpont beállításához és a fiók biztosításához.

Ha Azure-erőforrásokat szeretne biztosítani egy alhálózat számára:

- az alhálózaton konfigurálni kell egy szolgáltatásvégpontot;
- az erőforrást a virtuális hálózat számára úgy kell biztosítani, hogy az erőforráshoz hozzáad egy virtuális hálózati szabályt.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Az alhálózatnak biztosított erőforrásokhoz tartozó szolgáltatásvégpontok törlése
Ha az alhálózathoz Azure-szolgáltatási erőforrások vannak biztosítva, és a szolgáltatásvégpontot törlik, akkor az erőforrásokhoz többé nem lehet hozzáférni az alhálózatról.
A végpont újbóli engedélyezése nem állítja vissza az alhálózat hozzáférését a korábban biztosított erőforrásokhoz.

Ha újra biztosítani szeretné a szolgáltatási erőforrásokat ennek az alhálózatnak:

- engedélyezze újra a végpontot;
- távolítsa el az erőforrás régi virtuális hálózati szabályát;
- adjon meg egy új szabályt, amely biztosítja az erőforrást az alhálózatnak.

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpontok a virtuális hálózatokon külön-külön konfigurálhatók egy olyan felhasználó által, akiknek írási hozzáférése van a virtuális hálózathoz.

Ahhoz, hogy egy felhasználó Azure-szolgáltatási erőforrásokat rendelhessen egy virtuális hálózathoz, rendelkeznie kell a hozzáadott alhálózatokra vonatkozó „Microsoft.Network/JoinServicetoaSubnet” engedéllyel. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk az előzetes verzió használata során.

## <a name="next-steps"></a>Következő lépések

Ha további útmutatásra van szüksége a szolgáltatási erőforrások virtuális hálózatoknak való biztosításával kapcsolatban, olvassa el az alábbi cikkeket:

[Azure Storage-fiókok biztosítása virtuális hálózatokhoz](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Azure SQL biztosítása virtuális hálózatokhoz](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
