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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179216"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

A hub létrehozása után olvassa be a hub kapcsolati karakterláncát. Ezzel csatlakoztathatja az eszközöket és alkalmazásokat a hubhoz. 

1. Kattintson a hubra az IoT Hub ablaktáblájának a Beállításokkal és így tovább megtekintéséhez. Kattintson **a Megosztott hozzáférési házirendek elemre.**
   
2. A **Megosztott elérési szabályzatok** alatt válassza ki az **iothubowner** elemet. 

3. A **Megosztott hozzáférési kulcsok csoportban**másolja a Kapcsolat karakterláncot - a később használandó elsődleges **kulcsot.**

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel](../articles/iot-hub/iot-hub-devguide-security.md) foglalkozó részét.
