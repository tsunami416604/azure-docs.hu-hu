---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403948"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely hozzáadja a kívánt tulajdonságokat egy eszközhöz, majd lekérdezi az azonosító beállításjegyzékét, hogy megtalálja az összes olyan eszközt, amelynek a jelentett tulajdonságai megfelelően frissültek. A szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére az eszközök kívánt tulajdonságainak módosításához, és a **beállításjegyzék olvasási** engedélyre van szüksége az azonosító beállításjegyzék lekérdezéséhez. Nincs olyan alapértelmezett megosztott hozzáférési szabályzat, amely csak ezt a két engedélyt tartalmazza, ezért létre kell hoznia egyet.
