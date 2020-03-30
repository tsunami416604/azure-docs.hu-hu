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
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050439"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Ebben a cikkben hozzon létre egy háttérszolgáltatás, amely hozzáadja a kívánt tulajdonságokat egy ikereszközhöz, majd lekérdezi az identitás-beállításjegyzéket, hogy megtalálja az összes olyan jelentett tulajdonsággal rendelkező eszközt, amely ennek megfelelően frissült. A szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére az ikereszköz kívánt tulajdonságainak módosításához, és a rendszerleíró **adatbázis olvasási** engedélyére van szüksége az identitásbeállítás lekérdezéséhez. Nincs olyan alapértelmezett megosztott hozzáférési házirend, amely csak ezt a két engedélyt tartalmazza, ezért létre kell hoznia egyet.
