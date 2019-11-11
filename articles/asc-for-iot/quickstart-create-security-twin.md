---
title: 'Gyors útmutató: biztonsági modul létrehozása Azure Security Center IoT'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Security Center a IoT-modulhoz, amely a Azure Security Center for IoT használható.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904168"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Gyors útmutató: azureiotsecurity-modul létrehozása – Twin

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egyedi _azureiotsecurity_ -modult az új eszközökhöz, vagy létrehozhat egy batch-modult az összes eszközhöz egy IoT hub.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Az ikrek azureiotsecurity-moduljának ismertetése 

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is. 

Azure Security Center for IoT teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, így kezelheti az eszköz biztonsági állapotát, és használhatja a meglévő eszköz-vezérlési képességeket.
A IoT-integráció Azure Security Center a IoT Hub Twin mechanizmus használatával érhető el.  

További információ az IoT Hub Azure-beli modulbeli ikrek általános fogalmáról: [IoT hub modul ikrek](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) . 
 
A IoT Azure Security Center a modul Twin mechanizmusát használja, és minden egyes eszközhöz fenntart egy _azureiotsecurity_ nevű biztonsági modult.

A biztonsági modul Twin az eszközök biztonságára vonatkozó összes információt tartalmazza. 
 
Ahhoz, hogy a Azure Security Center teljes mértékben használhassa a IoT-funkciókat, létre kell hoznia, konfigurálnia és használnia kell ezeket a biztonsági modulokat a szolgáltatás minden eszközéhez.  

## <a name="create-azureiotsecurity-module-twin"></a>Twin azureiotsecurity-modul létrehozása 

a _azureiotsecurity_ modul ikrek két módon hozhatók létre:
1. [Modul Batch-parancsfájl](https://aka.ms/iot-security-github-create-module) – a automatikusan létrehozza a modult az új eszközök vagy eszközök számára az alapértelmezett konfiguráció használata nélkül.
2. Manuálisan szerkessze az egyes modulokat külön-külön az egyes eszközökhöz tartozó konfigurációkkal.

>[!NOTE] 
> A Batch metódus használata nem írja felül az ikrek meglévő azureiotsecurity-modulját. A Batch metódus csak az ikrek új modulját hozza létre olyan eszközökhöz, amelyek még nem rendelkeznek különálló biztonsági modullal. 

Tekintse meg az [ügynök konfigurációja](how-to-agent-configuration.md) című témakört, amelyből megtudhatja, hogyan módosíthatja vagy módosíthatja a meglévő modulok konfigurációját. 

Ha manuálisan szeretne létrehozni egy új _azureiotsecurity_ -modult egy eszközhöz, kövesse az alábbi utasításokat: 

1. A IoT Hubban keresse meg és válassza ki azt az eszközt, amelyhez külön biztonsági modult szeretne létrehozni.
1. Kattintson az eszközre, majd a **modul identitásának hozzáadása**elemre.
1. A **modul identitásának neve** mezőbe írja be a **azureiotsecurity**nevet.

1. Kattintson a **Save** (Mentés) gombra. 

## <a name="verify-creation-of-a-module-twin"></a>Modul létrehozásának ellenőrzése

Annak ellenőrzése, hogy van-e különálló biztonsági modul egy adott eszközhöz:

1. Az Azure-IoT Hub válassza a **IoT eszközök** lehetőséget a **felfedezők** menüből.    
1. Adja meg az eszköz AZONOSÍTÓját, vagy válasszon ki egy beállítást a **lekérdezési eszköz mezőben** , majd kattintson az **eszközök lekérdezése**elemre. 
    ![lekérdezési eszközök](./media/quickstart/verify-security-module-twin.png)
1. Válassza ki az eszközt, vagy kattintson rá duplán az eszköz részletei lap megnyitásához. 
1. Válassza a **modul identitások** menüt, és erősítse meg a **azureiotsecurity** modul létezését az eszközhöz társított modul-identitások listájában. 
    eszközhöz társított ![-modulok](./media/quickstart/verify-security-module-twin-3.png)


Ha többet szeretne megtudni az IoT-modulok Azure Security Center tulajdonságainak testreszabásáról, tekintse meg az [ügynök konfigurálása](how-to-agent-configuration.md)című témakört.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan konfigurálhat egyéni riasztásokat...

> [!div class="nextstepaction"]
> [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
