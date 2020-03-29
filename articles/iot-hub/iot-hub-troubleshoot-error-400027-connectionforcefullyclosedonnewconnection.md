---
title: Az Azure IoT Hub 400027-es hibaesetén A ConnectionForcefullyClosedOnNewConnection
description: A 400027-es hiba kijavítása ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960528"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Ez a cikk a **400027 ConnectionForcefullyClosedOnNewConnection** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az eszközről a felhőbe irányuló ikerművelet (például az olvasás vagy a javítás jelentett tulajdonságainak javítása) vagy a közvetlen metódusmeghívás a **400027**hibakóddal sikertelen.

## <a name="cause"></a>Ok

Egy másik ügyfél új kapcsolatot hozott létre az IoT Hubhoz ugyanazzal a hitelesítő adatokkal, így az IoT Hub bezárta az előző kapcsolatot. Az IoT Hub nem engedélyezi egynél több ügyfél számára, hogy azonos hitelesítő adatokkal csatlakozzon.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy minden ügyfél csatlakozik az IoT Hubhoz a saját identitásával.