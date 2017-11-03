---
title: "Használja az IoT kiépítés Azure Hub eszköz szolgáltatást betöltése között eszközök beállításához igényel IoT-központok |} Microsoft Docs"
description: "Terjesztési pontok automatikus kiépítés betöltése között elosztott terhelésű Azure-portálon az IoT-központok"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Különböző terhelésű IoT-központok eszközök beállításához

Ez az oktatóanyag bemutatja, hogyan ellátja az eszközöket, több terhelésű IoT-központok az eszköz kiépítése szolgáltatáshoz (terjesztési pontok) használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure portál segítségével egy második IoT-központ második eszköz kiépítése 
> * A második eszköz beléptetési lista bejegyzés hozzáadása
> * A terjesztési pontok elosztási házirend beállítása **terjesztési is**
> * Az új IoT hub csatolása terjesztési pontok

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag épít az előző [hub eszköz kiépítése](tutorial-provision-device-to-hub.md) oktatóanyag.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Az Azure portál segítségével egy második IoT-központ második eszköz kiépítése

Kövesse a [hub eszköz kiépítése](tutorial-provision-device-to-hub.md) oktatóanyag második eszközöket, hogy egy másik IoT-központ telepítéséhez.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>A második eszköz beléptetési lista bejegyzés hozzáadása

A tanúsítványigénylési listája be van állítva a terjesztési pontok igazolási (általában egy eszközidentitás módszer) módszer az használ az eszközzel. A következő lépés, hogy vegye fel a második eszköz beléptetési lista bejegyzését. 

1. A terjesztési pontok lapján kattintson a **regisztrációkat kezelése**. A **beléptetési Listaelem hozzáadása** lap jelenik meg. 
2. Kattintson a lap tetején **Hozzáadás**.
2. Töltse ki a mezőket, és kattintson a **mentése**.

## <a name="set-the-dps-allocation-policy"></a>A terjesztési pontok elosztási házirend beállítása

Az-elosztási házirend a terjesztési pontok beállítás határozza meg, hogyan eszközök hozzá vannak-e rendelve az IoT-központ. Számos három támogatott elosztási házirendeket. 

1. **Legkisebb mértékű késleltetést**: eszközökhöz a hub alapján a legkisebb mértékű késleltetést, az eszköznek az IoT hubhoz törlődnek.
2. **Terjesztési egyenletesen súlyozott** (alapértelmezett): csatolt IoT-központok valószínűleg egyaránt számukra kiosztott eszközök. Ez az alapértelmezett beállítás. Ha csak egy IoT-központ eszközök kiépíteni, ezzel a beállítással tárolhatja. 
3. **A beléptetési listán keresztül statikus konfigurációs**: a regisztráció listán a kívánt IoT-központ megadását prioritást élvez a terjesztési pontok szintű elosztási házirend.

Kövesse az alábbi lépéseket a elosztási házirend beállításához:

1. A foglalási házirend beállítása, a terjesztési pontok lapon kattintson a **elosztási házirend kezelése**.
2. A foglalási házirend beállítása **egyenletesen súlyozott terjesztési**.
3. Kattintson a **Save** (Mentés) gombra.

## <a name="link-the-new-iot-hub-to-dps"></a>Az új IoT hub csatolása terjesztési pontok

Csatolás a terjesztési pontok és az IoT hub, hogy a terjesztési pontok regisztrálhatja az eszközöket, hogy hubhoz.

1. Az a **összes erőforrás** lapján kattintson a korábban létrehozott terjesztési pontok.
2. A terjesztési pontok lapon kattintson a **csatolt IoT-központok**.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **hivatkozás hozzáadása az IoT hubhoz** lapon, a megfelelő választógomb segítségével meghatározhatja, hogy a csatolt IoT-központ az aktuális előfizetésben, vagy egy másik előfizetésben található. Ezután válassza ki az IoT-központ nevét, a **IoT-központ** mezőbe.
5. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure portál segítségével egy második IoT-központ második eszköz kiépítése 
> * A második eszköz beléptetési lista bejegyzés hozzáadása
> * A terjesztési pontok elosztási házirend beállítása **terjesztési is**
> * Az új IoT hub csatolása terjesztési pontok

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
