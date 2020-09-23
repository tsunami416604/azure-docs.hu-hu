---
title: A Azure Cosmos DB API Mongo-verziójának frissítése a MongoDB-fiókhoz
description: A meglévő Azure Cosmos DB API-k MongoDB-protokoll-verziójának frissítése zökkenőmentesen a MongoDB-fiókokhoz
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: lbosq
ms.openlocfilehash: 795b7641ac22373b02ab23126055c080af5e851d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935131"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>A Azure Cosmos DB API-MongoDB-fiók MongoDB-verziójának frissítése

Ez a cikk azt ismerteti, hogyan lehet frissíteni a Azure Cosmos DB API-ját a MongoDB-fiókhoz. A vezetékes protokoll verziójának frissítése után a MongoDB-hez készült Azure Cosmos DB API legújabb funkcióit is használhatja. A frissítési folyamat nem szakítja meg a fiók rendelkezésre állását, és nem használja fel RU/s-t, vagy nem csökkenti az adatbázis kapacitását. Ez a folyamat nem érinti a meglévő és az indexeket.

>[!Note]
> Jelenleg csak a 3,2-es verziójú kiszolgálóval rendelkező fiókokat lehet a 3,6-es verzióra frissíteni. Ha a fiókja nem jeleníti meg a frissítési lehetőséget, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Frissítés a 3,2-es verzióról 3,6-re

### <a name="benefits-of-upgrading-to-version-36"></a>A 3.6-os verzióra való frissítés előnyei

A 3,6-es verzióban található új funkciók a következők:
- Jobb teljesítmény és stabilitás
- Új adatbázisparancsok támogatása
- Összesítési folyamat támogatása alapértelmezés szerint és új összesítési szakaszok
- Adatfolyam-változások támogatása
- Összetett indexek támogatása
- Több partíciós támogatás a következő műveletekhez: frissítés, törlés, Darabszám és rendezés
- Továbbfejlesztett teljesítmény a következő összesített műveletekhez: $count, $skip, $limit és $group
- A helyettesítő karakteres indexelés mostantól támogatott

### <a name="changes-from-version-32"></a>Az 3,2-es verzió változásai

- **A RequestRateIsLarge hibák el lettek távolítva**. Az ügyfélalkalmazás kérelmeinek száma többé nem ad vissza 16500 hibát. Ehelyett a kérelmek mindaddig folytatódnak, amíg befejeződik, vagy nem teljesítik az időkorlátot.
- A kérelmek időtúllépése 60 másodpercre van állítva.
- Az új huzal protokoll verziójában létrehozott MongoDB-gyűjtemények esetében alapértelmezés szerint csak a `_id` tulajdonság lesz indexelve.

### <a name="action-required"></a>Szükséges beavatkozás

A 3,6-es verzióra való frissítéshez az adatbázis-fiók végpontjának utótagja a következő formátumban lesz frissítve:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

A meglévő végpontot le kell cserélnie az alkalmazásokban és az ezzel az adatbázis-fiókkal csatlakozó illesztőprogramokat. **Csak az új végpontot használó kapcsolatok férhetnek hozzá a MongoDB 3,6-es verziójában lévő funkciókhoz**. Az előző végpontnak utótaggal kell rendelkeznie `.documents.azure.com` .

>[!Note]
> Ez a végpont némileg eltérő lehet, ha a fiókja egy szuverén, kormányzati vagy korlátozott Azure-felhőben lett létrehozva.

### <a name="how-to-upgrade"></a>A frissítés módja

1. Először nyissa meg a Azure Portal, és navigáljon a MongoDB-fiók áttekintés paneljének Azure Cosmos DB API-hoz. Ellenőrizze, hogy a kiszolgáló verziója a-e `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure Portal MongoDB-fiókkal – áttekintés" border="false":::

2. A bal oldali beállítások közül válassza ki a panelt `Features` . Ez az adatbázis-fiókhoz elérhető fiók szintű funkciókat mutatja be.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal MongoDB-fiókkal – áttekintés a funkciók panel kiemelésével" border="false":::

3. Kattintson a `Upgrade to Mongo server version 3.6` sorra. Ha nem látja ezt a lehetőséget, előfordulhat, hogy a fiókja nem jogosult erre a frissítésre. Ha ez a helyzet, küldjön [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Funkciók panel a beállításokkal." border="false":::

4. Tekintse át az adott frissítéssel kapcsolatban megjelenő információkat. Vegye figyelembe, hogy a frissítés csak akkor lesz végrehajtva, ha az alkalmazások a frissített végpontot használják, az ebben a szakaszban leírtak szerint. Kattintson a gombra, amint `Enable` készen áll a folyamat elindítására.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Bővített frissítési útmutató." border="false":::

5. A folyamat elindítása után a `Features` menü megjeleníti a frissítés állapotát. Az állapot a, a `Pending` és a `In Progress` értékre fog esni `Upgraded` . Ez a folyamat nem befolyásolja az adatbázis-fiók meglévő funkcióit vagy műveleteit.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="A frissítés állapota az indítás után." border="false":::

6. A frissítés befejezése után az állapot a következő lesz: `Upgraded` . Kattintson ide, ha többet szeretne megtudni a folyamat véglegesítéséhez szükséges következő lépésekről és műveletekről. Ha hiba történt a kérelem feldolgozása során, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/en-us/support/create-ticket/) .

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="A fiók frissített állapota." border="false":::

7. Az **adatbázis-fiók frissített verziójának használatának megkezdéséhez**lépjen vissza a panelre `Overview` , és másolja az alkalmazásban használandó új kapcsolódási karakterláncot. Az alkalmazások az új végponthoz való kapcsolódáskor azonnal elkezdik használni a frissített verziót. A meglévő kapcsolatok nem lesznek megszakítva, és a szolgáltatás kényelmesen frissíthető. Az egységes élmény biztosítása érdekében minden alkalmazásnak az új végpontot kell használnia.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Új áttekintés panel." border="false":::

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg az MongoDB 3,6-es [verziójának](mongodb-feature-support-36.md)támogatott és nem támogatott szolgáltatásaival.
- További információ: [Mongo 3,6 version features](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
