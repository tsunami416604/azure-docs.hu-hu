---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146523"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Az eseményközpont létrehozása után beolvasni a kapcsolati karakterláncra. Ennek segítségével eszközök és alkalmazások a hubhoz való csatlakozáshoz. 

1. Kattintson a hub az IoT Hub panel beállításokkal látható, és így tovább. Kattintson a **Megosztott elérési szabályzatok** elemre.
   
2. A **Megosztott elérési szabályzatok** alatt válassza ki az **iothubowner** elemet. 

3. A **megosztott hozzáférési kulcsokat**, másolatot a **kapcsolati karakterlánc – elsődleges kulcs** későbbi felhasználás céljából.

    ![Hogyan kérheti le a kapcsolati karakterlánc megjelenítése](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel](../articles/iot-hub/iot-hub-devguide-security.md) foglalkozó részét.
