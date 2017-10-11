---
title: "Virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot: PowerShell: klasszikus: Azure |} Microsoft Docs"
description: "Ez a dokumentum áttekintést csatolása a virtuális hálózatokon (Vnetek) az ExpressRoute-Kapcsolatcsoportok a klasszikus telepítési modell és a PowerShell használatával."
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: ganesr
ms.openlocfilehash: 8df8a4c6ff0897c821e13248e0494b17e1a4992d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Egy virtuális hálózathoz csatlakozni powershellel (klasszikus) ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a virtuális hálózatokon (Vnetek) csatolása az Azure ExpressRoute-Kapcsolatcsoportok a klasszikus telepítési modell és a PowerShell használatával. Virtuális hálózatok ugyanahhoz az előfizetéshez lehet, vagy egy másik előfizetés része lehet.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek
1. Az Azure PowerShell-modulok legújabb verziójára van szüksége. Letöltheti a legújabb PowerShell-modulok PowerShell szakaszában a [Azure letöltőoldala](https://azure.microsoft.com/downloads/). Kövesse az utasításokat a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) kapcsolatos részletes útmutatás az Azure PowerShell-modulok használata a számítógép konfigurálása.
2. Át kell tekintenie a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
3. Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
   * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-classic.md) és a kapcsolat szolgáltatójánál engedélyezése a kapcsolatcsoport rendelkezik.
   * Győződjön meg arról, hogy rendelkezik az Azure magánhálózati társviszony-létesítés a kapcsolatcsoport konfigurálva. Tekintse meg a [útválasztás konfigurálása](expressroute-howto-routing-classic.md) útválasztási miként.
   * Győződjön meg arról, hogy az Azure magánhálózati társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik-e, hogy engedélyezheti a végpontok közötti kapcsolat.
   * Rendelkeznie kell egy virtuális hálózat és a virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [virtuális hálózat konfigurálása ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Legfeljebb 10 virtuális hálózatok ExpressRoute-kapcsolatcsoportot társíthatja. Az összes virtuális hálózatot geopolitikai ugyanabban a régióban kell lennie. Az ExpressRoute-kapcsolatcsoportot virtuális hálózatok, vagy hivatkozás virtuális hálózatok, amelyek más geopolitikai régiókban, ha engedélyezte a prémium szintű ExpressRoute-bővítmény nagyobb számú társíthatja. Ellenőrizze a [gyakran ismételt kérdések](expressroute-faqs.md) a prémium szintű bővítmény olvashat.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>A virtuális hálózati ugyanahhoz az előfizetéshez csatlakozni expressroute-kapcsolatcsoporthoz
A következő parancsmag használatával egy virtuális hálózatot is kapcsolhat ExpressRoute-kapcsolatcsoportot. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a csatolás a parancsmag futtatása előtt.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Több előfizetés ExpressRoute-kapcsolatcsoportot lehet megosztani. Az alábbi ábrán egy egyszerű sematikus ExpressRoute-Kapcsolatcsoportok megosztási hogyan működik a több előfizetésekhez.

A nagy felhőben kisebb felhők egyes szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. Egyetlen ExpressRoute-kapcsolatcsoportot való kapcsolódáshoz a helyszíni hálózat üzembe helyezése a szolgáltatásból, de a részlegek megoszthatja a szervezeten belüli osztályok mindegyikének alkalmazhatja a saját előfizetés. Egy részleghez (ebben a példában: informatikai) az ExpressRoute-kapcsolatcsoport rendelkezhet. A szervezeten belüli más előfizetések használhatja az ExpressRoute-kapcsolatcsoportot.

> [!NOTE]
> A dedikált kör kapcsolat és a sávszélesség költségek az ExpressRoute-kapcsolatcsoport tulajdonosát alkalmazandó. Az összes virtuális hálózatot megosztani a azonos sávszélességet.
> 
> 

![Az előfizetések közötti kapcsolathoz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Adminisztráció
A *kapcsolatcsoport tulajdonosát* az előfizetés, amelyben az ExpressRoute-kapcsolatcsoport jön létre, rendszergazdai/coadministrator van. A kapcsolatcsoport tulajdonosát adhatják meg a rendszergazdák/coadministrators más előfizetések néven *felhasználók áramkör*, akkor használja a dedikált áramkör saját. Kör felhasználók, akik jogosultak a szervezet ExpressRoute-kapcsolatcsoportot használatára után jogosultak a virtuális hálózatot az előfizetését a társíthatja az ExpressRoute-kapcsolatcsoportot.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Az engedélyt visszavonni azt eredményezi, hogy törli az előfizetést, amelyek hozzáférését visszavonták az összes hivatkozás.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát a megadott expressroute-kapcsolatcsoport használandó másik előfizetés rendszergazdája engedélyezi. A következő példában a rendszergazda a kapcsolat (a Contoso informatikai) lehetővé teszi, hogy a rendszergazda egy másik előfizetés (fejlesztői-teszt) legfeljebb két virtuális hálózatok összekapcsolása a kapcsolatcsoport. A Contoso informatikai rendszergazda lehetővé teszi, hogy ez a fejlesztés-tesztelés Microsoft azonosító megadásával A parancsmag nem e-mailt küld a megadott Microsoft. A kapcsolatcsoport tulajdonosát kell, hogy helyesek-e az engedélyezési explicit módon értesíteni az előfizetés tulajdonosa.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Engedélyek ellenőrzése**

A kapcsolatcsoport tulajdonosát tekintse át a következő parancsmag futtatásával egy adott körön kiállított összes engedélyek:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Engedélyek frissítése**

A kapcsolatcsoport tulajdonosát a következő parancsmag használatával módosíthatja az engedélyeket:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is revoke vagy törlése a felhasználó engedélyek a következő parancsmagot:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Kör felhasználói műveletek

**Engedélyek ellenőrzése**

A kapcsolatcsoport felhasználói engedélyek tekintse át a következő parancsmag használatával:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Hivatkozás engedélyek váltja be**

A kapcsolatcsoport felhasználó futtathatja egy hivatkozás hitelesítési beváltani a következő parancsmagot:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Futtassa ezt a parancsot az újonnan csatolt előfizetésben található virtuális hálózat:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Következő lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).

