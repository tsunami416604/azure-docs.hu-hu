---
title: Hozzon létre egy Azure Security Center biztonsági ikermodulja az IoT-előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Security Center IoT ikermodul használatra az ASC az IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 93b6aa6fe133953025cd504e0486a51c4a3d5e04
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758363"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Gyors útmutató: Hozzon létre egy azureiotsecurity ikermodul

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid magyarázatot bemutatja, hogyan hozhat létre egyéni _azureiotsecurity_ új eszközöket, vagy a batch az ikermodulokkal ikermodulokkal az összes eszköz hozzon létre egy IoT hubot.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Understanding azureiotsecurity ikermodulokkal 

IoT-megoldások az Azure-ban fejlesztett ikereszközök Eszközkezelés és a folyamatok automatizálásával kulcsfontosságú szerepet játszanak. 

Az Azure Security Center (ASC) az IoT magas teljes integráció a meglévő IoT Eszközkezelési platformok, így kezelheti, az eszköz biztonsági állapotát gyártmány és használja az eszközök vezérlési meglévő képességeit.
Használja az IoT Hub valósítja meg IoT-integráció az ASC ikereszköz mechanizmust.  

Lásd: [az IoT Hub ikermodulokkal](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) tudhat meg többet az Azure IoT Hub ikermodulokkal általános fogalma. 
 
Az IoT megkönnyíti a modul ikereszköz mechanizmus használata, és fenntartja a biztonsági ikermodul nevű ASC _azureiotsecurity_ minden az eszközhöz.
Biztonsági ikermodulja tartalmazza a minden kapcsolódó eszközök biztonsági minden az eszközhöz. 
 
Teljes körű kiaknázásával ASC az IoT-szolgáltatások, a következőket kell tennie létrehozása, konfigurálása, valamint ezek biztonsági ikermodulokkal használata minden eszközt a szolgáltatásban.  

## <a name="create-azureiotsecurity-module-twin"></a>Hozzon létre azureiotsecurity ikermodul 

_azureiotsecurity_ ikermodulokkal kétféle módon hozható létre:
1. [A modul kötegelt parancsprogramot](https://aka.ms/iot-security-github-create-module) – automatikusan hoz létre az új eszköz vagy eszközök ikermodul egy ikermodulja az alapértelmezett konfiguráció használata nélkül.
2. Manuális Szerkesztés egyes ikermodul külön-külön az egyes eszközök konfigurációkkal.

>[!NOTE] 
> A batch metódussal nem felülírja a meglévő azureiotsecurity ikermodulokkal. CSAK a batch metódussal hoz létre új ikermodulokkal eszközök számára, amelyek még nem rendelkezik egy biztonsági ikermodul. 

Lásd: [ügynökkonfiguráció](how-to-agent-configuration.md) megtudhatja, hogyan módosítása, vagy módosítsa egy meglévő ikermodul konfigurációját. 

Manuális létrehozása egy új _azureiotsecurity_ modul ikereszköze a kövesse az alábbi utasításokat: 

1. Keresse meg és válassza ki az eszköz létrehoz egy biztonsági ikermodulja az az IoT hub az IoT Hub.
1. Kattintson az eszközén, majd a **modul identitás hozzáadása**.
1. Az a **identitás modulnév** írja be a következőt **azureiotsecurity**.

1. Kattintson a **Save** (Mentés) gombra. 

## <a name="verify-creation-of-a-module-twin"></a>Egy ikermodul létrehozásának ellenőrzése

Annak ellenőrzése, hogy egy adott eszközhöz tartozó létezik-e a biztonsági ikermodul:

1. Válassza ki az Azure IoT hub **IoT-eszközök** származó a **tallózók** menü.    
1. Adja meg az eszköz azonosítója, vagy válasszon egy beállítást a **lekérdezési eszköz mező** , és kattintson a **eszközök lekérdezése**. 
    ![Eszközök lekérdezése](./media/quickstart/verify-security-module-twin.png)
1. Válassza ki az eszközt, vagy kattintson duplán az, hogy nyissa meg az eszköz részleteit tartalmazó oldalra. 
1. Válassza ki a **modul identitások** menüben, majd erősítse meg a létezik-e a **azureiotsecurity** modul az eszközhöz társított modul identitások listájában. 
    ![Az eszközök modulok](./media/quickstart/verify-security-module-twin-3.png)


Az IoT-ikermodulokkal ASC tulajdonságainak testreszabása kapcsolatos további információkért lásd: [ügynökkonfiguráció](how-to-agent-configuration.md).

## <a name="next-steps"></a>További lépések

Folytassa a következő cikk további egyéni riasztások konfigurálása...

> [!div class="nextstepaction"]
> [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
