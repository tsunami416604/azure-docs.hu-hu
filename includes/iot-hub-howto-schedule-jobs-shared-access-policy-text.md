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
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049049"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Ebben a cikkben hozzon létre egy háttérszolgáltatás, amely ütemezi a feladatot, hogy egy közvetlen metódus t egy eszközön, ütemezése feladat az eszköz iker, és figyeli az egyes feladat előrehaladását. A műveletek végrehajtásához a szolgáltatásnak szüksége van a **rendszerleíró adatbázis olvasási** és **beállításjegyzék-írási** engedélyeire. Alapértelmezés szerint minden IoT hub jön létre egy megosztott hozzáférési szabályzat nevű **registryReadWrite,** amely megadja ezeket az engedélyeket.
