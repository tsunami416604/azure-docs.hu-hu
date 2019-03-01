---
title: Mi az Azure-on Előzetesben Sentinel-? |} A Microsoft Docs
description: Ismerje meg az Azure-Sentinel, annak főbb funkcióit és működését.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: dccbfea0fd26b9d9673ff7652ba6fc4ee632cc46
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992797"
---
# <a name="what-is-azure-sentinel-preview"></a>Mi az Azure-on Előzetesben Sentinel?

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Microsoft Azure Sentinel-egy skálázható, felhő-natív, **biztonsági információk eseménykezelés (SIEM)** és **biztonsági vezénylési automatikus válasz (MEGUGRIK)** megoldás. Az Azure Sentinel intelligenciával intelligens biztonsági elemzés és veszélyészlelés céljából a vállalaton belül egyetlen megoldást kínálva a riasztási észlelési, veszélyforrások elleni láthatóságát, proaktív vadászat és reagáláshoz. 

Azure Sentinel esetében a madarak-nézet az egyre kifinomultabb támadásokkal, erős terhelést okoz enyhítése vállalati növelését kötetek riasztások, és mennyi ideig feloldási időkeretének meghatározása.

- **Adatgyűjtés felhőszinten** felhasználók, eszközök, alkalmazások és infrastruktúra, mind a helyszíni és a több felhő. 

- **Korábban fel nem ismert fenyegetések észlelése**, és a hamis pozitív riasztások minimalizálása a Microsoft analytics, és páratlan fenyegetésfelderítés használatával. 

- **A mesterséges intelligenciát fenyegetések vizsgálata**, és a gyanús tevékenységek ipari méretekben, a Microsoft internetes biztonsági munkahelyi évnyi kiaknázásával hunt. 

- **Eseményekre való válaszadást gyorsan** a beépített koordinálási és automatizálási megoldások az általános feladatok.


![Az Azure Sentinel alapképességek](./media/overview/core-capabilities.png)

A meglévő Azure-szolgáltatások teljes körét építve, Azure Sentinel-bevált alapköve, mint például a Log Analytics és a Logic Apps natív módon tartalmazza. Azure Sentinel bővíti a vizsgálati és mesterséges intelligenciával, észlelési és biztosít a Microsoft threat intelligence stream, valamint lehetővé teszi a is használhatja a saját fenyegetésészlelési intelligencia. 

 
## <a name="connect-to-all-your-data"></a>Csatlakozhat az adataihoz

A helyi Azure Sentinel, szüksége lesz a [a biztonsági forrásokhoz kapcsolódhat](connect-data-sources.md). Az Azure-Sentinel együttműködik a Microsoft-megoldások, a box és a valós idejű integráció, beleértve a Microsoft veszélyforrások elleni védelmi megoldások és a Microsoft 365 forrásból, például az Office 365, Azure AD-ben az Azure ATP, azonnal összekötőket és A Microsoft Cloud App Security, és többet. Emellett számos beépített összekötőkről, a szélesebb körű biztonsági ökoszisztéma nem Microsoft-megoldások. Az adatforrások csatlakoztatása az Azure-Sentinel, valamint a Syslog vagy a REST-API formátum. esemény közös is használhatja.  

