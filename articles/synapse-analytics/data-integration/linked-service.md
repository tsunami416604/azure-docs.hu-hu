---
title: Összekapcsolt szolgáltatás kiépítése és biztonságossá tétele az Azure Synapse Analytics szolgáltatásban
description: A felügyelt virtuális hálózattal összekapcsolt szolgáltatás kiépítése és biztonságossá tétele
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430551"
---
# <a name="securing-a-linked-service-with-private-links"></a>Csatolt szolgáltatás biztonságossá tétele privát hivatkozásokkal 

Ebben a cikkben megtudhatja, hogyan biztonságossá egy csatolt szolgáltatás a synapsze-ban egy privát végpont.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* **Azure Storage-fiók:** Az Azure Data Lake Gen *2-t használja* forrásadattárként. Ha nem rendelkezik tárfiókkal, olvassa [el az Azure Storage-fiók létrehozása](../../storage/blobs/data-lake-storage-quickstart-create-account.md) című témakört a létrehozási lépésekért. Győződjön meg arról, hogy a tárfiók rendelkezik a Synapse Studio IP-szűrés eléréséhez, és hogy csak a **kiválasztott hálózatok** számára engedélyezi a Storage-fiók eléréséhez. A beállítás a panel alatt **tűzfalak és a virtuális hálózatok** kell kinéznie az alábbi képen.

![Biztonságos tárfiók](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Csatolt szolgáltatás létrehozása privát hivatkozásokkal

Az Azure Synapse Analytics egy összekapcsolt szolgáltatás, ahol meghatározza a kapcsolat adatait más szolgáltatásokkal. Ebben a szakaszban az Azure Synapse Analytics és az Azure Data Lake Gen 2 csatolt szolgáltatásokként.

1. Nyissa meg az Azure Synapse Studio alkalmazást, és lépjen a **Kezelés** lapra.
1. A **Külső kapcsolatok csoportban**válassza a **Csatolt szolgáltatások**lehetőséget.
1. Csatolt szolgáltatás hozzáadásához kattintson az **Új**gombra.
1. Válassza ki az Azure Data Lake Storage Gen2 csempét a listából, és kattintson a **Folytatás**gombra.
1. Győződjön meg arról, hogy engedélyezi **az interaktív szerzői lehetőséget.** Az engedélyezés körülbelül 1 percet vehet igénybe. 
1. Adja meg a hitelesítési hitelesítő adatokat. A fiókkulcs, az egyszerű szolgáltatás és a felügyelt identitás jelenleg támogatott hitelesítési típusok. Kattintson a kapcsolat tesztelésére, és ellenőrizze, hogy a hitelesítő adatok helyesek-e.
1. Válassza a **Kapcsolat tesztelése**lehetőséget, mert a tárfiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben meg kell jelennie egy hivatkozást, amely egy **privát végpontlétrehozásához,** amely követheti, hogy menjen a következő részhez. Ha követi ezt a linket, hagyja ki a következő részt.
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-a-managed-private-endpoint"></a>Felügyelt magánvégpont létrehozása

Abban az esetben, ha a fenti kapcsolat tesztelése során nem kattintott a hivatkozásra, kövesse az alábbi elérési utat. Most létre kell hoznia egy felügyelt magánvégpontot, amely csatlakozik a fent létrehozott csatolt szolgáltatáshoz.

1. Nyissa meg a **Kezelés** lapot.
1. Nyissa meg a **Felügyelt virtuális hálózatok szakaszt.**
1. Válassza a **+ Új** lehetőséget a Felügyelt magánvégpont csoportban.
1. Válassza ki az Azure Data Lake Storage Gen2 csempét a listából, és válassza a **Folytatás**lehetőséget.
1. Adja meg a fent létrehozott tárfiók nevét.
1. Válassza a **Létrehozás lehetőséget**
1. Néhány másodperc várakozás után látnia kell, hogy a létrehozott privát hivatkozásnak jóváhagyásra van szüksége.

## <a name="approval-of-a-private-link"></a>Privát hivatkozás jóváhagyása
1. Válassza ki a fent létrehozott privát végpontot. Láthatja a hivatkozást, amely lehetővé teszi, hogy jóváhagyja a privát végpont a tárfiók szintjén. *Egy másik lehetőség, hogy közvetlenül az Azure Portal storage-fiók, és lépjen be a **privát végpont kapcsolatok** panel.*
1. Jelölje be a Stúdióban létrehozott privát végpontot, és válassza a **Jóváhagyás lehetőséget.**
1. Leírás hozzáadása és az **igen** gombra kattintva
1. Lépjen vissza a Synapse Studio-ba a **Kezelés**lap **Felügyelt virtuális hálózatok** szakaszában.
1. Körülbelül 1 percet vesz igénybe, hogy a jóváhagyás tükröződjön a saját végpont.

## <a name="check-the-connection-works"></a>Ellenőrizze, hogy működik-e a kapcsolat
1. Nyissa meg a **Kezelés** lapot, és jelölje ki a létrehozott csatolt szolgáltatást.
1. Győződjön meg arról, hogy az **interaktív szerzői szolgáltatás** aktív.
1. Válassza a **Kapcsolat tesztelése** elemet. Látnia kell, hogy a kapcsolat sikeres lesz.

Most biztonságos és privát kapcsolatot létesített a Synapse és a kapcsolódó szolgáltatás között!

## <a name="next-steps"></a>További lépések

A Synapse Analytics felügyelt magánvégpontjának további megértéséhez tekintse meg a [Synapse által felügyelt](data-integration-data-lake.md) magánvégpont-témakört.

A Synapse Analytics adatintegrációjával kapcsolatos további információkért tekintse meg az [adatok betöltését egy Data Lake-cikkbe.](data-integration-data-lake.md)