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
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004679"
---
## <a name="enabling-extended-offline-operation-preview"></a>A kiterjesztett offline művelet (előzetes verzió) engedélyezése
Kezdve a [v1.0.2 kiadási](https://aka.ms/edge102) az Edge-futtatókörnyezet, az Edge-eszköz és a hozzá kapcsolódó alsóbb rétegbeli eszközök is konfigurálható a kiterjesztett offline művelet. 

Ezzel a képességgel helyi modulok vagy alsóbb rétegbeli eszközök újból hitelesítheti az Edge-eszköz az igény szerint és kommunikálnak egymással üzenetek és a módszerekkel, még akkor is, ha az IoT Hub kapcsolódik. Ez [blogbejegyzés](https://aka.ms/iot-edge-offline) és [fogalma a cikk](../articles/iot-edge/offline-capabilities.md) további részletek és ez a funkció hatókörét.

Egy átjáró használata esetén a kapcsolat nélküli kiterjesztett engedélyezésének peremhálózati eszköz, és csatlakozni tudjanak hozzá alsóbb rétegbeli eszközök közötti szülő-gyermek kapcsolat létrehozására.

1. A peremhálózati eszköz részleteit tartalmazó panel az IoT Hub portálon, kattintson a **gyermek eszközök felügyelete (előzetes verzió)** gombot a felső parancssávon.

1. Kattintson a **+ Hozzáadás** gombra.

1. Eszközök listájában válassza ki a gyermek eszközöket, és a jobbra mutató nyílra segítségével válassza ki a gyermekek hozzáadni azokat.

1. Kattintson a **OK** megerősítéséhez.

1. Az a **modulok beállítása** a peremhálózati eszköz részletei a képernyő, kattintson a **speciális Edge-futtatókörnyezet-beállítások konfigurálása**, majd a **Edge hubot** környezeti változók bejegyzéshozzáadása **UpstreamProtocol** értékkel **MQTT**. Ugyanaz a környezeti változó hozzáadása és az értékét a **az Edge Agent** is. 

1. Kattintson a **mentése** , és ügyeljen arra, hogy **küldés** kattintás után a változtatások **tovább** kétszer.

A peremhálózati eszköz, és annak gyermek eszközök most már engedélyezve vannak a kiterjesztett offline művelet.  