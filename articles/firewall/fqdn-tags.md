---
title: Az Azure tűzfal FQDN címkék áttekintése
description: Ismerje meg a teljes tartománynév címkéket az Azure-tűzfal
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/1/2018
ms.author: victorh
ms.openlocfilehash: 897ea3856516b5429ffb770164f863d71e7ae0dd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419010"
---
# <a name="fqdn-tags-overview"></a>Teljes tartománynév címkék áttekintése

Egy teljesen minősített tartománynév-címke egy teljesen minősített tartománynevet (FQDN), a jól ismert a Microsoft a szolgáltatásokkal kapcsolatos csoportját jelöli. Egy teljesen minősített tartománynév-címke használhatja az alkalmazás szabályok lehetővé teszik a szükséges kimenő hálózati forgalmat a tűzfalon keresztül.

Például ahhoz, hogy manuálisan a Windows Update hálózati adatforgalom a tűzfalon keresztül, meg kell a Microsoft-dokumentációt kiszolgálónként több alkalmazás szabályokat hozhat létre. FQDN címkéket használ, hozzon létre egy alkalmazás szabályt, adjon meg a **Windows-frissítések** címkét, és most már a hálózati forgalmat a Microsoft Windows Update végpontokat is áthaladhat a tűzfalat.

Nem hozható létre a saját teljesen minősített tartománynév címkét, és nem is, adja meg, amelyek teljes tartománynevek egyes címkék. A Microsoft kezeli az FQDN a teljes tartománynév-címke vonatkozik, és teljes tartománynevek megfelelően a címke frissíti. 

<!--- screenshot of application rule with a FQDN tag.-->

Az alábbi táblázat a jelenlegi teljes tartománynév címkét lehet használni. A Microsoft fenntartja a címkéket, és várhatóan rendszeresen hozzáadandó további címkéket.

|Teljes tartománynév-címke  |Leírás  |
|---------|---------|
|Windows Update     |Engedélyezi a kimenő hozzáférést a Microsoft Update, leírtak szerint [tűzfal konfigurálása a szoftverfrissítésekhez tartozó](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows diagnosztika|Engedélyezi a kimenő hozzáférést az összes [Windows diagnosztika végpontok](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Engedélyezi a kimenő hozzáférést a [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Az App Service Environment (ASE)|ASE platform forgalmat kimenő hozzáférést biztosít. Ez a címke nem fedi le az ASE által létrehozott ügyfél-specifikus tárolási és SQL végpontok. Ezek segítségével engedélyezni kell [Szolgáltatásvégpontok](../virtual-network/tutorial-restrict-network-access-to-resources.md) vagy manuálisan hozzáadni.<br><br>Az ASE Azure tűzfal integrálásával kapcsolatos további információkért lásd: [App Service-környezet sémákra](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Lehetővé teszi a kimenő hozzáférést az Azure Backup szolgáltatás.

> [!NOTE]
> Egy alkalmazás a szabály a teljes tartománynév-címke kiválasztásakor a protokoll: port mezőben állítsa **https**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan helyezhet üzembe egy Azure-tűzfal, lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](tutorial-firewall-deploy-portal.md).