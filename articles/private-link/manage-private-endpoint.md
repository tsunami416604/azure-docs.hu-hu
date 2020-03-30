---
title: Privát végpontkapcsolat kezelése az Azure-ban
description: A magánhálózati végpontkapcsolatok kezelése az Azure-ban
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452964"
---
# <a name="manage-a-private-endpoint-connection"></a>Privátvégpont-kapcsolat kezelése
Az Azure Private Link egy jóváhagyási hívásfolyamati modellen működik, amelyben a Private Link szolgáltatás fogyasztója kérheti a kapcsolatot a szolgáltatóval a szolgáltatás elhasználódása érdekében. A szolgáltató ezután eldöntheti, hogy engedélyezi-e a fogyasztónak a csatlakozást vagy sem. Az Azure Private Link lehetővé teszi a szolgáltatók számára, hogy kezeljék a privát végponti kapcsolatot az erőforrásaikon. Ez a cikk a privát végpontkapcsolatok kezelésével kapcsolatos utasításokat ismerteti.

![Privát végpontok kezelése](media/manage-private-endpoint/manage-private-endpoint.png)

A Private Link szolgáltatás fogyasztói két kapcsolat-jóváhagyási módszer közül választhatnak:
- **Automatikus**: Ha a szolgáltatás fogyasztója RBAC-engedélyekkel rendelkezik a szolgáltatóerőforráshoz, az automatikus jóváhagyási módszert választhatja. Ebben az esetben, amikor a kérelem eléri a szolgáltató erőforrását, nincs szükség műveletre a szolgáltatótól, és a kapcsolat automatikusan jóváhagyásra kerül. 
- **Kézikönyv:** Éppen ellenkezőleg, ha a szolgáltatás fogyasztója nem rendelkezik RBAC-engedélyekkel a szolgáltató erőforrásához, az fogyasztó kiválaszthatja a manuális jóváhagyási módszert. Ebben az esetben a kapcsolatkérés **függőben lévőként**jelenik meg a szolgáltatás erőforrásaiban. A szolgáltatónak manuálisan kell jóváhagynia a kérelmet, mielőtt kapcsolatokat létesíteni lehetne. Manuális esetekben a szolgáltatás fogyasztója is megadhat egy üzenetet a kéréssel, hogy több kontextust biztosítson a szolgáltatónak. A szolgáltató a következő lehetőségek közül választhat az összes magánvégfelhasználói kapcsolathoz: **Jóváhagyva**, **Elutasítva**, **Eltávolítás**.

Az alábbi táblázat a különböző szolgáltatói műveleteket és a privát végpontok eredő kapcsolati állapotait mutatja.  A szolgáltató a privát végpontkapcsolat állapotát is módosíthatja egy későbbi időpontban, fogyasztói beavatkozás nélkül. A művelet frissíti a végpont állapotát a fogyasztói oldalon. 


|Szolgáltatói művelet   |Szolgáltatás fogyasztói saját végpontállapota   |Leírás   |
|---------|---------|---------|
|None    |    Függőben     |    A kapcsolat manuálisan jön létre, és jóváhagyásra vár a Magánkapcsolat erőforrás tulajdonosa.       |
|Jóváhagyás    |  Approved       |  A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra.     |
|Elutasítás     | Elutasítva        | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa elutasította.        |
|Eltávolítás    |  Elválasztott       | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívsá válik, és törölni kell a karbantartáshoz.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Privátvégpont-kapcsolatok kezelése Azure PaaS-erőforrásokon
A portál az előnyben részesített módszer az Azure PaaS-erőforrások privát végponti kapcsolatainak kezelésére. Jelenleg nem rendelkezünk PowerShell/CLI-támogatással az Azure PaaS-erőforrások on-val kapcsolatos kapcsolatok kezeléséhez.
1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Keresse meg a Privát hivatkozási központot.
3. **Az Erőforrások csoportban**válassza ki a magánvégpont-kapcsolatokat kezelni kívánt erőforrástípust.
4. Az egyes erőforrástípusokesetében megtekintheti a hozzá társított privát végpontkapcsolatok számát. Szükség szerint szűrheti az erőforrásokat.
5. Válassza ki a privát végpontkapcsolatokat.  A felsorolt kapcsolatok csoportban jelölje ki a kezelni kívánt kapcsolatot. 
6. A kapcsolat állapotát a felső beállítások közül választva módosíthatja.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Privát végpontkapcsolatok kezelése ügyfél/partner tulajdonában lévő Private Link szolgáltatáson

Az Azure PowerShell és az Azure CLI a Microsoft Partner Services vagy az ügyfél tulajdonában lévő szolgáltatások privát végponti kapcsolatainak kezelésére szolgáló előnyben részesített módszerek. Jelenleg nincs portáltámogatás a privát kapcsolatszolgáltatáson lévő kapcsolatok kezeléséhez.  
 
### <a name="powershell"></a>PowerShell 
  
A magánhálózati végpontkapcsolatok kezeléséhez használja az alábbi PowerShell-parancsokat.  
#### <a name="get-private-link-connection-states"></a>Privát kapcsolat állapotának beszereznie 
A `Get-AzPrivateLinkService` parancsmag segítségével leszeretné kelni a privát végpontkapcsolatokat és azok állapotait.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása 
 
A `Approve-AzPrivateEndpointConnection` parancsmag használatával jóváhagyhatja a privát végpontkapcsolatot. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Privát végpontkapcsolat megtagadása 
 
A `Deny-AzPrivateEndpointConnection` parancsmag segítségével elutasíthatja a privát végpontkapcsolatot. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Privát végpontkapcsolat eltávolítása 
 
A `Remove-AzPrivateEndpointConnection` parancsmag segítségével távolítsa el a privát végpont kapcsolatot. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
A `az network private-link-service update` privát végpontkapcsolatok kezelésére használható. A kapcsolat állapota meg ```azurecli connection-status``` van adva a paraméterben. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>További lépések
- [További információ a privát végpontokról](private-endpoint-overview.md)
 
