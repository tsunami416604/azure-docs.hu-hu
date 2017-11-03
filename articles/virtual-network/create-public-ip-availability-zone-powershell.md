---
title: "Egy zoned nyilvános IP-cím létrehozása a PowerShell használatával |} Microsoft Docs"
description: "Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában, a PowerShell használatával."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában, a PowerShell használatával

A nyilvános IP-cím, egy Azure rendelkezésre állási zónában (előzetes verzió) telepítése. Egy rendelkezésre állási zóna a fizikailag különálló zónát Azure-régióban. Az alábbiak végrehajtásának módját ismerheti meg:

> * Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában
> * A rendelkezésre állási zónában létre kapcsolódó erőforrások azonosítása
  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ez a cikk, hogy 4.4.0 verzióval rendelkezik, vagy magasabb AzureRM modul telepítve van szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni kell, telepítse a legújabb verziót a AzureRM modul a [PowerShell-galériában](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ebben a példában az erőforráscsoport neve *myResourceGroup* jön létre a *westeurope* régióban. *westeurope* Azure-régiókban, amely támogatja a rendelkezésre állási zónák Preview egyike.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Egy zonal nyilvános IP-cím létrehozása

Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában, a következő parancsot:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="get-zone-information-about-a-public-ip-address"></a>A nyilvános IP-cím zóna adatainak beolvasása

A zóna információ a nyilvános IP-cím a következő parancsot:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Következő lépések

- További információ [rendelkezésre állási zónák](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- További információ [nyilvános IP-címek](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 