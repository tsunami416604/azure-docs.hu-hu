---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523052"
---
## <a name="select-an-ingestion-type"></a>Betöltési típus kiválasztása

A **Betöltés i. típusához**válasszon az alábbi lehetőségek közül:
   * **a storage-ból** – a Hivatkozás a **tárhoz** mezőben adja hozzá a tárfiók URL-címét. Blob [SAS URL-címe](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) magántárfiókokhoz.
   
      ![Egykattintásos betöltés a tárolóból](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **fájlból** – a **Tallózás gombra** a fájl megkereséséhez vagy a fájl húzásához.
  
      ![Egykattintásos betöltés fájlból](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **tárolóból** – a Hivatkozás a **tárolóhoz** mezőben adja hozzá a tároló [SAS URL-címét,](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) és adja meg a mintaméretét.

      ![Egykattintásos betöltés a tárolóból](media/data-explorer-one-click-ingestion-types/from-container.png)

  Megjelenik egy adatminta. Ha szeretné, szűrheti, hogy csak azok a fájlok jelenjenek meg, amelyek meghatározott karakterekkel kezdődnek. A szűrők beállításakor az előnézet automatikusan frissül.
  
  Szűrhet például minden olyan fájlra, amely a *szóadatokkal* kezdődik, és *.csv.gz* kiterjesztéssel végződik.

  ![Egykattintásos betöltési szűrő](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
