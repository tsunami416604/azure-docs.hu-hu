---
title: 'Virtuális központ tényleges útvonalának megtekintése: Azure Virtual WAN | Microsoft Docs'
description: Virtuális központ tényleges útvonalának megtekintése az Azure Virtual WAN-ban
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 20cdc55b474034480392f9dfb05b20ad25df6939
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037766"
---
# <a name="view-virtual-hub-effective-routes"></a>Virtuális központ érvényes útvonalainak megtekintése

A Azure Portalban megtekintheti a virtuális WAN-központ összes útvonalát. Ez a cikk végigvezeti a hatályos útvonalak megtekintésének lépésein. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

> [!NOTE]
> A Azure Portal ezen funkciók némelyike továbbra is kivezethető, és nem érhető el, amíg a hét augusztus 3. 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Kapcsolatok vagy útválasztási táblák kiválasztása

1. Navigáljon a virtuális hubhoz, majd válassza az **Útválasztás**elemet. Az Útválasztás lapon válassza az **érvényes útvonalak**lehetőséget.
1. A legördülő listából választhatja a **kapcsolattípus** vagy az **útválasztási tábla**lehetőséget. Ha nem látja az útválasztási táblázat lehetőséget, ez azt jelenti, hogy nincs beállítva egyéni vagy alapértelmezett útválasztási tábla ebben a virtuális központban.
1. A **kapcsolatok/útválasztási táblák**legördülő menüből a következő elemek közül választhat:

   * Virtual Network-kapcsolatok
   * VPN-hely kapcsolata
   * ExpressRoute-kapcsolatok
   * Pont – hely kapcsolat
   * Útválasztási táblázat

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="Útválasztás":::

## <a name="view-output"></a><a name="output"></a>Kimenet megtekintése

Az oldal kimenete a következő mezőket jeleníti meg:

* **Előtag**: az aktuális entitáshoz ismert címek előtagja.
* **Következő ugrás típusa**: lehet Virtual Network kapcsolat, a VPN_S2S_Gateway, a ExpressRouteGateway, a távoli központ vagy a Azure Firewall.
* **Következő ugrás**: ez az IP-cím, vagy egyszerűen azt mutatja, hogy a hivatkozás az aktuális hubot jelenti.
* **Forrás: az**útválasztási forrás erőforrás-azonosítója.
* **Elérési út**: a BGP attribútum as (autonóm rendszer) elérési útja felsorolja az összes olyan számot, amelyet át kell adni ahhoz, hogy elérje azt a helyet, ahol az elérési utat csatolták, a meghirdetve.

### <a name="example"></a><a name="example"></a>Például

A következő példában szereplő értékek azt jelzik, hogy a virtuális hub-kapcsolatok vagy az útválasztási táblázat megtanulta a 10.2.0.0/24 (ág-előtag) útvonalát. Megtanulta az útvonalat a **VPN következő ugrásának típusa** VPN_S2S_Gateway a **következő ugrás** VPN Gateway erőforrás-azonosítóval. Az **útvonal kezdőpontja** a kezdeményező VPN-átjáró/útválasztási tábla/kapcsolat erőforrás-azonosítójára mutat. Az **elérési** út a ág as elérési útját jelöli.

A táblázat alján található görgetősáv használatával megtekintheti az "AS Path" kifejezést.

| **Előtag** |  **Következő ugrási típus** | **Következő ugrás** |  **Útvonal forrása** |**Elérési út** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Szempontok**

* Ha a a 0.0.0.0/0 értéket látja a **hatékony útvonalak lekérése** kimenetben, az azt jelenti, hogy az útvonal létezik az egyik útválasztási táblában. Ha azonban ez az útvonal az internetre lett beállítva, egy további **"enableInternetSecurity"** jelzőt kell megadnia a kapcsolatban. A virtuálisgép-hálózati adapteren érvényes útvonal nem jeleníti meg az útvonalat, ha a kapcsolat "enableInternetSecurity" jelzője "false" (hamis).

* A virtuális hálózati kapcsolat, a VPN-kapcsolat vagy egy ExpressRoute-kapcsolat szerkesztése során az **alapértelmezett propagálási útvonal** mező látható az Azure Virtual WAN portálon. Ebben a mezőben a **enableInternetSecurity** jelző látható, amely a ExpressRoute és a VPN-kapcsolatok esetében mindig "false", de a virtuális hálózati kapcsolatok esetében "igaz".

## <a name="next-steps"></a>További lépések

* A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
