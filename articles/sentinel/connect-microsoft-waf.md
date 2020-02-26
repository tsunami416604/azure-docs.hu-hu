---
title: Webalkalmazási tűzfalak összekapcsolása az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan csatlakoztathatók a Microsoft webalkalmazási tűzfalak az Azure Sentinel szolgáltatáshoz.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588178"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Adatok összekapcsolása a Microsoft webalkalmazási tűzfallal



Az Azure Application Gateway Microsoft webalkalmazási tűzfala (WAF) segítségével továbbíthatja a naplókat. Ez a WAF védelmet nyújt az alkalmazások számára a gyakori webes biztonsági rések, például az SQL-injektálás és a helyek közötti parancsfájlok ellen, és lehetővé teszi a szabályok testreszabását a téves pozitív adatok csökkentése érdekében. Kövesse ezeket az utasításokat a Microsoft webalkalmazási tűzfal naplóinak az Azure Sentinel szolgáltatásba való továbbításához.


## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő Application Gateway-erőforrás

## <a name="connect-to-microsoft-web-application-firewall"></a>Kapcsolódás a Microsoft webalkalmazási tűzfallal

Ha már rendelkezik Microsoft webalkalmazási tűzfallal, ellenőrizze, hogy rendelkezik-e meglévő átjáró-erőforrással.
Miután telepítette és beolvasta a Microsoft webalkalmazási tűzfalát, a riasztási adatai könnyen továbbíthatók az Azure Sentinel szolgáltatásba.
    
1. Az Azure Sentinel portálon válassza az **adatösszekötők**lehetőséget.
1. Az adatösszekötők lapon válassza a **WAF** csempét.
1. Nyissa meg [Application Gateway erőforrás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) - , és válassza ki a WAF.
    1. Válassza a **diagnosztikai beállítások**lehetőséget.
    1. Válassza a **+ diagnosztikai beállítások hozzáadása** lehetőséget a tábla alatt.
    1. A **diagnosztikai beállítások** lapon adja meg a **nevet** , és válassza a **Küldés log Analytics**lehetőséget.
    1. A **log Analytics munkaterület** területen válassza ki az Azure Sentinel munkaterületet.
    1. Válassza ki az elemezni kívánt naplózási típusokat. Ajánlott: ApplicationGatewayAccessLog és ApplicationGatewayFirewallLog.
1. Ha a Microsoft webalkalmazási tűzfal riasztásai esetében szeretné használni a Log Analytics vonatkozó sémát, keresse meg a **AzureDiagnostics**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Microsoft webalkalmazási tűzfal az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
