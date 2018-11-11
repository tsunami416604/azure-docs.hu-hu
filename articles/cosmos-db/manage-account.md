---
title: Az Azure Portalon egy Azure Cosmos DB-fiók kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure Cosmos DB-fiókot az Azure Portalon keresztül. Útmutató megtekintése, másolása, törlése és hozzáférési fiókok az Azure Portalon lévő keresése.
keywords: Az Azure Portal, azure, Microsoft azure
services: cosmos-db
author: kirillg
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: abcf51c6bd196c2ffb0bb35e2df161531a53972d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229390"
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Az Azure Cosmos DB-fiók kezelése
Ismerje meg, hogyan globális összhangot, kulcsok használata, és törölje az Azure Cosmos DB-fiókot az Azure Portalon.

## <a id="consistency"></a>Azure Cosmos DB konzisztencia beállításainak kezelése
A jobb oldali konzisztenciaszint kiválasztása attól függ, hogy az alkalmazás szemantikáját. Ismerje meg az Azure Cosmos DB-ben elérhető konzisztenciaszintekről olvasásával [rendelkezésre állás és teljesítmény az Azure Cosmos DB maximalizálása a konzisztenciaszintek használatával][consistency]. Azure Cosmos DB biztosítja a konzisztencia, a rendelkezésre állás és a teljesítményre vonatkozó garanciákat, minden konzisztencia szinten érhető el a fiókot. Konfigurálás a fiókot erős konzisztencia szintű kell lennie, hogy az adatok egyetlen Azure-régióba zárt és globálisan elérhető. Más szóval, Könnyített konzisztenciaszintekről – korlátozott frissesség, munkamenet és végleges engedélyezése, hogy tetszőleges mennyiségű Azure-régióban, az adatbázis-fiókhoz társít. Az alábbi egyszerű lépések bemutatják, hogyan válassza ki az adatbázisfiók alapértelmezett konzisztenciaszintjét.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Az alapértelmezett konzisztenciát az Azure Cosmos DB-fiók megadása
1. Az a [az Azure portal](https://portal.azure.com/), az Azure Cosmos DB-fiók eléréséhez.
2. A fiók lapon kattintson a **alapértelmezett konzisztencia**.
3. Az a **alapértelmezett konzisztencia** lapon válassza ki az új konzisztencia szintjét, és kattintson **mentése**.
    ![Alapértelmezett konzisztencia munkamenet][5]

## <a id="keys"></a>Megtekintéséhez, másolásához és újragenerálásához hozzáférési kulcsait és jelszavait
Az Azure Cosmos DB-fiók létrehozásakor a szolgáltatás két fő kulcsot (vagy a MongoDB API-fiókok a két jelszó) hoz létre, amely használható a hitelesítéshez az Azure Cosmos DB-fiók eléréséhez. Azáltal, hogy a két hozzáférési kulcsot, az Azure Cosmos DB lehetővé teszi a kulcsok az Azure Cosmos DB-fiók megszakítás nélkül. 

Az a [az Azure portal](https://portal.azure.com/), hozzáférés az **kulcsok** lapot az erőforrás menüben a **Azure Cosmos DB-fiók** megtekintése, másolása és a hozzáférési kulcsok, amelyek szolgáló lap az Azure Cosmos DB-fiók eléréséhez. MongoDB API-fiókok esetében eléréséhez a **kapcsolati karakterlánc** az erőforrás menüben megtekintéséhez, másolásához és újragenerálásához a fiókja eléréséhez használt jelszavak lapon.

![Az Azure portal képernyőképe, a kulcsok oldalán](./media/manage-account/keys.png)

> [!NOTE]
> A **kulcsok** oldal is az elsődleges és másodlagos kapcsolati karakterláncok, amelyek segítségével csatlakozhat a fiókjához a [adatáttelepítési eszköz](import-data.md).
> 
> 

Írásvédett kulcsok is érhetők el ezen az oldalon. Olvasási és lekérdezések csak olvasható műveletekhez, ideje hoz létre, törölhet, és lecseréli nem.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Egy hozzáférési kulcs vagy jelszó másolása az Azure Portalon
Az a **kulcsok** lap (vagy **kapcsolati karakterlánc** MongoDB API-fiókok), kattintson a **másolási** jobb oldalán a kulcs vagy jelszó másolni kívánt gombra.

![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok oldalán](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Elérési kulcsok és a jelszó újragenerálása
Akkor kell megváltoztatnia, a tárelérési kulcsok (és a MongoDB API-fiókok jelszavát) az Azure Cosmos DB-fiók rendszeres időközönként annak érdekében, hogy a kapcsolat biztonságosabb. Lehetővé teszi az Azure Cosmos DB-fiók az egyik kulcs, míg más hozzáférési kulcs kapcsolatok fenntartásához két hozzáférési kulcsok és jelszavak vannak hozzárendelve.

> [!WARNING]
> A tárelérési kulcsok újragenerálása hatással van minden olyan alkalmazások, amelyek a jelenlegi kulcs függenek. Az Azure Cosmos DB-fiók eléréséhez a hozzáférési kulcsot használó összes ügyfelet frissíteni kell, hogy az új kulcsot használja.
> 
> 

Ha alkalmazások és a cloud services használata az Azure Cosmos DB-fiókot, akkor elveszti a kapcsolatokat Ha újragenerálja a kulcsokat, kivéve, ha rotálja a kulcsokat. Az alábbi lépéseket a működés közbeni a kulcsok és jelszavak részt folyamatot körvonalazzák.

1. A hozzáférési kulcsot az alkalmazáskódban való hivatkozáshoz a másodlagos elérési kulcsot az Azure Cosmos DB-fiók frissítéséhez.
2. Az Azure Cosmos DB-fiókja az elsődleges elérési kulcs újragenerálása. Az a [az Azure portal](https://portal.azure.com/), az Azure Cosmos DB-fiók eléréséhez.
3. Az a **Azure Cosmos DB-fiók** kattintson **kulcsok** (vagy **kapcsolati karakterlánc** a MongoDB-fiókok **).
4. Az a **kulcsok**/**kapcsolati karakterlánc** lapon, a újragenerálása gombra, majd kattintson a **Ok** annak ellenőrzéséhez, hogy szeretné-e egy új kulcsot létrehozni.
    ![Elérési kulcsok újragenerálása](./media/manage-account/regenerate-keys.png)
5. Miután ellenőrizte, hogy az új kulcs használatra kész (körülbelül 5 percig regenerálása után), a hozzáférési kulcsot az alkalmazáskódban való hivatkozáshoz az új elsődleges elérési kulcs frissítése
6. Generálja újra a másodlagos elérési kulcsot.
   
    ![Elérési kulcsok újragenerálása](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Egy újonnan létrehozott kulcs használhatók legyenek az Azure Cosmos DB-fiókja eléréséhez több percet is igénybe vehet.
> 
> 

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése
A kapcsolati karakterlánc lekéréséhez tegye a következőket: 

1. Az a [az Azure portal](https://portal.azure.com), az Azure Cosmos DB-fiók eléréséhez.
2. Az erőforrás menüben kattintson **kulcsok** (vagy **kapcsolati karakterlánc** MongoDB API-fiókok esetében).
3. Kattintson a **másolási** megjelenítő gombra a **elsődleges kapcsolati karakterlánc** vagy **másodlagos kapcsolati karakterlánc** mezőbe. 

Ha a kapcsolati karakterláncot használja a [Azure Cosmos DB adatbázis-áttelepítési eszköz](import-data.md), fűzze hozzá az adatbázis neve a kapcsolati karakterlánc végére. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Az Azure Cosmos DB-fiók törlése
Távolítsa el az Azure Cosmos DB-fiókot az Azure Portalról, amely már nem használ, kattintson a jobb gombbal a fiók nevét, majd kattintson **fiók törlése**.

![Az Azure Portalon egy Azure Cosmos DB-fiók törlése](./media/manage-account/deleteaccount.png)

1. Az a [az Azure portal](https://portal.azure.com/), a, hogy törölni kívánja az Azure Cosmos DB-fiók eléréséhez.
2. Az a **Azure Cosmos DB-fiók** lapon kattintson a jobb gombbal a fiókot, és kattintson a **fiók törlése**. 
3. Az eredményül kapott megerősítő lapon írja be az Azure Cosmos DB-fiók neve, győződjön meg arról, hogy szeretné-e a fiók törlése.
4. Kattintson a **törlése** gombra.

![Az Azure Portalon egy Azure Cosmos DB-fiók törlése](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Következő lépések
Ismerje meg, hogyan [Ismerkedés az Azure Cosmos DB-fiókja](https://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
