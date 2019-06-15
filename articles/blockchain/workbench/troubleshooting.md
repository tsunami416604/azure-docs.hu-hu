---
title: Hibaelhárítás az Azure Blockchain Workbench használatával
description: Hogyan háríthatók el az Azure Blockchain Workbench alkalmazás.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b0263761a4aaf663b16584fbf9caa11bb124d5c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510090"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Hibaelhárítás az Azure Blockchain Workbench használatával

Egy PowerShell-parancsprogram érhető el, amelyek segítik a hibakeresést fejlesztői vagy támogatja. A parancsfájl létrehozza összegzését, és összegyűjti a hibaelhárítási részletes naplók. Összegyűjtött naplók a következők:

* Hálózati Blockchain, Ethereum például
* Blockchain Workbenchet mikroszolgáltatások
* Application Insights
* Az Azure Monitoring (az Azure Monitor naplóira)

Az információk segítségével határozza meg a következő lépések és a problémák kiváltó okának meghatározásához.

## <a name="troubleshooting-script"></a>Parancsfájl hibaelhárítása

A PowerShell parancsfájl hibaelhárítása a Githubon érhető el. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/blockchain/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>A szkript futtatása
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Futtassa a `collectBlockchainWorkbenchTroubleshooting.ps1` parancsfájl gyűjtését, és hozzon létre egy mappát a hibaelhárítási információkat tartalmazó ZIP-fájlt. Példa:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
A szkript a következő paramétereket fogadja:

| Paraméter  | Leírás | Kötelező |
|---------|---------|----|
| SubscriptionID | Hozzon létre, vagy keresse meg az összes erőforrás SubscriptionID. | Igen |
| ResourceGroupName | Ahol Blockchain Workbench üzembe helyezésüket az Azure-erőforráscsoport nevére. | Igen |
| OutputDirectory | A kimenet létrehozása elérési útja. ZIP-fájlt. Ha nincs megadva, az alapértelmezett az aktuális könyvtár. | Nem |
| LookbackHours | Telemetriai adatok lekérése során használandó órák száma. Alapértelmezett érték: 24 óra. Maximális értéke 90 óra | Nem |
| OmsSubscriptionId | Az előfizetés-azonosító, ahol az Azure Monitor-naplók van üzembe helyezve. Csak adja át ezt a paramétert, ha az Azure Monitor-naplókat a blockchain-hálózat Blockchain Workbenchet erőforráscsoport kívül van telepítve.| Nem |
| OmsResourceGroup |Az erőforráscsoport, ahol az Azure Monitor-naplók van üzembe helyezve. Csak adja át ezt a paramétert, ha az Azure Monitor-naplókat a blockchain-hálózat Blockchain Workbenchet erőforráscsoport kívül van telepítve.| Nem |
| OmsWorkspaceName | A Log Analytics-munkaterület nevét. Csak ezt a paramétert átadhatja, ha az Azure Monitor-naplókat a blockchain-hálózat Blockchain Workbenchet erőforráscsoport kívül van telepítve. | Nem |

## <a name="what-is-collected"></a>Gyűjtött adatok?

A kimeneti ZIP-fájl tartalmazza a következő mappastruktúra:

| Fájl vagy mappa | Leírás  |
|---------|---------|
| \Summary.txt | A rendszer összefoglalása |
| \Metrics\blockchain | A blockchain kapcsolatos metrikák |
| \Metrics\Workbench | A munkaterület metrikáit |
| \Details\Blockchain | A blockchain kapcsolatos részletes naplók |
| \Details\Workbench | A workbench kapcsolatos részletes naplók |

Az összegző fájl lehetővé teszi az alkalmazás általános állapotát, és az alkalmazás állapotáról pillanatképet. Az összefoglalás kínál javasolt műveletet, kiemeli a legfontosabb hibák és -szolgáltatásokat futtató metaadatait.

A **metrikák** mappa rendszer különféle összetevők metrikáit tartalmazza idővel. Ha például a kimeneti fájl `\Details\Workbench\apiMetrics.txt` különböző válaszkódot és a válaszidőket a gyűjtési időszak során összegzését tartalmazza. A **részletek** mappa tartalmazza a részletes naplók hibaelhárítási cikkekből Workbench vagy a mögöttes blockchain-hálózat. Ha például `\Details\Workbench\Exceptions.csv` a rendszerben, ami akkor hasznos, ha az intelligens szerződések hibák vagy a blockchain interakciók hibáinak elhárítása a legutóbbi kivétel listáját tartalmazza. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights – hibaelhárítási útmutató](https://aka.ms/workbenchtroubleshooting)
