---
title: Hibaelhárítás az Azure Blockchain munkaterület
description: Egy Azure Blockchain munkaterület alkalmazás elhárításáról.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: df7fd71bee287a063e8d586de38522f3b71e14d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Hibaelhárítás az Azure Blockchain munkaterület

PowerShell parancsfájl elősegítve ezzel a hibakeresés fejlesztői vagy támogatja érhető el. A parancsfájl hoz létre összefoglaló és részletes naplókat a további hibaelhárítási gyűjti. Gyűjtött naplók tartalmazni:

* Blockchain hálózatokon, például Ethereum
* Blockchain munkaterület mikroszolgáltatások
* Application Insights
* Azure-(OMS) figyelése

Az információk segítségével határozza meg a következő lépések és a probléma okának megállapításához. 

## <a name="troubleshooting-script"></a>Hibaelhárítási parancsfájl

A PowerShell parancsfájl hibaelhárítás a Githubon érhető el. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/blockchain/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>A parancsfájl futtatása
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Futtassa a `collectBlockchainWorkbenchTroubleshooting.ps1` parancsfájl gyűjtését, és hozzon létre egy mappát a hibaelhárítási információkat tartalmazó ZIP-fájl. Példa:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
A parancsfájl a következő paraméterek fogadja el:

| Paraméter  | Leírás | Szükséges |
|---------|---------|----|
| SubscriptionID | A SubscriptionID létrehozásához, vagy keresse meg az összes erőforrást. | Igen |
| ResourceGroupName | Ha Blockchain munkaterület telepítve van az Azure erőforráscsoport nevét. | Igen |
| Kimeneti_könyvtár | A kimenet létrehozásához elérési utat. A ZIP-fájl. Ha nincs megadva, alapértelmezés szerint az aktuális könyvtárat. | Nem
| OmsSubscriptionId | Az előfizetés-azonosító OMS telepítési helyét. Csak adja át ennek a paraméternek, ha blockchain hálózat OMS Blockchain munkaterület erőforráscsoport kívül van telepítve.| Nem |
| OmsResourceGroup |Az erőforráscsoport, amelyen OMS üzembe van helyezve. Csak adja át ennek a paraméternek, ha blockchain hálózat OMS Blockchain munkaterület erőforráscsoport kívül van telepítve.| Nem |
| OmsWorkspaceName | Az OMS-munkaterület nevét. Csak adja át ennek a paraméternek, ha blockchain hálózat OMS Blockchain munkaterület erőforráscsoport kívül van telepítve | Nem |

## <a name="what-is-collected"></a>Gyűjtött adatok?

A kimeneti ZIP-fájl tartalmazza a következő gyökérmappa-szerkezetében:

| Mappa \ fájl | Leírás  |
|---------|---------|
| \Summary.txt | A rendszer összegzése |
| \metrics\blockchain | A blockchain kapcsolatos metrikákat |
| \metrics\workbench | A munkaterület kapcsolatos metrikákat |
| \details\blockchain | A blockchain kapcsolatos részletes naplókat |
| \details\workbench | A munkaterület kapcsolatos részletes naplókat |

Az összegző fájl lehetővé teszi az alkalmazás általános állapotának és az alkalmazás állapotáról pillanatképet. Az összegzés kínál javasolt műveletet, a legfontosabb hibák, és a szolgáltatások futtatásával kapcsolatos metaadatok emeli ki.

## <a name="next-steps"></a>További lépések

* [Azure Blockchain munkaterület-architektúra](blockchain-workbench-architecture.md)