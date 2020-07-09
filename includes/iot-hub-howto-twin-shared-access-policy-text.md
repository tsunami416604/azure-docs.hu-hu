---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70050439"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely hozzáadja a kívánt tulajdonságokat egy eszközhöz, majd lekérdezi az Identity registryt, hogy megtalálja az összes olyan eszközt, amelynek a jelentett tulajdonságai megfelelően frissültek. A szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére az eszközök kívánt tulajdonságainak módosításához, és a **beállításjegyzék olvasási** engedélyre van szüksége az azonosító beállításjegyzék lekérdezéséhez. Nincs olyan alapértelmezett megosztott hozzáférési szabályzat, amely csak ezt a két engedélyt tartalmazza, ezért létre kell hoznia egyet.
