---
title: Azure VMware-megoldás a ExpressRoute-t használó helyszíni CloudSimple
description: Ismerteti, hogyan lehet helyszíni internetkapcsolatot igényelni a ExpressRoute használatával a CloudSimple region Networkből
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c06cd53e408ebcae24de487fe1d4d25e14aae11b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240718"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával

Ha már rendelkezik Azure ExpressRoute-kapcsolattal egy külső helyről (például a helyszínen) az Azure-ba, akkor csatlakoztathatja azt a CloudSimple-környezethez. Ezt egy olyan Azure-szolgáltatáson keresztül teheti meg, amely lehetővé teszi, hogy két ExpressRoute áramkör kapcsolódjon egymáshoz. Ez a módszer biztonságos, privát, nagy sávszélességű, kis késleltetésű kapcsolatot létesít a két környezet között.

[![Helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Előkészületek

A helyszíni környezetből Global Reach-kapcsolatok létrehozásához a **/29** hálózati címterület szükséges.  A/29 címterület a ExpressRoute-áramkörök közötti átviteli hálózaton van használatban.  Az átviteli hálózat nem fedi átfedésben az Ön Azure-beli virtuális hálózatait, a helyszíni hálózatokat vagy a CloudSimple.

## <a name="prerequisites"></a>Előfeltételek

* Az áramkör és a CloudSimple privát felhőalapú hálózata közötti kapcsolat létrehozása előtt egy Azure ExpressRoute-áramkörre van szükség.
* A ExpressRoute-áramkör engedélyezési kulcsainak létrehozásához jogosultsággal rendelkező felhasználónak kell lennie.

## <a name="scenarios"></a>Forgatókönyvek

A helyszíni hálózat és a saját felhőalapú hálózat összekapcsolása lehetővé teszi a privát felhő különböző módokon történő használatát, többek között az alábbiakat:

* A saját felhőalapú hálózatát helyek közötti VPN-kapcsolat létrehozása nélkül érheti el.
* A helyszíni Active Directory identitás forrásaként használhatja a saját felhőben.
* Telepítse át a helyszínen futó virtuális gépeket a saját felhőbe.
* A saját Felhőjét a vész-helyreállítási megoldás részeként használhatja.
* Helyszíni erőforrásokat használhat a saját Felhőbeli számítási feladatokhoz tartozó virtuális gépeken.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-áramkörök csatlakoztatása

A ExpressRoute-kapcsolat létrehozásához létre kell hoznia egy engedélyt a ExpressRoute-áramkörön, és meg kell adnia az engedélyezési adatokat a CloudSimple.

### <a name="create-expressroute-authorization"></a>ExpressRoute-hitelesítés létrehozása

1. Jelentkezzen be az Azure portálra.

2. A felső keresési sávban keresse meg a **ExpressRoute áramkört** , és kattintson a **szolgáltatások**területen található **ExpressRoute-áramkörök** elemre.
    [![ExpressRoute-áramkörök](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Válassza ki azt a ExpressRoute-áramkört, amelyhez csatlakozni kíván a CloudSimple-hálózathoz.

4. A ExpressRoute lapon kattintson az **engedélyek**elemre, adja meg az engedély nevét, majd kattintson a **Mentés**gombra.
    [![ExpressRoute-áramkör engedélyezése](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Másolja az erőforrás-azonosítót és az engedélyezési kulcsot a másolás ikonra kattintva. Illessze be az azonosítót és a kulcsot egy szövegfájlba.
    [![ExpressRoute áramkör engedélyezési másolata](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > Az **erőforrás-azonosítót** át kell másolni a felhasználói felületről, és a ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` formátumot kell megadni a támogatáshoz.

6. Hozzon létre egy jegyet a létrehozandó kapcsolatok <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatásával</a> .
    * Probléma típusa: **Technikai**
    * Előfizetés: **Előfizetés, ahol a CloudSimple szolgáltatás telepítve van**
    * Szolgáltatás: **VMware-megoldás CloudSimple szerint**
    * Probléma típusa: **Szolgáltatási kérelem**
    * Probléma altípusa: **Helyszíni ExpressRoute-kapcsolatok létrehozása**
    * Adja meg a részleteket tartalmazó ablaktáblán a vágólapra másolt és mentett erőforrás-azonosítót és engedélyezési kulcsot.
    * Adjon meg egy/29 hálózati címtartományt az átviteli hálózat számára.

## <a name="next-steps"></a>További lépések

* [További információ az Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)  
