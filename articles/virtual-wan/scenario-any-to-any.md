---
title: 'Forgatókönyv: bármilyen'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztáshoz – bármely – bármely
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267737"
---
# <a name="scenario-any-to-any"></a>Forgatókönyv: bármilyen

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Bármilyen szituációban bármely küllő elérheti egy másikat. Ha több központ létezik, a hub – hub útválasztás (más néven az Inter-hub) alapértelmezés szerint engedélyezve van a standard virtuális WAN-ban. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

Ha szeretné megtudni, hogy hány útválasztási táblázatra van szükség egy virtuális WAN-forgatókönyvben, létrehozhat egy kapcsolati mátrixot, ahol minden cella azt jelöli, hogy egy forrás (sor) tud-e kommunikálni a célhoz (oszlophoz). Ebben a forgatókönyvben a kapcsolati mátrix triviális, de a többi forgatókönyvvel való összhang érdekében belefoglaljuk.

| Forrás |   Művelet |  *Virtuális hálózatok* | *Ágak* |
| -------------- | -------- | ---------- | ---|
| Virtuális hálózatok     | &#8594;|      X     |     X    |
| Ágak   | &#8594;|    X     |     X    |

Az előző táblázatban szereplő összes cella azt ismerteti, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a tábla sorai) megtanulja-e a cél előtagot (a folyamat "to" oldalát, a tábla oszlopainak fejlécét) egy adott forgalmi folyamathoz, ahol az "X" azt jelenti, hogy a kapcsolatot a virtuális WAN nyújtja.

Mivel a virtuális hálózatok és az ágak (VPN, ExpressRoute és felhasználói VPN) összes kapcsolata ugyanazokkal a kapcsolati követelményekkel rendelkezik, egyetlen útválasztási táblára van szükség. Ennek eredményeképpen minden kapcsolat társítva lesz, és a rendszer az alapértelmezett útválasztási táblázatba továbbítja az útválasztási táblázatot:

* Virtuális hálózatok:
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektúra

Az **1. ábrán**minden virtuális hálózatok és ág (VPN, EXPRESSROUTE, P2S) elérheti egymást. Egy virtuális központban a kapcsolatok a következőképpen működnek:

* A VPN-kapcsolat VPN-helyet csatlakoztat egy VPN-átjáróhoz.
* A virtuális hálózati kapcsolat virtuális hálózatot csatlakoztat egy virtuális hubhoz. A virtuális központ útválasztója biztosítja a virtuális hálózatok közötti átviteli funkciót.
* Az ExpressRoute-kapcsolat egy ExpressRoute áramkört csatlakoztat egy ExpressRoute-átjáróhoz.

Ezek a kapcsolatok (alapértelmezés szerint a létrehozáskor) az alapértelmezett útválasztási táblázathoz vannak társítva, hacsak nem állítja be a kapcsolat útválasztási konfigurációját **sem vagy**egy egyéni útválasztási táblázatot. Ezek a kapcsolatok a útvonalakat is propagálják, alapértelmezés szerint az alapértelmezett útválasztási táblázatra. Ez azt teszi lehetővé, hogy a bármilyen küllős (VNet, VPN, ER, P2S) típusú forgatókönyvben elérhetők legyenek egymással.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="1. ábra":::

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ez a forgatókönyv alapértelmezés szerint engedélyezve van a standard szintű virtuális WAN esetében. Ha a ág – ág beállítás le van tiltva a WAN-konfigurációban, ez letiltja a fiókirodák közötti kapcsolatot. A VPN/ExpressRoute/User VPN a virtuális WAN ág-küllői.

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
