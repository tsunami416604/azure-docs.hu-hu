---
title: Az Azure Blockchain Service konzorciuma
description: Annak áttekintése, hogy az Azure Blockchain szolgáltatás hogyan valósítja meg a konzorciumi blokklánc-hálózatokat.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247617"
---
# <a name="azure-blockchain-service-consortium"></a>Az Azure Blockchain Service konzorciuma

Az Azure Blockchain Szolgáltatás használatával privát konzorciumi blokklánc-hálózatokat hozhat létre, ahol az egyes blokklánc-hálózatok a hálózat bizonyos résztvevőire korlátozhatók. Csak a privát konzorcium blockchain hálózatának résztvevői tekinthetik meg és léphetnek kapcsolatba a blokklánccal. Az Azure Blockchain Szolgáltatás konzorciumi hálózatai kétféle tagi résztvevői szerepkört tartalmazhatnak:

* **Rendszergazda** – Kiemelt résztvevők, akik konzorciumkezelési műveleteket végrehajthatnak, és részt vehetnek a blokklánc-tranzakciókban.

* **Felhasználó** – Azok a résztvevők, akik nem tudnak konzorciumkezelési műveletet végrehajtani, de részt vehetnek a blokklánc-tranzakciókban.

A konzorciumi hálózatok a résztvevők szerepköreinek keverékei lehetnek, és az egyes szerepkörtípusok tetszőleges számmal rendelkezhetnek. Legalább egy rendszergazdának kell lennie.

Az alábbi ábrán egy több résztvevővel rendelkező konzorciumi hálózat látható:

![Magánkonzorcium hálózati diagramja](./media/consortium/network-diagram.png)

Az Azure Blockchain Service konzorciumi kezelésével kezelheti a konzorciumi hálózat résztvevőit. A konzorcium irányítása a hálózat konszenzusos modelljén alapul. A jelenlegi előzetes verzióban az Azure Blockchain Service egy központosított konszenzusos modellt biztosít a konzorciumkezeléshez. A felügyeleti szerepkörrel rendelkező kiemelt jogosultságú résztvevők konzorciumkezelési műveleteket is végrehajthatnak, például résztvevőket vehetnek fel vagy távolíthatnak el a hálózatról.

## <a name="roles"></a>Szerepkörök

A konzorcium résztvevői lehetnek személyek vagy szervezetek, és felhasználói vagy rendszergazdai szerepkört kaphatnak. Az alábbi táblázat a két szerepkör közötti magas szintű különbségeket sorolja fel:

| Műveletek | Felhasználói szerepkör | Rendszergazdai szerepkör
|--------|:----:|:------------:|
| Új tag létrehozása | Igen | Igen |
| Új tagok meghívása | Nem | Igen |
| Tagrésztvevői szerepkör beállítása vagy módosítása | Nem | Igen |
| Tag megjelenítendő nevének módosítása | Csak a saját tag | Csak a saját tag |
| Tagok eltávolítása | Csak a saját tag | Igen |
| Részvétel a blokklánc-tranzakciókban | Igen | Igen |

### <a name="user-role"></a>Felhasználói szerepkör

A felhasználók olyan konzorciumi résztvevők, akik nem rendelkeznek rendszergazdai képességekkel. Nem vehetnek részt a konzorciumhoz kapcsolódó tagok irányításában. A felhasználók módosíthatják a tagok megjelenítendő nevét, és eltávolíthatják magukat a konzorciumból.

### <a name="administrator"></a>Rendszergazda

A rendszergazda kezelheti a konzorcium tagjait. A rendszergazda tagokat hívhat meg, tagokat távolíthat el, vagy frissítheti a tagok szerepköreit a konzorciumon belül.
A konzorciumon belül mindig legalább egy rendszergazdának kell lennie. A legutóbbi rendszergazdának meg kell adnia egy másik résztvevőt rendszergazdai szerepkörként, mielőtt kilépne egy konzorciumból.

## <a name="managing-members"></a>Tagok kezelése

Csak a rendszergazdák hívhatnak meg más résztvevőket a konzorciumba. A rendszergazdák az Azure-előfizetés-azonosítójuk használatával hívják meg a résztvevőket.

A meghívottak a blockchain konzorciumhoz csatlakozhatnak, ha új tagot helyeznek üzembe az Azure Blockchain Szolgáltatásban. A meghívott konzorcium megtekintéséhez és az ahhoz való csatlakozáshoz meg kell adnia ugyanazt az Azure-előfizetés-azonosítót, amelyet a hálózati rendszergazda a meghívásban használt.

A rendszergazdák bármelyik résztvevőt eltávolíthatják a konzorciumból, beleértve a többi rendszergazdát is. A tagok csak a konzorciumból távolíthatják el magukat.

## <a name="consortium-management-smart-contract"></a>Konzorciumkezelési intelligens szerződés

Az Azure Blockchain Szolgáltatás konzorciumi kezelése konzorciumkezelési intelligens szerződéseken keresztül történik. Az intelligens szerződések automatikusan üzembe helyezése a csomópontok, amikor egy új blokklánc-tag üzembe helyezésekor.

A root konzorcium felügyeleti intelligens szerződés címe megtekinthető az Azure Portalon. A **RootContract cím** a blockchain tag áttekintő szakaszában található.

![RootContract-cím](./media/consortium/rootcontract-address.png)

A konzorciumkezelési intelligens szerződéssel a konzorciumfelügyeleti [PowerShell-modul](manage-consortium-powershell.md), az Azure Portal használatával, vagy közvetlenül az intelligens szerződésen keresztül kommunikálhat az Azure Blockchain Szolgáltatás által létrehozott Ethereum-fiók használatával.

## <a name="ethereum-account"></a>Ethereum-fiók

Tag létrehozásakor létrejön egy Ethereum-fiókkulcs. Az Azure Blockchain Service a kulccsal hozza létre a konzorciumkezeléshez kapcsolódó tranzakciókat. Az Ethereum-fiókkulcsot az Azure Blockchain Szolgáltatás automatikusan kezeli.

A tagfiók megtekinthető az Azure Portalon. A tagfiók a blockchain tag áttekintő részében található.

![Tagfiók](./media/consortium/member-account.png)

Az Ethereum-fiókot a tagfiókra kattintva és egy új jelszó megadásával állíthatja vissza. Mind az Ethereum fiók címét, mind a jelszót alaphelyzetbe állítja a rendszer.  

## <a name="next-steps"></a>További lépések

A konzorciumkezelési műveletek a PowerShellen keresztül érhetők el. További információ: [Konzorciumi tagok kezelése az Azure Blockchain szolgáltatásban a PowerShell használatával](manage-consortium-powershell.md)című témakörben talál.
