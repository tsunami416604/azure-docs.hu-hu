---
title: "A Cortana Intelligence Gallery az egyéni modulok |} Microsoft Docs"
description: "A Cortana Intelligence Gallery egyéni gépi tanulási modulok felderítése."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.openlocfilehash: 4bab94c04f09261eaa88b9e6a225c05f57992ab0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>A Cortana Intelligence Gallery egyéni gépi tanulási modulok felderítése
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>A Machine Learning Studióban az egyéni modulok
A Cortana Intelligence Gallery kínál számos [az egyéni modulok](https://gallery.cortanaintelligence.com/customModules) , bontsa ki az Azure Machine Learning Studio képességeit. Importálhatja a modulokat használ a kísérletekben, így még akkor is speciális prediktív elemzési megoldások fejleszthet.

Jelenleg a gyűjtemény kínál a modulok *time series analytics*, *társítási szabályok*, *algoritmusok Fürtszolgáltatás* (túl a k-közép), és  *képi megjelenítések*, és más workhorse segédprogram modulok.


## <a name="discover"></a>Ismertetők
Az egyéni modulok tallózással [a katalógusban](http://gallery.cortanaintelligence.com)a **további**, jelölje be **az egyéni modulok**.

![Válassza ki az egyéni modulok gyűjtemény kezdőlapján](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

A  **[egyéni modulok](https://gallery.cortanaintelligence.com/customModules)**  lap megjeleníti a legutóbb hozzáadott és népszerű modulokat. Minden egyéni modul megtekintéséhez jelölje ki a **láthatja az összes** gombra. Egy adott egyéni modult keres, válassza ki a **láthatja az összes**, és jelölje ki a szűrési feltételeket. Kifejezést is írhatja a **keresési** a gyűjtemény lap felső részén.

![Jelölje be "Lásd: minden" összes egyéni modulok tallózással](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Ismertetése

A közzétett egyéni modul működésének jelölje ki az egyéni modult a modul részletek lap megnyitásához. A Részletek lap egy egységes és informatív tanulási élményt nyújt. Például a Részletek lap emel ki, a modul célját, és várt bemenetek, kimenetek és paraméterek sorolja fel. A Részletek lap egy hivatkozást az alapul szolgáló adatforrás kód, amely megvizsgálja, és testre is rendelkezik.

### <a name="comment-and-share"></a>Megjegyzés és megosztása
A Részletek lap egyéni modulon a **megjegyzések** szakaszban lehet megjegyzést, visszajelzést vagy kérdése van a modul. A modul ismerősök vagy a Twitteren vagy LinkedIn munkatársak is megoszthatja. A meghívott más felhasználóknak a lapnak a megtekintésére a modul részleteit megjelenítő oldalra mutató hivatkozást is képes e-mail.

![Ez az elem megosztása ismerősök](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Adja hozzá a saját megjegyzéseit](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importálás
A saját kísérletek importálhatja bármely egyéni modul a gyűjteményből.

A Cortana Intelligence Gallery kétféleképpen egy példányát a modul importálásához:

* **A gyűjteményből**. Egyéni modul importálása a katalógusból, ha egy minta kísérletet, amely lehetővé teszi az példa bemutatja, hogyan modulját is kap.
* **A számítógép-Learning Studio**. A Machine Learning Studióban használata közben bármely egyéni modul importálható (ebben az esetben nem kap a mintakísérletet).

### <a name="from-the-gallery"></a>A gyűjteményből

1. Nyissa meg a modul részleteit megjelenítő oldalon a gyűjteményben. 
2. Válassza ki **Megnyitás a Studióban**.
   
    ![Nyissa meg egyéni modul a gyűjteményből](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Minden egyéni modul egy minta kísérletet, amely bemutatja, hogyan használja a modult tartalmaz. Ha bejelöli **Megnyitás a Studióban**, a Machine Learning Studio munkaterületen megnyílik a mintakísérletet. (Ha még nincs bejelentkezett Studio, kéri első bejelentkezés a Microsoft-fiókjával.)

A mintakísérletet mellett az egyéni modul másolja a munkaterületen. Az összes a beépített vagy egyéni Machine Learning Studio moduljai, a modulpalettán is bekerül. Most már használhatja a saját kísérletekben, mint bármely más modul a munkaterületen.

### <a name="from-within-machine-learning-studio"></a>A számítógép-Learning Studio

1. A Machine Learning Studióban, válassza ki a **új**.
2. Válassza ki **modul**. Gyűjteményelem modulokat lehetőségek közül választhat, vagy egy adott modult megkeresni a **keresési** mezőbe.
3. Az egér pont egy modult, majd válassza ki **importálási modul**. (Válassza ki ahhoz, hogy a modul információt, **-katalógus megtekintése**. Ezzel megnyitná a modul részleteit megjelenítő oldalon a katalógusban.)
   
    ![A Machine Learning Studióhoz egyéni modul importálása](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Az egyéni modul másolja a munkaterületet, és a beépített vagy egyéni Machine Learning Studio moduljai, a modulpalettán helyezett. Most már használhatja a saját kísérletekben, mint bármely más modul a munkaterületen.

## <a name="use"></a>Használat

Függetlenül attól, milyen módszert szeretne importálni egy egyéni modult, amikor importálja a modult, a modul kerül a Machine Learning Studióban a modulpalettán. A modul palettáról is használhatja az egyéni modul a kísérletben a munkaterületen, csakúgy, mint bármely más modul.

Az importált modul használata:

1. A kísérlet létrehozásának, vagy egy meglévő kísérlet megnyitásához.
2. Bontsa ki az egyéni modulok a munkaterületen, a modulpalettán listájában válassza ki **egyéni**. A modulpalettán a kísérletvászonra balra van.
   
    ![Egyéni Modullista Studio palettán](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Válassza ki azt a modult importált, és húzza a kísérletvászonra.


**[Ugrás a katalógusban](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

