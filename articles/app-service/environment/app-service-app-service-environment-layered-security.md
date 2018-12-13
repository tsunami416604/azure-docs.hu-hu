---
title: Biztonsági architektúra App Service Environment-környezetekkel – Azure szintekre épülő
description: Az App Service-környezetek többrétegű biztonsági architektúra megvalósítása.
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
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274163"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Az App Service-környezetek többrétegű biztonsági architektúra megvalósítása
## <a name="overview"></a>Áttekintés
App Service Environment-környezetek adjon meg egy virtuális hálózaton üzembe helyezett egy izolált futtatókörnyezetben, mivel a fejlesztők hozhat létre egy többrétegű biztonsági architektúra biztosítása a hálózati hozzáférés eltérő szintű fizikai alkalmazásrétegek esetében.

Egy közös törekszik, hogy elrejtése API háttérrendszereket általános Internet-hozzáférés, és csak a felsőbb rétegbeli webes alkalmazások által meghívandó API-k engedélyezése.  [Hálózati biztonsági csoportok (NSG-k)] [ NetworkSecurityGroups] használható API-alkalmazásokból való nyilvános hozzáférés korlátozása alhálózatok, amely tartalmazza az App Service Environment-környezetek.

Az alábbi ábrán egy példa architektúra látható WebAPI alapú alkalmazás az App Service-környezet üzembe helyezve.  Három különálló webes alkalmazás példánya, három külön App Service-környezetek, a hívások háttér-WebAPI ugyanahhoz az alkalmazáshoz.

![Fogalmi architektúra][ConceptualArchitecture] 

A zöld plusz jelre azt jelzik, hogy engedélyezi-e a hálózati biztonsági csoportot az "apiase" tartalmazó alhálózat a felsőbb rétegbeli webes alkalmazások bejövő hívások jól hívásokként ze sebe sama.  Azonban a azonos hálózati biztonsági csoport kifejezetten megtagadja a hozzáférést általános bejövő forgalom az internetről. 

Ez a cikk további részében lépésről lépésre bemutatja a hálózati biztonsági csoport konfigurálása az alhálózaton, amely tartalmazza a "apiase."

## <a name="determining-the-network-behavior"></a>A hálózati viselkedés meghatározására szolgál
Annak érdekében, hogy tudja, milyen hálózati biztonsági szabályok szükségesek, meg kell határoznia, melyik hálózati ügyfelek részére engedélyezett lesz elérni az API-alkalmazást tartalmazó App Service-környezetben, és mely ügyfelek le lesz tiltva.

Mivel [hálózati biztonsági csoportok (NSG-k)] [ NetworkSecurityGroups] alhálózatok, érvénybe lépnek és App Service Environment-környezetek alhálózatra vannak telepítve, a alkalmazni a hálózati biztonsági csoportok szabályai **összes** alkalmazások App Service-környezet futtatását.  Ebben a cikkben a minta architektúra használatával, egy hálózati biztonsági csoportot az alhálózathoz, amely tartalmazza a "apiase" alkalmazása után, a "apiase" App Service-környezetben futó összes alkalmazás fogja ellátni védelemmel ugyanazokat a biztonsági szabályokat. 

* **Határozza meg a felsőbb rétegbeli hívóit kimenő IP-címe:**  Mi az az IP-címe vagy a fölérendelt hívók?  Ezeket a címeket kell explicit módon hozzáférhessen az NSG-ben.  App Service Environment-környezetek közötti hívások "Internet" hívások számít, mivel a kimenő IP-cím hozzárendelve az egyes a három felsőbb szintű App Service-környezetek kell az NSG az "apiase" alhálózat hozzáférése engedélyezett.   Az App Service Environment-környezetben futó alkalmazások a kimenő IP-cím meghatározásához további információkért lásd: a [hálózati architektúra] [ NetworkArchitecture] áttekintését tartalmazó cikket.
* **A háttérrendszeri API-alkalmazást kell hívnia magának?**  Egy néha tényezőkből és változás is, hogy a forgatókönyv, ahol a háttéralkalmazás meghívásához magát kell.  Ha egy háttér-API-alkalmazás az App Service-környezet kell magát hívja, a rendszer is kezelni, az "Internet" hívásakor.  A minta az architektúrában ehhez, hogy lehetővé teszi a kimenő IP-címét a "apiase" App Service Environment-környezet, valamint hozzáférést.

## <a name="setting-up-the-network-security-group"></a>A hálózati biztonsági csoport beállítása
Miután a kimenő IP-címek készletét ismertek, a következő lépés az hozhat létre egy hálózati biztonsági csoport.  Mindkét Resource Manager-alapú virtuális hálózatok, valamint a klasszikus virtuális hálózatok, hálózati biztonsági csoportok létrehozhatók.  Az alábbi példák bemutatják, létrehozását és konfigurálását egy NSG-t a Powershell-lel a klasszikus virtuális hálózat.

A környezetek a mintaarchitektúra a találhatók az USA déli középső Régiójában, így egy üres NSG-t az adott régióban jön létre:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Először egy explicit engedélyezése a szabály lesz hozzáadva az Azure felügyeleti infrastruktúra, a cikkben leírtaknak megfelelően [bejövő forgalom] [ InboundTraffic] App Service Environment-környezetek.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Ezután két szabályokat adja hozzá az első felsőbb szintű App Service Environment ("fe1ase") a HTTP és HTTPS hívásait engedélyezése.

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

Végül hozzáférést a háttérrendszeri API App Service Environment-környezet kimenő IP-címét, hogy önmagába vissza meghívhatja.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nincs más hálózati biztonsági szabályok szükségesek, mivel minden NSG tartalmaz egy alapértelmezett szabálykészletet, amely blokkolja az internetről bejövő hozzáférést alapértelmezés szerint.

Teljes listája megtalálható a hálózati biztonsági csoport szabályai alább láthatók.  Vegye figyelembe, hogy hogyan a legutóbbi szabályt, amely ki van jelölve, blokkok bejövő hozzáférést minden hívónál eltérő hívóit, amely explicit módon kapott hozzáférést.

![Az NSG konfigurációs][NSGConfiguration] 

Az utolsó lépés a alkalmazni az NSG-t az alhálózathoz, amely tartalmazza a "apiase" App Service Environment-környezet.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Az NSG az alhálózathoz a alkalmazni csak a három felsőbb szintű App Service Environment-környezetek és az App Service Environment, amely tartalmazza az API-háttérrel, a "apiase" környezetbe irányuló hívásokat használata engedélyezett.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
Információ a [hálózati biztonsági csoportok](../../virtual-network/security-overview.md).

Tudnivalók a [kimenő IP-címek] [ NetworkArchitecture] és az App Service Environment-környezetek.

[Hálózati portok] [ InboundTraffic] App Service Environment-környezetek használják.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
