---
title: Bevezetés az Azure Sentinelbe | Microsoft Docs
description: Ismerje meg, hogyan gyűjthet adatokat az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 7f042cfe10bd8ca57d9a2dae511a13a82f053a67
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316817"
---
# <a name="on-board-azure-sentinel"></a>A fedélzeti Azure Sentinel



Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja ki a fedélzeten az Azure Sentinel szolgáltatást. 

Ahhoz, hogy a fedélzeti Azure Sentinel elérhető legyen, először engedélyeznie kell az Azure Sentinelt, majd össze kell kapcsolni az adatforrásokat. Az Azure Sentinel számos összekötőt tartalmaz a Microsoft-megoldásokhoz, és elérhetővé teszi a valós idejű integrációt, beleértve a Microsoft veszélyforrások elleni védelmi megoldásokat, Microsoft 365 forrásait, beleértve az Office 365, az Azure AD, az Azure ATP és a Microsoft Cloud App Security és így tovább. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. A Common Event Format, a syslog vagy a REST-API használatával is összekapcsolhatók az adatforrások az Azure Sentinel szolgáltatással.  

Az adatforrások összekapcsolását követően válasszon egy, az adatok alapján felszínre felkészített munkafüzetekből álló gyűjteményt. Ezek a munkafüzetek könnyen testreszabhatók az igényei szerint.


## <a name="global-prerequisites"></a>Globális előfeltételek

- Aktív Azure-előfizetés, ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- Log Analytics munkaterület. Megtudhatja, hogyan [hozhat létre log Analytics munkaterületet](../log-analytics/log-analytics-quick-create-workspace.md). További információ az Log Analytics munkaterületekről: [a Azure monitor naplók telepítésének megtervezése](../azure-monitor/platform/design-logs-deployment.md).

-  Az Azure Sentinel engedélyezéséhez közreműködői engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyben az Azure Sentinel-munkaterület található. 
- Az Azure Sentinel használatához közreműködői vagy olvasói engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyhez a munkaterület tartozik.
- Bizonyos adatforrások összekapcsolásához további engedélyekre lehet szükség.
- Az Azure Sentinel fizetős szolgáltatás. A díjszabással kapcsolatos információkért lásd: [Az Azure Sentinel ismertetése](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## Az Azure Sentinel engedélyezése<a name="enable"></a>

1. Lépjen be a Azure Portalba.
2. Győződjön meg arról, hogy az az előfizetés, amelyben az Azure Sentinel létrejött, be van jelölve. 
3. Keressen rá az Azure Sentinel kifejezésre. 
   ![Keresés](./media/quickstart-onboard/search-product.png)

1. Kattintson a **+ Hozzáadás**gombra.
1. Válassza ki a használni kívánt munkaterületet, vagy hozzon létre egy újat. Az Azure Sentinel több munkaterületen is futtatható, de az adategység egyetlen munkaterületre van elkülönítve.

   ![keresés](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - A Azure Security Center által létrehozott alapértelmezett munkaterületek nem jelennek meg a listában; Az Azure Sentinel nem telepíthető rajtuk.
   > - Az Azure Sentinel [log Analytics bármely GA régiójában](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) futtatható munkaterületeken, kivéve a kínai, a németországi és a Azure Government régiókat. Az Azure Sentinel által létrehozott adatok (például az incidensek, a könyvjelzők és a riasztási szabályok, amelyek tartalmazhatnak néhány ügyfél-adatforrást ezekből a munkaterületekről) a Nyugat-Európában (az Európában található munkaterületek esetében) vagy az USA keleti régiójában (az összes USA-beli munkaterülethez, valamint minden más régió, kivéve Európa).

6. Kattintson az **Azure Sentinel hozzáadása**lehetőségre.
  

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinel a szolgáltatáshoz való csatlakozással és az események és naplók Azure Sentinelbe való továbbításával hozza létre a kapcsolatot a szolgáltatásokkal és az alkalmazásokkal. A gépek és a virtuális gépek esetében telepítheti az Azure Sentinel-ügynököt, amely összegyűjti a naplókat, és továbbítja azokat az Azure Sentinelnek. Tűzfalak és proxyk esetén az Azure Sentinel egy Linux syslog-kiszolgálót használ. Az ügynök telepítve van, és az ügynök összegyűjti a naplófájlokat, és továbbítja azokat az Azure Sentinelnek. 
 
1. Kattintson **az adatgyűjtés**elemre.
2. A csatlakoztatott adatforrások csempéi is megtalálhatók.<br>
Kattintson például a **Azure Active Directory**elemre. Ha ezt az adatforrást kapcsolja össze, az Azure AD-ból származó összes naplót az Azure Sentinelbe továbbíthatja. Kiválaszthatja, hogy milyen típusú naplókat szeretne beolvasni a bejelentkezési naplókba és/vagy a naplókba. <br>
Alul az Azure Sentinel olyan javaslatokat tartalmaz, amelyekkel az egyes összekötők esetében telepíteni kell a munkafüzeteket, így azonnal megismerheti az összes adatelemzést. <br> További információért kövesse a telepítési utasításokat, vagy [tekintse meg a megfelelő kapcsolódási útmutatót](connect-data-sources.md) . További információ az adatösszekötők használatáról: a [Microsoft szolgáltatásainak összekapcsolása](connect-data-sources.md).

Az adatforrások csatlakoztatása után az adatai streamet kezdenek az Azure Sentinelbe, és készen állnak a használat megkezdésére. Megtekintheti a naplókat a [beépített irányítópultokon](quickstart-get-visibility.md) , és megkezdheti a lekérdezések létrehozását a log Analyticsban [az adatvizsgálathoz](tutorial-investigate-cases.md).



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók az adatforrások az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- A [gyakori hibákból](connect-common-event-format.md) származó adatok továbbítása az Azure sentinelbe.
