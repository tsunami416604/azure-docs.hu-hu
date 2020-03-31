---
title: 'Konvertálás adatkészletre: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Convert to Dataset modult az Azure Machine Learningben az adatbevitel microsoft Azure Machine Learning által használt belső adatkészlet-formátumra való konvertálásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456590"
---
# <a name="convert-to-dataset"></a>Átalakítás adathalmazzá

Ez a cikk ismerteti, hogyan használhatja a Convert to Dataset modul az Azure Machine Learning designer (előzetes verzió) konvertálni a folyamat a tervező belső formátumát.
  
Az átalakítás a legtöbb esetben nem szükséges. Az Azure Machine Learning implicit módon konvertálja az adatokat a natív adatkészlet formátumba, amikor bármilyen műveletet hajtanak végre az adatokon. 

Javasoljuk, hogy mentse az adatokat az adatkészlet formátumába, ha valamilyen normalizálást vagy tisztítást hajtott végre egy adathalmazon, és biztosítani szeretné, hogy a módosításokat más folyamatokban is használják.  
  
> [!NOTE]
> A Konvertálás adatkészletbe csak az adatok formátumát módosítja. Nem menti az adatok új példányát a munkaterületre. Az adatkészlet mentéséhez kattintson duplán a kimeneti portra, válassza a **Mentés adatkészletként**lehetőséget, és adjon meg egy új nevet.  
  
## <a name="how-to-use-convert-to-dataset"></a>A Konvertálás adatkészletgé használata  

Azt javasoljuk, hogy a [Metaadatok szerkesztése](edit-metadata.md) modulsegítségével készítse elő az adatkészletet, mielőtt adatkészletbe konvertálna. Oszlopneveket adhat hozzá vagy módosíthat, módosíthatja az adattípusokat, és szükség szerint egyéb módosításokat hajthat végre.

1.  Adja hozzá a Konvertálás adatkészletbe modult a folyamathoz. Ez a modul a tervező **Adatátalakítási** kategóriájában található. 

2. Csatlakoztassa bármely olyan modulhoz, amely adatkészletet ad ki.   

    Mindaddig, amíg az adatok [táblázatos,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)átalakíthatja őket adatkészletté. Ide tartoznak az [Adatok importálása](import-data.md), [az Adatok bevitele manuálisan](enter-data-manually.md)létrehozott adatok , illetve az [Átalakítás alkalmazása](apply-transformation.md)révén átalakított adatkészletek .

3.  A **Művelet** legördülő listában adja meg, hogy az adatkészlet mentése előtt szeretné-e az adatok karbantartását végezni:  
  
    - **Nincs**: Használja az adatokat a szerint.  
  
    - **SetMissingValue**: Egy adott értéket az adatkészlet hiányzó értékére állíthat be. Az alapértelmezett helyőrző a kérdőjel karakter (?), de az **Egyéni hiányzó érték** beállítással másik értéket adhat meg. Ha például a **Taxi** szót adja meg **az egyéni hiányzó értékhez,** akkor az adatkészletben lévő **Taxi** összes példánya a hiányzó értékre változik.
  
    - **ReplaceValues**: Ezzel a beállítással egyetlen pontos értéket adhat meg, amelyet bármely más pontos értékre le kell cserélni. A hiányzó értékeket vagy egyéni értékeket a **Csere** módszer beállításával helyettesítheti:

      - **Hiányzik**: Válassza ezt a lehetőséget a bemeneti adatkészlet hiányzó értékeinek lecseréléséhez. Az **Új érték mezőbe**írja be azt az értéket, amelyre a hiányzó értékeket le cseréli.
      - **Egyéni**: Válassza ezt a lehetőséget a bemeneti adatkészlet egyéni értékeinek lecseréléséhez. Az **Egyéni érték mezőbe**írja be a keresett értéket. Ha például az adatok `obs` a hiányzó értékek helyőrzőjeként `obs`használt karakterláncot tartalmazzák, írja be a értéket. Az **Új érték mezőbe**írja be azt az új értéket, amelyre az eredeti karakterláncot le szeretné cserélni.
  
    Ne feledje, hogy a **ReplaceValues** művelet csak a pontos egyezésekre vonatkozik. Ezeket a karakterláncokat például `obs.`ez `obsolete`nem érinti: , .  
 
  
5.  Küldje el a folyamatot.  

## <a name="results"></a>Results (Eredmények)

+  Ha az eredményül kapott adatkészletet új névvel szeretné menteni, jelölje ki az **adatkészlet regisztrálása** ikont a modul jobb oldali paneljének **Kimenetek** lapján.  
  
## <a name="technical-notes"></a>Technikai megjegyzések  

-   Bármely modul, amely egy adatkészletet bemenetként vesz fel, a CSV-fájlban vagy a TSV-fájlban is adatokat vehet fel. A modulkódok futtatása előtt a bemenetek előfeldolgozása történik. Az előfeldolgozás egyenértékű a Convert to Dataset modul futtatásával a bemeneten.  
  
-   Az SVMLight formátumból nem konvertálható adatkészletté.  
  
-   Egyéni csereművelet megadásakor a keresés és csere művelet a teljes értékekre vonatkozik. Részleges egyezésnem engedélyezett. A 3-at például lecserélheti egy -1-esre vagy 33-ra, de a 3-ast nem helyettesítheti kétjegyű számban, például 35-ben.  
  
-   Egyéni csereműveletek esetén a csere csendben sikertelen lesz, ha olyan karaktert használ csereként, amely nem felel meg az oszlop aktuális adattípusának.  

  
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
