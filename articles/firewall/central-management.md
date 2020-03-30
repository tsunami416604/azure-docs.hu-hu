---
title: Az Azure Firewall központi kezelése
description: További információ az Azure Firewall Manager központi felügyeletéről
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444549"
---
# <a name="azure-firewall-central-management"></a>Az Azure Firewall központi kezelése

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Ha több tűzfalat kezel, akkor a folyamatosan változó tűzfalszabályok megnehezítik azok szinkronizálását. A központi informatikai csapatoknak szükségük van az alaptűzfal-házirendek definiálására és több részlegközötti kényszerítésükre. Ugyanakkor devops csapatok szeretné létrehozni a saját helyi származtatott tűzfal-házirendek a jobb mozgékonyság érdekében.

Az Azure Firewall Manager előzetes verziója segíthet a problémák megoldásában.


## <a name="azure-firewall-manager-preview"></a>Az Azure firewall Manager előzetes verzió

Az Azure Firewall Manager Preview egy hálózati biztonságfelügyeleti szolgáltatás, amely központi biztonsági házirendet és útvonalkezelést biztosít a felhőalapú biztonsági peremterülethez. Megkönnyíti a vállalati informatikai csapatok számára, hogy központilag határozzák meg a hálózati és alkalmazásszintű szabályokat a forgalom szűréséhez több Azure Firewall-példányok között. Különböző Azure-régiókra és előfizetésekre is kiterjedhet a központi és küllős architektúrákban a forgalomszabályozás és -védelem érdekében. Emellett a DevOps-nak jobb agilitást biztosít a szervezetek között megvalósított származtatott helyi tűzfal-biztonsági házirendekkel.

### <a name="firewall-policy"></a>Tűzfal házirendje

A tűzfalszabályzat egy Azure-erőforrás, amely NAT- és alkalmazásszabály-gyűjteményeket és fenyegetésintelligencia-beállításokat tartalmaz. Ez egy globális erőforrás, amely több Azure Tűzfal-példányban használható *a biztonságos virtuális csomópontokban* és *a hub virtuális hálózatokban.* Új házirendek hozhatók létre a semmiből, vagy örökölt meglévő házirendek. Az öröklődés lehetővé teszi a DevOps számára, hogy helyi tűzfalházirendeket hozzon létre a szervezet által megbízott alapházirenden felül. A házirendek régiók és előfizetések között működnek.
 
Tűzfalszabályzatot és társításokat hozhat létre az Azure Firewall Manager rel. Azonban rest API, sablonok, Azure PowerShell és CLI használatával is létrehozhat és kezelhet szabályzatot. Miután létrehozott egy házirendet, társíthatja azt egy virtuális WAN hub tűzfalához, így *biztonságos virtuális központ* és/vagy egy virtuális hálózat tűzfala, így hub virtuális *hálózat.*

### <a name="pricing"></a>Díjszabás

A házirendek számlázása tűzfaltársítások alapján történik. A nulla vagy egy tűzfalas társítással rendelkező házirend ingyenes. A több tűzfaltársítással rendelkező házirendet fix díjtétellel számlázunk. További információ: [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Az Azure firewall management partnerei

A következő vezető külső megoldások támogatják az Azure Firewall központi felügyeletét a szabványos Azure REST API-k használatával. Mindegyik megoldásnak megvannak a maga egyedi jellemzői és jellemzői:

- [AlgoSec felhőfolyamat](https://www.algosec.com/azure/) 
- [Barracuda felhő biztonsági guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orka](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>További lépések

Az Azure Firewall Manager előzetes verziójáról a [Mi az Azure Firewall Manager előzetes verzió?](../firewall-manager/overview.md)