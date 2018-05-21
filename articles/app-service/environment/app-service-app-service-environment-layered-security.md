---
title: Az App Service-környezetek többrétegű biztonsági architektúrája
description: Az App Service Environment-környezetek egy többrétegű biztonsági architektúra megvalósítása.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 29c928c7d81eb3a2532f735be9132b49db5da373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Az App Service-környezetek egy többrétegű biztonsági architektúra megvalósítása
## <a name="overview"></a>Áttekintés
App Service Environment-környezetek adjon meg egy virtuális hálózatot helyezett izolált futtatókörnyezetben, mivel a fejlesztők egy többrétegű biztonsági architektúra eltérő szintű hálózati hozzáférést biztosít az egyes fizikai alkalmazás rétegek hozhat létre.

A közös desire API vissza-végpontok általános Internet-hozzáférés elrejtéséhez, és csak a felsőbb rétegbeli webes alkalmazások által használt hívása API-k engedélyezése.  [Hálózati biztonsági csoportokkal (NSG-k)] [ NetworkSecurityGroups] nyilvános hozzáférés korlátozása API-alkalmazások App Service Environment-környezetek tartalmazó alhálózatok használható.

Az alábbi ábrán egy példa architektúra látható, az App Service-környezetek telepített WebAPI-alapú alkalmazáshoz.  Három különálló webes alkalmazás-példányt, három különálló App Service Environment-környezetek, a háttér-hívások indítása WebAPI ugyanahhoz az alkalmazáshoz.

![Fogalmi architektúra][ConceptualArchitecture] 

A zöld plusz jel jelzi, hogy engedélyezi-e a hálózati biztonsági csoportot az "apiase" tartalmazó alhálózat bejövő hívások a felsőbb rétegbeli webes alkalmazásokból, jól hívások magából.  Azonban a hálózati biztonsági csoporton kifejezetten megtagadja általános bejövő forgalom az internethez való hozzáférést. 

Ez a cikk fennmaradó végigvezeti a lépéseken, amelyekkel a hálózati biztonsági csoport konfigurálása az alhálózat tartalmazó "apiase."

## <a name="determining-the-network-behavior"></a>A hálózati probléma meghatározása
Ahhoz, hogy tudja, milyen hálózati biztonsági szabályok szükségesek, meg kell határoznia, melyik hálózati ügyfelek részére engedélyezve lesz az App Service-környezet, az API-alkalmazást tartalmazó elérni, és mely ügyfelek le lesz tiltva.

Mivel [hálózati biztonsági csoportokkal (NSG-k)] [ NetworkSecurityGroups] alkalmazott alhálózatok, és az App Service Environment-környezetek alhálózatokra vannak telepítve, egy NSG foglalt szabályok vonatkoznak **összes** alkalmazások az App Service-környezetek futnak.  Használja a mintaarchitektúrája ebben a cikkben után a hálózati biztonsági csoport van alkalmazva tartalmazó "apiase", "apiase" App Service Environment-környezet futó minden alkalmazás fogja védeni az ugyanazokat a biztonsági szabályok. 

* **Határozza meg a felsőbb rétegbeli hívók kimenő IP-cím:** Mi az az IP-címeket a felsőbb rétegbeli hívók?  Ezeket a címeket kell explicit módon hozzáférhessen az NSG.  App Service Environment-környezetek közötti hívások "Internet" hívások minősülnek, mivel a kimenő IP-címet hozzárendelni a három felsőbb szintű App Service-környezetek mindegyikének kell az NSG az "apiase" alhálózat hozzáférése engedélyezett.   További információ a kimenő IP-cím egy App Service Environment-környezetben futó alkalmazások, lásd: a [hálózati architektúra] [ NetworkArchitecture] a cikk áttekintése.
* **A háttér-API-alkalmazásba kell hívnia magának?**  Néha kihagyott és finom pont a forgatókönyvet, ahol a háttér-alkalmazást kell hívhatja saját magát.  Ha egy háttér-API-alkalmazás az App Service-környezetek hívhatja saját magát, akkor a is rendszer egy "Internet" hívásként.  A minta-architektúrában ehhez a kimenő IP-címét a "apiase" App Service Environment-környezet, valamint a hozzáférés.

## <a name="setting-up-the-network-security-group"></a>A hálózati biztonsági csoport
Amennyiben az ismert kimenő IP-címek készlete, a következő lépésre hálózati biztonsági csoport létrehozásához.  Hálózati biztonsági csoportok mindkét Resource Manager-alapú virtuális hálózatokon, valamint a klasszikus virtuális hálózatok hozhatók létre.  Az alábbi példák bemutatják, létrehozása és konfigurálása egy NSG-t egy Powershell-lel klasszikus virtuális hálózaton.

Minta architektúrájának a környezetben található déli középső Régiójában, egy üres NSG az adott régióban jön létre:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Először egy explicit engedélyezése a szabály szerepel-e az Azure felügyeleti infrastruktúrát a a cikkben leírtaknak megfelelően [bejövő forgalom] [ InboundTraffic] App Service Environment-környezetek számára.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

A következő két szabály hozzáadódnak engedélyezze a HTTP és HTTPS hívásokat a az első felsőbb szintű App Service Environment-környezet ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Le, és ismételje meg a második és harmadik felsőbb szintű App Service Environment-környezetek ("fe2ase" és "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Végül hozzáférést a háttér-API App Service Environment-környezet kimenő IP-címét, hogy vissza tudja hívja az magát.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nincs más hálózati biztonsági szabály szükség, mert minden NSG tartozik egy alapértelmezett szabályokkal, amelyeket az internetről bejövő hozzáférésének letiltására alapértelmezés szerint.

A hálózati biztonsági csoport szabályainak teljes listája az alábbiakban tekintheti meg.  Vegye figyelembe, hogyan az utolsó szabályt, amely ki van jelölve, blokkolja a bejövő összes hívó eltérő hívóknak, amely kifejezetten hozzáférést kapott hozzáférést.

![NSG-konfiguráció][NSGConfiguration] 

Az utolsó lépés, hogy az NSG alkalmazása az alhálózatot, amely tartalmazza a "apiase" App Service Environment-környezet.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Az NSG alkalmazva, az csak a három felsőbb szintű App Service Environment-környezetek, és az App Service-környezet, az API-háttér tartalmazó engedélyezettek hívni a "apiase" környezetbe.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
Információ a [hálózati biztonsági csoportok](../../virtual-network/security-overview.md).

Understanding [kimenő IP-címek] [ NetworkArchitecture] és az App Service Environment-környezetek.

[Hálózati portok] [ InboundTraffic] App Service Environment-környezetek használják.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
