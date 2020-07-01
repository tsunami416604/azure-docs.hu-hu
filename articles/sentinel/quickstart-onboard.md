---
title: 'Gyors útmutató: Bevezetés az Azure Sentinelbe'
description: Ebből a rövid útmutatóból megtudhatja, hogyan teheti elérhetővé az Azure Sentinelt először a Sentinel engedélyezésével, majd az adatforrások összekapcsolásával.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d895ff3a16f8e21f73845c03d8bec83ded9bcd43
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85564540"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Gyors útmutató: Azure Sentinel

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja ki a fedélzeten az Azure Sentinel szolgáltatást. 

Ahhoz, hogy a fedélzeti Azure Sentinel elérhető legyen, először engedélyeznie kell az Azure Sentinelt, majd össze kell kapcsolni az adatforrásokat. Az Azure Sentinel számos összekötőt kínál a Microsoft-megoldások számára, és lehetővé teszi a valós idejű integrációt, beleértve a Microsoft Threat Protection-megoldásokat, Microsoft 365 forrásait (beleértve az Office 365-et), az Azure AD-t, az Azure ATP-t, a Microsoft Cloud App Security és egyebeket. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. A Common Event Format (CEF), a syslog vagy a REST-API használatával is összekapcsolhatók az adatforrások az Azure Sentinel szolgáltatással.  

Az adatforrások összekapcsolását követően válasszon egy, az adatok alapján felszínre felkészített munkafüzetekből álló gyűjteményt. Ezek a munkafüzetek könnyen testreszabhatók az igényei szerint.

>[!IMPORTANT] 
> Az Azure Sentinel használata során felmerülő költségekkel kapcsolatos információkért lásd: az [Azure Sentinel díjszabása](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globális előfeltételek

- Aktív Azure-előfizetés, ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- Log Analytics munkaterület. Megtudhatja, hogyan [hozhat létre log Analytics munkaterületet](../log-analytics/log-analytics-quick-create-workspace.md). További információ az Log Analytics munkaterületekről: [a Azure monitor naplók telepítésének megtervezése](../azure-monitor/platform/design-logs-deployment.md).

- Az Azure Sentinel engedélyezéséhez közreműködői engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyben az Azure Sentinel-munkaterület található. 
- Az Azure Sentinel használatához közreműködői vagy olvasói engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyhez a munkaterület tartozik.
- Bizonyos adatforrások összekapcsolásához további engedélyekre lehet szükség.
- Az Azure Sentinel fizetős szolgáltatás. A díjszabással kapcsolatos információkért lásd: [Az Azure Sentinel ismertetése](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Az Azure Sentinel engedélyezése<a name="enable"></a>

1. Jelentkezzen be az Azure portálra. Győződjön meg arról, hogy az Azure Sentinel-t létrehozó előfizetés van kiválasztva.

1. Keresse meg és válassza ki az **Azure Sentinel**elemet.

   ![keresés](./media/quickstart-onboard/search-product.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Válassza ki a használni kívánt munkaterületet, vagy hozzon létre egy újat. Az Azure Sentinel több munkaterületen is futtatható, de az adategység egyetlen munkaterületre van elkülönítve.

   ![keresés](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - A Azure Security Center által létrehozott alapértelmezett munkaterületek nem jelennek meg a listában; Az Azure Sentinel nem telepíthető rajtuk.
   > - Az Azure Sentinel [log Analytics bármely GA régiójában](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) futtatható munkaterületeken, kivéve a kínai és a németországi (szuverén) régiókat. Az Azure Sentinel által létrehozott adatok (például az incidensek, a könyvjelzők és a riasztási szabályok, amelyek tartalmazhatnak néhány ügyfél-adatforrást az adott munkaterületről) Európa (az Európai munkaterületek számára), Ausztráliában (az Ausztráliában található munkaterületek esetében) vagy az USA keleti régiójában (a más régiókban található munkaterületek esetében) mentik.

   >[!IMPORTANT]
   > - Miután az Azure Sentinel engedélyezve lett egy munkaterületen, a munkaterület áthelyezése más erőforráscsoportok vagy előfizetések között nem támogatott. Ha át kell helyeznie a munkaterületet, forduljon az [Azure sentinelhez](mailto:AzureSentinel@microsoft.com).

1. Válassza az **Azure Sentinel hozzáadása**lehetőséget.
  

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinel a szolgáltatáshoz való csatlakozással és az események és naplók Azure Sentinelbe való továbbításával hozza létre a kapcsolatot a szolgáltatásokkal és az alkalmazásokkal. A gépek és a virtuális gépek esetében telepítheti az Azure Sentinel-ügynököt, amely összegyűjti a naplókat, és továbbítja azokat az Azure Sentinelnek. Tűzfalak és proxyk esetén az Azure Sentinel egy Linux syslog-kiszolgálót használ. Az ügynök telepítve van, és az ügynök összegyűjti a naplófájlokat, és továbbítja azokat az Azure Sentinelnek. 
 
1. Kattintson **az adatösszekötők**elemre.
1. A csatlakoztatott adatforrások csempéi is megtalálhatók.<br>
Kattintson például a **Azure Active Directory**elemre. Ha ezt az adatforrást kapcsolja össze, az Azure AD-ból származó összes naplót az Azure Sentinelbe továbbíthatja. Kiválaszthatja, hogy milyen típusú naplókat szeretne beolvasni a bejelentkezési naplókba és/vagy a naplókba. <br>
Alul az Azure Sentinel olyan javaslatokat tartalmaz, amelyekkel az egyes összekötők esetében telepíteni kell a munkafüzeteket, így azonnal megismerheti az összes adatelemzést. <br> További információért kövesse a telepítési utasításokat, vagy [tekintse meg a megfelelő kapcsolódási útmutatót](connect-data-sources.md) . További információ az adatösszekötők használatáról: a [Microsoft szolgáltatásainak összekapcsolása](connect-data-sources.md).

Az adatforrások csatlakoztatása után az adatai streamet kezdenek az Azure Sentinelbe, és készen állnak a használat megkezdésére. Megtekintheti a naplókat a [beépített irányítópultokon](quickstart-get-visibility.md) , és megkezdheti a lekérdezések létrehozását a log Analyticsban [az adatvizsgálathoz](tutorial-investigate-cases.md).



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók az adatforrások az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Adatok továbbítása a [Common Event Format készülékekről](connect-common-event-format.md) az Azure sentinelbe.
