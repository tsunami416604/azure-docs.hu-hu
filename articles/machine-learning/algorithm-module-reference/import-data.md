---
title: 'Adatimportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan tölthetők be adatok egy gépi tanulási folyamatba a meglévő felhőalapú adatszolgáltatásokból a Azure Machine Learning szolgáltatás adatimportálási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693139"
---
# <a name="import-data-module"></a>Adatmodul importálása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal az adatok betölthetők egy gépi tanulási folyamatba a meglévő felhőalapú adatszolgáltatásokból.  

Először válassza ki, hogy milyen típusú felhőalapú tárhelyet szeretne olvasni, és fejezze be a további beállításokat. A kívánt adatok definiálása és a forráshoz való kapcsolódás után az [adatok importálása](./import-data.md) az egyes oszlopok adattípusát a benne foglalt értékek alapján következteti ki, és betölti az adatait a Azure Machine learning munkaterületre. Az [importálási adat](./import-data.md) kimenete bármely folyamattal használható adatkészlet.

  
Ha a forrásadatok megváltoznak, frissítheti az adatkészletet, és új adatokat adhat hozzá az [importálási adatokat](./import-data.md)újrafuttatva. Ha azonban nem kívánja újra beolvasni a forrást a folyamat minden egyes futtatásakor, válassza a **gyorsítótárazott eredmények használata** beállítást igaz értékre. Ha ez a beállítás be van jelölve, a modul ellenőrzi, hogy a folyamat korábban fut-e ugyanazzal a forrással és ugyanazokkal a bemeneti beállításokkal. Ha a rendszer egy korábbi futtatást talál, a rendszer a gyorsítótárban lévő adatok helyett a forrásból származó adatok újratöltését fogja használni.
 

## <a name="data-sources"></a>Adatforrások

Az Adatimportálási modul a következő adatforrásokat támogatja. Az egyes adatforrások használatára vonatkozó részletes utasításokhoz és példákhoz kattintson a hivatkozásokra. 
 
Ha nem tudja, hogyan vagy hol tárolja az adatait, tekintse meg ezt az útmutatót az adatelemzési folyamat általános adatforgatókönyvei: a [speciális elemzési forgatókönyvek Azure Machine Learningban](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)című témakörben. 


|Adatforrás| Használat|
|-----------|-----------|  
|[Webes URL-cím HTTP-n keresztül](./import-from-web-url-via-http.md)|A HTTP protokollt használó webes URL-címen tárolt és a CSV-, TSV-, ARFF-vagy SvmLight-formátumokban megadott adatforrások beolvasása|  
|[Importálás az Azure Blob Storageból](./import-from-azure-blob-storage.md) |Az Azure Blob Service-ben tárolt adatlekérdezés|  
|[Importálás Azure SQL Database](./import-from-azure-sql-database.md) |Adatok lekérése Azure SQL Databaseról|

## <a name="how-to-configure-import-data"></a>Az importálási adatgyűjtés konfigurálása
 
1. Adja hozzá az **adatimportálási** modult a folyamathoz. Ezt a modult a felület **adatbevitel és kimenet** kategóriájában találja.

1. Kattintson az **adatforrás**elemre, és válassza ki, hogy milyen típusú felhőalapú tárhelyet szeretne olvasni. 

    A további beállítások a kiválasztott tárolási típustól függenek, valamint azt, hogy a tárterület védett-e. Előfordulhat, hogy meg kell adnia a fiók nevét, a fájltípust vagy a hitelesítő adatokat. Egyes források nem igényelnek hitelesítést; mások számára előfordulhat, hogy ismernie kell a fiók nevét, kulcsát vagy tárolójának nevét.

1. Jelölje be a **gyorsítótárazott eredmények használata** lehetőséget, ha az adatkészletet az egymást követő futtatások újbóli felhasználásához szeretné gyorsítótárazni.

    Feltételezve, hogy a modul paramétereinek nincsenek más módosításai, a folyamat csak a modul első futtatásakor tölti be az adatokat, majd ezt követően az adatkészlet gyorsítótárazott verzióját használja.

    Törölje ezt a beállítást, ha a folyamat minden egyes futtatásakor újra be kell töltenie az adatátvitelt.

1. A folyamat futtatása.

    Amikor az adatok importálása betölti az adatok betöltését a felületre, az az egyes oszlopok adattípusát a benne foglalt értékek alapján, numerikus vagy kategorikusan következteti ki.

    - Ha van fejléc, a fejléc a kimeneti adatkészlet oszlopainak elnevezésére szolgál.

    - Ha az adatsorokban nem találhatók oszlopfejlécek, az új oszlopnevek a col1, a col2,... formátum használatával jönnek létre. , Coln *.

## <a name="results"></a>Eredmények

Amikor az importálás befejeződik, kattintson a kimeneti adatkészletre, és válassza a **Megjelenítés** lehetőséget, hogy megtekintse az adatokat az importálás sikeres volt-e.

Ha az adatokat újbóli használatra szeretné menteni, ahelyett, hogy új adatkészletet importál a folyamat minden egyes futtatásakor, kattintson a jobb gombbal a kimenetre, és válassza a **Mentés másként adatkészlet**lehetőséget. Válassza ki az adatkészlet nevét. A mentett adatkészlet megőrzi az adatokat a mentéskor, és az adatokat nem frissíti a folyamat újrafuttatásakor, még akkor is, ha a folyamat módosul. Ez hasznos lehet az adatpillanatképek készítésekor.

Az információk importálása után szükség lehet néhány további modellezési és elemzési előkészületre:


- Használja a [metaadatok szerkesztése](./edit-metadata.md) lehetőséget az oszlopnevek módosításához, egy oszlop más adattípusként való kezeléséhez, illetve annak jelzéséhez, hogy egyes oszlopok címkék vagy szolgáltatások.

- Az [adatkészletben az Oszlopok kiválasztása](./select-columns-in-dataset.md) lehetőséggel kiválaszthatja a modellezéshez átalakítani vagy használni kívánt oszlopok részhalmazát. Az átalakított vagy eltávolított oszlopok könnyedén csatlakoztathatók az eredeti adatkészlethez az [Oszlopok hozzáadása](./add-columns.md) modullal.  

- A [Partition és a Sample](./partition-and-sample.md) használatával ossza fel az adatkészletet, végezzen mintavételezést, vagy töltse le az első n sort.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 