---
title: Az Azure Container instance-példány recept
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezhet üzembe a Cognitive Services-tárolók az Azure-Tárolópéldányon
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207490"
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

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>2\. lépés: Indítsa el a tároló az Azure Container Instances (ACI)

**Az Azure Container Instance (ACI) erőforrás létrehozásához.**

1. Nyissa meg a [létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) Container Instances lapot.

1. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Oldal|Beállítás|Érték|
    |--|--|--|
    |Alapvető beállítások|Előfizetés|Válassza ki előfizetését.|
    |Alapvető beállítások|Erőforráscsoport|Válassza ki az elérhető erőforráscsoportot vagy hozzon létre például egy új `cognitive-services`.|
    |Alapvető beállítások|Tárolónév|Írjon be egy nevet, például `cognitive-container-instance`. Ez a név alacsonyabb nagybetűs kell lennie.|
    |Alapvető beállítások|Location egység|Válassza ki a régiót, üzembe helyezéshez.|
    |Alapvető beállítások|Lemezkép típusa|`Public`|
    |Alapvető beállítások|Kép neve|Adja meg a Cognitive Services-tároló helyét. Ez lehet ugyanazon a helyen használt a `docker pull` parancs _például_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Alapvető beállítások|Operációs rendszer típusa|`Linux`|
    |Alapvető beállítások|Méret|A javasolt javaslatokat az adott a Cognitive Services-tároló méretének módosítása.:<br>2 mag<br>4 GB
    ||||
  
1. Az a **hálózatkezelés** lapra, adja meg a következő adatokat:

    |Oldal|Beállítás|Érték|
    |--|--|--|
    |Hálózat|Portok|Szerkessze a meglévő portot a TCP `80` való `5000`. Ez azt jelenti, hogy a tároló 5000-es porton lévő adatokhoz hozzáférést biztosító.|
    ||||

1. Az a **speciális** lapra, adja meg a szükséges beállításokat a Tárolópéldány erőforrás számlázási tároló áthaladását a következő adatokat:

    |Speciális lap kulcs|Speciális lap érték|
    |--|--|
    |`apikey`|Átmásolja a **kulcsok** az erőforrás oldalán. Csak az egyik a két kulcs van szüksége. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Átmásolja a **áttekintése** az erőforrás oldalán. |
    |`eula`|`accept`|

    Ha a tárolón a konfigurációs beállításait, például csatlakoztatása bemeneti, kimeneti csatlakoztatása vagy naplózási, ezeket a beállításokat is hozzá kell adni.

1. Válassza ki **ellenőrzés és létrehozás**.
1. Ellenőrzés elvégzése után válassza **létrehozás** a létrehozási folyamat befejezéséhez.
1. Válassza a harang ikont a felső navigációs. Ez az az értesítési ablakot. Ekkor megjelenik egy kék **erőforrás megnyitása** gombra az erőforrás létrehozásakor. Válassza ki ezt a gombot választva nyissa meg az új erőforrás.

## <a name="use-the-container-instance"></a>A Tárolópéldány használata

1. Válassza ki a **áttekintése** , és másolja az IP-címet. Például egy numerikus IP-cím lesz `55.55.55.55`.
1. Nyisson meg egy új böngészőlapot, és az IP-címet, például használja `http://<IP-address>:5000 (http://55.55.55.55:5000`). Látni fogja a tároló kezdőlapja, amely közli, a tároló fut-e.

1. Válassza ki **szolgáltatás API-t leírása** a swagger-lap a tároló megtekintéséhez.

1. Válassza ki valamelyik a **POST** API-kat, és válassza ki **kipróbálás**.  A paraméterek megjelenik többek között a bemeneti. Adja meg a paramétereket.

1. Válassza ki **Execute** a kérést küldhet a Tárolópéldányt.

    Sikeresen létrehozott és a Cognitive Services-tárolók használt Azure-Tárolópéldányon.
