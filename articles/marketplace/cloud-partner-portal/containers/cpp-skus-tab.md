---
title: Egy Azure-tárolói rendszerképpel termékváltozatok |} A Microsoft Docs
description: SKU-k konfigurálása egy Azure-tárolóhoz.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 83acdb320e0be0837ec5188a11cb86b91ed077fa
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979754"
---
# <a name="container-skus-tab"></a>Tároló termékváltozatok lap

A **termékváltozatok** lapján a **új ajánlat** lap lehetővé teszi, hogy hozzon létre egy vagy több termékváltozatok és rendelheti őket hozzá az új ajánlat.  Különböző SKU-k segítségével különbséget tenni egy megoldás szolgáltatáskészleteket, számlázási modellek vagy egyéb jellemzők alapján.

## <a name="sku-settings"></a>Termékváltozat-beállítások

Amikor egy új ajánlat létrehozása, nincsenek bármely a tartozó termékváltozatokat. Hozzon létre egy új Termékváltozatban, kövesse az alábbi lépéseket:

1. Az SKU-k lapon válassza ki a **új Termékváltozat**

   ![Új Termékváltozat kérése](./media/containers-sku-settings.png)

2. Adja meg a Termékváltozat és a tároló szükséges információkat. Minden SKU felel meg egy tárolórendszerképet. A Termékváltozat két részből áll:

    -   Termékváltozat-metaadatok
    -   Tároló metaadatai

### <a name="sku-metadata"></a>Termékváltozat-metaadatok

A Termékváltozat-metaadatokat tároló listáján kirakat megjelenített információkat tartalmaz.

![Termékváltozat-metaadatok](./media/containers-sku-details.png)

### <a name="container-metadata"></a>Tároló metaadatai

A tároló metaadatait az adattár részletei belül az Azure Container Registry (ACR)-hivatkozási információi rendelkezik. Az Azure piactér a lemezkép másolja a nyilvános piactérről regisztrációs adatbázisba, és szeretné elérhetővé tenni az ügyfelek hitelesítő után. Használhat egy tárolórendszerképet az Azure-felhasználó minden kérelmeit a szolgáltatás a Marketplace-en tárolóregisztrációs adatbázisból szolgálja ki.

![Tároló metaadatai](./media/containers-image-repository.png)
    
A **adattár részletei** az előző képernyőn rögzítése a következő mezőket tartalmazzák:

-   **Előfizetés-azonosító** – ahol az ACR-REL megtalálható az Azure előfizetés-azonosító.
-   **Erőforráscsoport neve** – az ACR-REL, az erőforráscsoport nevét.
-   **Beállításjegyzék neve** – az ACR-név.
-   **Adattár neve** -adattár neve. Ez a név beállítása után ez az érték nem lehet módosítani. Használjon egy egyedi nevet a fiókjában ajánlatokkal való ütközés elkerülése érdekében.
-   **Felhasználónév** – az ACR-lemezképhez tartozó felhasználónév (rendszergazdai felhasználónév).
-   **Jelszó** – az ACR-lemezképéhez tartozó jelszót.

    >[!NOTE]
    >A felhasználónév és jelszó szükségesek győződjön meg arról, hogy partnereink férhetnek hozzá az ACR-REL már említettük, a közzétételi folyamat.

### <a name="image-version"></a>Lemezkép verziója

Amikor tesz közzé egy tárolórendszerképet, megadhat egy vagy több lemezképet címkét, és SHA digests.

**Kép címke- vagy**
 
- A címke- vagy tartalmaznia kell egy `latest` címke és a egy verzió címkéje (például kezdve `xx.xx.xx-` ahol xx számos). Lehetnek [címkék manifest](https://github.com/estesp/manifest-tool) , amelyekre több platformon. Jegyzékfájl címke által hivatkozott összes címkék is hozzá kell adni úgy is fel őket. 
- A címkék használatával több verzióját is hozzáadhat. Az összes manifest címkék (kivéve `latest`) kell kezdődnie vagy `X.Y-` vagy `X.Y.Z-` Z X, Y, amelyeknél egész számok. <br/> Például ha egy `latest` pontok megjelölése `1.0.1-linux-x64`, `1.0.1-linux-arm32`, és `1.0.1-windows-arm32`, ezekkel a címkékkel kell hozzá.

>[!NOTE]
>Ne felejtse el hozzáadni a **teszt címke** a lemezképre, így azonosíthatja a rendszerkép tesztelése során.

## <a name="next-steps"></a>További lépések

Használja a [piactér lapján](./cpp-marketplace-tab.md) hozhat létre a piactéren az ajánlat leírását. 