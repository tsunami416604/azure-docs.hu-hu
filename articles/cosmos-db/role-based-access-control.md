---
title: Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB az Azure Active Directory-integráció
description: Ismerje meg, hogyan nyújt az Azure Cosmos DB a Active directory-integráció (RBAC) az adatbázis-védelme.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243516"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-hez

Az Azure Cosmos DB beépített szerepköralapú hozzáférés-vezérlés (RBAC) biztosít a gyakori felügyeleti forgatókönyvek az Azure Cosmos DB-ben. Olyan személy, akinek profilt tartalmaz az Azure Active Directoryban ezen RBAC-szerepkörök rendelhet a felhasználók, csoportok, az egyszerű szolgáltatások, vagy felügyelt identitások engedélyezése vagy megtagadása műveletek az Azure Cosmos DB-erőforrásokat és erőforrásaihoz való hozzáférés. Szerepkör-hozzárendelések hatóköre vezérlősík hozzáférés csak, amely magában foglalja a hozzáférést az Azure Cosmos-fiókok, adatbázisok, tárolók, és (teljesítmény) kínál.

## <a name="built-in-roles"></a>Beépített szerepkörök

Az Azure Cosmos DB által támogatott beépített szerepkörök a következők:

|**Beépített szerepkör**  |**Leírás**  |
|---------|---------|
|[A DocumentDB-fiókok Közreműködője](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Kezelheti az Azure Cosmos DB-fiókokhoz.  |
|[A cosmos DB-fiók olvasói](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Olvashatja az Azure Cosmos DB-fiókja adatait.        |
|[Cosmos biztonságimásolat-felelős](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Egy Azure Cosmos-adatbázis vagy egy tárolót a visszaállítási kérelem is nyújt.       |
|[A cosmos DB-operátor](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Azure Cosmos fiókok, adatbázisok és tárolókat építhet, de nem tud hozzáférni a kulcsokat, amelyekre szükség van az adatok eléréséhez.         |

> [!IMPORTANT]
> Az RBAC-támogatás az Azure Cosmos DB vezérlési síkjával végzett műveletek vonatkozik. Adatok síkjával végzett műveletek biztonságosak főkulcsok vagy erőforrás-jogkivonatokat. További tudnivalókért lásd: [biztonságos hozzáférés az adatokhoz az Azure Cosmos DB-ben](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitás- és hozzáférés-felügyelet (IAM)

A **hozzáférés-vezérlés (IAM)** panel az Azure Portal szerepköralapú hozzáférés-vezérlés az Azure Cosmos-erőforrások konfigurálására szolgál. A szerepkörök, felhasználók, csoportok, szolgáltatásnevek és az Active Directory felügyelt identitások érvényesek. Felhasználók és csoportok beépített szerepkörökkel vagy egyéni szerepkörök használható. Az alábbi képernyőfelvételen az Active Directory-integráció (RBAC) hozzáférés-vezérlés (IAM) használatával az Azure Portalon:

![Hozzáférés-vezérlés (IAM) bemutatására adatbázis-biztonság – az Azure Portalon](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Egyéni szerepkörök

A beépített szerepkörök mellett felhasználók is létrehozhat [egyéni szerepkörök](../role-based-access-control/custom-roles.md) az Azure-ban és a alkalmazni ezeket a szerepköröket az egyszerű szolgáltatások az Active Directory-bérlőn belül az összes előfizetés. Egyéni szerepkörök RBAC szerepkör-definíciók létrehozása egyéni erőforrás más-szolgáltatói műveletek lehetőséget biztosíthat a felhasználóknak. Megtudhatja, hogy mely műveletek érhetők el, amellyel Azure Cosmos DB-hez lásd, egyéni szerepkörök [Azure Cosmos DB erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>További lépések

- [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások](../role-based-access-control/overview.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
- [Az Azure Cosmos DB erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
