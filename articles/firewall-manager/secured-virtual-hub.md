---
title: Mi az a biztonságos virtuális központ?
description: A biztonságos virtuális központok ismertetése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518436"
---
# <a name="what-is-a-secured-virtual-hub"></a>Mi az a biztonságos virtuális központ?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A virtuális központ a Microsoft által felügyelt virtuális hálózat, amely lehetővé teszi a kapcsolatot más erőforrásokból. Ha egy virtuális hub jön létre egy virtuális WAN az Azure Portalon, egy virtuális hub virtuális hálózat és átjárók (nem kötelező) jönnek létre, mint az összetevők.

A *biztonságos* virtuális központ egy [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) társított biztonsági és útválasztási szabályzatok által konfigurált Azure Firewall Manager. A biztonságos virtuális elosztók segítségével egyszerűen hozhat létre küllős és tranzitív architektúrákat a natív biztonsági szolgáltatásokkal a forgalom szabályozása és védelme érdekében. 

A biztonságos virtuális elosztót felügyelt központi virtuális hálózatként is használhatja, előzetes kapcsolat nélkül. Lecseréli a központi virtuális hálózat, amely korábban szükséges egy Azure Firewall központi telepítés. Mivel a biztonságos virtuális elosztó automatikus útválasztást biztosít, nincs szükség saját UDRs (felhasználó által definiált útvonalak) konfigurálására a tűzfalon keresztüli forgalom irányításához.

A biztonságos virtuális elosztók is használhatók egy teljes virtuális WAN-architektúra részeként. Ez az architektúra biztonságos, optimalizált és automatizált ágkapcsolatot biztosít az Azure-hoz és azAzure-on keresztül. Kiválaszthatja a hálózati forgalom védelmére és szabályozására szolgáló szolgáltatásokat, beleértve az Azure Tűzfalat és más külső biztonsági szolgáltatásokat szolgáltatásként (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Biztonságos virtuális elosztó létrehozása

A Firewall Manager használatával az Azure Portalon létrehozhat egy új, biztonságos virtuális központot, vagy konvertálhat egy meglévő virtuális központot, amelyet korábban az Azure Virtual WAN használatával hozott létre.

## <a name="next-steps"></a>További lépések

Biztonságos virtuális központ létrehozásához és a hub és a küllős hálózat védelméhez és szabályozásához az [Oktatóanyag: A felhőhálózat biztonságossá tétele az Azure Firewall Manager használatával az Azure Portalon keresztül](secure-cloud-network.md)című témakörben található.