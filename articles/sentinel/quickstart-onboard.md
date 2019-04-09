---
title: Azure Sentinel előzetes verzióban érhető el előkészítése |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti össze az Azure Sentinel-adatokat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5c6dfa359a85b5330e70a2618d59ffab15cf24f4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276643"
---
# <a name="on-board-azure-sentinel-preview"></a>A helyi Azure Sentinel előzetes verzió

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt ismerteti, hogyan lehet a helyi Azure Sentinel. 

A fedélzeti Azure Sentinel először, engedélyezze az Azure-Sentinel, és hogyan csatlakozhat az adatforrást. Az Azure-Sentinel együttműködik a Microsoft-megoldások, a box és a valós idejű integráció, többek között a Microsoft veszélyforrások elleni védelmi megoldások, Microsoft 365 forrásból, például az Office 365, Azure AD-ben az Azure ATP, azonnal összekötőket és A Microsoft Cloud App Security, és többet. Emellett számos beépített összekötőkről, a szélesebb körű biztonsági ökoszisztéma nem Microsoft-megoldások. Az adatforrások csatlakoztatása az Azure-Sentinel a Syslog vagy a REST-API formátum. esemény közös is használhatja.  

Miután csatlakozott az adatforrásokat, egy katalógus, amely az adatok alapján elemzéseket surface expertly létrehozott irányítópultok közül választhat. Ezek az irányítópultok az igényeinek megfelelően egyszerűen testre szabható.


## <a name="global-prerequisites"></a>Általános Előfeltételek

- Aktív Azure-előfizetést, ha nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

- Log Analytics-munkaterületet. Ismerje meg, hogyan [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md)

-  Engedélyezi az Azure-Sentinel, az előfizetés, amelyben az Azure Sentinel-munkaterület található közreműködői engedélyre van szükségük. 
- Azure Sentinel használatához a munkaterületet tartalmazó erőforráscsoporthoz közreműködő vagy megjelenítő engedélyek szükségesek
- További engedélyeket is szükséges, az adott adatforrások csatlakoztatása
 
## Az Azure Sentinel engedélyezése <a name="enable"></a>

1. Lépjen be az Azure Portalra.
2. Győződjön meg arról, hogy az előfizetés, amelyben az Azure-Sentinel létrehozta, van-e kiválasztva. 
3. Keresse meg az Azure Sentinel. 
   ![keresés](./media/quickstart-onboard/search-product.png)

1. Kattintson a **+ Hozzáadás**.
1. Válassza ki szeretné használni, vagy hozzon létre egy új munkaterületet. Egynél több munkaterület Azure Sentinel-futtatható, de az adatok el különítve egy munkaterülethez.

   ![keresés](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Munkaterület helye** fontos megérteni, hogy a kiválasztott munkaterület a földrajzi helyen tárolt, az Azure Sentinel-adatfolyam minden adat.  
   > - Azure Security Center által létrehozott alapértelmezett munkaterület nem jelenik a lista; nem telepíthető az Azure-Sentinel őket.
   > - Az Azure-Sentinel futtassa a következő régiók valamelyikében üzembe helyezett munkaterületek:  Délkelet-Ausztrália, közép-Kanada, közép-India, USA keleti RÉGIÓJA, USA 2. keleti régiója – EUAP (Kanári), kelet-japán, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati.

6. Kattintson a **hozzáadása Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure-Sentinel csatlakozik a szolgáltatáshoz, és az események és naplók az Azure-Sentinel hoz létre a kapcsolat-szolgáltatások és alkalmazások. A gépek és virtuális gépek az Azure Sentinel-ügynök, amely összegyűjti a naplókat, majd továbbítja azokat az Azure-Sentinel is telepítheti. Tűzfalak és proxyk az Azure-Sentinel használja egy Linux Syslog-kiszolgáló. Az ügynök telepítve van, és a fájlok a napló az ügynök összegyűjti és továbbítja azokat az Azure-Sentinel. 
 
1. Kattintson a **adatgyűjtés**.
2. Nincs minden adatforráshoz csatlakozhat egy csempét.<br>
Kattintson például **Azure Active Directory**. Ha ehhez az adatforráshoz kapcsolódni, akkor az Azure ad-ből a naplók streamelése az Azure Sentinel. Kiválaszthatja, hogy milyen típusú, a get - wan naplók bejelentkezési naplók és/vagy naplók. <br>
A lap alján Azure Sentinel-kapcsolatos javaslatokat tartalmaz az irányítópultokat kell telepítenie minden egyes összekötőt, akkor is azonnal első érdekes elemzéseket az adatok. <br> Kövesse a telepítési utasításokat vagy [tekintse meg a megfelelő kapcsolatot útmutatóját](connect-data-sources.md) további információt. Az adatösszekötők kapcsolatos információkért lásd: [csatlakoztatása a Microsoft services](connect-data-sources.md).

Után az adatok források csatlakoznak az adatok az Azure Sentinel-streamelés elindul, és készen áll, hogy elkezdheti a. A naplók megtekintéséhez a [beépített irányítópultok](quickstart-get-visibility.md) elkezdhet lekérdezéseket a Log Analyticsben [vizsgálja meg az adatok](tutorial-investigate-cases.md).



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerhette Azure Sentinel-adatforrásokhoz csatlakozik. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
- Stream adatainak [gyakori hiba formátum berendezések](connect-common-event-format.md) Azure Sentinel-be.
