---
title: Az Azure Data Share hibaelhárítása
description: Megtudhatja, hogyan lehet elhárítani a meghívásokkal és hibákkal kapcsolatos problémákat az Azure-beli adatmegosztások létrehozásakor vagy fogadásakor.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/02/2020
ms.openlocfilehash: 620fe1e693a177123e166220ab94bbd74c4826ff
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761532"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Az Azure Data Share gyakori hibáinak elhárítása 

Ez a cikk bemutatja, hogyan lehet elhárítani az Azure-adatmegosztással kapcsolatos gyakori problémákat. 

## <a name="azure-data-share-invitations"></a>Azure-adatmegosztási meghívások 

Bizonyos esetekben, amikor egy új felhasználó az elküldött e-mailes meghívóból kattint a **meghívó elfogadására** , a meghívókat tartalmazó üres lista jelenik meg. 

![Nincsenek meghívók](media/no-invites.png)

Ez az alábbi okok miatt történhet:

* **Az Azure Data Share szolgáltatás nincs erőforrás-szolgáltatóként regisztrálva az Azure-bérlő egyik Azure-előfizetésében sem.** Akkor ütközhet ebbe a problémába, ha nem található Data Share-erőforrás az Azure-bérlőben. Az Azure Data Share-erőforrást a létrehozásakor a rendszer automatikusan erőforrás-szolgáltatóként regisztrálja az Azure-előfizetésben. A Data Share szolgáltatást manuálisan is regisztrálhatja az alábbi lépésekkel. A lépések elvégzéséhez közreműködői Azure-szerepkörre lesz szüksége.

    1. Az Azure Portalon lépjen az **Előfizetések** felületre
    1. Válassza ki azt az előfizetést, amelyet az Azure Data Share-erőforrás létrehozásához kíván használni
    1. Kattintson az **Erőforrás-szolgáltatók** elemre.
    1. Keresse meg a **Microsoft.DataShare** elemet.
    1. Kattintson a **Regisztráció** parancsra. 

    A lépések elvégzéséhez az Azure- [közreműködő szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) kell rendelkeznie az Azure-előfizetéshez. 

* **A meghívót az Azure-hoz tartozó bejelentkezési e-mail-címe helyett az e-mail aliasára küldte a rendszer.** Ha regisztrálta az Azure Data Share szolgáltatást, vagy már létrehozott egy Data Share-erőforrást az Azure-bérlőben, de még mindig nem látja a meghívást, akkor annak az lehet az oka, hogy a szolgáltató az e-mail aliasát adta meg címzettként az Azure-hoz tartozó bejelentkezési e-mail-címe helyett. Forduljon az adatszolgáltatóhoz, és győződjön meg arról, hogy az Azure-hoz tartozó bejelentkezési e-mail-címre küldte a meghívót, nem pedig az e-mail aliasára.

* **A meghívás már el lett fogadva.** Az e-mailben található hivatkozás az Azure Portal Data Share-meghívási oldalára irányítja, amely csak a függőben lévő meghívások listáját mutatja. Ha már elfogadta a meghívást, nem jelenik meg többé a Data Share-meghívások oldalán. Navigáljon a meghívás elfogadására használt Data Share-erőforráshoz, amelyet a megosztások megtekintéséhez kapott, és konfigurálja a kívánt Azure Data Explorer-fürt beállításait.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Hiba új megosztás létrehozásakor vagy fogadásakor

"Nem sikerült hozzáadni az adatkészleteket"

"Nem sikerült leképezni az adatkészleteket"

"Nem lehet megadni az adatmegosztási erőforrás x-hozzáférését y-hez"

"Nem rendelkezik megfelelő engedélyekkel az x-hez"

"Az Azure-beli adatmegosztási fiókhoz nem lehet írási engedélyt adni egy vagy több kiválasztott erőforráshoz"

Ha a fenti hibák bármelyikét megkapja egy új megosztás vagy leképezési adatkészlet létrehozásakor, annak oka az lehet, hogy nem rendelkezik megfelelő engedélyekkel az Azure-adattárhoz. A szükséges engedélyekhez tartozó [szerepkörök és követelmények](concepts-roles-permissions.md) című témakörben talál további információt. 

Írási engedéllyel kell rendelkeznie az adatok megosztásához vagy fogadásához egy Azure-adattárból, amely általában a közreműködő szerepkörben található. 

Ha első alkalommal oszt meg vagy fogad el egy Azure-adattárat, szüksége lesz a *Microsoft. Authorization/szerepkör-hozzárendelésekre/írási* engedélyre, amely általában a tulajdonosi szerepkörben található. Még ha létrehozta az Azure adattár-erőforrást, az nem teszi automatikusan az erőforrás tulajdonosát. A megfelelő engedélyekkel az Azure adatmegosztási szolgáltatás automatikusan megadja az adatmegosztási erőforrás felügyelt identitás-hozzáférését az adattárhoz. Ez a folyamat néhány percet is igénybe vehet. Ha ez a késés miatt sikertelen, próbálkozzon újra néhány perc múlva.

Az SQL-alapú megosztáshoz további engedélyek szükségesek. Az előfeltételek részletes listájáért lásd: [SQL-források megosztása](how-to-share-from-sql.md) .

## <a name="snapshot-failed"></a>A pillanatkép nem sikerült
A pillanatkép különböző okok miatt sikertelen lehet. A részletes hibaüzenetet a pillanatkép kezdési időpontjára, majd az egyes adatkészletek állapotára kattintva tekintheti meg. A következő okok miatt nem sikerül a pillanatkép:

* Az adatmegosztásnak nincs engedélye a forrás-adattárból való olvasásra vagy a cél adattárba való írásra. A részletes engedélyekkel kapcsolatos követelményekért lásd a [szerepkörök és követelmények](concepts-roles-permissions.md) című témakört. Ha először készít pillanatképet, eltarthat néhány percig, amíg az adatmegosztási erőforrás hozzáférést kap az Azure-adattárhoz. Várjon néhány percet, és próbálkozzon újra.
* A tűzfal blokkolja az adatmegosztási kapcsolatokat a forrás-vagy a célként megadott adattárhoz.
* A rendszer törli a megosztott adatkészletet vagy a forrás vagy a cél adattárát.
* SQL-megosztás esetén a pillanatkép-készítési folyamat vagy a cél adattár nem támogatja az adattípusokat. A részletekért tekintse [meg az SQL-források megosztását](how-to-share-from-sql.md#supported-data-types) ismertető témakört.

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot. 

Ha szeretné megtudni, hogyan fogadhat adatfogadást, folytassa az [elfogadás és fogadás adatgyűjtéssel](subscribe-to-data-share.md) foglalkozó oktatóanyagot.

