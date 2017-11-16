---
title: "Az Azure Machine Learning modell rendszerbe állítása Azure IoT peremhálózati eszköz |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan telepíthetők Azure Machine Learning modellek Azure IoT peremeszközök."
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 924766aee7486f0cf5006dd89d5d77b83753833b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Központi telepítése, és Azure IoT peremhálózati eszköz a modell Azure Machine Learning

Docker-alapú webszolgáltatásként indexelése Azure Machine Learning modellek is Azure IoT peremeszközök futtathatja. További parancsfájlok és utasítások megtalálhatók a [Azure IoT peremhálózati AI eszköztára](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Azok a modell
Üzembe helyezheti modelljét utasításait követve [Azure Machine Learning modell felügyeleti webes szolgáltatástelepítés](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy) Docker lemezkép létrehozása a modell.

## <a name="deploy-to-azure-iot-edge"></a>Az Azure IoT peremhálózati telepítése
Az Azure IoT peremhálózati felhő elemzés és üzleti logika áthelyezése eszközök. Minden gépi tanulási modellek IoT peremeszközök is futtathatók. IoT peremhálózati eszköz beállítását, és hozzon létre egy központi telepítési dokumentációjában található [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc).

Az alábbiakban további ügyeljen a következőkre.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Adja hozzá a peremhálózati eszköz a peremhálózati runtime beállításjegyzék hitelesítő adatokat
A számítógépen, ahol az IoT-Edge, amelyen vegye fel a hitelesítő adatokat, a beállításjegyzék, a futtatókörnyezet is hozzáférhet a tároló lekéréses.

A Windows a következő parancsot:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
A Linux a következő parancsot:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>A Machine Learning tároló kép keresése
A Machine Learning-tároló lemezkép helyét van szüksége. A tároló kép keresése:

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com/).
2. Az a **Azure tároló beállításjegyzék**, válassza ki a megvizsgálni kívánt beállításjegyzék.
3. Kattintson a beállításjegyzékben **Tárházak** a tárolóhelyekkel és a képek listájának megjelenítéséhez.













