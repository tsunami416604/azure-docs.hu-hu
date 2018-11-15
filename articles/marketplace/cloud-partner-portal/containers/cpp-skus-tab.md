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
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683978"
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

A tároló metaadatait az adattár részletei belül az Azure Container Registry (ACR)-hivatkozási információi rendelkezik. Az Azure Marketplace-en másolja át a lemezkép egy Marketplace-specifikus, nyilvános regisztrációs adatbázisba, és majd elérhetővé teszi a lemezkép az ügyfelek hitelesítő után. Érkező kérések az Azure felhasználói az Azure Marketplace-en tárolórendszerképet fel a szolgáltatás a nyilvános beállításjegyzék a Marketplace-en, nem ACR szolgálja ki.

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