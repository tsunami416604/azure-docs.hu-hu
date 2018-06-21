---
title: Eszközök regisztrálása elosztott terhelésű IoT-központokon az Azure IoT Hub Device Provisioning Service használatával | Microsoft Docs
description: DPS automatikus eszközregisztráció elosztott terhelésű IoT-központokon az Azure Portalon
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d0a3720fe729d5e260bbe5b0902460c8c7cfc7cb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629626"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Eszközök regisztrálása elosztott terhelésű IoT-központokon

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat eszközöket több elosztott terhelésű IoT-központhoz a Device Provisioning Service (DPS) használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával 
> * Regisztrációs listabejegyzés hozzáadása a második eszközhöz
> * A DPS kiosztási szabályzat beállítása **egyenletes eloszláshoz**
> * Új IoT-központ és a DPS csatolása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az előző, [Eszköz regisztrálása központhoz](tutorial-provision-device-to-hub.md) című oktatóanyagra épít.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával

Egy második eszköz második IoT-központhoz való regisztrációjához kövesse az [Eszköz regisztrálása központhoz](tutorial-provision-device-to-hub.md) című oktatóanyag lépéseit.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Regisztrációs listabejegyzés hozzáadása a második eszközhöz

A regisztrációs lista megmondja a DPS-nek, hogy melyik igazolási metódust (az eszközidentitás megerősítésének metódusa) használja az eszközhöz. A következő lépés a regisztrációs listabejegyzés hozzáadása a második eszközhöz. 

1. A DPS lapján kattintson a **Regisztrációk kezelése** elemre. Megjelenik a **Regisztrációs listabejegyzés hozzáadása** lap. 
2. Kattintson a **Hozzáadás** gombra a lap tetején.
2. Töltse ki a mezőket, majd kattintson a **Mentés** gombra.

## <a name="set-the-dps-allocation-policy"></a>A DPS kiosztási szabályzat beállítása

A kiosztási szabályzat egy DPS-beállítás, amely meghatározza az eszközök IoT-központokhoz való hozzárendelésének módját. Három támogatott kiosztási szabályzat létezik: 

1. **Legkisebb mértékű késleltetés**: Az eszközök abban az IoT-központban lesznek kiépítve, amely a legkisebb mértékű késleltetéssel rendelkezik az adott eszköz irányába.
2. **Egyenletesen súlyozott elosztás** (alapértelmezett): Az eszközök egyforma valószínűséggel lesznek kiépítve a csatolt IoT-központokban. Ez az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket. 
3. **Statikus konfigurálás a regisztrációs listán keresztül**: A regisztrációs listában meghatározott IoT-központ elsőbbséget élvez a DPS-szintű kiosztási szabályzattal szemben.

Kövesse az alábbi lépéseket a kiosztási szabályzat beállításához:

1. A kiosztási szabályzat beállításához kattintson a **Kiosztási szabályzat kezelése** lehetőségre a DPS lapján.
2. Állítsa a kiosztási szabályzatot **Egyenletesen súlyozott elosztás** értékre.
3. Kattintson a **Save** (Mentés) gombra.

## <a name="link-the-new-iot-hub-to-dps"></a>Új IoT-központ és a DPS csatolása

Csatolja egymáshoz a DPS-t és az IoT-központot, hogy a DPS regisztrálni tudja az eszközöket a központhoz.

1. Az **Összes erőforrás** lapon kattintson a korábban létrehozott DPS-re.
2. A DPS lapján kattintson a **Csatolt IoT-központok** lehetőségre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Hivatkozás hozzáadása IoT-központhoz** lapon a választógombok segítségével adja meg, hogy a csatolt IoT-központ az aktuális előfizetésben vagy egy másik előfizetésben található. Ezután válassza ki az IoT-központ nevét az **IoT-központ** mezőben.
5. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával 
> * Regisztrációs listabejegyzés hozzáadása a második eszközhöz
> * A DPS kiosztási szabályzat beállítása **egyenletes eloszláshoz**
> * Új IoT-központ és a DPS csatolása

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
