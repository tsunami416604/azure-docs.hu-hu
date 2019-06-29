---
title: Az Azure Container instance-példány recept
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezhet üzembe a Cognitive Services-tárolók az Azure-Tárolópéldányon
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445820"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Üzembe helyezése és futtatása a tárolót az Azure Container Instance (aci) Szolgáltatásban

Az alábbi lépéseket követve egyszerűen az Azure-ral a felhőben az Azure Cognitive Services-alkalmazások méretezése [Tárolópéldány](https://docs.microsoft.com/azure/container-instances/) (aci Szolgáltatásban). Ez segít az infrastruktúra kezelése helyett az alkalmazások összpontosíthat.

## <a name="prerequisites"></a>Előfeltételek

Ez a megoldás együttműködik egy Cognitive Services-tárolóra. A Cognitive Services-szolgáltatás-erőforrás a recept használata előtt kell létrehozni az Azure Portalon. Minden egyes Cognitive Service tárolókat támogató rendelkezik egy "Telepítése útmutató" a dokumentum kifejezetten a tárolóhoz a szolgáltatás telepítése és konfigurálása. Mivel egyes szolgáltatások megkövetelik egy fájl vagy fájlok a tároló bemeneti adatként, fontos megérteni, és a korábban már használt a tároló sikeresen ezt a megoldást.

* A Cognitive Services erőforrás, az Azure Portalon jött létre.
* A cognitive Services **végponti URL-cím** – tekintse át az adott szolgáltatás "hogyan telepítése" tároló, található, ahol az Azure Portalon, és mi az a végpont URL-címe az URL-cím helyes-e például így néz ki. Formátummal módosíthatja a szolgáltatás a szolgáltatás.
* A cognitive Services **kulcs** – a kulcsokat a rendszer a **kulcsok** oldal az Azure-beli erőforráshoz. Csak akkor kell egyik két kulcsot. A kulcs a 32 alfanumerikus karakterekből álló karakterláncot.
* Egy egyetlen Cognitive Services-tároló a helyi gazdagép (a számítógép). Ellenőrizze, hogy a következőket teheti:
  * Kérje le a rendszerképet egy `docker pull` parancsot.
  * A helyi tároló sikeresen futott az összes szükséges konfigurációs beállításokkal egy `docker run` parancsot.
  * Hívja meg a tároló végpontja, a válasz a 2xx és a egy JSON-válasz visszaállítani.

Csúcsos zárójelpárban van, minden változót `<>`, ki kell cserélni a saját értékeire. Ez a csere magában foglalja a csúcsos zárójeleket.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>A Tárolópéldány használata

1. Válassza ki a **áttekintése** , és másolja az IP-címet. Például egy numerikus IP-cím lesz `55.55.55.55`.
1. Nyisson meg egy új böngészőlapot, és az IP-címet, például használja `http://<IP-address>:5000 (http://55.55.55.55:5000`). Látni fogja a tároló kezdőlapja, amely közli, a tároló fut-e.

1. Válassza ki **szolgáltatás API-t leírása** a swagger-lap a tároló megtekintéséhez.

1. Válassza ki valamelyik a **POST** API-kat, és válassza ki **kipróbálás**.  A paraméterek megjelenik többek között a bemeneti. Adja meg a paramétereket.

1. Válassza ki **Execute** a kérést küldhet a Tárolópéldányt.

    Sikeresen létrehozott és a Cognitive Services-tárolók használt Azure-Tárolópéldányon.
