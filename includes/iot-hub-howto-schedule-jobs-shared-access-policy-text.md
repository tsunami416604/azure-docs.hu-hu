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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402660"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Ebben a cikkben egy olyan háttér-szolgáltatást hoz létre, amely egy adott eszköz közvetlen metódusának meghívásához ütemez egy feladatot, és az egyes feladatok állapotát figyeli. Ezeknek a műveleteknek a végrehajtásához a szolgáltatásnak szüksége van a **beállításjegyzék olvasási** és **beállításjegyzék-írási** engedélyeire. Alapértelmezés szerint minden IoT Hub egy **registryReadWrite** nevű megosztott hozzáférési házirenddel jön létre, amely megadja ezeket az engedélyeket.
