---
title: 'Adatimportálás: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan tölthetők be adatok egy gépi tanulási kísérletbe a meglévő felhőalapú adatszolgáltatásokból a Azure Machine Learning szolgáltatás adatimportálási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e8522c759efb0cc599bbcf8e7569f6045da571a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128785"
---
# <a name="import-data-module"></a>Adatmodul importálása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal az adatok betölthetők egy gépi tanulási kísérletbe a meglévő felhőalapú adatszolgáltatásokból.  
A modul mostantól tartalmaz egy varázslót, amely segítséget nyújt a tárolási lehetőségek kiválasztásához, és a meglévő előfizetések és fiókok közül választhatja ki az összes beállítás gyors konfigurálását. Módosítania kell egy meglévő adatkapcsolatokat? Nincs probléma; a varázsló betölti az összes korábbi konfigurációs részletet, így nem kell újból elindítania. 
  
A kívánt adatok definiálása és a forráshoz való kapcsolódás után az [adatok importálása](./import-data.md) az egyes oszlopok adattípusát a benne foglalt értékek alapján következteti ki, és betölti az adatait a Azure Machine learning munkaterületre. Az importálási [adat](./import-data.md) kimenete egy olyan adatkészlet, amely bármilyen kísérlethez használható.

  
Ha a forrásadatok megváltoznak, frissítheti az adatkészletet, és új adatokat adhat hozzá az [importálási adatokat](./import-data.md)újrafuttatva. Ha azonban nem kívánja újra beolvasni a forrást a kísérlet minden egyes futtatásakor, válassza a gyorsítótárazott **eredmények használata** beállítást igaz értékre. Ha ez a beállítás be van jelölve, a modul ellenőrzi, hogy a kísérlet korábban is ugyanazzal a forrással és azonos beviteli lehetőségekkel fut-e. Ha a rendszer egy korábbi futtatást talál, a rendszer a gyorsítótárban lévő adatok helyett a forrásból származó adatok újratöltését fogja használni.
 

## <a name="data-sources"></a>Adatforrások

Az Adatimportálási modul a következő adatforrásokat támogatja. Az egyes adatforrások használatára vonatkozó részletes utasításokhoz és példákhoz kattintson a hivatkozásokra. 
 
Ha nem tudja, hogyan vagy hol tárolja az adatait, tekintse meg az útmutató az adatelemzési folyamat általános adatforgatókönyveit:  [Speciális elemzési forgatókönyvek Azure Machine Learningban](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Adatforrás| Használat|
|-----------|-----------|  
|[Webes URL-cím HTTP-n keresztül](./import-from-web-url-via-http.md)|A HTTP protokollt használó webes URL-címen tárolt és a CSV-, TSV-, ARFF-vagy SvmLight-formátumokban megadott adatforrások beolvasása|  
|[Importálás az Azure Blob Storageból](./import-from-azure-blob-storage.md) |Az Azure Blob Service-ben tárolt adatlekérdezés|  

## <a name="how-to-use-import-data"></a>Az importálási információ használata
 
1. Adja hozzá az Adatimportálási modult a kísérlethez. Ezt a modult a felület **adatbevitel és kimenet** kategóriájában találja.

2. Az adatforrás varázsló használatával történő konfigurálásához kattintson az **adatimportálás indítása varázsló** elemre.

    A varázsló lekéri a fiók nevét és a hitelesítő adatokat, és segítséget nyújt más beállítások konfigurálásához. Ha meglévő konfigurációt szerkeszt, először az aktuális értékeket tölti be.

3. Ha nem szeretné használni a varázslót, kattintson az **adatforrás**elemre, és válassza ki, hogy milyen típusú felhőalapú tárhelyet szeretne olvasni. 

    A további beállítások a kiválasztott tárolási típustól függenek, valamint azt, hogy a tárterület védett-e. Előfordulhat, hogy meg kell adnia a fiók nevét, a fájltípust vagy a hitelesítő adatokat. Egyes források nem igényelnek hitelesítést; mások számára előfordulhat, hogy ismernie kell a fiók nevét, kulcsát vagy tárolójának nevét.

4. Jelölje be a **gyorsítótárazott eredmények használata** lehetőséget, ha az adatkészletet az egymást követő futtatások újbóli felhasználásához szeretné gyorsítótárazni.

    Feltéve, hogy a modul paramétereinek nincsenek más módosításai, a kísérlet csak a modul első futtatásakor tölti be az adatokat, majd ezt követően az adatkészlet gyorsítótárazott verzióját használja.

    Törölje ezt a beállítást, ha a kísérlet futtatásakor minden alkalommal újra be kell töltenie az adatfrissítést.

5. Futtassa a kísérletet.

    Amikor az adatok importálása betölti az adatok betöltését a felületre, az az egyes oszlopok adattípusát a benne foglalt értékek alapján, numerikus vagy kategorikusan következteti ki.

    - Ha van fejléc, a fejléc a kimeneti adatkészlet oszlopainak elnevezésére szolgál.

    - Ha az adatsorokban nem találhatók oszlopfejlécek, az új oszlopnevek a col1, a col2,... formátum használatával jönnek létre. , Coln *.

## <a name="results"></a>Results (Eredmények)

Amikor az importálás befejeződik, kattintson a kimeneti adatkészletre, és válassza a **Megjelenítés** lehetőséget, hogy megtekintse az adatokat az importálás sikeres volt-e.

Ha az adatokat újbóli használatra szeretné menteni, ahelyett, hogy a kísérlet minden egyes futtatásakor új adatkészletet importál, kattintson a jobb gombbal a kimenetre, és válassza a **Mentés másként adatkészlet**lehetőséget. Válassza ki az adatkészlet nevét. A mentett adatkészlet megőrzi az adatokat a mentéskor, és a kísérlet újrafuttatásakor nem frissül az adat, még akkor is, ha a kísérletben szereplő adatkészlet megváltozik. Ez hasznos lehet az adatpillanatképek készítésekor.

Az információk importálása után szükség lehet néhány további modellezési és elemzési előkészületre:


- Használja a [metaadatok szerkesztése](./edit-metadata.md) lehetőséget az oszlopnevek módosításához, egy oszlop más adattípusként való kezeléséhez, illetve annak jelzéséhez, hogy egyes oszlopok címkék vagy szolgáltatások.

- Az adatkészletben az [Oszlopok kiválasztása](./select-columns-in-dataset.md) lehetőséggel kiválaszthatja a modellezéshez átalakítani vagy használni kívánt oszlopok részhalmazát. Az átalakított vagy eltávolított oszlopok könnyedén csatlakoztathatók az eredeti adatkészlethez az [Oszlopok hozzáadása](./add-columns.md) modullal.  

- A [Partition és a Sample](./partition-and-sample.md) használatával ossza fel az adatkészletet, végezzen mintavételezést, vagy töltse le az első n sort.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 