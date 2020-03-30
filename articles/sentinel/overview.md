---
title: Mi az Azure Sentinel?| Microsoft dokumentumok
description: Ismerje meg az Azure Sentinelt, annak legfontosabb funkcióit és működését.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581718"
---
# <a name="what-is-azure-sentinel"></a>Mi az Azure Sentinel?

A Microsoft Azure Sentinel egy méretezhető, felhőalapú natív, **biztonsági információk eseménykezelési (SIEM)** és **biztonsági vezénylési automatizált válasz (SZÁRNYLony)** megoldás. Az Azure Sentinel intelligens biztonsági elemzéseket és fenyegetésfelderítést biztosít a vállalaton belül, egyetlen megoldást kínálva a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésre adott válaszlépésekre. 

Az Azure Sentinel a legszembetűnőbb nézet a vállalaton belül, enyhítve az egyre kifinomultabb támadások, a riasztások növekvő mennyiségét és a hosszú megoldási időkeretek stresszét.

- **Adatokat gyűjthet felhőméretekben** az összes felhasználó, eszköz, alkalmazás és infrastruktúra között, mind a helyszínen, mind több felhőben. 

- A Microsoft elemzése és a páratlan fenyegetésekkel kapcsolatos intelligencia segítségével **észlelheti a korábban fel nem derített fenyegetéseket,** és minimálisra csökkentheti a hamis pozitív állításokat. 

- **Vizsgálja meg a mesterséges intelligenciával kapcsolatos fenyegetéseket**, és a Microsoft nál végzett több éves kiberbiztonsági munkát kihasználva nagy mennyiségű gyanús tevékenységre vadászik. 

- **Reagáljon az incidensekre gyorsan** a beépített vezényléssel és a gyakori feladatok automatizálásával.

![Az Azure Sentinel alapvető képességei](./media/overview/core-capabilities.png)

A meglévő Azure-szolgáltatások teljes körére építve az Azure Sentinel natív módon magában foglalja a bevált alapokat, például a Log Analytics-et és a Logic Apps-alkalmazásokat. Az Azure Sentinel gazdagítja a vizsgálatot és az észlelést a mesterséges intelligenciával, és biztosítja a Microsoft fenyegetésfelderítési adatfolyamát, és lehetővé teszi, hogy saját fenyegetésfelderítési intelligenciát hozzon létre. 

## <a name="connect-to-all-your-data"></a>Csatlakozás az összes adathoz

A beépített Azure Sentinelhez először csatlakoznia kell [a biztonsági forrásokhoz.](connect-data-sources.md) Az Azure Sentinel számos, a microsoftos megoldásokhoz elérhető összekötőt kínál, amelyek azonnal elérhetők, és valós idejű integrációt biztosítanak, beleértve a Microsoft Threat Protection megoldásokat és a Microsoft 365-forrásokat, beleértve az Office 365-öt, az Azure AD-t, az Azure ATP-t és a Microsoft Cloud App Security, és így tovább. Emellett a nem Microsoft-megoldások szélesebb biztonsági ökoszisztémájához beépített összekötők is találhatók. A közös eseményformátum, a Syslog vagy a REST-API segítségével is csatlakoztathatja az adatforrásokat az Azure Sentinelhez.  

