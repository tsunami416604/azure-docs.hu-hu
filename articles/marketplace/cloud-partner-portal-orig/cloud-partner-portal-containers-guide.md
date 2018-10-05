---
title: Tárolók |} A Microsoft Docs
description: Egy tárolórendszerkép-közzététel lépéseket.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810599"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>A Cloud Partner portálra egy Tárolórendszerképet közzététele
========================================================

Ez a cikk ismerteti, hogyan tehet közzé egy új tárolórendszerképet a Cloud Partner portálra.

Használja az alábbi lépéseket egy új tárolórendszerképet közzététele.

1. Válassza ki **új ajánlat** majd **tárolók**

    ![Tárolók a lehetőséggel új ajánlat](media/cpp-containers-guide/azure-container-offer.png)

2. Az ajánlat identitás alatt ajánlat beállítások lapon adja meg a **Ajánlatazonosító**, **közzétevő-azonosító**, és **neve**.

    ![Az ajánlat identitás](media/cpp-containers-guide/containers-offer-settings.png)

3. Az SKU-k lapon válassza ki a **új Termékváltozat**.
    >[!NOTE]
    >Egynél több Termékváltozat adhat hozzá.

    ![Új Termékváltozat kérése](media/cpp-containers-guide/containers-sku-settings.png)

4. Adja meg a Termékváltozat és a tároló adatait. Minden SKU felel meg egy tárolórendszerképet. A Termékváltozat két részből áll:

    -   Termékváltozat-metaadatok
    -   Tároló metaadatai

    A Termékváltozat-metaadatok a következő a tárolórendszerképeket megjelenítendő adatait tartalmazza.

    ![Termékváltozat-metaadatok](media/cpp-containers-guide/containers-sku-details.png)

    A tároló metaadatait az adattár részletei-hivatkozási információi rendelkezik az Azure container registry (ACR) belül. Az Azure Marketplace-en Ezután másolja át a rendszerkép a nyilvános piactérről regisztrációs adatbázisba, és szeretné elérhetővé tenni az ügyfelek hitelesítő után. Használhat egy tárolórendszerképet az Azure-felhasználó minden kérelmeit a szolgáltatás a Marketplace-en tárolóregisztrációs adatbázisból szolgálja ki.

   ![Tároló metaadatai](media/cpp-containers-guide/containers-image-repository.png)

    Az előző képernyő-rögzítési az adattár részletei a következő mezőket tartalmazzák:

    -   **Előfizetés-azonosító** – ahol az ACR-beállításjegyzékbe megtalálható az Azure előfizetés-azonosító.
    -   **Erőforráscsoport neve** – az erőforráscsoport neve, az ACR-beállításjegyzékbe.
    -   **Beállításjegyzék neve** – az ACR-beállításjegyzék neve.
    -   **Adattár neve** -adattár neve. A beállítása után, ezt az értéket később már nem módosítható. Ez meg kell adni egy egyedi nevet, hogy a fiók nincs egyéb ajánlattal nem ugyanazzal a névvel.
    -   **Felhasználónév** – az ACR-REL (rendszergazdai felhasználónév) tartozó felhasználónév.
    -   **Jelszó** – az ACR-REL tartozó jelszót.

    >[!NOTE]
    >A felhasználónév és jelszó szükségesek győződjön meg arról, hogy partnereink férhetnek hozzá az ACR-REL már említettük, a közzétételi folyamat.

    Közzététel egy tárolórendszerképet, miközben egy vagy több címkéket is megadhatja. Mellett képcímkeként partnerek SHA emésztett is megadhatja. Kérjük, adjon hozzá egy **teszt címke** a lemezképen, a lemezkép azonosíthassa a tesztelés során.

5. A piactér lap hozzáadása a marketing-specifikus tartalmait.

    ![Marketplace-információ](media/cpp-containers-guide/containers-marketplace-tab.png)

6. A támogatási lapon adja meg az engineering contact és ügyfél-támogatási információkat.

   ![Támogatási információk](media/cpp-containers-guide/containers-support-tab.png)

7. Válassza ki **közzététel** az ajánlat közzététele. Után válassza a közzététel lehetőséget, megjelenik egy idővonalon, amely megjeleníti a lépések tehetők közzé a tárolórendszerkép.
