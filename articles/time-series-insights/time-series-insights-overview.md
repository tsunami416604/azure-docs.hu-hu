---
title: "Mi az Azure idő adatsorozat Insights? | Microsoft Docs"
description: "Bevezetés az Azure idő adatsorozat Insights, egy új szolgáltatás idő adatsorozat adatelemzés és az IoT-megoldások."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: cb4b3c701f3d3ec52addd1b98c01b97c2220ae34
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure idő adatsorozat Insights?

Idő adatsorozat Insights épül, tárolás, megjeleníteni és nagy mennyiségű időt adatsorozat adatok, például az IoT-eszközök által generált lekérdezése.  Ha szeretné tárolni, kezelheti, lekérdezése vagy idő adatsorozat adato a felhőben, idő adatsorozat Insights valószínű jogot az Ön számára.  

Ha egy alkalmazás, egy belső díjakon vagy külső felhasználók használatához idő adatsorozat Insights alkalmas, egy háttér indexelő, tárolásának és idő adatsorozat-adatok összesítésének.  Létrehozhat egy egyéni képi megjelenítés és a felhasználói élmény felül.  Idő adatsorozat Insights REST lekérdezés API-k ezt a forgatókönyvet mutatja.  

Ha biztos abban, hogy ha az adatok a time series, itt található tudnivalók.  Adatsorozat időadatok jelöli, hogy egy eszköz vagy folyamat idővel hogyan változik.  Ez abban egyedi, hogy a időbélyeggel rendelkezik, és egy tengely, lényeges.  Adatsorozat időadatok általában időrendjét érkezik, és általában a rendszer az adatbázis frissítése helyett egy INSERT utasítás.  Idő adatsorozat Insights rögzíti, és egy sor minden új esemény tárolja, mert változás mérése adott idő alatt, így lehetővé teszi az előző verziókkal való kereséséhez és előre jelezni jövőbeli változás.  A nagy méretű köteteket tárolása, indexelő, lekérdezése, elemzése és idő adatsor megjelenítése kihívást jelenthet.  

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Adattárolás idő adatsorozat skálázható módon.  
  - A a fő idő adatsorozat Insights van tartva idő adatsorozat adatokkal tervezett adatbázis.  Mivel méretezhető és teljes körűen felügyelt, az idő adatsorozat Insights tárolásához és kezeléséhez események munkájának kezeli.

- Közel valós idejű adatok feltárása.  
  - Idő adatsorozat Insights egy olyan környezetben adatfolyam visualizes explorer biztosít.  Egy eseményforrás csatlakozás után hamarosan eseményadatok is megtekinthetők, felderített és lekérdezett idő adatsorozat Insights belül.  Az adatok esetén hasznos ellenőrzése, hogy kibocsátó adatot várt, és egy IoT-eszköz állapotát, hatékonyságot és átfogó hatékonyság figyelését.  

- Kiváltó okának elemzéshez és anomáliadetektálási észleléséhez.
  - Idő adatsorozat Insights rendelkezik mintákat és perspektíva nézeteket, végezze el, és mentse a többlépéses probléma alapvető okát.  További idő adatsorozat Insights szolgáltatások például az Azure Stream Analytics riasztási együtt működik, értesítések és észlelt rendellenességeket tekinthető majdnem valós időben a idő adatsorozat Insights Explorer.  

- Több-asset vagy hely összehasonlításra különböző helyekről streaming idő adatsorok globális nézet.
  - Egy alkalommal adatsorozat Insights környezetben több eseményforrások csatlakozhat.  Ez azt jelenti, hogy több, különböző helyről a streamelési adatok tekintheti meg egyszerre közel valós időben.  Felhasználók kihasználhatják a láthatóság üzleti vezetők adatok megosztásához, és jobb együttműködés alkalmazhatja a segítséget megoldani a problémákat, akik tartományi szakértőivel engedélyezése bevált gyakorlatot, valamint learnings megosztani.

- Egy ügyfél alkalmazás fölött idő adatsorozat Insights felépítését. 
  - Idő adatsorozat Insights REST lekérdezés API-k, így lehetővé teszi a sorozat időadatok használó alkalmazások közzététele.

## <a name="capabilities"></a>Funkciók

- **Gyors használatbavétel:** Azure idő adatsorozat Insights kell nem jár előzetes adatok készíteni. Csatlakozás több millió esemény a Azure IoT-központ vagy az Eseményközpont perc múlva. Miután csatlakozott, megjelenítheti, és használhatja érzékelő adatokat az IoT-megoldások gyorsan érvényesítéséhez. Kód írása nélkül is kommunikálnak az adatokkal.
Nincs nincs új nyelv további; Idő adatsorozat Insights egy részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználók és majd feltárása.
- **Majdnem valós idejű elemzése:** idő adatsorozat Insights, egy perc késést fogadására képes több millió napi érzékelő esemény. Idő adatsorozat Insights segítséget nyújt betekintést nyerhet az érzékelő adatokat segítenek követni a trendeket és rendellenességeket, kiváltó okának elemzést, és elkerülheti a költséges állásidőt. A valós idejű és az előzményadatok összevetésének biztosításával a Time Series Insights segít felderíteni az adataiban észlelhető rejtett trendeket.
- **Egyéni megoldások:** beágyazása Azure idő adatsorozat Insights adatokat a meglévő alkalmazások, vagy hozzon létre új egyéni megoldások idő adatsorozat Insights REST API-k. Fedezze fel az elemzések másoknak megoszthatja személyre szabott nézetek létrehozása.
- **Méretezhetőség:** idő adatsorozat elemzések célja léptékű IoT támogatásához. 1 millió a 100 millió esemény napi egy alapértelmezett megőrzést span 31 napos érkező használhatja. Megjelenítheti és az élő adatok elemzése lévő adatfolyamok majdnem valós időben, előzményadatokat mellett. Bemenő és a megőrzési díjszabás növeli soron, hogy megfeleljen a vállalati méretű.

## <a name="getting-started"></a>Első lépések
Első lépések kevesebb, mint 5 percet vesz igénybe. 

1.  A beolvasandó elindítva, rendelkezni a idő adatsorozat Insights környezet az Azure portálon. 
2.  Csatlakozás egy eseményforrás, például egy Azure IoT-központ vagy az Eseményközpont kiválasztásával.  
3.  Töltse fel a referenciaadatok (ez nem egy másik szolgáltatást).
4.  Az adatok megjelenítéséhez idő adatsorozat Insights Explorer percben.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer
Ez az ábra szemlélteti, a time series insights megtekinteni az Intéző:! [Idő adatsorozat Insights explorer] (media/time-series-insights-explorer/explorer4.png)


## <a name="next-steps"></a>További lépések
 - [Megismerkedhet a bemutató környezetben idő adatsorozat Insights Explorerrel](./time-series-quickstart.md)
 - [Saját idő adatsorozat Insights környezet megtervezése](time-series-insights-environment-planning.md)

