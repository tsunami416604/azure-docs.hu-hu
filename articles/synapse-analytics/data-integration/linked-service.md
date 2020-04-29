---
title: Társított szolgáltatás kiépítése és biztonságossá tétele az Azure szinapszis Analytics szolgáltatásban
description: Ismerje meg, hogyan hozhat létre és biztosíthat biztonságossá egy társított szolgáltatást a felügyelt vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430551"
---
# <a name="securing-a-linked-service-with-private-links"></a>Társított szolgáltatás biztonságossá tétele privát hivatkozásokkal 

Ebből a cikkből megtudhatja, hogyan védheti egy társított szolgáltatást a Szinapszisban egy privát végponttal.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure Storage-fiók**: a 2. generációs Azure Data Lake használja *forrás* adattárként. Ha nem rendelkezik Storage-fiókkal, tekintse meg az [Azure Storage-fiók létrehozása](../../storage/blobs/data-lake-storage-quickstart-create-account.md) lépéseit. Győződjön meg arról, hogy a Storage-fiók rendelkezik a szinapszis Studio IP-szűréssel a hozzáféréshez, és hogy csak a **kiválasztott hálózatok** férhetnek hozzá a Storage-fiókhoz. A panel alatti **tűzfalak és a virtuális hálózatok** beállításának az alábbi ábrához hasonlóan kell kinéznie.

![Biztonságos Storage-fiók](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Társított szolgáltatás létrehozása privát hivatkozásokkal

Az Azure szinapszis Analyticsben a társított szolgáltatás a kapcsolati adatok más szolgáltatásokhoz való definiálására szolgál. Ebben a szakaszban az Azure szinapszis Analytics és a 2. generációs Azure Data Lake fogja hozzáadni társított szolgáltatásként.

1. Nyissa meg az Azure szinapszis Studio alkalmazást, és lépjen a **kezelés** lapra.
1. A **külső kapcsolatok**területen válassza a **társított szolgáltatások**elemet.
1. Társított szolgáltatás hozzáadásához kattintson az **új**elemre.
1. Válassza ki a Azure Data Lake Storage Gen2 csempét a listából, majd kattintson a **Folytatás**gombra.
1. Győződjön meg arról, hogy az **interaktív szerzői műveletek**engedélyezve vannak. Előfordulhat, hogy az engedélyezése körülbelül 1 percet vesz igénybe. 
1. Adja meg a hitelesítő adatait. A fiók kulcsa, az egyszerű szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típus. A hitelesítő adatok helyességének ellenőrzéséhez kattintson a kapcsolódás tesztelése elemre.
1. Válassza a **kapcsolat tesztelése**lehetőséget, mert a Storage-fiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy **privát végpontot** , amelyet követve a következő részre léphet. Ha követi ezt a hivatkozást, ugorja át a következő részt.
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Abban az esetben, ha a fenti kapcsolat tesztelésekor nem kattintott a hiperhivatkozásra, kövesse az alábbi elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a fent létrehozott társított szolgáltatáshoz.

1. Lépjen a **kezelés** lapra.
1. Lépjen a **felügyelt virtuális hálózatok** szakaszra.
1. Válassza a **+ új** lehetőséget a felügyelt privát végpont alatt.
1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás**lehetőséget.
1. Adja meg a fent létrehozott Storage-fiók nevét.
1. **Létrehozás** kiválasztása
1. Néhány másodperc várakozás után kell megjelennie, hogy a privát kapcsolat létrehozása jóváhagyást igényel.

## <a name="approval-of-a-private-link"></a>Privát hivatkozás jóváhagyása
1. Válassza ki a fent létrehozott privát végpontot. Megtekintheti azt a hiperhivatkozást, amely lehetővé teszi a magánhálózati végpont jóváhagyását a Storage-fiók szintjén. *Egy másik lehetőség, hogy közvetlenül a Azure Portal Storage-fiókba lép, és bemegy a **privát Endpoint Connections** panelre.*
1. Jelölje be a stúdióban létrehozott privát végpontot, és válassza a **jóváhagyás**lehetőséget.
1. Adja meg a leírást, és kattintson az **Igen** gombra.
1. Lépjen vissza a szinapszis studióhoz a **kezelés**lap **felügyelt virtuális hálózatok** szakaszában.
1. Ahhoz, hogy a jóváhagyás megjelenjen a privát végponton, körülbelül 1 percnek kell lennie.

## <a name="check-the-connection-works"></a>A kapcsolatok működésének ellenõrzése
1. Lépjen a **kezelés** lapra, és válassza ki a létrehozott társított szolgáltatást.
1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** aktívak.
1. Válassza a **Kapcsolat tesztelése** elemet. Látnia kell a sikeres kapcsolatokat.

Ezzel létrehozott egy biztonságos és privát kapcsolatot a szinapszis és a társított szolgáltatás között!

## <a name="next-steps"></a>További lépések

Ha további ismereteket szeretne megtudni a saját felügyelt privát végpontról a szinapszis Analyticsben, tekintse meg a [következő koncepciót: szinapszis felügyelt privát végpontja](data-integration-data-lake.md)

A szinapszis Analytics adatintegrálásával kapcsolatos további információkért tekintse meg az adatok betöltését [Data Lake](data-integration-data-lake.md) cikkben.