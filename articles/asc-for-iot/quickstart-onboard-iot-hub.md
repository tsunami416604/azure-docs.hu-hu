---
title: IoT-szolgáltatás IoT Hub előzetes verzióban érhető el az ASC engedélyezése |} A Microsoft Docs
description: Ismerje meg az IoT hub IoT-szolgáltatást az ASC engedélyezése.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4fd7fb68fd2eb8be0c1c109d76cff341faba9391
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541638"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Gyors útmutató: Az IoT Hub szolgáltatás engedélyezése

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az ASC az IoT hub IoT-előzetes szolgáltatás engedélyezése a magyarázattal szolgál.  

> [!NOTE]
> ASC IOT jelenleg csak a standard szintű és a magasabb szintű IoT-központok támogatja.
>ASC az IoT hub egyetlen megoldás is. Ha több van szüksége, több megoldás szükség. 

## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezéséhez szükséges előfeltételek

- Log Analytics-munkaterület
  - Két típusú adatok tárolódnak a Log Analytics munkaterületén az ASC által alapértelmezés szerint az IoT; **biztonsági riasztások** és **javaslatok**. 
  - Dönthet úgy, hogy adjon hozzá további információk típusa, a storage **nyers események**. Vegye figyelembe, hogy tárolására **nyers események** a Log Analytics sorozatéhoz további tárolási költségeket. 
- Az IoT Hub (standard szintű vagy újabb)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Az IoT hub IoT ASC engedélyezése 

Ahhoz, hogy az IoT hub biztonsági, tegye a következőket: 

1. Nyissa meg a **az IoT Hub** az Azure Portalon. 
2. Válassza ki, és nyissa meg a **biztonsági** a bal oldali menüből. 
3. Válasszon **IoT-biztonság engedélyezése**. 
4. Adja meg a Log Analytics-munkaterület adatait. 
   - Tárolhatja **nyers események** mellett az alapértelmezett típusokat. Ha tárolási a **nyers események** váltógomb **a**. 
   - Dönthet úgy, hogy engedélyezze, hogy **ikereszköz gyűjtemény** távozó a **ikereszköz gyűjtemény** váltógomb **a**. 
5. Kattintson az **OK** gombra. 
6. Kattintson a **Save** (Mentés) gombra. 

Gratulálunk! Ön teljesítette az IoT hub IoT ASC engedélyezéséhez. 

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben talál a megoldás konfigurálása...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)