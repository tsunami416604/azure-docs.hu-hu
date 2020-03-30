---
title: Az Azure Data Share hibaelhárítása
description: Ismerje meg, hogyan háríthatja el a meghívókkal és hibákkal kapcsolatos problémákat, amikor adatmegosztásokat hoz létre vagy fogad az Azure Data Share-tel.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964226"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Az Azure Data Share gyakori problémáinak elhárítása 

Ez a cikk bemutatja, hogyan hárítsa el az Azure Data Share gyakori problémáit. 

## <a name="azure-data-share-invitations"></a>Azure-adatmegosztási meghívók 

Bizonyos esetekben, amikor egy új felhasználó az elküldött e-mail meghívó **meghívójának Meghívó elfogadása** elemre kattint, a meghívók üres listája jelenhet meg. 

![Nincs meghívó](media/no-invites.png)

Ennek oka a következő oka lehet:

* **Az Azure Data Share szolgáltatás nincs regisztrálva egyetlen Azure-előfizetés erőforrás-szolgáltatójaként sem az Azure-bérlőben.** Ezt a problémát akkor fogja tapasztalni, ha nincs adatmegosztási erőforrás az Azure-bérlőben. Amikor létrehoz egy Azure Data Share-erőforrást, az automatikusan regisztrálja az erőforrás-szolgáltatót az Azure-előfizetésében. Az adatmegosztási szolgáltatást manuálisan is regisztrálhatja az alábbi lépések végrehajtásával. A lépések végrehajtásához rendelkeznie kell az Azure Közreműködőszerepkörsel.

    1. Az Azure Portalon keresse meg az **Előfizetések**
    1. Válassza ki az Azure Data Share-erőforrás létrehozásához használni kívánt előfizetést
    1. Kattintson az **erőforrás-szolgáltatókra**
    1. **Microsoft.DataShare** keresése
    1. Kattintson a **Regisztráció gombra** 

    A lépések végrehajtásához az [Azure Contributor RBAC szerepkört](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) kell rendelkeznie. 

* **Meghívó küldése az e-mail alias helyett az Azure-bejelentkezési e-mail.** Ha regisztrálta az Azure Data Share szolgáltatást, vagy már létrehozott egy adatmegosztási erőforrást az Azure-bérlőben, de még mindig nem látja a meghívót, akkor talán azért, mert a szolgáltató az Azure bejelentkezési e-mail címe helyett az e-mail aliast adta meg címzettként. Lépjen kapcsolatba az adatszolgáltatóval, és győződjön meg arról, hogy a meghívót az Azure bejelentkezési e-mail címére küldte, és nem az e-mail aliasára.

* **A meghívásmár elfogadásra került.** Az e-mailben található hivatkozás az Azure Portal adatmegosztási meghívólapjára mutat, amely csak a függőben lévő meghívókat sorolja fel. Ha már elfogadta a meghívást, az a továbbiakban nem jelenik meg az Adatmegosztási meghívó lapon. Folytassa az adatmegosztási erőforrás, amely elfogadta a meghívást a fogadott megosztások megtekintésére és konfigurálja a cél Azure Data Explorer fürt beállítása.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Hiba új megosztás létrehozásakor vagy fogadásakor

"Nem sikerült adatkészleteket hozzáadni"

"Nem sikerült leképezni az adatkészleteket"

"Nem lehet adatmegosztási erőforrást x hozzáférést adni az y-hoz"

"Nem rendelkezik megfelelő engedélyekkel az x-hez"

"Nem tudtunk írási engedélyeket hozzáadni az Azure Data Share-fiókhoz egy vagy több kiválasztott erőforráshoz"

Ha a fenti hibák bármelyikét kapja egy új megosztás vagy leképezésadatkészletek létrehozásakor, annak oka lehet az Azure-adattár hoz való elégtelen engedélyek. Lásd: [Szerepkörök és követelmények](concepts-roles-permissions.md) a szükséges engedélyekhez. 

Írási engedélyre van szükség az Azure-adattárból származó adatok megosztásához vagy fogadásához, amelyek általában a közreműködői szerepkörben léteznek. 

Ha ez az első alkalom, hogy adatokat oszt meg vagy fogad az Azure-adattárból, *microsoft.authorization/role assignments/write* engedélyre is szüksége van, amely általában a Tulajdonos szerepkörben található. Még akkor is, ha létrehozta az Azure-adattár-erőforrás, nem automatikusan teszi az erőforrás tulajdonosa. Megfelelő engedélyekkel az Azure Data Share szolgáltatás automatikusan hozzáférést biztosít az adatmegosztási erőforrás felügyelt identitásának az adattárhoz. Ez a folyamat néhány percet is igénybe vehet. Ha a késés miatt hibát tapasztal, próbálkozzon újra néhány perc múlva.

Az SQL-alapú megosztás további engedélyeket igényel. A részleteket lásd az SQL-alapú megosztás hibaelhárítása című témakörben.

## <a name="troubleshooting-sql-based-sharing"></a>SQL-alapú megosztás hibáinak elhárítása

"X felhasználó nem létezik az SQL adatbázisban"

Ha ez a hibaüzenet jelenik meg, amikor egy SQL-alapú forrásból származó adatkészletet ad hozzá, annak az lehet az oka, hogy nem hozott létre felhasználót az Azure Data Share felügyelt identitásához az SQL Server kiszolgálón.  A probléma megoldásához futtassa a következő parancsfájlt:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Ha ez a hibaüzenet jelenik meg, amikor az adatkészletet sql-alapú célhoz rendeli, annak az lehet az oka, hogy nem hozott létre felhasználót az Azure Data Share felügyelt identitásához az SQL Server en.  A probléma megoldásához futtassa a következő parancsfájlt:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Ne feledje, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve.      

Győződjön meg arról, hogy követte az adatok [megosztása,](share-your-data.md) valamint az [adatok elfogadása és fogadása](subscribe-to-data-share.md) című oktatóanyagban felsorolt összes előfeltételt.

## <a name="snapshot-failed"></a>A pillanatkép nem sikerült
A pillanatkép számos okból sikertelen lehet. A részletes hibaüzenetet a pillanatkép kezdési időpontjára, majd az egyes adatkészletek állapotára kattintva találhatja meg. 

Ha a hibaüzenet az engedélyhez kapcsolódik, ellenőrizze, hogy az Adatmegosztás szolgáltatás rendelkezik-e a szükséges engedéllyel. A részletekért lásd a [Szerepkörök és követelmények](concepts-roles-permissions.md) című témakört. Ha ez az első alkalommal, amikor pillanatképet készít, eltarthat néhány percig, amíg az adatmegosztási erőforrás hozzáférést kap az Azure-adattárhoz. Várjon néhány percet, majd próbálkozzon újra.

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md) 

Az adatok fogadásának módjáról folytassa az [adatelfogadási és -fogadási](subscribe-to-data-share.md) oktatóanyaggal.

