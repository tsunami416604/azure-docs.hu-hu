---
title: Ismerkedés az Azure Security Center (ASC) használatával for IoT-előzetes verzió |} A Microsoft Docs
description: Ismerkedjen meg az alapvető munkafolyamatát, ASC IoT-szolgáltatások és a szolgáltatás ismertetése.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 37815c1f1afe3e4c99e2fe171b21857a2018f709
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648316"
---
# <a name="get-started-with-azure-security-center-asc-for-iot"></a>Az IoT első lépései az Azure Security Center (ASC) 

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az ASC a különböző építőelemeit, magyarázattal szolgál abban az IoT-szolgáltatás, és azt ismerteti, hogyan kezdheti el az [engedélyezni a szolgáltatást](quickstart-onboard-iot-hub.md). 

Az IoT ASC zökkenőmentesen integrálható az IoT hubot, hogy adja meg a biztonsági elemzés az IoT hub konfigurációs, eszköz identitás- és hub-eszköz kommunikációs minták.
Fokozott biztonsági képességei az IoT ASC biztosít az IoT-eszközökről származó biztonsági adatok gyűjtését az ügynök-alapú.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC IoT zökkenőmentes az IoT Hub-integráció

Megkísérelte az egyes IoT-eszközök védelme, szükség arra, hogy adatokat gyűjteni az eszközök közvetlenül a, vagy a hálózatról. Ebből a törekvésből támogatása érdekében ASC az IoT-eszköz figyelése és vezethet be alacsony erőforrás-igényű biztonsági ügynökök arsenal kínál.

Az ASC IoT előzetes verzióra, a referenciaarchitektúra a Linux és Windows biztonsági ügynökök, a is C# és C vannak megadva.
Az ügynökök kezelésére az eszköz operációs rendszere, az esemény összesítést a csökkentheti a költségeket, és konfigurációja a modul az ikereszközök keresztül érkező nyers események gyűjtése.
Biztonsági üzeneteket küld az IoT Hub segítségével az IoT-elemzési szolgáltatások az ASC-be.

## <a name="asc-for-iot-basics"></a>Az ASC IoT alapjai

Válassza ki a munkafolyamat-forgatókönyvet, amely az IoT-eszköz és a környezet követelményeinek legjobban megfelelő:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Ismerkedés az ASC IoT zökkenőmentes az IoT Hub-integráció 

>[!Note]
>Ez a munkafolyamat lehetővé teszi a szolgáltatás használata az IoT biztonsági ügynökök esetében ASC használata nélkül. 

Engedélyezze a monitorozást az eszköz az Identitáskezelés, eszköz-felhő és a felhő eszköz kommunikációs mintái, használja a következő alapvető munkafolyamattal teszteléshez és a szolgáltatás elindítása: 

1. [Az IoT Hub szolgáltatás IoT ASC engedélyezése](quickstart-onboard-iot-hub.md)
1. Ha az IoT Hub nem tartozik regisztrált eszköz, [új eszköz regisztrálása](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Az eszközök azureiotsecurity biztonsági modul létrehozása](quickstart-create-security-twin.md) eszközökhöz. 
1. Megadni az eszköz és a rendszer működését normál keresztül [egyéni riasztások](quickstart-create-custom-alerts.md). 
1. Hajtsa végre a rendszer tesztelésen, szolgáltatás és az eszköz állapotát. 
1. Ismerkedés a [riasztások](concept-security-alerts.md), [javaslatok](concept-recommendations.md), és [részletes ismertetése a Log Analytics használatával](how-to-security-data-access.md) IoT Hub használatával. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Ismerkedés az ASC IoT biztonsági ügynökök esetében

Ellenőrizze, hogy IoT-fokozott biztonsági képességei, például a távoli kapcsolatokat, aktív alkalmazások, bejelentkezési események és az operációs rendszer konfigurálási eljárásoknak megfelelő beállításában figyelést végezni a következő alapvető munkafolyamattal teszteléséhez, és engedélyezze a szolgáltatást az ASC használja: 

1. [Az IoT Hub szolgáltatás IoT ASC engedélyezése](quickstart-onboard-iot-hub.md)
1. Ha az IoT Hub nem tartozik regisztrált eszköz, [új eszköz regisztrálása](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Hozzon létre egy azureiotsecurity biztonsági modult](quickstart-create-security-twin.md) eszközökhöz.
1. Az ügynök telepítése egy tényleges eszközön telepítése helyett az Azure szimulált eszközön [léptetéses egy új Azure virtuális gép (VM) be](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) elérhető zónában. 
1. [Az ASC IoT biztonsági ügynök telepítése](how-to-deploy-linux-cs.md) a IoT-eszközt, vagy új virtuális Gépen.
1. Kövesse az utasításokat [trigger_events](https://aka.ms/iot-security-github-trigger-events) egy ártalmatlan támadás szimulációjának futtatásához.
1. ASC ellenőrizze az előző lépésben a szimulált támadás válaszul IoT-riasztások esetén. Ellenőrzési öt perc alatt a szkript futtatása után kezdődik.
1. Ismerkedés a [riasztások](concept-security-alerts.md), [javaslatok](concept-recommendations.md), és [részletes ismertetése a Log Analytics használatával](how-to-security-data-access.md) IoT Hub használatával. 

## <a name="next-steps"></a>További lépések

- Engedélyezése [ASC az IoT](quickstart-onboard-iot-hub.md)
- Konfigurálja a [megoldás](quickstart-configure-your-solution.md)
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- Konfigurálása [egyéni riasztások](quickstart-create-custom-alerts.md)
- [A biztonsági ügynök telepítése](how-to-deploy-agent.md)
