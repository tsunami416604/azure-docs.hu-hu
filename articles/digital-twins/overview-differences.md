---
title: Eltérések az előző verziótól
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogy mi változott az Azure Digital Twins új verziójában
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 9f95c3d03fed1950d6151ad1ed910bf2a8cd14f5
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125258"
---
# <a name="how-is-the-new-azure-digital-twins-different-from-the-previous-version-2018"></a>Miben különbözik az új Azure Digital Twins az előző verziótól (2018)?

Az Azure Digital Twins első nyilvános előzetes verziója 2018 októberében jelent meg. Habár a korábbi verzió alapfogalmai már az új szolgáltatásra kerültek a nyilvános előzetes verzióban, számos interfész és implementációs adat módosult, hogy a szolgáltatás rugalmasabb és hozzáférhető legyen. Ezeket a módosításokat az ügyfelek visszajelzései indokolták.

> [!IMPORTANT]
> Az új szolgáltatás kibővített képességeinek fényében az előző Azure digitális Twins szolgáltatás a 2020-as év végéig megszűnik.

Ha az előző nyilvános előzetes verzióban használta az Azure Digital Twins első verzióját, a jelen cikkben található információk és ajánlott eljárások segítségével megismerheti az új szolgáltatás használatát, és kihasználhatja a funkcióit.

## <a name="differences-by-topic"></a>Különbségek témakör szerint

Az alábbi diagram a szolgáltatás előző verziója és az új (aktuális) szolgáltatás között megváltoztatott fogalmak egymás melletti nézetét tartalmazza.

| Témakör | A korábbi verzióban | Új verzióban |
| --- | --- | --- | --- |
| **Modellezés**<br>*Rugalmasabb* | Az előző kiadás az intelligens tárolóhelyek körül lett kialakítva, ezért beépített szókincset kapott az épületekhez. | Az új Azure-beli digitális ikrek tartományi-függetlenek. Saját egyéni szókincsét és egyéni modelljét is meghatározhatja a megoldáshoz, hogy több fajta környezetet képviselni rugalmasabb módon.<br><br>További információ a [*fogalmakról: egyéni modellek*](concepts-models.md). |
| **Topológia**<br>*Rugalmasabb*| Az előző kiadás egy faszerkezetes adatstruktúrát támogatott, amely intelligens tárolóhelyekhez van igazítva. A digitális Twins hierarchikus kapcsolatokkal kapcsolódott. | Az új kiadással a digitális ikrek tetszőleges gráf-topológiához csatlakoztathatók, azonban azt szeretné. Ez nagyobb rugalmasságot biztosít a valós világ összetett kapcsolatainak kinyilvánítása érdekében.<br><br>További információ a [*fogalmakról: digitális ikrek és a Twin Graph*](concepts-twins-graph.md). |
| **Számítás**<br>*Gazdagabb, rugalmasabb* | Az előző kiadásban az események és a telemetria logikáját definiálták a JavaScript felhasználó által definiált függvényekben (UDF). A hibakeresés a UDF korlátozott volt. | Az új kiadás egy nyitott számítási modellel rendelkezik: egyéni logikát biztosít külső számítási erőforrások (például [Azure functions](../azure-functions/functions-overview.md)) csatolásával. Ez lehetővé teszi a választott programozási nyelv használatát, az egyéni kódtárak korlátozás nélküli elérését, és kihasználhatja a külső szolgáltatás által esetlegesen használt fejlesztési és hibakeresési erőforrásokat.<br><br>További információ [*: Azure-függvény beállítása az adatfeldolgozáshoz*](how-to-create-azure-function.md). |
| **Eszközkezelés IoT Hub**<br>*Könnyebben elérhető* | Az előző kiadás által felügyelt eszközök [IoT hub](../iot-hub/about-iot-hub.md) egy példányával, amely az Azure Digital Twins szolgáltatásban belső volt. Ez az integrált központ nem volt teljesen elérhető a fejlesztők számára. | Az új kiadásban a "saját" IoT hub-t egy önállóan létrehozott IoT Hub-példány csatolásával (a már felügyelt eszközökkel együtt) helyezheti el. Ez teljes hozzáférést biztosít a IoT Hub képességeihez, és lehetővé teszi az eszközkezelés irányítását.<br><br>További információ [*: telemetria beolvasása IoT hubból*](how-to-ingest-iot-hub-data.md). |
| **Biztonság**<br>*További szabványos* | Az előző kiadás olyan előre definiált szerepkörökkel rendelkezett, amelyeket a példányhoz való hozzáférés kezeléséhez használhat. | Az új kiadás a többi Azure-szolgáltatás által használt Azure [szerepköralapú hozzáférés-vezérlési (RBAC)](../role-based-access-control/overview.md) háttér-szolgáltatással integrálható. Ez egyszerűbbé teheti a megoldásban lévő más Azure-szolgáltatások (például a IoT Hub, a Azure Functions, a Event Grid stb.) hitelesítését.<br>A RBAC-mel előre definiált szerepköröket is használhat, vagy létrehozhat és konfigurálhat egyéni szerepköröket.<br><br>További információ a [*fogalmakról: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md). |
| **Méretezhetőség**<br>*Nagyobb* | Az előző kiadásban az eszközök, az üzenetek, a diagramok és a skálázási egységek méretezési korlátai voltak. Az előfizetések esetében csak egy Azure digitális Twins-példány támogatott.  | Az új kiadás egy új architektúrára támaszkodik, amely továbbfejlesztett méretezhetőséggel rendelkezik, és nagyobb számítási teljesítményt nyújt. Emellett régiónként legfeljebb 10 példányt támogat.<br><br>A nyilvános előzetes verzióban megjelenő korlátozások részleteiért tekintse meg a következő [*hivatkozást:*](reference-service-limits.md) a nyilvános előzetes verzióra vonatkozó korlátozások. |

## <a name="service-limits-in-public-preview"></a>Nyilvános előzetes verzióban elérhető a szolgáltatás korlátai

A nyilvános előzetes verzióban elérhető Azure Digital Twins-korlátok listáját a következő témakörben tekintheti meg [*: referenciák a nyilvános előzetes verziókra vonatkozó korlátozások*](reference-service-limits.md).

## <a name="next-steps"></a>További lépések

Következő lépésként az Azure Digital Twins használatáról az első oktatóanyagban tájékozódhat:

> [!div class="nextstepaction"]
> [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)