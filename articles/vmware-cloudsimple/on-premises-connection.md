---
title: Azure VMware-megoldások (AVS) – helyszíni kapcsolatok ExpressRoute használatával
description: Ismerteti, hogyan lehet helyszíni internetkapcsolatot igényelni az ExpressRoute-ből az AVS region Network használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019621"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Kapcsolódás a helyszínről az AVS-hez a ExpressRoute használatával

Ha már rendelkezik Azure ExpressRoute-kapcsolattal egy külső helyről (például a helyszínen) az Azure-ba, csatlakoztathatja azt az AVS-környezethez. Ezt egy olyan Azure-szolgáltatáson keresztül teheti meg, amely lehetővé teszi, hogy két ExpressRoute áramkör kapcsolódjon egymáshoz. Ez a módszer biztonságos, privát, nagy sávszélességű, kis késleltetésű kapcsolatot létesít a két környezet között.

[![helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Előzetes teendők

A helyszíni környezetből Global Reach-kapcsolatok létrehozásához a **/29** hálózati címterület szükséges. A/29 címterület a ExpressRoute-áramkörök közötti átviteli hálózaton van használatban. Az átviteli hálózatnak nem szabad átfedésben lennie az Azure-beli virtuális hálózatokkal, a helyszíni hálózatokkal vagy az AVS Private Cloud Networks szolgáltatással.

## <a name="prerequisites"></a>Előfeltételek

* Az áramkör és az AVS Private Cloud Networks közötti kapcsolat létrehozása előtt egy Azure ExpressRoute-áramkörre van szükség.
* A ExpressRoute-áramkör engedélyezési kulcsainak létrehozásához jogosultsággal rendelkező felhasználónak kell lennie.

## <a name="scenarios"></a>Alkalmazási helyzetek

A helyszíni hálózat és az AVS Private Cloud Network összekapcsolása lehetővé teszi az AVS Private Cloud használatát különféle módokon, például a következő helyzetekben:

* Hozzáférés az AVS Private Cloud networkhez helyek közötti VPN-kapcsolat létrehozása nélkül.
* A helyszíni Active Directory identitás forrásaként használhatja az AVS Private-felhőben.
* Telepítse át a helyszínen futó virtuális gépeket az AVS Private-felhőbe.
* A vész-helyreállítási megoldás részeként használja az AVS saját Felhőjét.
* Használja fel a helyszíni erőforrásokat az AVS Private Cloud munkaterhelés virtuális gépeken.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-áramkörök csatlakoztatása

A ExpressRoute-kapcsolat létrehozásához létre kell hoznia egy engedélyt a ExpressRoute-áramkörön, és meg kell adnia az engedélyezési adatokat az AVS-nek.


### <a name="create-expressroute-authorization"></a>ExpressRoute-hitelesítés létrehozása

1. Jelentkezzen be az Azure portálra.

2. A felső keresési sávban keresse meg a **ExpressRoute áramkört** , és kattintson a **szolgáltatások**területen található **ExpressRoute-áramkörök** elemre.
    [![ExpressRoute áramkörök](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Válassza ki azt a ExpressRoute-áramkört, amelyhez csatlakozni kíván az AVS-hálózathoz.

4. A ExpressRoute lapon kattintson az **engedélyek**elemre, adja meg az engedély nevét, majd kattintson a **Mentés**gombra.
    [![ExpressRoute-áramkör engedélyezése](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Másolja az erőforrás-azonosítót és az engedélyezési kulcsot a másolás ikonra kattintva. Illessze be az azonosítót és a kulcsot egy szövegfájlba.
    [![ExpressRoute áramkör engedélyezési másolata](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > Az **erőforrás-azonosítót** át kell másolni a felhasználói felületről, és ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` formátumban kell lennie, amikor megadja azt a támogatáshoz.

6. Hozzon létre egy jegyet a létrehozandó kapcsolatok <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatásával</a> .
    * Probléma típusa: **technikai**
    * Előfizetés: **az AVS szolgáltatás üzembe helyezésének előfizetése**
    * Szolgáltatás: **VMware-megoldások (AVS)**
    * Probléma típusa: **szolgáltatás kérése**
    * Probléma altípusa: **ExpressRoute-kapcsolatok létrehozása a** helyszíni környezethez
    * Adja meg a részleteket tartalmazó ablaktáblán a vágólapra másolt és mentett erőforrás-azonosítót és engedélyezési kulcsot.
    * Adjon meg egy/29 hálózati címtartományt az átviteli hálózat számára.
    * Elküldi az alapértelmezett útvonalat a ExpressRoute-on keresztül?
    * Az AVS Private Cloud Traffic a ExpressRoute-on keresztül továbbított alapértelmezett útvonalat használja?

    > [!IMPORTANT]
    > Az alapértelmezett útvonal küldése lehetővé teszi az összes internetes forgalom küldését az AVS Private Cloud-ból a helyszíni internetkapcsolat használatával. Ha le szeretné tiltani az AVS Private-felhőben konfigurált alapértelmezett útvonalat, és a helyszíni kapcsolat alapértelmezett útvonalát szeretné használni, adja meg a támogatási jegy részleteit.

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)  
