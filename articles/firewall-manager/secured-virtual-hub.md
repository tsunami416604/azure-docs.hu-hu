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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518436"
---
# <a name="what-is-a-secured-virtual-hub"></a>Mi az a biztonságos virtuális központ?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A virtuális központ egy Microsoft által felügyelt virtuális hálózat, amely lehetővé teszi a más erőforrásokból való kapcsolódást. Ha egy virtuális hubot a Azure Portal virtuális WAN-ból hoz létre, a rendszer a virtuális központ VNet és átjáróit (opcionális) hozza létre összetevőkként.

A *biztonságos* virtuális központ egy Azure-beli [virtuális WAN-központ](../virtual-wan/virtual-wan-about.md#resources) , amely a Azure Firewall Manager által konfigurált biztonsági és útválasztási házirendekkel rendelkezik. A biztonságos virtuális hubok használatával könnyedén hozhat létre olyan sugaras és tranzitív architektúrákat, amelyek natív biztonsági szolgáltatásokat biztosítanak a forgalom szabályozásához és védelméhez. 

A biztonságos virtuális központ felügyelt központi VNet is használható, helyszíni kapcsolat nélkül. Lecseréli azokat a központi VNet, amelyekre korábban szükség volt egy Azure Firewall központi telepítéshez. Mivel a biztonságos virtuális központ automatikus útválasztást biztosít, nincs szükség a saját UDR (felhasználó által megadott útvonalak) konfigurálására a tűzfalon keresztüli forgalom irányításához.

A biztonságos virtuális hubok a teljes virtuális WAN-architektúra részeként is használhatók. Ez az architektúra biztonságos, optimalizált és automatizált ág-kapcsolatot biztosít az Azure-ban és az-n keresztül. Kiválaszthatja a hálózati forgalom védelméhez és szabályozásához szükséges szolgáltatásokat, beleértve a Azure Firewall és más, harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) szolgáltatókat is.

## <a name="create-a-secured-virtual-hub"></a>Biztonságos virtuális központ létrehozása

A Azure Portal a Firewall Manager használatával létrehozhat egy új biztonságos virtuális központot, vagy átalakíthat egy meglévő virtuális hubot, amelyet korábban az Azure Virtual WAN használatával hozott létre.

## <a name="next-steps"></a>További lépések

A biztonságos virtuális központ létrehozásához és a központi és küllős hálózat biztonságossá tételéhez és használatához tekintse meg a következőt [: oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Managerrel a Azure Portal használatával](secure-cloud-network.md).