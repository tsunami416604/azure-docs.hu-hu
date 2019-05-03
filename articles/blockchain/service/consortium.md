---
title: Az Azure Blockchain-szolgáltatás Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027915"
---
# <a name="azure-blockchain-service-consortium"></a>Az Azure Blockchain-szolgáltatás Consortium

Használja az Azure Blockchain-szolgáltatás, hozhat létre saját consortium blockchain hálózatokhoz, ahol minden egyes blockchain-hálózat lehet korlátozni meghatározott résztvevők a hálózatban. Csak a privát consortium blockchain hálózati résztvevői megtekintheti és interakcióba a blockchain. Consortium hálózatok az Azure Blockchain Service kétféle típusú tag a résztvevő szerepkörök tartalmazza:

* **Rendszergazdai** – az emelt szintű résztvevők emelik, akik consortium felügyeleti műveleteket hajthatja végre, és részt vehet a blockchain-tranzakciókat.

* **Felhasználói** -résztvevők emelik, akik nem semmit sem consortium felügyeleti, de a blockchain tranzakciók vehet részt.

Consortium hálózatok résztvevő szerepkörök kombinációját, és a egy tetszőleges számú mindegyik. Legalább egy rendszergazdának kell lennie.

Az alábbi ábrán látható egy konzorciumhálózat több résztvevők:

![Privát consortium hálózati diagramja](./media/consortium/network-diagram.png)

A consortium-kezelés az Azure Blockchain-szolgáltatás kezelheti a consortium network résztvevői. Felügyeleti konzorcium a consensus modell, a hálózat alapján történik. A jelenlegi előzetes kiadásban az Azure Blockchain-szolgáltatás központi konszenzus modell consortium kezelését biztosítja. Egy rendszergazda szerepkörrel rendelkező bármely kiemelt résztvevő consortium felügyeleti műveleteket, például hozzáadásával vagy eltávolításával a résztvevők hálózatról is igénybe vehet.

## <a name="roles"></a>Szerepkörök

Konzorcium résztvevői lehet felhasználók vagy szervezetek, és egy felhasználó vagy egy rendszergazda szerepkör lehet hozzárendelni. Az alábbi táblázat a két lehetséges szerepkör magas szintű különbségeit sorolja fel:

| Műveletek | Felhasználói szerepkör | Rendszergazdai szerepkör
|--------|:----:|:------------:|
| Új tag létrehozása | Igen | Igen |
| Új tagok meghívása | Nem | Igen |
| Beállítása vagy módosítása a tag résztvevők szerepkör | Nem | Igen |
| Tag megjelenítendő nevének módosítása | Csak a saját tag | Csak a saját tag |
| Tagok eltávolítása | Csak a saját tag | Igen |
| Blockchain-tranzakciók részt | Igen | Igen |

### <a name="user-role"></a>Felhasználói szerepkör

Nincs rendszergazdai képességek consortium résztvevő, a felhasználók. Ezek nem vehet részt a consortium kapcsolatos tagok kezelése. A felhasználók módosíthatják a tag megjelenítendő neve, és eltávolíthat maguk konzorcium.

### <a name="administrator"></a>Rendszergazda

A rendszergazda tagok találhatóak a consortium kezelheti. A rendszergazda tagok meghívása, tagok eltávolítása, vagy frissítse a consortium tagok szerepköröket.
Mindig legalább egy rendszergazdai konzorcium belül kell. Az utolsó rendszergazdának meg kell adnia egy másik résztvevő rendszergazdai szerepköréhez konzorcium elhagyása előtt.

## <a name="managing-members"></a>Tagok kezelése

Csak a rendszergazdák további résztvevőket a consortium küldhetnek meghívót. A rendszergazdák a résztvevők saját Azure-előfizetés-azonosítójával meghívása

Ha meghívót, résztvevők csatlakozhatnak a blockchain consortium üzembe helyezése az Azure Blockchain-szolgáltatás az új tag. Megtekintheti, és csatlakozzon a meghívott consortium, meg kell adnia az ugyanazon a meghívó a hálózati rendszergazda által használt Azure-előfizetés azonosítója.

A rendszergazdák bármely résztvevő eltávolíthatja a consortium, beleértve a más rendszergazdák számára. A tagok csak eltávolíthatók maguk konzorcium.

## <a name="consortium-management-smart-contract"></a>Consortium felügyeleti intelligens szerződés

Consortium-kezelés az Azure Blockchain-szolgáltatás consortium felügyeleti smart contracts használatával történik. Az intelligens szerződések a rendszer automatikusan telepíti a csomópontokat, amikor telepít egy új blockchain tag.

A cím a legfelső szintű consortium felügyeleti intelligens szerződés az Azure Portalon is megtekinthetők. A **RootContract cím** blockchain-tag-áttekintés szakaszban szerepel.

![RootContract címe](./media/consortium/rootcontract-address.png)

Használhatja a consortium felügyeleti intelligens szerződés a consortium Management [PowerShell-modul](manage-consortium-powershell.md), az Azure Portal webhelyen, vagy közvetlenül az intelligens szerződés használatával az Azure Blockchain-szolgáltatás generált Ethereum fiók.

## <a name="ethereum-account"></a>Ethereum-fiók

Tag létrehozásakor jön létre Ethereum fiók kulcsára. Az Azure Blockchain-szolgáltatás a kulccsal létrehozott consortium kezelésével kapcsolatos tranzakciók. Az Ethereum-fiókkulcs automatikusan kezeli az Azure Blockchain-szolgáltatás.

A tagsági fiók az Azure Portalon is megtekinthetők. A tag fiókneve blockchain-tag-áttekintés szakaszban szerepel.

![Tagsági fiók](./media/consortium/member-account.png)

Ethereum-fiókja alaphelyzetbe állíthatja a fiókja tagja kattint, és új jelszó megadásával. A Ethereum-fiók címét és jelszavát is alaphelyzetbe állítja.  

## <a name="next-steps"></a>További lépések

[A PowerShell-lel az Azure Blockchain-szolgáltatás tagok kezelése](manage-consortium-powershell.md)
