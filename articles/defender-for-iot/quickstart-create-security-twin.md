---
title: 'Gyors útmutató: biztonsági modul létrehozása – Twin'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a Defender for IoT modul Twin-et az Azure Defender for IoT használatával.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 822e9dc237dd6f4d73e1860c6a0b240fb94c3105
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947182"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Gyors útmutató: azureiotsecurity-modul létrehozása – Twin

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egyedi _azureiotsecurity_ -modult az új eszközökhöz, vagy létrehozhat egy batch-modult az összes eszközhöz egy IoT hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Az ikrek azureiotsecurity-moduljának ismertetése

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

A IoT Defender teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközök vezérlési képességeinek kihasználását.
A IoT-integrációs Defender a IoT Hub Twin mechanizmus használatával érhető el.

További információ az IoT Hub Azure-beli modulbeli ikrek általános fogalmáról: [IoT hub modul ikrek](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

A IoT Defender a modul Twin mechanizmusát használja, és minden eszközhöz fenntart egy _azureiotsecurity_ nevű biztonsági modult.

A biztonsági modul Twin az eszközök biztonságára vonatkozó összes információt tartalmazza.

Ahhoz, hogy a Defender teljes mértékben használhassa a IoT-funkciókat, létre kell hoznia, konfigurálnia és használnia kell ezeket a biztonsági modulokat a szolgáltatás minden eszközéhez.

## <a name="create-azureiotsecurity-module-twin"></a>Twin azureiotsecurity-modul létrehozása

a _azureiotsecurity_ modul ikrek két módon hozhatók létre:

1. [Modul Batch-parancsfájl](https://aka.ms/iot-security-github-create-module) – a automatikusan létrehozza a modult az új eszközök vagy eszközök számára az alapértelmezett konfiguráció használata nélkül.
1. Manuálisan szerkessze az egyes modulokat külön-külön az egyes eszközökhöz tartozó konfigurációkkal.

>[!NOTE]
> A Batch metódus használata nem írja felül az ikrek meglévő azureiotsecurity-modulját. A Batch metódus csak az ikrek új modulját hozza létre olyan eszközökhöz, amelyek még nem rendelkeznek különálló biztonsági modullal.

Tekintse meg az [ügynök konfigurációja](how-to-agent-configuration.md) című témakört, amelyből megtudhatja, hogyan módosíthatja vagy módosíthatja a meglévő modulok konfigurációját.

Ha manuálisan szeretne létrehozni egy új _azureiotsecurity_ -modult egy eszközhöz, kövesse az alábbi utasításokat:

1. A IoT Hubban keresse meg és válassza ki azt az eszközt, amelyhez külön biztonsági modult szeretne létrehozni.
1. Kattintson az eszközre, majd a **modul identitásának hozzáadása**elemre.
1. A **modul identitásának neve** mezőbe írja be a **azureiotsecurity**nevet.

1. Kattintson a **Mentés** gombra.

## <a name="verify-creation-of-a-module-twin"></a>Modul létrehozásának ellenőrzése

Annak ellenőrzése, hogy van-e különálló biztonsági modul egy adott eszközhöz:

1. Az Azure-IoT Hub válassza a **IoT eszközök** lehetőséget a **felfedezők** menüből.
1. Adja meg az eszköz AZONOSÍTÓját, vagy válasszon ki egy beállítást a **lekérdezési eszköz mezőben** , majd kattintson az **eszközök lekérdezése**elemre.
    ![Eszközök lekérdezése](./media/quickstart/verify-security-module-twin.png)
1. Válassza ki az eszközt, vagy kattintson rá duplán az eszköz részletei lap megnyitásához.
1. Válassza a **modul identitások** menüt, és erősítse meg a **azureiotsecurity** modul létezését az eszközhöz társított modul-identitások listájában.
    ![Eszközhöz társított modulok](./media/quickstart/verify-security-module-twin-3.png)

Ha többet szeretne megtudni az IoT-modulhoz készült Defender tulajdonságainak testreszabásáról, tekintse meg az [ügynök konfigurálása](how-to-agent-configuration.md)című témakört.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan vizsgálhatja meg a biztonsági javaslatokat...

> [!div class="nextstepaction"]
> [Biztonsági javaslatok vizsgálata](quickstart-investigate-security-recommendations.md)
