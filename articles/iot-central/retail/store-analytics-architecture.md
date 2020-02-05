---
title: Az elemzési architektúra tárolása
description: Ismerje meg, hogyan hozhat létre egy áruházbeli elemzési alkalmazást a pénztári alkalmazás sablonjának használatával IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6c2514bd078cc3feee4bd2802cf314079b824311
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022120"
---
# <a name="in-store-analytics-architecture"></a>Áruházbeli elemzési architektúra



Az áruházbeli elemzési megoldások lehetővé teszik a kiskereskedelmi tárolási környezet különböző feltételeinek figyelését. Ezek a megoldások a IoT Centralon belüli alkalmazások egyikével és az alábbi architektúrával kapcsolatos útmutatásként használhatók.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
- Telemetria küldő és összesített bepillantást IoT Central
- Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipulációhoz
- Az adatszerkezet a kívánt formátumban strukturálható, és elküldhető egy tárolási szolgáltatásba
- Az üzleti alkalmazások lekérhetik az adatok lekérdezését, és elemzéseket készíthetnek a kiskereskedelmi működésről
 
Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét egy áruházbeli elemzési megoldásban játszanak.

## <a name="condition-monitoring-sensors"></a>Állapot-figyelési érzékelők

Az IoT-megoldások érzékelők készletével kezdődnek a kereskedelmi tárolók környezetében lévő értelmes jelek. A fenti architektúra-diagram bal szélén különböző típusú érzékelők is tükröződnek.

## <a name="gateway-devices"></a>Átjáróeszközök

Számos IoT érzékelő közvetlenül a felhőbe vagy a közelében található átjáró-eszközre képes nyers jeleket felvenni. Az átjáró-eszköz az adatok összesítését hajtja végre a peremen, mielőtt összefoglaló elemzéseket küld egy IoT Central alkalmazásnak. Az átjáró-eszközök feladata a parancs-és vezérlési műveletek továbbítása is a szenzoros eszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central-alkalmazás különböző típusú IoT-érzékelőkből származó adatok betöltését, valamint a kiskereskedelmi tároló környezetében található átjáró-eszközöket, valamint az értelmes elemzések készletét hozza létre.

Az Azure IoT Central egy személyre szabott élményt nyújt az áruház kezelője számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és felügyeletét.

## <a name="data-transform"></a>Adatátalakítás
A megoldáson belüli Azure IoT Central alkalmazás úgy konfigurálható, hogy nyers vagy összesített elemzéseket exportáljon az Azure Pásti (szolgáltatásként nyújtott platform) szolgáltatásaiba, amelyek adatkezelést végezhetnek, és gazdagítják ezeket a bepillantást, mielőtt a vállalaton belül kihelyezik őket alkalmazás. 

## <a name="business-application"></a>Üzleti alkalmazás
A IoT-ben a kiskereskedelmi környezetben üzembe helyezett különböző típusú üzleti alkalmazások is használhatók. A kiskereskedelmi áruház kezelője vagy a személyzet tagjai ezeket az alkalmazásokat felhasználhatják az üzleti eredmények megjelenítéséhez és a valós idejű, értelmes műveletek elvégzéséhez. Ha szeretné megtudni, hogyan hozhat létre valós idejű Power BI irányítópultot a kereskedelmi csapatának, kövesse az [oktatóanyagot](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg az [áruházbeli elemzési pénztár](https://aka.ms/checkouttemplate) és [az áruházbeli elemzési feltételek figyelése](https://aka.ms/conditiontemplate) alkalmazás-sablonokkal. 
* Tekintse meg a teljes [körű oktatóanyagot](https://aka.ms/storeanalytics-tutorial) , amely végigvezeti a megoldás létrehozásán, hogyan hozhat létre megoldást az egyik áruházbeli elemzési alkalmazás-sablon használatával.
