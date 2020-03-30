---
title: FQDN-címkék áttekintése az Azure Tűzfalhoz
description: Az FQDN-címke a jól ismert Microsoft-szolgáltatásokhoz társított, teljesen minősített tartománynevek (FQDN-ek) csoportját jelöli.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169041"
---
# <a name="fqdn-tags-overview"></a>Teljes tartománynév-címkék – áttekintés

Az FQDN-címke a jól ismert Microsoft-szolgáltatásokhoz társított, teljesen minősített tartománynevek (FQDN-ek) csoportját jelöli. Az alkalmazásszabályokban egy Teljes tartománynév-címkét használhat a szükséges kimenő hálózati forgalom engedélyezéséhez a tűzfalon keresztül.

Ha például manuálisan szeretné engedélyezni a Windows Update hálózati forgalmát a tűzfalon keresztül, a Microsoft dokumentációjában több alkalmazásszabályt kell létrehoznia. Az FQDN-címkék használatával létrehozhat egy alkalmazásszabályt, felveheti a **Windows Frissítések** címkét, és most a Microsoft Windows Update végpontjainak hálózati forgalma átfolyhat a tűzfalon.

Nem hozhat létre saját Teljes tartománynév-címkéket, és azt sem adhatja meg, hogy mely teljes tartománynokat tartalmazza a címke. A Microsoft kezeli a teljes tartománynév-címkét magában foglaló teljes tartománynokat, és frissíti a címkét a teljes tartománynnaváltozáskor. 

<!--- screenshot of application rule with a FQDN tag.-->

Az alábbi táblázat a használható aktuális Teljes tartománynév-címkéket mutatja be. A Microsoft fenntartja ezeket a címkéket, és további címkék rendszeres hozzáadására számíthat.

## <a name="current-fqdn-tags"></a>Aktuális Teljes tartománynév-címkék

|Teljes tartománynév-címke  |Leírás  |
|---------|---------|
|Windows Update     |Kimenő hozzáférés engedélyezése a Microsoft Update szolgáltatáshoz a [Tűzfal konfigurálása szoftverfrissítésekhez](https://technet.microsoft.com/library/bb693717.aspx)című szakaszban leírtak szerint.|
|Windows diagnosztika|Kimenő hozzáférés engedélyezése az összes [Windows diagnosztikai végponthoz.](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)|
|Microsoft Active Protection Service (MAPS)|Kimenő hozzáférés engedélyezése a [MAPS-hoz.](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)|
|App Service-környezet (ASE)|Lehetővé teszi a kimenő hozzáférést az ASE platformforgalomhoz. Ez a címke nem terjed ki az ASE által létrehozott ügyfélspecifikus storage és SQL-végpontokra. Ezeket engedélyezni kell [a szolgáltatásvégpontokon keresztül,](../virtual-network/tutorial-restrict-network-access-to-resources.md) vagy manuálisan kell hozzáadni.<br><br>Az Azure Firewall ASE-vel való integrálásáról az [App Service-környezet zárolása](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)című témakörben talál további információt.|
|Azure Backup|Lehetővé teszi a kimenő hozzáférést az Azure Backup szolgáltatásokhoz.|
|Azure HDInsight|Kimenő hozzáférést biztosít a HDInsight platformforgalmához. Ez a címke nem terjed ki az ügyfél-specifikus storage vagy SQL-forgalmat hdinsight. Engedélyezze ezeket [a szolgáltatásvégpontok használatával,](../virtual-network/tutorial-restrict-network-access-to-resources.md) vagy adja hozzá manuálisan.|

> [!NOTE]
> Amikor egy alkalmazásszabályban a Teljes tartománynévcímkét választja, a protocol:port mezőt **https-re**kell állítani.

## <a name="next-steps"></a>További lépések

Az Azure-tűzfal üzembe helyezéséről az [Oktatóanyag: Az Azure-tűzfal telepítése és konfigurálása az Azure Portalon című témakörben](tutorial-firewall-deploy-portal.md)olvashat.
