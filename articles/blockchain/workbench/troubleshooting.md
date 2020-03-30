---
title: Az Azure Blockchain Workbench hibaelhárítása
description: Azure Blockchain Workbench Preview-alkalmazások hibaelhárítása.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324303"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Az Azure Blockchain Workbench előzetes verzió – hibaelhárítás

Egy PowerShell-parancsfájl érhető el a fejlesztői hibakeresés vagy támogatás segítésére. A parancsfájl összegzést hoz létre, és részletes naplókat gyűjt a hibaelhárításhoz. Az összegyűjtött naplók a következők:

* Blockchain hálózat, mint például az Ethereum
* Blockchain Workbench mikroszolgáltatások
* Application Insights
* Azure Monitoring (Azure Monitor naplók)

Az információk segítségével meghatározhatja a következő lépéseket, és meghatározhatja a problémák kiváltó okait.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Parancsfájl – hibaelhárítás

A PowerShell hibaelhárítási parancsfájl érhető el a GitHubon. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/blockchain/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>A szkript futtatása
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Futtassa a parancsfájlt a `collectBlockchainWorkbenchTroubleshooting.ps1` naplók gyűjtéséhez, és hozzon létre egy hibaelhárítási információkat tartalmazó zip-fájlt. Példa:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
A parancsfájl a következő paramétereket fogadja el:

| Paraméter  | Leírás | Kötelező |
|---------|---------|----|
| Előfizetésazonosító | SubscriptionID az összes erőforrás létrehozásához vagy megkereséséhez. | Igen |
| ResourceGroupName | Annak az Azure Resource Groupnak a neve, ahol a Blokklánc-munkaterület telepítve van. | Igen |
| Kimenetkönyvtár | A kimenet létrehozásának elérési útja . ZIP-fájl. Ha nincs megadva, az aktuális könyvtár alapértelmezés e. | Nem |
| Visszatekintési órák | A telemetriai adatok lehúzásakor használandó órák száma. Az alapértelmezett érték 24 óra. A maximális érték 90 óra | Nem |
| OmsSubscriptionId | Az előfizetés-azonosító, ahol az Azure Monitor naplók telepítve van. Csak adja át ezt a paramétert, ha az Azure Monitor naplók a blockchain hálózat telepítve van a Blockchain Workbench erőforráscsoporton kívül.| Nem |
| OmsResourceGroup |Az az erőforráscsoport, ahol az Azure Monitor-naplók telepítve vannak. Csak adja át ezt a paramétert, ha az Azure Monitor naplók a blockchain hálózat telepítve van a Blockchain Workbench erőforráscsoporton kívül.| Nem |
| OmsWorkspaceName | A Log Analytics munkaterület neve. Csak akkor adja át ezt a paramétert, ha a blokklánc-hálózat Azure Monitor-naplói a Blockchain Workbench erőforráscsoportján kívül vannak telepítve. | Nem |

## <a name="what-is-collected"></a>Mit gyűjtenek?

A kimeneti ZIP-fájl a következő mappastruktúrát tartalmazza:

| Mappa vagy fájl | Leírás  |
|---------|---------|
| \Summary.txt | A rendszer összefoglalása |
| \Metrics\blockchain | A blokkláncra vonatkozó mérőszámok |
| \Metrics\Munkaterület | A munkaterületre vonatkozó mérőszámok |
| \Részletek\Blokklánc | Részletes naplók a blockchain |
| \Részletek\Munkaterület | Részletes naplók a munkaterületről |

Az összefoglaló fájl pillanatképet ad az alkalmazás általános állapotáról és az alkalmazás állapotáról. Az összefoglaló ajánlott műveleteket tartalmaz, kiemeli a legfontosabb hibákat és a szolgáltatások futtatásával kapcsolatos metaadatokat.

A **Metrikák** mappa az idő múlásával különböző rendszerösszetevők mutatóit tartalmazza. A kimeneti fájl `\Details\Workbench\apiMetrics.txt` például a különböző válaszkódok és a válaszidők összegzését tartalmazza a gyűjtési időszak során. A **Részletek** mappa részletes naplókat tartalmaz a Workbench vagy az alapul szolgáló blokklánc-hálózattal kapcsolatos problémák elhárításához. Például `\Details\Workbench\Exceptions.csv` a rendszerben előforduló legutóbbi kivételek listáját tartalmazza, amely hasznos az intelligens szerződésekkel kapcsolatos hibák vagy a blokklánccal való interakciók hibáinak elhárításához. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench Application Insights hibaelhárítási útmutatója](https://aka.ms/workbenchtroubleshooting)
