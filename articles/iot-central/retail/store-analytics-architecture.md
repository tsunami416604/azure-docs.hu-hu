---
title: Az elemzési architektúra tárolása
description: Ismerje meg, hogyan hozhat létre egy áruházbeli elemzési alkalmazást a pénztári alkalmazás sablonjának használatával IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 5c0c4d8f8f69941d182ff5e35d4e32d29efdf869
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958020"
---
# <a name="in-store-analytics-architecture"></a>Áruházbeli elemzési architektúra

Az áruházbeli elemzési megoldások lehetővé teszik a kiskereskedelmi tárolási környezet különböző feltételeinek figyelését. Ezek a megoldások a IoT Centralon belüli alkalmazások egyikének kihasználásával és az alábbi architektúra útmutatásként használhatók.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
- Telemetria küldő és összesített bepillantást IoT Central
- Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipulációhoz
- Az adatszerkezet a kívánt formátumban strukturálható, és elküldhető egy tárolási szolgáltatásba
- Az üzleti alkalmazások lekérhetik az adatok lekérdezését, és elemzéseket készíthetnek a kiskereskedelmi működésről
 
Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét egy áruházbeli elemzési megoldásban játszanak.

## <a name="condition-monitoring-sensors"></a>Állapot-figyelési érzékelők

Az IoT-megoldások érzékelők készletével kezdődnek a kereskedelmi tárolók környezetében lévő értelmes jelek. A fenti architektúra-diagram bal szélén számos érzékelő látható.

## <a name="gateway-devices"></a>Átjáróeszközök

Számos IoT érzékelő közvetlenül a felhőbe vagy a közelében található átjáró-eszközre képes nyers jeleket felvenni. Az átjáró-eszköz az adatok összesítését hajtja végre a peremen, mielőtt összefoglaló elemzéseket küld egy IoT Central alkalmazásnak. Az átjáró-eszközök feladata a parancs-és vezérlési műveletek továbbítása is a szenzoros eszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central-alkalmazás különböző IoT-érzékelőkből származó adatok betöltését, valamint a kiskereskedelmi tároló környezetében lévő átjáró-eszközöket, valamint az értelmes elemzések készletét hozza létre.

Az Azure IoT Central egy személyre szabott élményt nyújt az áruház kezelője számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és felügyeletét.

## <a name="data-transform"></a>Adatátalakítás
A megoldáson belüli Azure IoT Central alkalmazás úgy konfigurálható, hogy nyers vagy összesített elemzéseket exportáljon az Azure Pásti (szolgáltatásként nyújtott platform) szolgáltatásaiba, amelyek adatkezelést végezhetnek, és gazdagítják ezeket a bepillantást, mielőtt a vállalaton belül kihelyezik őket alkalmazás. 

## <a name="business-application"></a>Üzleti alkalmazás
A IoT-ben a kiskereskedelmi környezetben üzembe helyezett különböző üzleti alkalmazások is kihasználhatók. A Retail Store Manager vagy a személyzet tagjai ezeket az alkalmazásokat felhasználhatják az üzleti eredmények megjelenítéséhez, és valós időben végezhetnek értelmes műveleteket. Ha szeretné megtudni, hogyan hozhat létre valós idejű Power BI irányítópultot a kereskedelmi csapatának, kövesse az [oktatóanyagot](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg az [áruházbeli elemzési pénztár](https://aka.ms/checkouttemplate) és [az áruházbeli elemzési feltételek figyelése](https://aka.ms/conditiontemplate) alkalmazás-sablonokkal. 
* Tekintse meg a teljes [körű oktatóanyagot](https://aka.ms/storeanalytics-tutorial) , amely bemutatja, hogyan hozhat létre olyan megoldást, amely az egyik áruházbeli elemzési alkalmazás-sablont használja.
