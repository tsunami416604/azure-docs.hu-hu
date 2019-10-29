---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk összefoglalja, hogyan segíti az Azure IoT Central az eszközök csatlakoztatását és kifogástalan állapotba lépését.
author: aaronbjork
ms.author: abjork
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 23e9feea49f10a2fbc27961813cf02a4993467e9
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991988"
---
# <a name="stay-connected-with-azure-iot-central-preview-features"></a>Az Azure IoT Central (előzetes verzió) szolgáltatással kapcsolatban

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk áttekintést nyújt arról, hogyan segíti az Azure IoT Central az eszközök csatlakoztatását és kifogástalan állapotát.

A nagy léptékű működésre tervezett IoT-megoldásokkal fontos a jól strukturált megközelítés az eszközkezelés terén. Nem elég ahhoz, hogy az eszközök "csatlakoztatva" legyenek az eszközhöz, egy olyan megközelítésre van szükség, amely megtartja az eszközök csatlakozását és kifogástalan állapotát, miközben a megoldás fejlődik, nő és korosztály. Az Azure IoT Central biztosítja a szükséges képességeket annak biztosításához, hogy a IoT-megoldás eszközei jól törődnek az alkalmazás életciklusa során.

## <a name="dashboards"></a>Irányítópultok 
A beépített [irányítópultok](howto-manage-devices.md#import-devices) testreszabható felületet biztosítanak az eszközök állapotának és telemetria monitorozásához. Kezdjen el egy előre elkészített irányítópulttal egy [alkalmazás sablonból](howto-use-app-templates-pnp.md) , vagy hozzon létre saját irányítópultokat az alkalmazás igényeinek megfelelően. Az irányítópultok az alkalmazás összes felhasználójára megoszthatók, vagy magánjellegűek is tarthatók.

## <a name="rules-and-actions"></a>Szabályok és műveletek 
Az eszközök állapotának és telemetria alapuló [Egyéni szabályok](howto-create-event-rules.md) létrehozásával gyorsan azonosíthatja a figyelmet igénylő eszközöket. Konfigurálja azokat a [műveleteket](howto-create-event-rules.md#configure-actions) , amelyek értesítik a megfelelő személyeket arról, hogy a javító intézkedések időben történnek.

## <a name="jobs"></a>Feladatok 
A [feladatok](howto-run-a-job.md) lehetővé teszik az eszközök tulajdonságainak, beállításainak és parancsainak egyszeri vagy tömeges frissítését. 

## <a name="user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek
A [szerepkörök és engedélyek](howto-manage-users-roles-pnp.md) lehetővé teszi az egyes felhasználói élmények testreszabását. A szerepköröket közvetlenül a felhasználói felületen hozhatja létre, és egyszerűen hozzárendelheti a megfelelő engedélyeket. 




