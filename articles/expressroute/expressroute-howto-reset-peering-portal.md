---
title: 'Azure ExpressRoute: áramkör-társak visszaállítása a Azure Portal használatával'
description: Megtudhatja, hogyan tilthatja le és engedélyezheti egy Azure ExpressRoute-áramkör összevonását a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582301"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>ExpressRoute-áramköri társítások alaphelyzetbe állítása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan lehet letiltani és engedélyezni egy ExpressRoute-áramkör társításait a Azure Portal használatával. Ha letilt egy társítást, akkor a ExpressRoute-áramkör elsődleges és másodlagos kapcsolatának BGP-munkamenete le lesz állítva. Ezzel a kapcsolattal elveszítheti a kapcsolatot a Microsofttal. Ha engedélyezi a társítást, a rendszer a BGP-munkamenetet a ExpressRoute-áramkör elsődleges és másodlagos kapcsolatán is felveszi. Ezzel a kapcsolattal visszanyerheti a kapcsolatot a Microsofttal. A Microsoft-és az Azure-beli privát ExpressRoute egymástól függetlenül engedélyezheti és tilthatja le. Amikor először konfigurálja a ExpressRoute áramkörön lévő társításokat, a rendszer alapértelmezés szerint engedélyezi a társításokat.

Van néhány olyan forgatókönyv, ahol hasznos lehet a ExpressRoute-társítások alaphelyzetbe állítása.
* Tesztelje a vész-helyreállítási tervét és megvalósítását. Tegyük fel, hogy két ExpressRoute-áramkörrel rendelkezik. Letilthatja egy áramkör társításait, és kényszerítheti a hálózati forgalmat, hogy átadja a feladatátvételt a másik áramkörnek.
* Engedélyezze a kétirányú továbbítási észlelést (BFD) a ExpressRoute-áramkör Azure-beli privát vagy Microsoft-hálózatán. A BFD alapértelmezés szerint engedélyezve van az Azure-beli privát kiszolgálókon, ha a ExpressRoute-áramkör a 1 2018-es és a Microsoft-partneri kapcsolatok után jön létre. Ha a ExpressRoute-áramkör a január 10 2020 után jön létre. Ha az áramkört korábban már létrehozták, a BFD nem volt engedélyezve. A BFD engedélyezéséhez tiltsa le a társítást, és engedélyezze újra. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="reset-a-peering"></a>Társítás alaphelyzetbe állítása

1. Válassza ki azt az áramkört, amelynek a konfigurációját módosítani szeretné.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute áramköri lista":::

1. Válassza ki az engedélyezni vagy letiltani kívánt egyenrangú konfigurációt.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="A ExpressRoute Circuit áttekintése":::

1. Jelölje be a társítás **engedélyezése** jelölőnégyzetet, majd a társítási konfiguráció letiltásához válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Privát társak letiltása":::

1. A társítás újbóli engedélyezéséhez ellenőrizze a társítás **engedélyezése** lehetőséget, és kattintson a **Mentés** lehetőségre.

## <a name="next-steps"></a>További lépések
Ha segítségre van szüksége egy ExpressRoute kapcsolatos probléma elhárításához, tekintse meg a következő cikkeket:
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)
