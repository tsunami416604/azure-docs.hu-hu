---
title: fájl belefoglalása
description: az IOT edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264044"
---
## <a name="enabling-extended-offline-operation-preview"></a>A kiterjesztett offline művelet (előzetes verzió) engedélyezése
Az a [v1.0.4 kiadási](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) az Edge-futtatókörnyezet, az Edge-eszköz és a hozzá kapcsolódó alsóbb rétegbeli eszközök is konfigurálható a kiterjesztett offline művelet. 

Ezzel a képességgel helyi modulok vagy alsóbb rétegbeli eszközök újból hitelesítheti az Edge-eszköz az igény szerint és kommunikálnak egymással üzenetek és a módszerekkel, még akkor is, ha az IoT Hub kapcsolódik. Ez [blogbejegyzés](https://aka.ms/iot-edge-offline) és [fogalma a cikk](../articles/iot-edge/offline-capabilities.md) további részletek és ez a funkció hatókörét.

Egy átjáró használata esetén a kapcsolat nélküli kiterjesztett engedélyezésének peremhálózati eszköz, és csatlakozni tudjanak hozzá alsóbb rétegbeli eszközök közötti szülő-gyermek kapcsolat létrehozására.

1. A peremhálózati eszköz részleteit tartalmazó panel az IoT Hub portálon, kattintson a **gyermek eszközök felügyelete (előzetes verzió)** gombot a felső parancssávon.

1. Kattintson a **+ Hozzáadás** gombra.

1. Eszközök listájában válassza ki a gyermek eszközöket, és a jobbra mutató nyílra segítségével válassza ki a gyermekek hozzáadni azokat.

1. Kattintson a **OK** megerősítéséhez.

A peremhálózati eszköz, és annak gyermek eszközök most már engedélyezve vannak a kiterjesztett offline művelet.  