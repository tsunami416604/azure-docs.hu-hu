---
title: Azure Firewall FQDN-szűrés a hálózati szabályokban (előzetes verzió)
description: FQDN-szűrés használata a hálózati szabályokban
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 46d1f2eae1342901a660f99891d1ac1529e8ad77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568850"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>FQDN-szűrés a hálózati szabályokban (előzetes verzió)

> [!IMPORTANT]
> A hálózati szabályok FQDN-szűrése jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A teljes tartománynév (FQDN) a gazdagép tartománynevét jelöli. A tartománynév egy vagy több IP-címhez van társítva. Az alkalmazási szabályokban engedélyezheti vagy letilthatja a teljes tartománynevek és a teljes tartománynevek címkéit. Az egyéni DNS-és DNS-proxybeállítások használatával a hálózati szabályokban is használhatja a teljes tartománynév-szűrést.

## <a name="how-it-works"></a>Működés

Azure Firewall lefordítja a teljes tartománynevet egy IP-cím (ek) ra a DNS-beállításaival, és Azure DNS vagy egyéni DNS-konfiguráció alapján végzi el a szabályok feldolgozását.

A hálózati szabályokban a teljes tartománynevek használatához engedélyeznie kell a DNS-proxyt. Ha nem engedélyezi a DNS-proxyt, a megbízható szabályok feldolgozása veszélyben van. Ha engedélyezve van, a DNS-forgalom Azure Firewallre lesz irányítva, ahol konfigurálhatja az egyéni DNS-kiszolgálót. Ezután a tűzfal és az ügyfelek ugyanazt a konfigurált DNS-kiszolgálót használják. Ha a DNS-proxy nincs engedélyezve, a Azure Firewall eltérő választ eredményezhet, mivel az ügyfél és a tűzfal más kiszolgálókat is használhat névfeloldáshoz. A hálózati szabályok FQDN-szűrése hibás vagy inkonzisztens lehet, ha az ügyfél és a tűzfal eltérő DNS-válaszokat kap.

## <a name="next-steps"></a>További lépések

[Azure Firewall DNS-beállítások](dns-settings.md)
