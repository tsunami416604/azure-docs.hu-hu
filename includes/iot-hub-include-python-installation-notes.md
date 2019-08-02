---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640455"
---
* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

* **Windows**

    * [Python 2. x vagy 3. x](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).

    * Ha Windows operációs rendszert használ, győződjön meg arról, hogy rendelkezik a [ C++ vizualizáció újraterjeszthető csomagjának](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) megfelelő verziójával, hogy lehetővé tegye a natív DLL-fájlok használatát a Pythonból. Javasoljuk, hogy a legújabb verziót használja.

    * Ha szükséges, telepítse az [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) csomagot a parancs használatával.`pip install azure-iothub-device-client`

    * Ha szükséges, telepítse az [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) csomagot a parancs használatával.`pip install azure-iothub-service-client`

* **Mac OS**

    Mac OS esetében Python 3.7.0 (vagy 2,7) + libboost-1,67 + curl 7.61.1 szükséges (az összes Homebrew-n keresztül települ). Minden más disztribúció/operációs rendszer valószínűleg különböző verziókat ágyaz be a Boost & függőségekhez, amelyek nem fognak működni, és egy ImportError futásidőben fognak eredményezni.

    `azure-iothub-service-client` és `azure-iothub-device-client` rendszerhez a *pip*-csomagok jelenleg csak Windows operációs rendszer alatt érhetőek el. Linux/Mac OS esetén tekintse meg a Linux-és Mac OS-specifikus szakaszt a [fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) a Pythonhoz című témakörben.

> [!NOTE]
> A iothub_client egy mintába való importálásakor több jelentés is érkezett a hibákról. A **ImportError** problémák kezelésével kapcsolatos további információkért tekintse meg a [ImportError problémákkal foglalkozó](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)témakört.
