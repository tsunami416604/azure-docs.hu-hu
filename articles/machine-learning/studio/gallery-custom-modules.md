---
title: Az Azure, AI-katalógusban egyéni modulok – Azure Machine Learning Studióban |} A Microsoft Docs
description: Fedezze fel az egyéni machine learning-modulok az Azure AI-katalógusban.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: a504bcfbbcd70c1d6fc8402d4c232e7e8a5476b0
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313741"
---
# <a name="machine-learning-studio-discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Machine Learning Studio: Az Azure AI-katalógusban egyéni machine learning-modulok felderítése

[Az Azure AI-katalógusban](http://gallery.cortanaintelligence.com) fájlközzétételi [egyéni modulok](https://gallery.cortanaintelligence.com/customModules) , bontsa ki az Azure Machine Learning Studio képességeit. A modulok, így még több speciális prediktív elemzési megoldásokat fejleszthet a kísérletek használandó importálhatja.

Jelenleg a katalógust kínál a modulok *time series analytics*, *társítási szabályok*, *algoritmusok Fürtszolgáltatás* (kívül a k-közép), és  *Vizualizációk*, és más workhorse segédprogram modulok.


## <a name="discover"></a>Ismertetők
Egyéni modulok tallózással [a katalógusban](http://gallery.cortanaintelligence.com)alatt **további**válassza **egyéni modulok**.

![Válassza ki az egyéni modulok a tár kezdőlapján](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

A **[egyéni modulok](https://gallery.cortanaintelligence.com/customModules)** lap nemrégiben hozzáadott és népszerű modulok listáját jeleníti meg. Minden egyéni modulok megtekintéséhez jelölje ki a **összes** gombra. Egy adott egyéni modult keres, válassza ki a **összes**, és majd szűrőfeltételeket. A keresési kifejezéseket is megadhatja a **keresési** a katalógus lap tetején.

![Válassza "tekintse meg az összes", keresse meg az összes egyéni modulok](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Ismertetése

Egy közzétett egyéni modul működésének megismerése, jelölje ki az egyéni modult a modul részletei lap megnyitásához. A Részletek lap egy egységes és informatív tanulási élményt tesz lehetővé. Például a Részletek lap kiemeli a modul célját, és a várt bemenetek, kimenetek és paramétereket sorolja fel. A Részletek lap az alapul szolgáló forrás kód, amely megvizsgálja, és testre szabhatja mutató hivatkozást is tartalmaz.

### <a name="comment-and-share"></a>Megjegyzés és megosztás
A Részletek lap egy egyéni modult a **megjegyzések** szakaszban el megjegyzés, visszajelzés, vagy a modul kapcsolatos kérdések feltevése. A modul barátainak vagy Twitter- vagy LinkedIn munkatársaival is megoszthatja. A modul részleteit megjelenítő oldalra, más felhasználók számára a lap megtekintéséhez meghívni egy hivatkozást is biztonságosan e.

![Ez az elem megoszthatja ismerősök felvételének engedélyezése](./media/gallery-how-to-use-contribute-publish/share-links.png)

![A saját Megjegyzések hozzáadása](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importálás
Egyéni modulok, a saját kísérletek importálhatja a katalógusból.

Az Azure AI-katalógusban importálhat egy másolatot a modul két lehetőséget kínál:

* **A katalógusból**. Amikor egy egyéni modult a katalógusból, érhet el egy mintakísérlet, amelyen a modul használata egy példát.
* **A Machine Learning Studióba**. Egyéni modulok importálhatja a Machine Learning Studióban használata közben (ebben az esetben nem kap a mintakísérlet).

### <a name="from-the-gallery"></a>A katalógusból

1. A katalógusban a modul részletei lap megnyitásához. 
2. Válassza ki **Megnyitás a Studióban**.
   
    ![Nyissa meg egyéni modult a katalógusból](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Minden egyéni modul, amely bemutatja, hogyan használható a modul egy mintakísérlet tartalmazza. Ha bejelöli **Megnyitás a Studióban**, mintakísérlet megnyílik a Machine Learning Studio munkaterületét. (Ha még nem jelentkezett Studióban, kéri az első bejelentkezéskor a Microsoft-fiókjával.)

Az egyéni modult mintakísérlet mellett a munkaterület másolja. A modulpaletta, minden a beépített vagy egyéni Machine Learning Studio modul is helyezi. Most már használhatja a saját kísérletek, mint bármely egyéb modult a munkaterületén.

### <a name="from-within-machine-learning-studio"></a>A Machine Learning Studióba

1. A Machine Learning Studióban, válassza ki a **új**.
2. Válassza ki **modul**. Galéria moduljai listájából válassza ki, vagy keresse meg az adott modulok használatával a **keresési** mezőbe.
3. Mutasson az egérmutatóval egy moduljába, és válassza ki **modul importálása**. (Információt szeretne kapni a modult, válassza ki a **megjelenítése katalógusban**. Ekkor megjelenik a katalógusban a modul részleteit megjelenítő oldalra.)
   
    ![Egyéni modul importálása a Machine Learning studióba](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Az egyéni modult a workspace másolja és a beépített vagy egyéni Machine Learning Studio-modulok a modulpaletta helyezi el. Most már használhatja a saját kísérletek, mint bármely egyéb modult a munkaterületén.

## <a name="use"></a>Használat

Függetlenül attól, milyen módszert szeretne importálni egy egyéni modult, amikor importálja a modult, a modul kerül a modulpaletta a Machine Learning Studióban. A modul palettáról az egyéni modult használhatja bármely kísérletben a munkaterületen, csakúgy, mint bármely egyéb modult.

Az importált modul használata:

1. A kísérlet létrehozásának, vagy egy meglévő kísérlet megnyitásához.
2. Bontsa ki a munkaterületen, a modulpaletta az egyéni modulok listájában válassza ki a **egyéni**. A modulpaletta, hogy a bal oldalon, a kísérletvászonra.
   
    ![A modul egyéni lista Studio palettán](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Válassza ki az importált modul, és húzza a kísérletvászonra.


**[A katalógus megnyitása](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

