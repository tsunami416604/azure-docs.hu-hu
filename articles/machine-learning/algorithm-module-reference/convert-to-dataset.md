---
title: 'Átalakítás adatkészletbe: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan alakíthatja át az adatbevitelt a Microsoft Azure Machine Learning által használt belső adatkészlet-formátumba a Azure Machine Learning szolgáltatásban az átalakítás adatkészlethez modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516252"
---
# <a name="convert-to-dataset"></a>Átalakítás adatkészletbe

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designer (előzetes verzió) [adatkészletre való konvertálása](convert-to-dataset.md) a folyamat összes adatának a tervező által használt belső formátumra való átalakításához.
  
A legtöbb esetben nem szükséges az átalakítás, mert a Azure Machine Learning implicit módon alakítja át az adatokat a natív adatkészlet formátumára, amikor bármilyen műveletet végeznek az adaton. 

Ha azonban az adatokat az adatkészlet formátumára szeretné menteni, ajánlott valamilyen normalizálás vagy tisztítást végrehajtani egy adott adathalmazon, és biztosítani szeretné, hogy a módosítások a további folyamatokban legyenek használatban.  
  
> [!NOTE]
> Az [adatkészletre való átalakítás](convert-to-dataset.md) csak az adat formátumát módosítja. Nem menti a munkaterületen lévő új másolatát. Az adatkészlet mentéséhez kattintson duplán a kimeneti portra, válassza a **Mentés adatkészletként**lehetőséget, és írjon be egy új nevet.  
  
## <a name="how-to-use-convert-to-dataset"></a>A convert for DataSet használata  

Javasoljuk, hogy a [metaadatok szerkesztése](edit-metadata.md) modul használatával készítse elő az adatkészletet az [adatkészletre való átalakítás](convert-to-dataset.md)használata előtt.  Hozzáadhat vagy módosíthat oszlopokat, módosíthatja az adattípusokat, és így tovább.

1.  Adja hozzá az [átalakítás adatkészlethez](convert-to-dataset.md) modult a folyamathoz. Ezt a modult a Designer **Adatátalakítási** kategóriájában találja. 

2. Csatlakoztatása bármely modulhoz, amely egy adatkészletet eredményez.   

    Ha az adat [táblázatos](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), akkor átalakíthatja azt egy adatkészletbe. Ez magában foglalja az [importálási adatokat](import-data.md), az [adatbevitel manuális](enter-data-manually.md)használatával létrehozott adatokat, vagy a [transzformáció alkalmazása](apply-transformation.md)használatával átalakított adatkészleteket.

3.  A **művelet** legördülő listában adja meg, hogy az adatkészlet mentése előtt szeretné-e törölni az adatokat:  
  
    - **Nincs**: használja az-t a következőképpen.  
  
    - **SetMissingValue**: egy adott értéket állítson be hiányzó értékre az adatkészletben. Az alapértelmezett helyőrző a kérdőjel (?) karakter, de az **Egyéni hiányzó érték** lehetőség használatával más értéket is beírhat. Ha például a "taxi" értéket írja be az **Egyéni hiányzó értékre**, akkor az adatkészlet összes "taxi" értéke hiányzó értékre változik.
  
    - **ReplaceValues**: ezzel a beállítással egyetlen pontos értéket adhat meg, amelyet más pontos értékkel kell helyettesíteni.  A hiányzó értékeket vagy egyéni értékeket lecserélheti a **replace** metódus:- **missing**: válassza ezt a lehetőséget, ha a bemeneti adatkészlet hiányzó értékeit szeretné lecserélni. Az **új érték**mezőbe írja be az értéket, hogy a hiányzó értékeket cserélje le a következőre:.
            - **Egyéni**: válassza ezt a lehetőséget, ha az egyéni értékeket a bemeneti adatkészletben szeretné lecserélni. **Egyéni érték**esetén írja be a keresett értéket. Tegyük fel például, hogy az adatok a hiányzó értékek helyőrzőként használt karakterláncot tartalmazzák `obs` a `obs`t írja be. Az **új érték**mezőbe írja be az új értéket, és cserélje le az eredeti karakterláncot a következőre:.
  
    Vegye figyelembe, hogy a **ReplaceValues** művelet csak a pontos egyezésekre vonatkozik. Ezek a karakterláncok például nem érintettek: `obs.`, `obsolete`.  
 
  
5.  Futtassa a folyamatot, vagy kattintson a jobb gombbal a [Konvertálás adatkészletre](convert-to-dataset.md) elemre, és válassza a **kijelölt futtatása**lehetőséget.  

## <a name="results"></a>Results (Eredmények)

+  Ha az eredményül kapott adatkészletet új névvel szeretné menteni, kattintson a jobb gombbal az [adatkészletre való átalakítás](convert-to-dataset.md) kimenetére, majd válassza a **Mentés másként adatkészlet**lehetőséget.  
  
## <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

-   Az adatkészletet bemenetként használó modulok a CSV vagy a TSV adatokat is igénybe vehetnek. A modul kódjának végrehajtása előtt a rendszer végrehajtja a bemenetek előfeldolgozását, ami egyenértékű az [átalakítás adatkészlet](convert-to-dataset.md) -modulba való futtatásával a bemeneten.  
  
-   A SVMLight formátuma nem konvertálható adatkészletre.  
  
-   Egyéni Csere művelet megadásakor a keresés és csere művelet a teljes értékekre vonatkozik; a részleges egyezések nem engedélyezettek. Például lecserélheti a 3 értéket a-1 vagy a 33-es értékre, de a 3 karakter nem helyettesíthető két számjegyből (például 35).  
  
-   Az egyéni csere műveletei esetén a csere csendesen meghiúsul, ha helyettesítő karaktert használ, amely nem felel meg az oszlop aktuális adattípusának.  

  
## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
