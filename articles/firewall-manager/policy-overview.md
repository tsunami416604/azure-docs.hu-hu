---
title: Az Azure Firewall Manager előzetes verzióra vonatkozó szabályzatának áttekintése
description: További információ az Azure Firewall Manager irányelveiről
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77445020"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Az Azure Firewall Manager előzetes verzióra vonatkozó szabályzatának áttekintése

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A tűzfalszabályzat egy Azure-erőforrás, amely NAT-, hálózati és alkalmazásszabály-gyűjteményeket, valamint fenyegetésintelligencia-beállításokat tartalmaz. Ez egy globális erőforrás, amely több Azure tűzfal-példányon keresztül használható biztonságos virtuális csomópontokban és hub virtuális hálózatokban. A házirendek régiók és előfizetések között működnek.

![Az Azure Firewall Manager házirendje](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Házirend létrehozása és társítása

A szabályzat többféleképpen hozható létre és kezelhető, beleértve az Azure Portalt, a REST API-t, a sablonokat, az Azure PowerShellt és a CLI-t.

A meglévő szabályokat az Azure Firewallből is áttelepítheti a portál vagy az Azure PowerShell használatával szabályzatok létrehozásához. További információ: [Az Azure tűzfal-konfigurációk áttelepítése az Azure Tűzfal-házirendbe (előzetes verzió)](migrate-to-policy.md)című témakörben talál. 

A házirendek egy vagy több virtuális hubhoz vagy virtuális hálózathoz társíthatók. A tűzfal a fiókjához társított bármely előfizetésben és bármely régióban lehet.

## <a name="hierarchical-policies"></a>Hierarchikus házirendek

Új házirendek hozhatók létre a semmiből, vagy örökölt meglévő házirendek. Az öröklődés lehetővé teszi a DevOps számára, hogy helyi tűzfalházirendeket hozzon létre a szervezet által megbízott alapházirenden felül.

A nem üres szülőházirendekkel létrehozott házirendek öröklik az összes szabálygyűjteményt a szülőházirendből. A szülőházirendből örökölt hálózati szabálygyűjtemények mindig elsőbbséget élveznek az új házirend részeként definiált hálózati szabálygyűjtemények felett. Ugyanez a logika vonatkozik az alkalmazásszabály-gyűjteményre is. A hálózati szabálygyűjtemények feldolgozása azonban mindig az alkalmazásszabály-gyűjtemények előtt van feldolgozva, az öröklődéstől függetlenül.

A fenyegetésfelderítési mód a szülőházirendtől is öröklődik. Beállíthatja a fenyegetés intelligencia mód egy másik értéket, hogy felülbírálja ezt a viselkedést, de nem lehet kikapcsolni. Csak szigorúbb értékkel lehet felülbírálni. Ha például a szülőházirend **beállítása csak riasztás,** beállíthatja ezt a helyi házirendet **riasztásra és megtagadásra.**

A NAT-szabálygyűjtemények nem öröklődnek, mert egy adott tűzfalra jellemzőek.

Öröklődés esetén a szülőházirend módosításai automatikusan érvénybe lépnek a társított tűzfalalárendelt házirendekre.

## <a name="traditional-rules-and-policies"></a>Hagyományos szabályok és politikák

Az Azure Firewall támogatja a hagyományos szabályokat és szabályzatokat is. Az alábbi táblázat a szabályzatokat és szabályokat hasonlítja össze:


|         |Szabályzat  |Szabályok  |
|---------|---------|---------|
|Contains     |NAT-, hálózati, alkalmazásszabályok és fenyegetésintelligencia-beállítások|NAT- és alkalmazásszabályok |
|Védi     |Virtuális csomópontok és virtuális hálózatok|Csak virtuális hálózatok|
|Portal-felület     |Központi felügyelet a Tűzfalkezelő használatával|Önálló tűzfal-élmény|
|Több tűzfal támogatása     |A tűzfalházirend egy különálló erőforrás, amely tűzfalakon keresztül használható|Manuálisexportálási és importálási szabályok, illetve külső felügyeleti megoldások használata |
|Díjszabás     |A számlázás tűzfal-társítás alapján történik. Lásd: [Árképzés](#pricing).|Ingyenes|
|Támogatott telepítési mechanizmusok     |Portál, REST API, sablonok, Azure PowerShell és CLI|Portál, REST API, sablonok, PowerShell és CLI. |
|Kiadás állapota     |Nyilvános előzetes verzió|Általános elérhetőség|

## <a name="pricing"></a>Díjszabás

A házirendek számlázása tűzfaltársítások alapján történik. A nulla vagy egy tűzfalas társítással rendelkező házirend ingyenes. A több tűzfaltársítással rendelkező házirendet fix díjtétellel számlázunk. További információ: [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>További lépések

Az Azure-tűzfal üzembe helyezéséről az [Oktatóanyag: A felhőhálózat biztonságossá tétele az Azure Firewall Manager előzetes verziójával az Azure Portal használatával](secure-cloud-network.md)című témakörben olvashat.
