---
title: Egy Azure-tárolók hoz tartozó sk-ek | Azure Piactér
description: Konfigurálja a biztonsági készletet egy Azure-tárolóhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 9c5cf218632c720fd042cc5f5d4ed95d5096b5b5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270196"
---
# <a name="container-skus-tab"></a>Tárolósulékok lap

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure Container-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Container-ajánlat létrehozása](https://aka.ms/CreateContainerOffer) című, az áttelepített ajánlatok kezeléséhez című útmutatóutasításait.

Az **Új ajánlat** lap **SK-ja** lehetővé teszi egy vagy több skus létrehozását, és társíthatja őket az új ajánlathoz.  Különböző sk-ek segítségével különbséget tehet a megoldás szolgáltatáskészletek, számlázási modellek vagy más jellemzők szerint.

## <a name="sku-settings"></a>Termékváltozat beállításai

Amikor új ajánlatot hoz létre, nincs az ajánlathoz társított skus. Új termékváltozat létrehozásához kövesse az alábbi lépéseket:

1. A Termékváltozatok lapon válassza az **Új termékváltozat lehetőséget.**

   ![Új termékváltozat-kérdés](./media/containers-sku-settings.png)

2. Adja meg a szükséges termékváltozat- és tárolóadatokat. Minden termékváltozat egy tárolórendszerképnek felel meg. A termékváltozatnak két része van:

    -   Termékváltozat metaadatai
    -   Tároló metaadatai


### <a name="sku-metadata"></a>Termékváltozat metaadatai

A Termékváltozat metaadatai a tárolólista kirakati megjelenítési adatait tartalmazzák.

![Termékváltozat metaadatai](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Tároló metaadatai

A tároló metaadatai hivatkozási információkat tartalmaz nak a képtár adattár az Azure Container Registry (ACR) belül. Az Azure Marketplace másolja ezt a lemezképet egy Piactér-specifikus, nyilvános beállításjegyzékbe, majd a lemezképet a minősítés után elérhetővé teszi az ügyfelek számára. Az Azure-felhasználó által egy Azure Marketplace-tároló rendszerkép használatára vonatkozó összes kérés a Marketplace nyilvános beállításjegyzékéből kerül ki, nem az ACR-ből.

![Tároló metaadatai](./media/containers-image-repository.png)
    
Az előző képernyőfelvétel **képtáradatai** a következő mezőket tartalmazzák.  A kötelező mezőket csillag (*) vádolja.

-   **Előfizetés-azonosító\* ** – Az Azure-előfizetés-azonosító, ahol az ACR jelen van.
-   **Erőforráscsoport\* neve** – Az ACR erőforráscsoport neve.
-   **Rendszerleíró\* adatbázis neve** - Az ACR név.
-   **Tárház\* neve** - A tárház neve. A név beállítása után ez az érték nem módosítható. Használjon egyedi nevet, hogy elkerülje a fiókjában lévő más ajánlatokkal való ütközést.
-   **Felhasználónév\* ** – Az ACR-lemezképhez társított felhasználónév (rendszergazdai felhasználónév).
-   **Jelszó\* ** - Az ACR-lemezképhez társított jelszó.

    >[!NOTE]
    >A felhasználónév és a jelszó szükséges annak biztosításához, hogy a partnerek hozzáférhessenek a közzétételi folyamatban említett ACR-hez.


### <a name="image-version"></a>Rendszerképverzió

Tárolólemezkép közzétételekor egy vagy több képcímkéket adhat meg, és az SHA kivonatolók.

**Képcímke\* vagy kivonatoló**
 
- Ennek a címkének `latest` vagy kivonatolásnak tartalmaznia kell `xx.xx.xx-` egy címkét és egy verziócímkét (például azzal kezdve, hogy hol xx egy szám). Több platformot megcélozhatnak. [manifest tags](https://github.com/estesp/manifest-tool) A jegyzékcímkét hivatkozó összes címkét hozzá kell adni, hogy feltölthessük őket. 
- A tárolók több verzióját is hozzáadhatja címkék használatával. Minden jegyzéknév-címkének (kivéve `latest`) az `X.Y-` `X.Y.Z-` X, Y, Z egész számokkal kell kezdődnie. <br/> Ha például `latest` egy címke `1.0.1-linux-x64` `1.0.1-linux-arm32`a `1.0.1-windows-arm32`pontjára mutat, és a , ezeket a címkéket itt kell hozzáadni.

>[!NOTE]
>Ne felejtsen el **tesztcímkét** hozzáadni a képhez, hogy a tesztelés során azonosíthassa a képet.


## <a name="next-steps"></a>További lépések

A [Piactér lapon](./cpp-marketplace-tab.md) hozzon létre egy piactér leírását az ajánlathoz. 
