---
title: Webalkalmazás tűzfal adatainak a Microsoft Azure-on Előzetesben Sentinel-csatlakozás |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a webalkalmazás tűzfal adatainak a Microsoft Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 99c553145f0a600826e48bec437449f278b74fbf
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205643"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Adatok csatlakoztatása a Microsoft webalkalmazási tűzfal

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Application Gateway a Microsoft webalkalmazási tűzfal (WAF) naplóinak streamelheti. A WAF úgy védi az alkalmazásokat a gyakori internetes biztonsági rések, például az SQL-injektálás vagy többhelyes scripting, és lehetővé teszi, hogy Ön testre szabhatja a szabályokat a vakriasztások számának csökkentése érdekében. Kövesse ezeket az utasításokat a Microsoft webalkalmazási tűzfal naplói streamelése az Azure-Sentinel.


## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő application gateway-erőforrás

## <a name="connect-to-microsoft-web-application-firewall"></a>Csatlakozás a Microsoft webalkalmazási tűzfal

Ha már rendelkezik a Microsoft webalkalmazási tűzfal, ellenőrizze, hogy egy meglévő átjáró-erőforrást.
A Microsoft webalkalmazási tűzfal van telepítve, és a fogad adatokat, a riasztási adatok könnyen továbbítható Azure Sentinel-be.
    
1. Az Azure-Sentinel-portálon válassza **adatösszekötők**.
1. Az összekötők lapon, válassza ki a **WAF** csempére.
1. Lépjen a [Application Gateway-erőforrásának](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) , és válassza a WAF.
    1. Válassza ki **diagnosztikai beállítások**.
    1. Válassza ki **+ diagnosztikai beállítás hozzáadása** a táblázat alatt.
    1. Az a **diagnosztikai beállítások** írja be egy **neve** válassza **Küldés a Log Analyticsnek**.
    1. A **Log Analytics-munkaterület** válassza ki az Azure Sentinel-munkaterületet.
    1. Válassza ki az elemezni kívánt napló típusait. Azt javasoljuk: ApplicationGatewayAccessLog és ApplicationGatewayFirewallLog.
1. A megfelelő sémát használ a Log Analytics a Microsoft web application tűzfal riasztásokat, keresse meg **AzureDiagnostics**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Microsoft webalkalmazási tűzfal Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
