---
title: Hozzon létre egy biztonsági ikermodulja az ASC IoT-előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy IoT az ikermodul használatra ASC ASC az IoT.
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
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541646"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Gyors útmutató: Hozzon létre egy IoT az ikermodul ASC

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutatóban találja egyes ASC létrehozása az új eszközök IoT ikermodulokkal, vagy a batch-ikermodulokkal az összes eszköz az IoT Hub létrehozása.  

## <a name="understanding-asc-for-iot-module-twins"></a>Az IoT-ikermodulokkal ASC ismertetése 

IoT-megoldások az Azure-ban fejlesztett ikereszközök Eszközkezelés és a folyamatok automatizálásával kulcsfontosságú szerepet játszanak. 

Az IoT ASC kínál szolgáltatás teljesen integrálható a meglévő IoT device-kezelési platform, az, hogy az eszköz biztonsági állapotát kezelése lehetővé teszi az eszközök vezérlési meglévő képességeit használja gyártmány és. Használja az IoT Hub valósítja meg IoT-integráció az ASC ikereszköz mechanizmust.  

Lásd: [az IoT Hub ikermodulokkal](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) tudhat meg többet az Azure IoT Hub ikermodulokkal általános fogalma. 
 
Az IoT, a modul ikereszköz mechanizmus használata és a egy biztonsági ikermodul minden egyes eszközhöz fenntart révén az ASC. Biztonsági ikermodulja tartalmazza a minden kapcsolódó eszközök biztonsági minden az eszközhöz. 
 
Teljes körű kiaknázásával ASC az IoT-szolgáltatások, a következőket kell tennie létrehozása, konfigurálása, valamint ezek biztonsági ikermodulokkal használata minden eszközt a szolgáltatásban.  

## <a name="create-asc-for-iot-module-twin"></a>Hozzon létre ASC IoT az ikermodul 

Az IoT-ikermodulokkal ASC kötegelt mód az alapértelmezett konfigurációt használja, vagy külön-külön az egyes eszközök adott konfigurációval hozható létre. A batch új eszközök és a egy ikermodul nélküli eszközök létrehozni, használja a [modul kötegelt parancsprogramot](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> A batch metódussal nem felülírja a meglévő ikermodulokkal. CSAK a batch metódussal hoz létre új ikermodulokkal eszközök számára, amelyek még nem rendelkezik egy ikermodul. 

Lásd: [módosítása egy biztonsági ikermodul](how-to-modify-security-module-twin.md) megtudhatja, hogyan módosítása, vagy módosítsa egy meglévő ikermodul konfigurációját. 

Egy új ASC az ikermodul IoT-eszköz létrehozásához használja az alábbi utasításokat: 

1. Keresse meg és válassza ki az eszköz létrehoz egy biztonsági ikermodulja az az IoT hub az IoT Hub. 
1. Az a **Microsoft Szolgáltatásidentitás neve** írja be a következőt **ascforiotsecurity**.
1. Kattintson a **Save** (Mentés) gombra. 

## <a name="verify-creation-of-a-module-twin"></a>Egy ikermodul létrehozásának ellenőrzése

Annak ellenőrzése, hogy egy adott eszközhöz tartozó létezik-e a biztonsági ikermodul:

1. Válassza ki az Azure IoT hub **IoT-eszközök** származó a **tallózók** menü.    
1. Adja meg az eszköz azonosítója, vagy válasszon egy beállítást a **lekérdezési eszköz mező** , és kattintson a **eszközök lekérdezése**. 
    ![Eszközök lekérdezése](./media/quickstart/verify-security-module-twin.png)
1. Válassza ki az eszközt, vagy kattintson duplán az, hogy nyissa meg az eszköz részleteit tartalmazó oldalra. 
1. Válassza ki a **modul identitások** menüben, majd erősítse meg a létezik-e a **ascforiotsecurity** modul és a egy **kapcsolódási állapot** , **csatlakoztatva**az eszközhöz társított modul identitások listájában. 
    ![Az eszközök modulok](./media/quickstart/verify-security-module-twin-2.png)


Az IoT-ikermodulokkal ASC tulajdonságainak testreszabása kapcsolatos további információkért lásd: [ügynökkonfiguráció](concept-agent-configuration.md).

## <a name="next-steps"></a>További lépések

Folytassa a következő cikk további egyéni riasztások konfigurálása...

> [!div class="nextstepaction"]
> [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
