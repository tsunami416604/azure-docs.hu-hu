---
title: Tömeges másolás fájlokból adatbázisba
description: Megtudhatja, hogyan másolhat adatokat tömegesen az Azure Data Lake Storage Gen2-ből az Azure Synapse Analytics / Azure SQL Database szolgáltatásba.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414872"
---
# <a name="bulk-copy-from-files-to-database"></a>Tömeges másolás fájlokból adatbázisba

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk egy megoldássablont ismertet, amely az Azure Data Lake Storage Gen2-ből tömegesen másolhatja az adatokat az Azure Synapse Analytics / Azure SQL Database szolgáltatásba.

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon az Azure Data Lake Storage Gen2 forrásból szerzi be a fájlokat. Ezután a forrás minden egyes fájlját átitatja, és átmásolja a fájlt a céladattárba. 

Jelenleg ez a sablon csak **a DelimitedText** formátumú adatok másolását támogatja. A más adatformátumú fájlok a forrásadattárból is beolvashatók, de nem másolhatók a céladattárba.  

A sablon három tevékenységet tartalmaz:
- **A Metaadat-tevékenység beolvasása** fájlokat az Azure Data Lake Storage Gen2, és átadja azokat a későbbi *ForEach* tevékenység.
- **ForEach** tevékenység lekéri a fájlokat a *Metaadat-jelentés* tevékenység és iterates minden fájlt a *Másolás* tevékenység.
- **A** másolási tevékenység a *ForEach* tevékenységben található, így az egyes fájlokat a forrásadattárból a céladattárba másolhatja.

A sablon a következő két paramétert határozza meg:
- *SourceContainer* a gyökértároló elérési útja, ahonnan az adatok az Azure Data Lake Storage Gen2. 
- *A SourceDirectory* a gyökértároló alatti könyvtárelérési út, ahonnan az adatok at az Azure Data Lake Storage Gen2-ből másolja a rendszer.

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Nyissa meg a **Fájlokból az adatbázisból tömeges** másolássablont. **Új** kapcsolat létrehozása a forrás Gen2 tárolóval. Ne feledje, hogy a "GetMetadataDataset" és a "SourceDataset" a forrásfájl-tároló ugyanazon kapcsolatára mutató hivatkozások.

    ![Új kapcsolat létrehozása a forrásadattárhoz](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. **Hozzon** létre egy új kapcsolatot a fogadó adattár, amely az adatok másolása.

    ![Új kapcsolat létrehozása a fogadó adattárával](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Válassza **a Sablon használata lehetőséget.**

    ![Sablon használata](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. A következő példában látható módon egy folyamat jönne létre:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Ha az **Azure Synapse Analytics (korábban SQL DW)** adatcélként a fent említett **2.** Ahogy a következő képernyőképen látható, a sablon automatikusan létrehoz egy *tárolási útvonalat* a Blob storage számára. Ellenőrizze, hogy a tároló a folyamat futtatása után lett-e létrehozva.
        
    ![Polibázis beállítása](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![Kattintson a **Debug**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Ha a folyamat sikeresen befejeződik, a következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)