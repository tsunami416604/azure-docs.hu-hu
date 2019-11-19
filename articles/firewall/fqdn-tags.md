---
title: Az Azure Firewall FQDN-címkék áttekintése
description: Az FQDN címke a jól ismert Microsoft-szolgáltatásokhoz társított teljes tartománynevek (FQDN-EK) csoportját jelöli.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169041"
---
# <a name="fqdn-tags-overview"></a>FQDN-címkék – áttekintés

Az FQDN címke a jól ismert Microsoft-szolgáltatásokhoz társított teljes tartománynevek (FQDN-EK) csoportját jelöli. Az alkalmazási szabályokban az FQDN címke használatával engedélyezheti a szükséges kimenő hálózati forgalmat a tűzfalon keresztül.

Ha például manuálisan szeretné engedélyezni Windows Update hálózati forgalmat a tűzfalon keresztül, létre kell hoznia több alkalmazási szabályt a Microsoft dokumentációjában. A FQDN-címkék használatával létrehozhat egy alkalmazási szabályt, belefoglalhatja a **Windows Updates** címkét, és most a Microsoft Windows Update-végpontok felé irányuló hálózati forgalmat a tűzfalon keresztül is elvégezheti.

Nem hozhat létre saját FQDN-címkéket, és nem adhatja meg, hogy mely teljes tartománynevek szerepeljenek a címkén belül. A Microsoft kezeli a teljes tartománynév címke által felölelt teljes tartományneveket, és frissíti a címkét FQDN-ként. 

<!--- screenshot of application rule with a FQDN tag.-->

A következő táblázat a jelenleg használható FQDN-címkéket mutatja. A Microsoft fenntartja ezeket a címkéket, és rendszeres időközönként további címkéket is hozzáadhat.

## <a name="current-fqdn-tags"></a>Jelenlegi FQDN-Címkék

|FQDN címke  |Leírás  |
|---------|---------|
|Windows Update     |A Microsoft Update kimenő hozzáférésének engedélyezése a [tűzfal konfigurálása a szoftverfrissítések számára](https://technet.microsoft.com/library/bb693717.aspx)című témakörben leírtak szerint.|
|Windows diagnosztika|Engedélyezze a kimenő hozzáférést az összes [Windows diagnosztikai végponthoz](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|A [Maps](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)-hez való kimenő hozzáférés engedélyezése.|
|App Service Environment (ASE)|Lehetővé teszi a kimenő hozzáférést a beadási platform forgalmához. Ez a címke nem fedi le a beszállító által létrehozott ügyfél-specifikus tárterületet és SQL-végpontokat. Ezeket a [szolgáltatás-végpontokon](../virtual-network/tutorial-restrict-network-access-to-resources.md) keresztül kell engedélyezni, vagy manuálisan kell hozzáadni.<br><br>További információ a Azure Firewall beépítéséről a bevonással: [app Service Environment zárolása](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Engedélyezi a kimenő hozzáférést a Azure Backup szolgáltatásokhoz.|
|Azure HDInsight|Engedélyezi a kimenő hozzáférést a HDInsight-platform forgalmához. Ez a címke nem fedi le az ügyfél-specifikus tárterületet vagy az HDInsight-ből származó SQL-forgalmat. Engedélyezze ezeket a [szolgáltatás-végpontok](../virtual-network/tutorial-restrict-network-access-to-resources.md) használatával, vagy manuálisan adja hozzá őket.|

> [!NOTE]
> Ha egy alkalmazási szabályban kijelöli a teljes tartománynevet, a port mezőt a **https**értékre kell beállítani.

## <a name="next-steps"></a>Következő lépések

A Azure Firewall telepítésének megismeréséhez tekintse meg [az oktatóanyag: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md)című témakört.
