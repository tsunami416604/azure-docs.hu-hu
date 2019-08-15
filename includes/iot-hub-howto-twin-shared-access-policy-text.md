---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912386"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely hozzáadja a kívánt tulajdonságokat egy eszközhöz, majd lekérdezi az azonosító beállításjegyzékét, hogy megtalálja az összes olyan eszközt, amelynek a jelentett tulajdonságai megfelelően frissültek. A szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére az eszközök kívánt tulajdonságainak módosításához, és a **beállításjegyzék olvasási** engedélyre van szüksége az azonosító beállításjegyzék lekérdezéséhez. Nincs olyan alapértelmezett megosztott hozzáférési szabályzat, amely csak ezt a két engedélyt tartalmazza, ezért létre kell hoznia egyet.
