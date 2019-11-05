---
title: Az Azure Data Share hibaelhárítása
description: Megtudhatja, hogyan lehet elhárítani a meghívásokkal és hibákkal kapcsolatos problémákat az Azure-beli adatmegosztások létrehozásakor vagy fogadásakor.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490564"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Az Azure-adatmegosztás gyakori problémáinak elhárítása 

Ez a cikk bemutatja, hogyan lehet elhárítani az Azure-adatmegosztással kapcsolatos gyakori problémákat. 

## <a name="azure-data-share-invitations"></a>Azure-adatmegosztási meghívások 

Bizonyos esetekben, amikor egy új felhasználó az elküldött e-mailes meghívóból kattint a **meghívó elfogadására** , a meghívókat tartalmazó üres lista jelenik meg. 

![Nincsenek meghívók](media/no-invites.png)

A fenti hiba a szolgáltatás ismert problémája, és jelenleg zajlik. Áthidaló megoldásként kövesse az alábbi lépéseket. 

1. A Azure Portal navigáljon az **előfizetések** elemre.
1. Válassza ki az Azure-beli adatmegosztáshoz használt előfizetést
1. Kattintson az **erőforrás-szolgáltatók** elemre
1. A Microsoft. DataShare keresése
1. Kattintson a **regisztráció** elemre.

A lépések végrehajtásához az [Azure KÖZREMŰKÖDŐI RBAC szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) kell rendelkeznie. 

Ha továbbra sem látja az adatmegosztási meghívót, forduljon az adatszolgáltatóhoz, és győződjön meg arról, hogy elküldte a meghívót az Azure-beli bejelentkezési e-mail-címre, és *nem* az e-mail aliasát. 

> [!IMPORTANT]
> Ha már elfogadta az Azure-beli adatmegosztási meghívást, és a tárterület konfigurálása előtt kilépett a szolgáltatásból, kövesse az [adatkészlet-megfeleltetés konfigurálása](how-to-configure-mapping.md) útmutatóban ismertetett útmutatást, amelyből megtudhatja, hogyan fejezheti be a fogadott adatmegosztások konfigurálását és az Adatfogadás megkezdése. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Hiba történt az új adatmegosztás létrehozásakor vagy fogadásakor

"Hiba: a művelet érvénytelen állapotkódot adott vissza" BadRequest "

"Hiba: AuthorizationFailed"

"Hiba: szerepkör-hozzárendelés a Storage-fiókhoz"

![Jogosultsági hiba](media/error-write-privilege.png)

Ha egy új adatmegosztás létrehozása vagy fogadása során a fenti hibák valamelyikével találkozik, ennek oka, hogy a tárfiók nem rendelkezik a megfelelő engedélyekkel. A szükséges engedély a *Microsoft. engedélyezés/szerepkör-hozzárendelés/írás*, amely a Storage tulajdonosi szerepkörben található, vagy egy egyéni szerepkörhöz rendelhető hozzá. Még ha Ön is hozta létre a tárfiókot, azzal NEM válik automatikusan a tárfiók tulajdonosává. Kövesse az alábbi lépéseket, ha a tárfiók tulajdonosa szeretne lenni. Azt is megteheti, hogy létrehoz egy egyéni szerepkört ezzel az engedéllyel, és hozzáadhatja magát a alkalmazáshoz.  

1. Lépjen a tárfiókhoz az Azure Portalon
1. **Hozzáférés-vezérlés kiválasztása (iam)**
1. Kattintson a **Hozzáadás** gombra
1. Önmaga hozzáadása tulajdonosként.

## <a name="troubleshooting-sql-based-sharing"></a>SQL-alapú megosztás hibaelhárítása

"Hiba: az x-adatkészletek nem lettek hozzáadva, mert nem rendelkezik a megosztáshoz szükséges engedélyekkel."

Ha ezt a hibaüzenetet akkor kapja meg, amikor SQL-alapú forrásból vesz fel egy adatkészletet, annak oka az lehet, hogy nem hozott létre felhasználót az Azure-beli adatmegosztási MSI-hez a SQL Server.  A probléma megoldásához futtassa a következő parancsfájlt:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Vegye figyelembe, hogy a *< share_acc_name >* az adatmegosztási fiók neve. Ha még nem hozott létre adatmegosztási fiókot, később is visszatérhet ehhez az előfeltételhöz.         

Győződjön meg arról, hogy követte az [adatmegosztási](share-your-data.md) oktatóanyagban felsorolt összes előfeltételt.

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.

