---
title: Komplex veszélyforrások elleni védelem az Azure Cosmos DB-hez
description: Ismerje meg, hogy az Azure Cosmos DB hogyan biztosítja az inaktív adatok titkosítását, és hogyan valósítható meg.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614835"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Komplex veszélyforrások elleni védelem az Azure Cosmos DB számára (előzetes verzió)

Az Azure Cosmos DB komplex veszélyforrások elleni védelem egy további biztonsági intelligenciaréteget biztosít, amely észleli az Azure Cosmos DB-fiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül is, hogy biztonsági szakértő lenne, és integrálja azokat a központi biztonsági felügyeleti rendszerekkel.

A biztonsági riasztások akkor aktiválódnak, ha tevékenységi rendellenességek lépnek fel. Ezek a biztonsági riasztások integrálva vannak [az Azure Security Centerrel,](https://azure.microsoft.com/services/security-center/)és e-mailben is elküldésre kerülnek az előfizetés-rendszergazdáknak, a gyanús tevékenység részleteivel és a fenyegetések kivizsgálásával és elhárításával kapcsolatos javaslatokkal.

> [!NOTE]
>
> * Az Azure Cosmos DB komplex veszélyforrások elleni védelem jelenleg csak az SQL API-hoz érhető el.
> * Az Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg nem érhető el az Azure kormányzati és a szuverén felhőrégióiban.

A biztonsági riasztások teljes körű vizsgálati tapasztalata érdekében azt javasoljuk, hogy engedélyezzük a diagnosztikai naplózást az [Azure Cosmos DB-ben,](https://docs.microsoft.com/azure/cosmos-db/logging)amely naplózza a műveleteket az adatbázisban, beleértve a CRUD-műveleteket az összes dokumentumon, tárolón és adatbázison.

## <a name="threat-types"></a>Fenyegetéstípusok

Az Azure Cosmos DB komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérleteket jeleznek. Jelenleg a következő riasztásokat indíthatja el:

- **Hozzáférés szokatlan helyekről:** Ez a riasztás akkor aktiválódik, ha egy Azure Cosmos-fiók hozzáférési mintájában megváltozik, ahol valaki szokatlan földrajzi helyről csatlakozott az Azure Cosmos DB-végponthoz. Bizonyos esetekben a riasztás észleli a jogos művelet, azaz egy új alkalmazás vagy fejlesztő karbantartási művelet. Más esetekben a riasztás rosszindulatú műveletet észlel egy korábbi alkalmazotttól, külső támadótól stb.

- **Szokatlan adatkinyerés:** Ez a riasztás akkor aktiválódik, ha egy ügyfél szokatlan mennyiségű adatot nyer ki egy Azure Cosmos DB-fiókból. Ez lehet a tünete néhány adat kiszivárgásvégzett át a fiókban tárolt összes adatot egy külső adattárba.

## <a name="set-up-advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem beállítása

### <a name="set-up-atp-using-the-portal"></a>Az ATP beállítása a portál használatával

1. Indítsa el az [https://portal.azure.com](https://portal.azure.com/)Azure Portalt a helyen.

2. Az Azure Cosmos DB-fiókjában a **Beállítások** menüben válassza a **Speciális biztonság parancsot.**

    ![Az ATP beállítása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. A **Speciális biztonsági** konfiguráció panelen:

    * Kattintson a **Speciális veszélyforrások elleni védelem** beállításra, ha **be**szeretné állítani.
    * Az új vagy frissített Advanced Threat Protection-szabályzat mentéséhez kattintson a **Mentés** lehetőségre.   

### <a name="set-up-atp-using-rest-api"></a>Atp beállítása REST API-val

A Rest API-parancsok használatával hozzon létre, frissítse vagy szerezzen be egy adott Azure Cosmos DB-fiók komplex veszélyforrások elleni védelmi beállítást.

* [Komplex veszélyforrások elleni védelem – létrehozás](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Komplex veszélyforrások elleni védelem - Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Az ATP beállítása az Azure PowerShell használatával

Használja a következő PowerShell-parancsmagokat:

* [A Komplex veszélyforrások elleni védelem engedélyezése](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Speciális veszélyforrások elleni védelem](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Irányított veszélyforrások elleni védelem letiltása](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

Azure Resource Manager-sablon használatával állítsa be a Cosmos DB-t, ha a komplex veszélyforrások elleni védelem engedélyezve van.
További információ: [Create a CosmosDB Account with Advanced Threat Protection (CosmosDB-fiók létrehozása speciális veszélyforrások elleni védelemmel).](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)

### <a name="using-azure-policy"></a>Az Azure-szabályzat használata

Azure-szabályzat használatával engedélyezze a Cosmos DB komplex veszélyforrások elleni védelmét.

1. Indítsa el az Azure **Policy - Definíciók** lapot, és keresse meg a Központi veszélyforrások elleni védelem üzembe helyezése a **Cosmos DB-szabályzatot.**

    ![Keresési szabályzat](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Kattintson a **CosmosDB speciális veszélyforrások elleni védelmének telepítésére,** majd a **Hozzárendelés gombra.**

    ![Előfizetés vagy csoport kiválasztása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. A **Hatókör mezőben** kattintson a három elemre, válasszon ki egy Azure-előfizetést vagy erőforráscsoportot, majd kattintson a **Kijelölés gombra.**

    ![Házirend-definíciók lap](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Írja be a többi paramétert, majd kattintson **a Hozzárendelés gombra.**

## <a name="manage-atp-security-alerts"></a>Az ATP biztonsági riasztásainak kezelése

Amikor az Azure Cosmos DB tevékenységa anomáliák fordulnak elő, egy biztonsági riasztás aktiválódik a gyanús biztonsági esemény információkat. 

 Az Azure Security Center ben áttekintheti és kezelheti az aktuális [biztonsági riasztásokat.](../security-center/security-center-alerts-overview.md)  Kattintson egy adott riasztásra a [Security Centerben](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) a lehetséges okok és a potenciális fenyegetés kivizsgálására és csökkentésére javasolt műveletek megtekintéséhez. Az alábbi képen egy példa a biztonsági központban megadott riasztási részletekre mutat be.

 ![Fenyegetés részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

A rendszer e-mailértesítést is küld a riasztás részleteivel és az ajánlott műveletekkel. Az alábbi képen egy figyelmeztető e-mail látható.

 ![Riasztás részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP riasztások

 Az Azure Cosmos DB-fiókok figyelésekén létrehozott riasztások listájának megtekintéséhez tekintse meg a [Cosmos DB riasztások](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) szakaszaz Azure Security Center dokumentációjában.

## <a name="next-steps"></a>További lépések

* További információ a [diagnosztikai naplózásról az Azure Cosmos DB-ben](cosmosdb-monitor-resource-logs.md)
* További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
