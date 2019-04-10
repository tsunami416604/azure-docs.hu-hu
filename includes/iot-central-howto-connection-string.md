---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426713"
---
1. Használja a `dps-keygen` parancssori segédprogram egy kapcsolati karakterláncot létrehozni:

    Telepítése a [key generator segédprogram](https://github.com/Azure/dps-keygen), futtassa a következő parancsot:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Hozzon létre egy kapcsolati karakterláncot, futtassa a következő parancsot a korábban feljegyzett kapcsolati adatok használatával:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Másolja a kapcsolati karakterláncot, a `dps-keygen` kimeneti az eszköz-kódjában használandó.