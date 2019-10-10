---
title: Komplex veszélyforrások elleni védelem Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja a Azure Cosmos DB az inaktív adatok titkosítását és megvalósítását.
author: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 2b12d639e734502113b6afdd7250fca6a520c687
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176024"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Komplex veszélyforrások elleni védelem Azure Cosmos DB

A Azure Cosmos DB komplex veszélyforrások elleni védelme további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel Azure Cosmos DB fiókok eléréséhez vagy kihasználásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére, akár biztonsági szakértő nélkül, akár a központi biztonsági figyelő rendszerekkel való integrációhoz.

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak. Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és szervizelésével kapcsolatos ajánlásokkal együtt.

> [!NOTE]
>
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg csak az SQL API esetében érhető el.
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A biztonsági riasztások teljes körű vizsgálatához javasolt a [diagnosztikai naplózás engedélyezése Azure Cosmos DBban](https://docs.microsoft.com/azure/cosmos-db/logging), amely maga az adatbázison naplózza a műveleteket, beleértve a szifiliszi műveleteket az összes dokumentumon, tárolón és adatbázison.

## <a name="set-up-advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem beállítása

### <a name="set-up-atp-using-the-portal"></a>ATP beállítása a portál használatával

1. Indítsa el a Azure Portalt a következő helyen: [https://portal.azure.com](https://portal.azure.com/).

2. A Azure Cosmos DB fiók **Beállítások** menüjében válassza a **fokozott biztonság**lehetőséget.

    ![ATP beállítása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. A **speciális biztonsági** beállítások panelen:

    * Kattintson a komplex **veszélyforrások elleni védelem** lehetőségre a **beállításához.**
    * Kattintson a **Save (Mentés** ) gombra az új vagy frissített összetett veszélyforrások elleni védelmi szabályzat mentéséhez.   

### <a name="set-up-atp-using-rest-api"></a>ATP beállítása REST API használatával

A REST API-parancsokkal létrehozhat, frissíthet vagy beszerezhet egy adott Azure Cosmos DB fiók komplex veszélyforrások elleni védelmének beállítását.

* [Komplex veszélyforrások elleni védelem – létrehozás](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Komplex veszélyforrások elleni védelem – Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>ATP beállítása Azure PowerShell használatával

Használja a következő PowerShell-parancsmagokat:

* [Komplex veszélyforrások elleni védelem engedélyezése](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Komplex veszélyforrások elleni védelem](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [A komplex veszélyforrások elleni védelem letiltása](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használata

Azure Resource Manager-sablonnal beállíthatja, hogy a rendszer engedélyezve legyen a komplex veszélyforrások elleni védelem Cosmos DB.
További információ: CosmosDB- [fiók létrehozása komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/en-us/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Azure Policy használata

A Cosmos DB a komplex veszélyforrások elleni védelem engedélyezéséhez használjon Azure Policy.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot, és keressen rá a Cosmos db házirend komplex **veszélyforrások elleni védelmének üzembe helyezése** lehetőségre.

    ![Keresési szabályzat](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Kattintson a komplex **veszélyforrások elleni védelem telepítése CosmosDB** házirendre, majd kattintson a **hozzárendelés**elemre.

    ![Előfizetés vagy csoport kiválasztása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. A **hatókör** mezőben kattintson a három pontra, válasszon ki egy Azure-előfizetést vagy erőforráscsoportot, majd kattintson a **kiválasztás**elemre.

    ![Házirend-definíciók lap](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Adja meg a többi paramétert, majd kattintson a **hozzárendelés**elemre.

## <a name="manage-atp-security-alerts"></a>ATP biztonsági riasztások kezelése

Ha Azure Cosmos DB tevékenység anomália történik, a rendszer egy biztonsági riasztást indít el a gyanús biztonsági eseménnyel kapcsolatos információkkal. 

 Azure Security Center az aktuális [biztonsági riasztásokat](../security-center/security-center-alerts-overview.md)áttekintheti és kezelheti.  Kattintson egy adott riasztásra [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) a lehetséges okok és az ajánlott műveletek megtekintéséhez és a lehetséges fenyegetések enyhítéséhez. Az alábbi képen egy példa látható a Security Centerban megadott riasztási adatokra.

 ![Fenyegetés részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

A rendszer a riasztás részleteivel és a javasolt műveletekkel kapcsolatos e-mail-értesítést is küld. Az alábbi képen egy riasztási e-mail látható.

 ![Riasztás részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>ATP-riasztások Cosmos DB

 A Azure Cosmos DB-fiókok figyelése során generált riasztások listájának megtekintéséhez tekintse meg a Security Center dokumentációjának [Cosmos db riasztások](../security-center/security-center-alerts-data-services.md#cosmos-db) című szakaszát.

## <a name="next-steps"></a>Következő lépések

* További információ a [diagnosztikai naplózásról Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
