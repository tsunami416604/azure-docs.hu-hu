---
title: Azure VMware-megoldás a CloudSimple által – Helyszíni kapcsolat az ExpressRoute használatával
description: Ez a témakör azt ismerteti, hogy miként lehet helyszíni kapcsolatot kérni az ExpressRoute használatával a CloudSimple régióhálózatból
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019621"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Csatlakozás a helyszíni és a CloudSimple szolgáltatáshoz az ExpressRoute használatával

Ha már rendelkezik egy Azure ExpressRoute-kapcsolategy külső helyről (például a helyszíni) az Azure-ba, csatlakoztathatja azt a CloudSimple környezethez. Ezt egy Azure-szolgáltatáson keresztül teheti meg, amely lehetővé teszi, hogy két ExpressRoute-kapcsolatcsatlakozzon egymáshoz. Ez a módszer biztonságos, privát, nagy sávszélességű, alacsony késleltetésű kapcsolatot hoz létre a két környezet között.

[![Helyszíni ExpressRoute-kapcsolat – Globális elérés](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Előkészületek

A helyszíni globális elérési kapcsolat létrehozásához **/29** hálózati címblokk szükséges.  A /29 címterületet az ExpressRoute-áramkörök közötti tranzithálózat használja.  Az átviteli hálózat nem lehet átfedésben az Azure virtuális hálózataival, a helyszíni hálózatokkal vagy a CloudSimple magánfelhő-hálózatokkal.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure ExpressRoute-kapcsolat szükséges, mielőtt létrehozna a kapcsolatot a kapcsolat a kapcsolat a kapcsolat a circuit és a CloudSimple private cloud hálózatok között.
* A felhasználónak jogosultságokkal kell létrehoznia az engedélyezési kulcsokat egy ExpressRoute-kapcsolaton.

## <a name="scenarios"></a>Forgatókönyvek

A helyszíni hálózat nak a magánfelhő-hálózathoz való csatlakoztatása lehetővé teszi a magánfelhő különböző módokon történő használatát, beleértve a következő eseteket is:

* A helyek közötti VPN-kapcsolat létrehozása nélkül érheti el a magánfelhő-hálózatot.
* Használja a helyszíni Active Directoryt identitásforrásként a magánfelhőben.
* A helyszíni futó virtuális gépek áttelepítése a magánfelhőbe.
* Használja a privát felhő részeként egy vész-helyreállítási megoldás.
* A helyszíni erőforrásokat a privát felhőbeli számítási feladatok virtuális gépein használja fel.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-áramkörök csatlakoztatása

Az ExpressRoute-kapcsolat létrehozásához létre kell hoznia egy engedélyt az ExpressRoute-kapcsolaton, és meg kell adnia az engedélyezési adatokat a CloudSimple számára.


### <a name="create-expressroute-authorization"></a>ExpressRoute-engedélyezés létrehozása

1. Jelentkezzen be az Azure portálra.

2. A felső keresősávon keresse meg az **ExpressRoute-áramkört,** és kattintson az **ExpressRoute-áramkörök** elemre a **Szolgáltatások csoportban.**
    [![ExpressRoute-áramkörök](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Válassza ki azt az ExpressRoute-áramkört, amelyhez csatlakozni kíván a CloudSimple-hálózathoz.

4. Az ExpressRoute lapon kattintson az **Engedélyezés gombra,** adja meg az engedélyezés nevét, majd kattintson a **Mentés gombra.**
    [![ExpressRoute-kapcsolat engedélyezése](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Másolja az erőforrás-azonosítót és az engedélyezési kulcsot a másolásikonra kattintva. Illessze be az azonosítót és a kulcsot egy szövegfájlba.
    [![ExpressRoute-kapcsolat engedélyezési másolása](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Az erőforrás-azonosítót** a felhasználói felületről kell másolni, és a támogatás megadásához a formátumnak ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` kell lennie.

6. A létrehozandó <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">kapcsolathoz</a> nyújtson be egy jegyet a Támogatás szolgáltatással.
    * Probléma típusa: **Műszaki**
    * Előfizetés: **Előfizetés, ahol a CloudSimple szolgáltatás telepítve van**
    * Szolgáltatás: **VMware megoldás a CloudSimple-től**
    * Probléma típusa: **Szolgáltatáskérés**
    * Probléma altípusa: **ExpressRoute-kapcsolat létrehozása a helyszíni**
    * Adja meg a részleteket tartalmazó ablaktáblán másolt és mentett erőforrás-azonosítót és engedélyezési kulcsot.
    * /29 hálózati címterület biztosítása a tranzithálózat számára.
    * Alapértelmezett útvonalat küld az ExpressRoute-on keresztül?
    * A magánfelhő-forgalom nak az ExpressRoute-on keresztül küldött alapértelmezett útvonalat kell használnia?

    > [!IMPORTANT]
    > Az alapértelmezett útvonal küldése lehetővé teszi, hogy az összes internetes forgalmat a magánfelhőből a helyszíni internetkapcsolaton keresztül küldje el.  A magánfelhőben konfigurált alapértelmezett útvonal letiltásához és a helyszíni kapcsolat alapértelmezett útvonalának használatához adja meg a támogatási jegy ben található részleteket.

## <a name="next-steps"></a>További lépések

* [További információ az Azure hálózati kapcsolatairól](cloudsimple-azure-network-connection.md)  
