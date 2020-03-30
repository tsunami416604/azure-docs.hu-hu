---
title: Réteges biztonsági v1
description: Ismerje meg, hogyan valósíthat meg egy réteges biztonsági architektúrát az App Service-környezetben. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688800"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Réteges biztonsági architektúra megvalósítása App Service-környezetekkel
Mivel az App Service-környezetek egy virtuális hálózatba telepített elkülönített futásidejű környezetet biztosítanak, a fejlesztők létrehozhatnak egy réteges biztonsági architektúrát, amely minden fizikai alkalmazásréteghez különböző szintű hálózati hozzáférést biztosít.

Gyakori vágy, hogy elrejtse az API-háttérrendszereket az általános internet-hozzáféréselől, és csak az UPSTREAM webalkalmazások számára engedélyezze az API-k hívását.  [A hálózati biztonsági csoportok (NSG-k)][NetworkSecurityGroups] az App Service-környezeteket tartalmazó alhálózatokon használhatók az API-alkalmazásokhoz való nyilvános hozzáférés korlátozására.

Az alábbi ábrán egy példaarchitektúra egy WebAPI-alapú alkalmazás telepített egy App Service-környezetben.  Három különálló webalkalmazás-példány, amelyek három különálló App Service-környezetben vannak telepítve, háttérhívásokat kezdeményeznek ugyanahhoz a WebAPI-alkalmazáshoz.

![Koncepcionális architektúra][ConceptualArchitecture] 

A zöld plusz jelek azt jelzik, hogy az "apiase" tartalmazó alhálózaton lévő hálózati biztonsági csoport lehetővé teszi a bejövő hívásokat a felső streamelési webalkalmazásokból, valamint magától a hívásokat.  Ugyanaz a hálózati biztonsági csoport azonban kifejezetten megtagadja a hozzáférést az általános bejövő forgalomhoz az internetről. 

A cikk további része végigvezeti az "apiase" nevű alhálózaton lévő hálózati biztonsági csoport konfigurálásához szükséges lépéseken.

## <a name="determining-the-network-behavior"></a>A hálózati viselkedés meghatározása
Annak megállapításához, hogy milyen hálózati biztonsági szabályokra van szükség, meg kell határoznia, hogy mely hálózati ügyfelek érhetik el az API-alkalmazást tartalmazó App Service-környezetet, és mely ügyfelek lesznek letiltva.

Mivel [a hálózati biztonsági csoportok (NSG-k)][NetworkSecurityGroups] alhálózatokra vannak alkalmazva, és az App Service-környezetek alhálózatokba vannak telepítve, az NSG-ben szereplő szabályok az App Service-környezetben futó **összes** alkalmazásra vonatkoznak.  A mintaarchitektúra ebben a cikkben, ha egy hálózati biztonsági csoport alkalmazva az alhálózat" tartalmazó "apiase", az "apiase" App Service Environment futó összes alkalmazás védi az azonos biztonsági szabályok. 

* **Határozza meg a felső csoportba tartozó hívók kimenő IP-címét:**  Mi a felső csoporthívók IP-címe vagy címe?  Ezeket a címeket kifejezetten engedélyezni kell az NSG-ben.  Mivel az App Service-környezetek közötti hívások "internetes" hívásoknak minősülnek, a három upstream App Service-környezethez rendelt kimenő IP-címet az "apiase" alhálózat NSG-ben kell biztosítani.   Az App Service-környezetben futó alkalmazások kimenő IP-címének meghatározásáról a [Hálózati architektúra][NetworkArchitecture] áttekintése című cikkben olvashat bővebben.
* **A háttér-API-alkalmazásnak meg kell hívnia magát?**  A néha figyelmen kívül hagyott és finom pont az a forgatókönyv, ahol a háttéralkalmazás kell hívni magát.  Ha egy back-end API-alkalmazás egy App Service-környezetben kell hívni magát, azt is kezeli, mint egy "internet" hívás.  A mintaarchitektúrában ez lehetővé teszi a hozzáférést a kimenő IP-cím a "apiase" App Service Environment is.

## <a name="setting-up-the-network-security-group"></a>A hálózati biztonsági csoport beállítása
Miután a kimenő IP-címek készlete ismertté válik, a következő lépés egy hálózati biztonsági csoport összeállítása.  Hálózati biztonsági csoportok létrehozhatók mind az Erőforrás-kezelő n alapuló virtuális hálózatokhoz, mind a klasszikus virtuális hálózatokhoz.  Az alábbi példák egy NSG létrehozása és konfigurálása egy klasszikus virtuális hálózaton a Powershell használatával.

A mintaarchitektúra esetében a környezetek az USA déli középső részén találhatók, így egy üres NSG jön létre az adott régióban:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Először egy explicit engedélyezési szabály kerül hozzáadásra az Azure felügyeleti infrastruktúrához, amint azt az App Service-környezetek [bejövő forgalomról][InboundTraffic] szóló cikk ismerteti.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Ezután két szabály kerül hozzáadásra, hogy http- és HTTPS-hívásokat engedélyezzen az első upstream App Service Environment ("fe1áz") rendszerből.

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Öblítse le és ismételje meg a második és harmadik upstream App Service környezetben ("fe2ase" és "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Végül adjon hozzáférést a háttér-API App Service-környezet kimenő IP-címéhez, hogy visszahívhassa önmagát.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nincs szükség más hálózati biztonsági szabályokra, mert minden NSG rendelkezik egy alapértelmezett szabálykészlettel, amely alapértelmezés szerint blokkolja az internetről érkező hozzáférést.

A hálózati biztonsági csoport szabályainak teljes listája az alábbiakban látható.  Vegye figyelembe, hogy az utolsó szabály, amely ki van emelve, blokkolja a bejövő hozzáférést az összes hívótól, kivéve a kifejezetten hozzáférési engedével rendelkező hívókat.

![NSG-konfiguráció][NSGConfiguration] 

Az utolsó lépés az NSG alkalmazása az "apiase" App Service Environment-t tartalmazó alhálózatra.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Az NSG-t az alhálózatra alkalmazva csak a három upstream App Service-környezetés az API-háttér-rendszer tartalmazó App Service-környezet ben engedélyezett az "apiase" környezetbe való hívása.

## <a name="additional-links-and-information"></a>További linkek és információk
Információ a [hálózati biztonsági csoportokról](../../virtual-network/security-overview.md).

A [kimenő IP-címek][NetworkArchitecture] és az App Service-környezetek ismertetése.

[Az][InboundTraffic] App Service-környezetek által használt hálózati portok.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