![Adatgyűjtők](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Munkafüzetek

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinelhez, figyelheti az adatokat az Azure Sentinel-integráció és az Azure Monitor-munkafüzetek használatával, amely sokoldalúságot biztosít az egyéni munkafüzetek létrehozásában. Bár a munkafüzetek eltérően jelennek meg az Azure Sentinelben, hasznos lehet, ha megszeretné tekinteni, hogyan [hozhat létre interaktív jelentéseket az Azure Monitor munkafüzetekkel.](../azure-monitor/app/usage-workbooks.md) Az Azure Sentinel lehetővé teszi, hogy egyéni munkafüzeteket hozzon létre az adatok között, és beépített munkafüzetsablonokat is tartalmaz, amelyek lehetővé teszik, hogy gyorsan betekintést nyerjen az adatokba, amint egy adatforrást csatlakoztat.

![Irányítópultok](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Elemzés

A zaj csökkentése és a felülvizsgálatot és a vizsgálatot figyelembe vethetés érdekében az Azure Sentinel [elemzésekkel korrelálja a riasztásokat az incidensekbe.](tutorial-detect-threats-built-in.md) **Az incidensek** kapcsolódó riasztások csoportjai, amelyek együttesen egy végrehajtható lehetséges fenyegetést hoznak létre, amelyet kivizsgálhat és feloldhat. Használja a beépített korrelációs szabályokat, ahogy van, vagy használja őket kiindulási pontként a saját létrehozásához. Az Azure Sentinel gépi tanulási szabályokat is biztosít a hálózati viselkedés leképezéséhez, majd az erőforrások közötti anomáliák megkereséséhez. Ezek az elemzések összekapcsolják a pontokat azáltal, hogy a különböző entitásokra vonatkozó alacsony hűségű riasztásokat potenciális anamvaló-biztonsági incidensekké kombinálják.

![Incidensek](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Biztonsági automatizálás & vezénylés

Automatizálhatja gyakori feladatait, és [egyszerűsítheti a biztonsági vezénylést az](tutorial-respond-threats-playbook.md) Azure-szolgáltatásokkal és a meglévő eszközökkel integrálható forgatókönyvekkel. Az Azure Logic Apps alapokra épülő Azure Sentinel automatizálási és vezénylési megoldása rendkívül bővíthető architektúrát biztosít, amely lehetővé teszi a méretezhető automatizálást az új technológiák és fenyegetések megjelenésekor. Ha az Azure Logic Apps alkalmazásokkal szeretne forgatókönyveket készíteni, a beépített forgatókönyvek egyre bővülő galériája közül választhat. Ezek közé tartozik [a 200+ összekötők](https://docs.microsoft.com/azure/connectors/apis-list) szolgáltatások, például az Azure-függvények. Az összekötők lehetővé teszik, hogy bármilyen egyéni logikát alkalmazzon a kódban, a ServiceNow, a Jira, a Zendesk, a HTTP-kérelmek, a Microsoft Teams, a Slack, a Windows Defender ATP és a Cloud App Security.

Ha például a ServiceNow jegykezelő rendszert használja, az Azure Logic Apps használatával automatizálhatja a munkafolyamatokat, és megnyithat egy jegyet a ServiceNow-ban minden alkalommal, amikor egy adott eseményt észlel.

![Forgatókönyvek](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Vizsgálat

Jelenleg előzetes verzióban, az Azure Sentinel [mély vizsgálati](tutorial-investigate-cases.md) eszközök segítségével megismerheti a hatókört, és megtalálni a kiváltó oka, egy potenciális biztonsági fenyegetés. Az interaktív grafikonon kiválaszthat egy entitást, hogy érdekes kérdéseket tegyen fel egy adott entitáshoz, és leásson az adott entitásba és annak kapcsolataiba, hogy eljusson a fenyegetés kiváltó okához. 

![Vizsgálat](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Veszélyforrás-keresés

Használja az Azure Sentinel [hatékony vadászati keresési és lekérdezési eszközeit,](hunting.md)amelyek a MITRE keretrendszeren alapulnak, amelyek lehetővé teszik, hogy proaktív módon keressen biztonsági fenyegetéseket a szervezet adatforrásaiban, mielőtt egy riasztás aktiválódna. Miután felfedezte, hogy melyik vadászati lekérdezés nyújt nagy értékű betekintést a lehetséges támadásokba, egyéni észlelési szabályokat is létrehozhat a lekérdezés alapján, és ezeket az elemzéseket a biztonsági incidensek válaszadóinak riasztásaként láthatja el. Vadászat közben könyvjelzőket hozhat létre érdekes eseményekhez, lehetővé téve, hogy később visszatérjen hozzájuk, megossza őket másokkal, és csoportosítsa őket más, korrelációs eseményekkel, hogy egy kényszerítő eseményt hozzon létre a vizsgálathoz.

![Veszélyforrás-keresés](./media/overview/hunting.png)

## <a name="community"></a>Közösség

Az Azure Sentinel-közösség hatékony erőforrás a fenyegetések észleléséhez és automatizálásához. Microsoft biztonsági elemzőink folyamatosan hoznak létre és adnak hozzá új munkafüzeteket, forgatókönyveket, vadászati lekérdezéseket és egyebeket, és közzéteszik azokat a közösségnek, hogy használhassa őket a környezetében. A mintatartalmakat a magánközösségi [GitHub-tárházból](https://aka.ms/asicommunity) töltheti le egyéni munkafüzetek, vadászati lekérdezések, jegyzetfüzetek és forgatókönyvek azure Sentinel létrehozásához. 

![Közösség](./media/overview/community.png)

## <a name="next-steps"></a>További lépések

- Az Azure Sentinel első lépéseihez előfizetésre van szüksége a Microsoft Azure-ra. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [integrálhatja adatait az Azure Sentinelbe,](quickstart-onboard.md)és [hogyan láthatja az adatokat és a potenciális fenyegetéseket.](quickstart-get-visibility.md)
