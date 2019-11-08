---
title: SKU egy Azure containers-rendszerképhez | Azure piactér
description: Adja meg az SKU-t egy Azure-tárolóhoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823146"
---
# <a name="container-skus-tab"></a>Tároló SKU-i lapja

Az **új ajánlat** oldal **SKU** -i lapján létrehozhat egy vagy több SKU-t, és hozzárendelheti őket az új ajánlathoz.  Különböző SKU-ket használhat a megoldások a szolgáltatások, a számlázási modellek vagy más jellemzők megkülönböztetésére.

## <a name="sku-settings"></a>SKU-beállítások

Új ajánlat létrehozásának megkezdése esetén nincs az ajánlathoz társított SKU. Új SKU létrehozásához kövesse az alábbi lépéseket:

1. A SKU-i lapon válassza az **új SKU** elemet.

   ![Új SKU-kérés](./media/containers-sku-settings.png)

2. Adja meg a szükséges SKU-és tároló-információkat. Az egyes SKU-azonosítók egy tároló-rendszerképnek felelnek meg. Az SKU két részből áll:

    -   SKU-metaadatok
    -   Tároló metaadatai


### <a name="sku-metadata"></a>SKU-metaadatok

Az SKU-metaadatok a tárolók listájának kirakati információit tartalmazzák.

![SKU-metaadatok](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Tároló metaadatai

A tároló metaadatai a rendszerképek tárházának részletes információit Azure Container Registry (ACR) belül. Az Azure Marketplace ezt a rendszerképet egy Marketplace-specifikus, nyilvános beállításjegyzékbe másolja, majd a minősítés után elérhetővé teszi a rendszerképet az ügyfeleknek. Az Azure-beli felhasználók által az Azure Marketplace-tárolók használatával kapcsolatos összes kérést a piactér nyilvános beállításjegyzékében kell kiszolgálni, nem az ACR-t.

![Tároló metaadatai](./media/containers-image-repository.png)
    
A **kép tárházának részletei** az előző képernyőfelvételen a következő mezőket tartalmazzák.  A kötelező mezőket csillag (*) alapján vádoljuk.

-   **Előfizetés-azonosító\*** – az ACR-t tartalmazó Azure-előfizetés azonosítója.
-   **Erőforráscsoport neve\*** – az ACR erőforráscsoport-neve.
-   **Beállításjegyzék neve\*** – az ACR neve.
-   **Adattár neve\*** – a tárház neve. A név beállítása után ez az érték nem módosítható. Egy egyedi név használatával elkerülhető, hogy ütközik a fiókjában lévő egyéb ajánlatokkal.
-   **Username\*** – az ACR-képhez társított Felhasználónév (rendszergazdai Felhasználónév).
-   **Password\*** – az ACR-képhez társított jelszó.

    >[!NOTE]
    >A felhasználónévnek és a jelszónak meg kell győződnie arról, hogy a partnerek hozzáférhetnek a közzétételi folyamatban említett ACR-hez.


### <a name="image-version"></a>Rendszerképverzió

A tárolók rendszerképének közzétételekor egy vagy több képcímkét és SHA-kivonatot is megadhat.

**Képcímke\* vagy kivonatoló**
 
- A címkének vagy a kivonatnak tartalmaznia kell egy `latest` címkét és egy Version címkét (például `xx.xx.xx-`, ahol XX egy szám). A több platform megcélzásához meg kell jelennie a [címkéknek](https://github.com/estesp/manifest-tool) . A jegyzékfájl által hivatkozott összes címkét is fel kell venni, hogy fel lehessen tölteni őket. 
- Címkék használatával több tároló-verziót is hozzáadhat. Az összes manifest-címkének (kivéve `latest`) `X.Y-` vagy `X.Y.Z-` kell kezdődnie, ahol az X, az Y, a Z egész számok. <br/> Ha például egy `latest` címke `1.0.1-linux-x64`ra, `1.0.1-linux-arm32`ra és `1.0.1-windows-arm32`re mutat, ezeket a címkéket hozzá kell adni.

>[!NOTE]
>Ne feledje, hogy adjon hozzá egy **teszt címkét** a képhez, hogy a tesztelés során azonosítható legyen a rendszerkép.


## <a name="next-steps"></a>További lépések

A [piactér lapon](./cpp-marketplace-tab.md) létrehozhat egy piactér-leírást az ajánlathoz. 
