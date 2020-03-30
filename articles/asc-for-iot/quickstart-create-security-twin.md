---
title: 'Rövid útmutató: Hozzon létre egy ikerbiztonsági modult az Azure Security Center for IoT-hez'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy Azure Security Center for IoT module twin-t az Azure Security Center for IoT-hez való használatra.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904168"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Rövid útmutató: Hozzon létre egy azureiotsecurity modul iker

Ez a rövid útmutató bemutatja, hogyan hozhat létre egyéni _azureiotsecurity_ modul twins új eszközökhöz, vagy a batch hozzon létre modul twins az Összes eszköz egy IoT Hub.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Az azureiotsecurity modul ikermoduljának ismertetése 

Az Azure-ban készült IoT-megoldások esetében az ikereszközök kulcsfontosságú szerepet játszanak mind az eszközkezelésben, mind a folyamatautomatizálásban. 

Az Azure Security Center for IoT teljes integrációt kínál a meglévő IoT-eszközfelügyeleti platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközvezérlési képességek kihasználását.
Az Azure Security Center az IoT-integrációhoz az IoT Hub ikermechanizmusának használatával érhető el.  

Tekintse meg az [IoT Hub-modul twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) további megismeréséhez az Azure IoT Hub ban a modultwins általános koncepciójával kapcsolatban. 
 
Az Azure Security Center for IoT használja a modul iker mechanizmus, és fenntartja a biztonsági modul két nevű _azureiotsecurity_ az egyes eszközök.

A biztonsági modul iker tartalmazza az összes vonatkozó információt az eszköz biztonságát az egyes eszközök. 
 
Az Azure Security Center for IoT-funkciók teljes körű kihasználásához létre kell hoznia, konfigurálnia és használnia kell ezeket a biztonsági modultwins-eket a szolgáltatás minden eszközéhez.  

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity modul ikerelem létrehozása 

_Azureiotsecurity_ modul ikrek hozhatók létre két módon:
1. [Modul kötegelt parancsfájl](https://aka.ms/iot-security-github-create-module) – automatikusan létrehozza az ikermodult az új eszközökhöz vagy eszközökhöz az alapértelmezett konfiguráció használatával.
2. Manuálisan szerkesztheti az egyes modulokat, külön-külön, az egyes eszközök adott konfigurációival.

>[!NOTE] 
> A kötegelt módszer használata nem írja felül a meglévő azureiotsecurity modul twins. A kötegelt módszer használata csak új modul ikrek olyan eszközökhöz, amelyek még nem rendelkeznek a biztonsági modul iker. 

Tekintse meg [az ügynök konfigurációját,](how-to-agent-configuration.md) amelyből megtudhatja, hogyan módosíthatja vagy módosíthatja egy meglévő ikermodul konfigurációját. 

Ha manuálisan szeretne új _azureiotsecurity_ modult létrehozni egy eszközhöz, kövesse az alábbi utasításokat: 

1. Az IoT Hubban keresse meg és válassza ki azt az eszközt, amelyhez egy ikerbiztonsági modult szeretne létrehozni.
1. Kattintson az eszközre, majd a **Modulidentitás hozzáadása parancsra.**
1. A **Modulidentitás neve** mezőbe írja be az **azureiotsecurity értéket.**

1. Kattintson a **Mentés** gombra. 

## <a name="verify-creation-of-a-module-twin"></a>Ikermodul létrehozásának ellenőrzése

Annak ellenőrzése, hogy létezik-e ikerbiztonsági modul egy adott eszközhöz:

1. Az Azure IoT Hubban válassza az **IoT-eszközök** az **Explorers** menüben.    
1. Írja be az eszközazonosítót, vagy válasszon egy beállítást a **Lekérdezés eszközmezőben,** és kattintson az **Eszközök lekérdezése**gombra . 
    ![Eszközök lekérdezése](./media/quickstart/verify-security-module-twin.png)
1. Jelölje ki az eszközt, vagy kattintson rá duplán az Eszköz részletei lap megnyitásához. 
1. Válassza ki a **modul identitások** menüt, és erősítse meg az **azureiotsecurity** modul létezését az eszközhöz társított modulidentitások listájában. 
    ![Az eszközhöz társított modulok](./media/quickstart/verify-security-module-twin-3.png)


Ha többet szeretne tudni az Azure Security Center ioT-modultwins-ek tulajdonságainak testreszabásáról, olvassa el az [Ügynök konfigurációja.](how-to-agent-configuration.md)

## <a name="next-steps"></a>További lépések

A következő cikkhez, hogy megtudja, hogyan konfigurálhatja az egyéni riasztásokat...

> [!div class="nextstepaction"]
> [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
