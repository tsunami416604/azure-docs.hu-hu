---
title: "Frissítés és az Azure API Management példány méretezése |} Microsoft Docs"
description: "Ez a témakör ismerteti, hogyan frissítheti, és az Azure API Management példány méretezése."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 22cc917eb6f296724bf535e48b0dd6ba8927e5d3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Frissítés és az API Management példány méretezése 

Ügyfelek hozzáadása és eltávolítása egységek által méretezhető API Management (APIM) példánya. A **egység** dedikált Azure-erőforrások áll, és egy bizonyos betöltési-hatással API számos meghívja a havi kifejezett kapacitás. Tényleges átviteli sebesség és a késleltetés körben függően változhatnak száma és az egyidejű kapcsolatok sebessége tényezők, típusa és beállított házirendek, a kérelem és a válasz méretek és a háttérkiszolgáló késleltetés száma. 

Kapacitás és az egyes egységek ára függ a **réteg** a egység létezik. Három réteg közül választhat: **fejlesztői**, **szabványos**, **prémium**. Ha egy réteget egy kézbesítési kapacitásbővítés van szüksége, adja hozzá egy egység. A jelenleg kiválasztott APIM példány réteg nem engedélyezi a további egységeket, ha szüksége, váltson egy magasabb szintű csomagra. 

Az ár, az egyes egységek hozzáadása egységek, függetlenül attól, hogy bizonyos funkciók (például a több területi központi telepítés) lehetőséget a réteg a APIM példány választott függ. A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk, azt ismerteti, milyen ára egység és a szolgáltatások beolvasása minden egyes rétegben. 

>[!NOTE]
>A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a cikk bemutatja egység kapacitás hozzávetőleges számát az egyes rétegek. Ahhoz, hogy pontosabb számok, tekintse meg az API-jainak valós forgatókönyv kell. Az alábbi "Útmutató a kapacitástervezés" című szakaszában talál.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Hogyan lehet kapacitást tervezni?

Tudja meg van-e elegendő számú egység a forgalmat fog kezelni, az várt munkaterhelések vizsgálatára. 

Második APIM másodpercenkénti kérések számát fent említett is folyamat függ sok változók. Például a kapcsolat mintát, a kérelem és válasz, a házirendek minden API-kérelmeket küld ügyfelek számára konfigurált méretétől.

Használjon **metrikák** (használt Azure figyelő képességek) ismertetése, mekkora kapacitást szolgál az adott időpontban.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Vizsgálja meg a metrikák az Azure-portál használatával 

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **metrikák**.
3. Válassza ki **kapacitás** a metrika **elérhető**. 

    A kapacitás metrika lehetővé teszi az egyes meghatározni, hogy mekkora kapacitást használja az Ön bérelt szolgáltatásának. Tegyen további tesztelheti, és több betölteni, és megtudhatja, mi a kivételezési terhelését. Beállíthatja, hogy a mérték riasztások tájékoztatja a felhasználót, amikor valamilyen nem várt történik. A APIM példányát például több mint 5 percig kapacitás csatlakozik. 

    >[!TIP]
    > Beállíthatja, hogy lehetővé teszi, hogy ismeri a kevés kapacitása, vagy ellenőrizze a szolgáltatás azt hívás esetén, amely automatikusan átméretezi a egység felvételével logikai alkalmazás a riasztás...

## <a name="upgrade-and-scale"></a>A frissítés és a skála 

Ahogy korábban említettük, választhat három réteg: **fejlesztői**, **szabványos**, **prémium**. A **fejlesztői** réteg értékelni a szolgáltatást kell használni; ez nem használható termelési környezetben. A **fejlesztői** réteg nem rendelkezik SLA-t, és a réteg (hozzáadása egységek) nem lehet méretezni. 

**Standard** és **prémium** éles rétegek rendelkezik SLA-t, és megadhat. A **szabványos** kiterjeszthető legfeljebb négy egységeire. Tetszőleges számú egységeket is hozzáadhat a **prémium** réteg. 

A **prémium** réteg lehetővé teszi a kívánt Azure-régiók tetszőleges számú API management egypéldányos szét. Kezdeti létrehozásakor az API Management service, a példány csak egy egységet tartalmaz, és egyetlen Azure-régiót találhatók. A kezdeti régió van kijelölve a **elsődleges** régióban. További régiókban könnyen lehet hozzáadni. Egy régiót hozzáadásakor meg kell adnia a lefoglalni kívánt egységek számát. Például lehet egy egységet a **elsődleges** régió és néhány más régióban öt egység. Átalakítható minden régióban van bármilyen forgalmat. További információkért lásd: [üzembe helyezése az Azure API Management szolgáltatáspéldány több Azure-régiók](api-management-howto-deploy-multi-region.md).

Frissítse, és bármely réteg érkező vagy oda irányuló visszaminősítését. Vegye figyelembe, hogy alacsonyabb verziójúra változtatása eltávolíthat néhány funkció, például a Vnetek vagy több régió központi telepítését, ha a Standard a prémium tarifacsomagra alacsonyabb verziójúra változtatása.

>[!NOTE]
>A frissítés vagy skálája eltarthat 15 – 30 percet alkalmazni. Ha elkészült értesítést kap.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Az Azure portál segítségével frissítése és méretezése

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **és az árképzés**.
3. Válassza ki a kívánt réteget.
4. Adja meg, hány **egységek** hozzá szeretné adni. A csúszka, vagy írja be a egységek számát.<br/>
    Ha úgy dönt, a **prémium** réteg, először válasszon ki egy régiót.
5. Nyomja le az **mentése**

## <a name="next-steps"></a>Következő lépések

[Az Azure API Management szolgáltatáspéldány üzembe helyezése több Azure-régiók](api-management-howto-deploy-multi-region.md)

