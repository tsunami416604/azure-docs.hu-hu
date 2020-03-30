---
title: Mi az OPC Twin - Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az OPC Twin. Az OPC Twin az ipari eszközök felderítését, regisztrációját és távvezérlését biztosítja a REST API-kon keresztül.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826227"
---
# <a name="what-is-opc-twin"></a>Mi az OPC Twin?

Az OPC Twin olyan mikroszolgáltatásokból áll, amelyek az Azure IoT Edge és az IoT Hub segítségével csatlakoztatják a felhőt és a gyári hálózatot. Az OPC Twin az ipari eszközök felderítését, regisztrációját és távvezérlését biztosítja a REST API-kon keresztül. Az OPC Twin nem igényel OPC Unified Architecture (OPC UA) SDK-t, a nyelvi agnosztikus programozási, és kiszolgáló nélküli munkafolyamatba foglalható. Ez a cikk több OPC Twin használati esetet ismertet.

## <a name="discovery-and-control"></a>Felderítés és vezérlés
Használhatja OPC Twin egyszerű felderítése és regisztráció.

### <a name="simple-discovery-and-registration"></a>Egyszerű felfedezés és regisztráció
Az OPC Twin lehetővé teszi a gyári üzemeltetők számára a gyári hálózat átvizsgálatát, hogy az OPC UA kiszolgálók felfedezhetők és regisztrálhatók legyenek. Alternatív megoldásként a gyári operátorok manuálisan is regisztrálhatják az OPC UA-eszközöket egy ismert felderítési URL használatával. Ha például az összes OPC UA-eszközhöz szeretne csatlakozni, miután az IoT Edge átjáró egy OPC Twin modullal telepítve van a gyári szinten, a gyárüzemeltetője távolról elindíthatja a hálózat vizsgálatát, és vizuálisan láthatja az összes OPC UA-kiszolgálót. 

### <a name="simple-control"></a>Egyszerű vezérlés
Az OPC Twin lehetővé teszi a gyári üzemeltetők számára, hogy reagáljanak az eseményekre, és gyári gépeiket a felhőből automatikusan vagy manuálisan konfigurálják. Az OPC Twin REST API-kat biztosít az OPC UA-kiszolgálón lévő szolgáltatások meghívásához, a címterület böngészéséhez, valamint a változók olvasásához/írásához és a metódusok végrehajtásához. Például egy kazán a kpi hőmérsékletet használja a gyártósor vezérléséhez. A hőmérséklet-érzékelő közzéteszi az adatok változását az OPC Publisher használatával. A gyár üzemeltetője riasztást kap, hogy a hőmérséklet elérte a küszöbértéket. A gyártósor automatikusan lehűl az OPC Twin-en keresztül. A gyár üzemeltetője értesítést kap a lehűlésről.

## <a name="authentication"></a>Hitelesítés
Az OPC Twin egyszerű hitelesítéshez és egyszerű fejlesztői élményhez használható.

### <a name="simple-authentication"></a>Egyszerű hitelesítés 
Az OPC Twin az Azure Active Directory (AAD) alapú hitelesítést és naplózást használja az egész től a végéig. Az OPC Twin például lehetővé teszi, hogy az alkalmazás az OPC Twin tetejére épülve határozza meg, hogy a kezelő mit hajtott végre a gépen. A gép oldalán, ez keresztül OPC UA auditálás. A felhő oldalán egy nem módosítható ügyfél-naplózási napló és AAD-hitelesítés tárolásán keresztül történik a REST API-n.

### <a name="simple-developer-experience"></a>Egyszerű fejlesztői élmény 
Az OPC Twin bármely programozási nyelven írt alkalmazásokkal használható REST API-kon keresztül. Mivel a fejlesztők egy OPC UA klienst integrálnak egy megoldásba, az OPC UA SDK ismerete nem szükséges. Az OPC Twin zökkenőmentesen integrálható az állapotnélküli, kiszolgáló nélküli architektúrákba. Például egy teljes verem webfejlesztő, aki egy riasztási és esemény-irányítópultot fejleszt, a C, C# vagy a teljes OPC UA verem implementáció ismerete nélkül írhatja a logikát a JavaScript vagy TypeScript eseményekre való válaszadáshoz. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az OPC Twin-vel és annak felhasználásával, itt van a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Mi az az OPC-tároló?](overview-opc-vault.md)
