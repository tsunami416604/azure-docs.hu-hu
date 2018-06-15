---
title: Frissítés és az Azure API Management példány méretezése |} Microsoft Docs
description: Ez a témakör ismerteti, hogyan frissítheti, és az Azure API Management példány méretezése.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6ae977344101c02222fd9930e26a083bf5e3f800
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
ms.locfileid: "26658637"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Frissítés és az API Management példány méretezése 

Ügyfelek hozzáadása és eltávolítása egységek által méretezhető API Management (APIM) példánya. A **egység** dedikált Azure-erőforrások össze, és egy bizonyos terhelést hordozó API számos meghívja a havi kifejezett kapacitás. Ez az érték nem felel meg a hívás korlátozása, de ahelyett, hogy a maximális átviteli sebesség az értéket a nyers kapacitástervezés. Tényleges átviteli sebesség és a késleltetés függenek körben száma és az egyidejű kapcsolatok sebessége tényezők, a konfigurált házirendek, a kérelem-válasz méretek és a háttérkiszolgáló késleltetés száma és típusa.

Kapacitás és az egyes egységek ára függ a **réteg** a egység létezik. Négy szintek közül választhat: **fejlesztői**, **alapvető**, **szabványos**, **prémium**. Ha egy réteget egy kézbesítési kapacitásbővítés van szüksége, adja hozzá egy egység. A jelenleg kiválasztott APIM példány réteg nem engedélyezi a további egységeket, ha szüksége, váltson egy magasabb szintű csomagra. 

Az ár tárolóegységekhez és a rendelkezésre álló funkciók (például több területi telepítési) a réteg a APIM példány választott függ. A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk, egység és funkciónak, amelyeket az egyes rétegekbe ára ismerteti. 

>[!NOTE]
>A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a cikk bemutatja egység kapacitás hozzávetőleges számát az egyes rétegek. Ahhoz, hogy pontosabb számok, tekintse meg az API-jainak valós forgatókönyv kell. Az alábbi "Útmutató a kapacitástervezés" című szakaszában talál.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Hogyan lehet kapacitást tervezni?

Tudja meg van-e elegendő számú egység a forgalmat fog kezelni, az várt munkaterhelések vizsgálatára. 

Fent említett egy APIM egység képes a másodpercenkénti kérések számát számos tényező függ. Például a kapcsolat mintát, a kérelem és válasz, a házirendek minden API-kérelmeket küld ügyfelek számára konfigurált méretétől.

Használjon **metrikák** (használt Azure figyelő képességek) ismertetése, mekkora kapacitást szolgál az adott időpontban.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Vizsgálja meg a metrikák az Azure-portál használatával 

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **metrikák**.
3. Válassza ki **kapacitás** a metrika **elérhető**. 

    A kapacitás metrika lehetővé teszi az egyes meghatározni, hogy mekkora az elérhető számítási kapacitás használatban van az Ön bérelt szolgáltatásának. Az érték a számítási erőforrásokat, például a memória, a CPU és a hálózati várólista-hosszúságok meggátolják a bérlő által használt származik. A feldolgozás alatt álló kérelmek számának közvetlen mérték nincs. A bérlő kérelem terhelésének növekedése, és milyen értéket a kapacitás mérőszám felel meg a csúcsterhelés figyelési tesztelheti. Beállíthatja, hogy a mérték riasztások tájékoztatja a felhasználót, amikor valamilyen nem várt történik. Például APIM-példány túllépte a várható kapacitásához több mint 10 percig.

    >[!TIP]
    > Lehetővé teszi, hogy ha a szolgáltatás kevés a kapacitás, vagy hívja az logikai alkalmazás automatikusan méretezési egység felvételével riasztások konfigurálása

## <a name="upgrade-and-scale"></a>A frissítés és a skála 

Ahogy korábban említettük, választhat négy rétegek: **fejlesztői**, **alapvető**, **szabványos** és **prémium**. A **fejlesztői** réteg értékelni a szolgáltatást kell használni; ez nem használható termelési környezetben. A **fejlesztői** réteg nem rendelkezik SLA-t, és a réteg (hozzáadása egységek) nem lehet méretezni. 

**Alapszintű**, **szabványos** és **prémium** éles rétegek, amelyek SLA-t, és megadhat vannak. A **alapvető** réteg a legolcsóbb szint, amely rendelkezik SLA-t, és azok méretezett legfeljebb 2 egység **szabványos** kiterjeszthető legfeljebb négy egységeire. Tetszőleges számú egységeket is hozzáadhat a **prémium** réteg.

A **prémium** réteg lehetővé teszi a kívánt Azure-régiók tetszőleges számú API management egypéldányos szét. Kezdeti létrehozásakor az API Management service, a példány csak egy egységet tartalmaz, és egyetlen Azure-régiót találhatók. A kezdeti régió van kijelölve a **elsődleges** régióban. További régiókban könnyen lehet hozzáadni. Egy régiót hozzáadásakor meg kell adnia a lefoglalni kívánt egységek számát. Például lehet egy egységet a **elsődleges** régió és néhány más régióban öt egység. Könnyebben igazíthatja a forgalomra, minden régióban van egységek száma. További információkért lásd: [üzembe helyezése az Azure API Management szolgáltatáspéldány több Azure-régiók](api-management-howto-deploy-multi-region.md).

Frissítse, és bármely réteg érkező vagy oda irányuló visszaminősítését. Vegye figyelembe, hogy frissítése vagy alacsonyabb verziójúra változtatása távolíthatja el bizonyos szolgáltatásokat – például a Vnetek vagy több régió központi telepítését, ha a prémium szintű rétegtől alapszintű vagy Standard visszaminősítése.

>[!NOTE]
>A frissítés vagy skálája eltarthat 15 45 percig alkalmazni. Ha elkészült értesítést kap.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Az Azure portál segítségével frissítése és méretezése

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **és az árképzés**.
3. Válassza ki a kívánt réteget.
4. Adja meg, hány **egységek** hozzá szeretné adni. A csúszka, vagy írja be a egységek számát.<br/>
    Ha úgy dönt, a **prémium** réteg, először válasszon ki egy régiót.
5. Nyomja le az **mentése**

## <a name="next-steps"></a>Következő lépések

[Az Azure API Management szolgáltatáspéldány üzembe helyezése több Azure-régiók](api-management-howto-deploy-multi-region.md)

