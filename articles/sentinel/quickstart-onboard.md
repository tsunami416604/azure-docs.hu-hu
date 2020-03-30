---
title: 'Rövid útmutató: Alaplap az Azure Sentinelben'
description: Ismerje meg, hogyan gyűjthet adatokat az Azure Sentinelben a rövid útmutató dokumentum követésével.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 11fecd875385d8ba044cbe44e2270eed11d61ce1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581549"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Rövid útmutató: Fedélzeti Azure Sentinel

Ebben a rövid útmutatóban megtudhatja, hogyan lehet az Azure Sentinelt. 

A beépített Azure Sentinel, először engedélyeznie kell az Azure Sentinel, majd csatlakoztassa az adatforrásokat. Az Azure Sentinel számos, a dobozból elérhető Microsoft-megoldásösszekötővel rendelkezik, és valós idejű integrációt biztosít, beleértve a Microsoft Threat Protection megoldásokat, a Microsoft 365-forrásokat, beleértve az Office 365-öt, az Azure AD-t, az Azure ATP-t és a Microsoft Cloud App Security, és így tovább. Emellett a nem Microsoft-megoldások szélesebb biztonsági ökoszisztémájához beépített összekötők is találhatók. A közös eseményformátum, a Syslog vagy a REST-API segítségével is csatlakoztathatja az adatforrásokat az Azure Sentinelhez.  

Miután csatlakoztatta az adatforrásokat, válasszon a szakszerűen létrehozott munkafüzetek ből, amelyek az adatok alapján felületezik az elemzéseket. Ezek a munkafüzetek könnyen testreszabhatók az Ön igényeinek megfelelően.

>[!IMPORTANT] 
> Az Azure Sentinel használata során felmerülő költségekről az [Azure Sentinel díjszabása](https://azure.microsoft.com/pricing/details/azure-sentinel/)című témakörben talál további információt.
  

## <a name="global-prerequisites"></a>Globális előfeltételek

- Aktív Azure-előfizetés, ha nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Log Analytics munkaterület. További információ a [Log Analytics-munkaterület létrehozásáról.](../log-analytics/log-analytics-quick-create-workspace.md) A Log Analytics-munkaterületekről az [Azure Monitor-naplók üzembe helyezésének tervezése című témakörben](../azure-monitor/platform/design-logs-deployment.md)talál további információt.

- Az Azure Sentinel engedélyezéséhez közreműködői engedélyekre van szüksége ahhoz az előfizetéshez, amelyben az Azure Sentinel-munkaterület található. 
- Az Azure Sentinel használatához közreműködői vagy olvasói engedélyeket kell használnia ahhoz az erőforráscsoporthoz, amelyhez a munkaterület tartozik.
- Adott adatforrások összekapcsolásához további engedélyekre lehet szükség.
- Az Azure Sentinel egy fizetős szolgáltatás. A díjszabási információkért lásd: [Az Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Az Azure Sentinel engedélyezése<a name="enable"></a>

1. Jelentkezzen be az Azure portálra. Győződjön meg arról, hogy az előfizetés, amelyben az Azure Sentinel jön létre van kiválasztva.

1. Keresse meg és válassza az **Azure Sentinel**lehetőséget.

   ![keresés](./media/quickstart-onboard/search-product.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Jelölje ki a használni kívánt munkaterületet, vagy hozzon létre egy újat. Az Azure Sentinel több munkaterületen is futtatható, de az adatok egyetlen munkaterületre vannak elkülönítve.

   ![keresés](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Az Azure Security Center által létrehozott alapértelmezett munkaterületek nem jelennek meg a listában; nem telepítheti rájuk az Azure Sentinelt.
   > - Az Azure Sentinel a Log Analytics bármely [GA régiójában](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) futtatható munkaterületeken, kivéve A Kína, Németország és az Azure Government régióit. Az Azure Sentinel által létrehozott adatokat (például incidenseket, könyvjelzőket és riasztási szabályokat, amelyek tartalmazhatnak néhány, ezekből a munkaterületekből származó ügyféladatokat) a rendszer Nyugat-Európában (az Európai Európában található munkaterületeken) vagy az USA keleti részén (az USA összes amerikai munkaterületén, valamint az összes USA-beli munkaterületen, valamint bármely más régióban, kivéve Európát).

1. Válassza **az Azure Sentinel hozzáadása**lehetőséget.
  

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinel létrehozza a kapcsolatot a szolgáltatásokkal és az alkalmazásokkal a szolgáltatáshoz való csatlakozással, valamint az események és naplók továbbításával az Azure Sentinelnek. Gépek és virtuális gépek esetén telepítheti az Azure Sentinel-ügynököt, amely összegyűjti a naplókat, és továbbítja azokat az Azure Sentinelnek. Tűzfalak és proxyk esetén az Azure Sentinel Linux Syslog kiszolgálót használ. Az ügynök telepítve van, és amelyből az ügynök összegyűjti a naplófájlokat, és továbbítja azokat az Azure Sentinel. 
 
1. Kattintson **az Adatgyűjtés gombra.**
2. Minden adatforráshoz van egy csempe, amelyhez csatlakozhat.<br>
Kattintson például az **Azure Active Directory**elemre. Ha csatlakoztatja ezt az adatforrást, az Azure AD-ből az Azure Sentinelbe továbbítja az összes naplót. Kiválaszthatja, hogy milyen típusú naplókat szeretne beszerezni - bejelentkezési naplók és/vagy naplónaplók. <br>
Az Azure Sentinel az alján javaslatokat tesz, amelyekhez az egyes összekötőkhöz telepítenie kell a munkafüzeteket, így azonnal érdekes elemzéseket kaphat az adatok között. <br> További információt a telepítési útmutatóban vagy [a megfelelő csatlakozási útmutatóban talál.](connect-data-sources.md) Az adatösszekötőkről a [Microsoft-szolgáltatások csatlakoztatása című](connect-data-sources.md)témakörben talál további információt.

Az adatforrások csatlakoztatása után az adatok streamelésbe kerülnek az Azure Sentinelbe, és készen áll a munkára. Megtekintheti a naplókat a [beépített irányítópultokon,](quickstart-get-visibility.md) és elkezdheti a lekérdezések létrehozását a Log Analytics szolgáltatásban [az adatok vizsgálatához.](tutorial-investigate-cases.md)



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban az adatforrások Azure Sentinelhez való csatlakoztatásáról szerzett tudomást. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Adatokat továbbítson [a Common Event Format készülékekről](connect-common-event-format.md) az Azure Sentinelbe.
