---
title: Azure Firewall FQDN-szűrés a hálózati szabályokban (előzetes verzió)
description: A Azure Firewall FQDN-szűrés használata a hálózati szabályokban
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7a30238250c9fcb86f1cc01226d44ab385c61843
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086625"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>FQDN-szűrés használata a hálózati szabályokban (előzetes verzió)

> [!IMPORTANT]
> A hálózati szabályok FQDN-szűrése jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A teljes tartománynév (FQDN) a gazdagép tartománynevét jelöli. A tartománynév egy vagy több IP-címhez van társítva. Az alkalmazási szabályokban engedélyezheti vagy letilthatja a teljes tartománynevek és a teljes tartománynevek címkéit. Az egyéni DNS-és DNS-proxybeállítások használatával a hálózati szabályokban is használhatja a teljes tartománynév-szűrést.

## <a name="how-it-works"></a>Működés

Azure Firewall lefordítja a teljes tartománynevet egy IP-cím (ek) ra a DNS-beállításaival, és Azure DNS vagy egyéni DNS-konfiguráció alapján végzi el a szabályok feldolgozását.

A hálózati szabályokban a teljes tartománynevek használatához engedélyeznie kell a DNS-proxyt. Ha nem engedélyezi a DNS-proxyt, a megbízható szabályok feldolgozása veszélyben van. Ha engedélyezve van, a DNS-forgalom Azure Firewallre lesz irányítva, ahol konfigurálhatja az egyéni DNS-kiszolgálót. Ezután a tűzfal és az ügyfelek ugyanazt a konfigurált DNS-kiszolgálót használják. Ha a DNS-proxy nincs engedélyezve, a Azure Firewall eltérő választ eredményezhet, mivel az ügyfél és a tűzfal más kiszolgálókat is használhat névfeloldáshoz. A hálózati szabályok FQDN-szűrése hibás vagy inkonzisztens lehet, ha az ügyfél és a tűzfal eltérő DNS-válaszokat kap.

Ezt a követelményt úgy is felülbírálhatja, hogy elismeri a kockázatot, mielőtt kiválasztja a **Save (Mentés** ) lehetőséget a szabály gyűjteményében.

## <a name="next-steps"></a>További lépések

[Azure Firewall DNS-beállítások](dns-settings.md)
