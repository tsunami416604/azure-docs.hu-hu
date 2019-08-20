---
title: Bevezetés az Azure Sentinel előzetes verziójába | Microsoft Docs
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
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: 0c37d6167012af46204cbca29397f5d226b9649b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611896"
---
# <a name="on-board-azure-sentinel-preview"></a>A fedélzeti Azure Sentinel előzetes verziója

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja ki a fedélzeten az Azure Sentinel szolgáltatást. 

Ahhoz, hogy a fedélzeti Azure Sentinel elérhető legyen, először engedélyeznie kell az Azure Sentinelt, majd össze kell kapcsolni az adatforrásokat. Az Azure Sentinel számos összekötőt tartalmaz a Microsoft-megoldásokhoz, és elérhetővé teszi a valós idejű integrációt, beleértve a Microsoft veszélyforrások elleni védelmi megoldásokat, Microsoft 365 forrásait, beleértve az Office 365, az Azure AD, az Azure ATP és a Microsoft Cloud App Security és így tovább. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. A Common Event Format, a syslog vagy a REST-API használatával is összekapcsolhatók az adatforrások az Azure Sentinel szolgáltatással.  

Az adatforrások összekapcsolását követően válassza ki a szakszerűen létrehozott irányítópultok gyűjteményét, amely az adatok alapján felszínes elemzéseket végez. Ezek az irányítópultok könnyen testreszabhatók az igényeinek megfelelően.


## <a name="global-prerequisites"></a>Globális előfeltételek

- Aktív Azure-előfizetés, ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- Log Analytics munkaterület. Megtudhatja, hogyan [hozhat létre log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) munkaterületet

-  Az Azure Sentinel engedélyezéséhez közreműködői engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyben az Azure Sentinel-munkaterület található. 
- Az Azure Sentinel használatához közreműködői vagy olvasói engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyhez a munkaterület tartozik
- Bizonyos adatforrások összekapcsolásához további engedélyekre lehet szükség
 
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
   > - Az Azure Sentinel a következő régiókban üzembe helyezett munkaterületeken is futtatható:  Délkelet-Ausztrália, Közép-India, USA keleti régiója, USA 2. keleti régiója, – EUAP (Kanári), Kelet-Japán, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati régiója.

6. Kattintson az **Azure Sentinel hozzáadása**lehetőségre.
  

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinel a szolgáltatáshoz való csatlakozással és az események és naplók Azure Sentinelbe való továbbításával hozza létre a kapcsolatot a szolgáltatásokkal és az alkalmazásokkal. A gépek és a virtuális gépek esetében telepítheti az Azure Sentinel-ügynököt, amely összegyűjti a naplókat, és továbbítja azokat az Azure Sentinelnek. Tűzfalak és proxyk esetén az Azure Sentinel egy Linux syslog-kiszolgálót használ. Az ügynök telepítve van, és az ügynök összegyűjti a naplófájlokat, és továbbítja azokat az Azure Sentinelnek. 
 
1. Kattintson **az adatgyűjtés**elemre.
2. A csatlakoztatott adatforrások csempéi is megtalálhatók.<br>
Kattintson például a **Azure Active Directory**elemre. Ha ezt az adatforrást kapcsolja össze, az Azure AD-ból származó összes naplót az Azure Sentinelbe továbbíthatja. Kiválaszthatja, hogy milyen típusú naplókat szeretne beolvasni a bejelentkezési naplókba és/vagy a naplókba. <br>
Alul az Azure Sentinel olyan javaslatokat tartalmaz, amelyekkel az egyes összekötők esetében telepíteni kell az irányítópultokat, így azonnal megismerheti az adataival kapcsolatos érdekes ismereteket. <br> További információért kövesse a telepítési utasításokat, vagy [tekintse meg a megfelelő kapcsolódási útmutatót](connect-data-sources.md) . További információ az adatösszekötők használatáról: a [Microsoft szolgáltatásainak](connect-data-sources.md)összekapcsolása.

Az adatforrások csatlakoztatása után az adatai streamet kezdenek az Azure Sentinelbe, és készen állnak a használat megkezdésére. Megtekintheti a naplókat a [beépített irányítópultokon](quickstart-get-visibility.md) , és megkezdheti a lekérdezések létrehozását a log Analyticsban [az](tutorial-investigate-cases.md)adatvizsgálathoz.



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók az adatforrások az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- A [gyakori hibákból](connect-common-event-format.md) származó adatok továbbítása az Azure sentinelbe.
