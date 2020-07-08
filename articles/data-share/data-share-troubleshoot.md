---
title: Az Azure Data Share hibaelhárítása
description: Megtudhatja, hogyan lehet elhárítani a meghívásokkal és hibákkal kapcsolatos problémákat az Azure-beli adatmegosztások létrehozásakor vagy fogadásakor.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 510787b395388f0dc895b9646aee8208013f3b42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322042"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Az Azure-adatmegosztás gyakori problémáinak elhárítása 

Ez a cikk bemutatja, hogyan lehet elhárítani az Azure-adatmegosztással kapcsolatos gyakori problémákat. 

## <a name="azure-data-share-invitations"></a>Azure-adatmegosztási meghívások 

Bizonyos esetekben, amikor egy új felhasználó az elküldött e-mailes meghívóból kattint a **meghívó elfogadására** , a meghívókat tartalmazó üres lista jelenik meg. 

![Nincsenek meghívók](media/no-invites.png)

Ennek oka a következő lehet:

* **Az Azure-beli adatmegosztási szolgáltatás nincs regisztrálva az Azure-bérlő bármely Azure-előfizetésének erőforrás-szolgáltatója.** Ezt a problémát akkor fogja tapasztalni, ha nincs adatmegosztási erőforrás az Azure-bérlőben. Azure-beli adatmegosztási erőforrás létrehozásakor a automatikusan regisztrálja az erőforrás-szolgáltatót az Azure-előfizetésében. Az alábbi lépéseket követve manuálisan is regisztrálhatja az adatmegosztási szolgáltatást. A lépések végrehajtásához rendelkeznie kell az Azure közreműködő szerepkörrel.

    1. A Azure Portal navigáljon az **előfizetések** elemre.
    1. Válassza ki az Azure-beli adatmegosztási erőforrás létrehozásához használni kívánt előfizetést
    1. Kattintson az **erőforrás-szolgáltatók** elemre
    1. A **Microsoft. DataShare** keresése
    1. Kattintson a **regisztráció** elemre. 

    A lépések végrehajtásához az [Azure KÖZREMŰKÖDŐI RBAC szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) kell rendelkeznie. 

* **A meghívót az Azure bejelentkezési e-mail-címe helyett az e-mail aliasára küldi a rendszer.** Ha regisztrálta az Azure-beli adatmegosztási szolgáltatást, vagy már létrehozott egy adatmegosztási erőforrást az Azure-bérlőben, de még mindig nem látja a meghívót, akkor valószínűleg azért, mert a szolgáltató az e-mail aliasát címzettként adta meg az Azure bejelentkezési e-mail-címe helyett. Forduljon az adatszolgáltatóhoz, és győződjön meg arról, hogy elküldte a meghívót az Azure-beli bejelentkezési e-mail címre, és nem az e-mail aliasát.

* **A meghívást már elfogadták.** Az e-mailben található hivatkozás a Azure Portal adatmegosztási Meghívási oldalára kerül, amely csak a függőben lévő meghívókat listázza. Ha már elfogadta a meghívót, az nem fog megjelenni az adatmegosztási Meghívási oldalon. Folytassa az adatmegosztási erőforrással, amelyet a meghívás fogadására használt a kapott megosztások megtekintéséhez és a cél Azure Adatkezelő-fürt beállításainak konfigurálásához.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Hiba új megosztás létrehozásakor vagy fogadásakor

"Nem sikerült hozzáadni az adatkészleteket"

"Nem sikerült leképezni az adatkészleteket"

"Nem lehet megadni az adatmegosztási erőforrás x-hozzáférését y-hez"

"Nem rendelkezik megfelelő engedélyekkel az x-hez"

"Az Azure-beli adatmegosztási fiókhoz nem lehet írási engedélyt adni egy vagy több kiválasztott erőforráshoz"

Ha a fenti hibák bármelyikét megkapja egy új megosztás vagy leképezési adatkészlet létrehozásakor, annak oka az lehet, hogy nem rendelkezik megfelelő engedélyekkel az Azure-adattárhoz. A szükséges engedélyekhez tartozó [szerepkörök és követelmények](concepts-roles-permissions.md) című témakörben talál további információt. 

Írási engedéllyel kell rendelkeznie az adatok megosztásához vagy fogadásához egy Azure-adattárból, amely általában a közreműködő szerepkörben található. 

Ha első alkalommal oszt meg vagy fogad el egy Azure-adattárat, szüksége lesz a *Microsoft. Authorization/szerepkör-hozzárendelésekre/írási* engedélyre, amely általában a tulajdonosi szerepkörben található. Még ha létrehozta az Azure adattár-erőforrást, az nem teszi automatikusan az erőforrás tulajdonosát. A megfelelő engedélyekkel az Azure adatmegosztási szolgáltatás automatikusan megadja az adatmegosztási erőforrás felügyelt identitás-hozzáférését az adattárhoz. Ez a folyamat néhány percet is igénybe vehet. Ha ez a késés miatt sikertelen, próbálkozzon újra néhány perc múlva.

Az SQL-alapú megosztáshoz további engedélyek szükségesek. További részletek: SQL-alapú megosztás hibaelhárítása.

## <a name="troubleshooting-sql-based-sharing"></a>SQL-alapú megosztás hibáinak elhárítása

"A felhasználó x nem létezik a SQL Databaseban"

Ha ezt a hibaüzenetet akkor kapja meg, amikor SQL-alapú forrásból vesz fel egy adatkészletet, annak oka az lehet, hogy nem hozott létre felhasználót az Azure-beli adatmegosztás felügyelt identitásához SQL Databaseban.  A probléma megoldásához futtassa a következő parancsfájlt:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Ha ezt a hibaüzenetet kapja, amikor az adatkészletet SQL-alapú célra rendeli hozzá, annak oka az lehet, hogy nem hozott létre felhasználót az Azure-beli adatmegosztás felügyelt identitásához a SQL Server.  A probléma megoldásához futtassa a következő parancsfájlt:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve.      

Győződjön meg arról, hogy követte az [adatai megosztása](share-your-data.md) , valamint az Adatfogadás [és-fogadás](subscribe-to-data-share.md) című részben felsorolt összes előfeltételt.

## <a name="snapshot-failed"></a>A pillanatkép nem sikerült
A pillanatkép különböző okok miatt sikertelen lehet. A részletes hibaüzenetet a pillanatkép kezdési időpontjára, majd az egyes adatkészletek állapotára kattintva tekintheti meg. 

Ha a hibaüzenet az engedélyhez kapcsolódik, ellenőrizze, hogy az adatmegosztási szolgáltatás rendelkezik-e a szükséges engedéllyel. Részletekért lásd a [szerepkörök és követelmények](concepts-roles-permissions.md) című témakört. Ha először készít pillanatképet, eltarthat néhány percig, amíg az adatmegosztási erőforrás hozzáférést kap az Azure-adattárhoz. Várjon néhány percet, és próbálkozzon újra.

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot. 

Ha szeretné megtudni, hogyan fogadhat adatfogadást, folytassa az [elfogadás és fogadás adatgyűjtéssel](subscribe-to-data-share.md) foglalkozó oktatóanyagot.

