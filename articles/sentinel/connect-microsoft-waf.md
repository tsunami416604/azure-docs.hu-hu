---
title: Webalkalmazás-tűzfal adatainak csatlakoztatása az Azure Sentinelhez
description: Ismerje meg, hogyan kapcsolhatja össze a Microsoft webalkalmazás-tűzfaladatait az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588178"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Adatok csatlakoztatása a Microsoft webalkalmazás-tűzfalból



Naplók streamelése az Azure Application Gateway Microsoft webalkalmazás-tűzfaláról (WAF). Ez a WAF megvédi alkalmazásait a gyakori webes biztonsági résektől, például az SQL injektálástól és a webhelyek közötti parancsfájlok futtatásától, és lehetővé teszi a szabályok testreszabását a hamis pozitív értékek csökkentése érdekében. Kövesse ezeket az utasításokat a Microsoft webalkalmazás tűzfalának az Azure Sentinelbe való streameléséhez.


## <a name="prerequisites"></a>Előfeltételek

- Meglévő alkalmazásátjáró-erőforrás

## <a name="connect-to-microsoft-web-application-firewall"></a>Csatlakozás a Microsoft webalkalmazás-tűzfalához

Ha már rendelkezik a Microsoft webalkalmazás-tűzfallal, győződjön meg arról, hogy rendelkezik egy meglévő átjáró-erőforrással.
Miután a Microsoft webalkalmazás tűzfal a telepített és az adatok beszerzése, a riasztási adatok könnyen streamelhető az Azure Sentinel.
    
1. Az Azure Sentinel portálon válassza az **Adatösszekötők**lehetőséget.
1. Az Adatösszekötők lapon jelölje ki a **WAF** csempét.
1. Nyissa meg az [Application Gateway erőforrást,](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) és válassza ki a WAF-ot.
    1. Válassza **a Diagnosztikai beállítások lehetőséget**.
    1. Válassza **a + Diagnosztikai beállítás hozzáadása** lehetőséget a táblázat alatt.
    1. A **Diagnosztikai beállítások** lapon írjon be egy **nevet,** és válassza **a Küldés a Log Analytics szolgáltatásba**lehetőséget.
    1. A **Log Analytics-munkaterület csoportban** válassza ki az Azure Sentinel-munkaterületet.
    1. Jelölje ki az elemezni kívánt naplótípusokat. A következőt ajánlottuk: ApplicationGatewayAccessLog és ApplicationGatewayFirewallLog.
1. Ha a Microsoft webalkalmazás tűzfalriasztásaihoz a Megfelelő sémát szeretné használni a Log Analytics szolgáltatásban, keresse meg az **AzureDiagnostics**című területet.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Microsoft webalkalmazás-tűzfalat az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