![Adatgyűjtők](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Irányítópultok

Adatforrások csatlakoztatása után választhat katalógusából [expertly létrehozott irányítópultok](quickstart-get-visibility.md#dashboards) , amely insights surface adatforrásokból származó. Minden irányítópult teljes mértékben testre szabható – adhat hozzá a saját logikai vagy lekérdezések módosítása, vagy sablon nélkül is létrehozhat egy irányítópultot.

Az irányítópultok a fejlett analitika használata érdekében a biztonsági elemzőknek, mi történik a támadás során jobban megismerheti interaktív vizualizációs adja meg. A vizsgálati eszközök lehetővé teszi, hogy bármely mező részletes bemutatása a kívánt adatokat, gyorsan kialakítható threat környezetben. 

![Irányítópultok](./media/overview/dashboards.png)

## <a name="analytics"></a>Elemzés

Zaj csökkentésére, és tekintse át és vizsgálja meg a riasztások számának minimalizálása érdekében az Azure-Sentinel használ [analytics korrelációját, riasztást küld, az esetek](tutorial-detect-threats.md). **Esetek** csoportjai, a kapcsolódó riasztásokat, amelyek együtt hozzon létre egy gyakorlatban hasznosítható lehetséges – fenyegetést, amelyet Ön kivizsgálásához és megoldásához. A beépített korrelációs szabályokkal,-, vagy hozhat létre saját kiindulási pontként használhatja őket. Az Azure Sentinel is biztosít a machine learning szabályok képezze le a hálózati probléma, és keresse meg a rendellenességeket az erőforrások között. Ezek az analitikák a pontok összekötése, alacsony megbízhatósági szintű riasztást eredményeznek különböző entitások kapcsolatos potenciális élethű biztonsági incidensekre való kombinálásával.

![Esetek](./media/overview/cases.png)

## <a name="user-analytics"></a>A felhasználói elemzéséhez

A natív integrációi a machine learning (gépi tanulás) és [felhasználói analytics](user-analytics.md), Sentinel-az Azure segítségével gyorsan észlelheti a fenyegetéseket. Az Azure Sentinel zökkenőmentesen integrálható az Azure Advanced Threat Protection felhasználói viselkedés elemzéséhez, és priorizálhatja mely felhasználók először meg kell vizsgálni, riasztások, és a gyanús tevékenység minták alapján Azure Sentinel-és a Microsoft 365.

![A felhasználói elemzéséhez](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Biztonsági automatizálás és vezénylés

A gyakori feladatok automatizálásához és [biztonsági vezénylési forgatókönyveket az egyszerűbb](tutorial-respond-threats-playbook.md) , amely integrálható az Azure-szolgáltatások, valamint a meglévő eszközökkel. Az Azure Logic Apps alapjaira épül, Azure Sentinel automatizálás és vezénylés megoldást kínál a nagy mértékben bővíthetők architektúra, amely lehetővé teszi a méretezhető új technológiák az automatizálás és a fenyegetések bontakozik ki. Hozhat létre forgatókönyveket az Azure Logic Apps, a beépített forgatókönyvek közül választhat. Ezek közé tartozik a [több mint 200 összekötőt](https://docs.microsoft.com/azure/connectors/apis-list) például az Azure functions-szolgáltatásokhoz. Az összekötők lehetővé teszik, hogy a kód, ServiceNow, Jira, Zendesk, a HTTP kérelmeket, Microsoft Teams, Slack, a Windows Defender ATP és a Cloud App Security bármilyen egyéni logikát.

Például ha a ServiceNow hibajegyalapú rendszert használ, használhatja az Azure Logic Apps használatával a munkafolyamatok automatizálása és a egy jegyet ServiceNow minden alkalommal, amikor egy adott eseményt észlel a biztosított eszközökkel.

![Forgatókönyvek](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Vizsgálat

Az Azure Sentinel [mélyreható vizsgálat](tutorial-investigate-cases.md) eszközök körének ismerete és a fő okát, potenciálisan biztonsági fenyegetést talál segítséget. Választhat egy entitás az interaktív grafikonon egy adott entitás érdekes kérdéseket tehet fel, és az entitás- és a kapcsolatok kattintva megtekintheti a fenyegetés kiváltó okának feltárásához. 

![Vizsgálat](./media/overview/investigation.png)


## <a name="hunting"></a>Vadászat

Használja az Azure Sentinel [hatékony hunting keresési lekérdezés-és eszközök](hunting.md)MITRE keretében, amelyek lehetővé teszik, hogy proaktív módon hunt biztonsági fenyegetések között a szervezet az adatforrásokat, mielőtt egy riasztás akkor aktiválódik, alapján. Miután, Fedezze fel, melyik vadászat lekérdezés lehetséges támadások értékes betekintést nyújt, is a lekérdezés alapján egyéni észlelési szabályok létrehozása, és felszíni információkat riasztásai a biztonsági incidens válaszadók. Vadászat, miközben hozhat létre érdekes események könyvjelzők őket később visszatérhet és csoport azokat más correlating események meggyőző vizsgálati eset létrehozása, majd megosztja őket másokkal.

![Vadászat](./media/overview/hunting.png)

## <a name="community"></a>Közösség

Az Azure Sentinel-Közösség a fenyegetések észlelése és automatizálási hatékony erőforrásként. A Microsoft biztonsági elemzőknek folyamatosan létrehozása és hozzáadása az új irányítópultok, forgatókönyvek, vadászat lekérdezéseket és más, könyvelés a Közösség számára, hogy használja a környezetében. Minta tartalmat tölthet le a GitHub személyes Közösség [tárház](https://aka.ms/asicommunity) egyéni irányítópultokat, vadászat lekérdezéseket, jegyzetfüzeteket és forgatókönyvek létrehozása Azure-Sentinel. 

![Közösség](./media/overview/community.png)

## <a name="next-steps"></a>További lépések

- Ismerkedés az Azure Sentinel-, Microsoft Azure-előfizetéssel kell. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [előkészíteni az adatok Azure Sentinel-](quickstart-onboard.md), és [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
