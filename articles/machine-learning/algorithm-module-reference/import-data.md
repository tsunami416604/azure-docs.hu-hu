---
title: 'Adatok importálása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használja az adatok importálása modullal az Azure Machine Learning szolgáltatásban egy machine learning-kísérletbe meglévő felhőalapú adatszolgáltatásokból származó adatok betöltéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028305"
---
# <a name="import-data-module"></a>Adatok modul importálása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával tölthet be egy machine learning-kísérlet meglévő felhőalapú adatszolgáltatásokból származó adatokat.  
A modul most a varázsló segít egy tárolási megoldás, és válasszon a meglévő előfizetések és fiókok közül minden beállításokat gyorsan funkciókat. Az adatok meglévő kapcsolatok szerkesztésére van szüksége? Nincs a problémát. a varázsló betölti az összes korábbi konfigurációs adatait, így nem kell újra kezdenie a folyamatot. 
  
Az adatok kívánt, és csatlakozzon a forrás-, meghatározása után [adatok importálása](./import-data.md) kikövetkezteti tartalmaz, és betölti az adatokat az Azure Machine Learning-munkaterület értékei alapján minden oszlop adattípusát. A kimenet a [adatok importálása](./import-data.md) van egy adatkészletet, amely használható bármilyen kísérletet.

  
Ha megváltozik a forrásadatokat, frissítse az adatkészletet, és adja hozzá az új adatok újrafuttatásával [adatok importálása](./import-data.md). Azonban ha nem szeretné újra olvassa el a forráskiszolgálóról minden alkalommal, amikor Ön futtathatja a kísérletet, válassza ki a **használja a gyorsítótárazott eredményeket** igaz értékre a beállítást. Ha ezt a lehetőséget választja, a modul ellenőrzi, e kísérlet futott korábban a azonos forrásból és ugyanazt a bemeneti beállítások használatával. Ha egy korábbi futtatás található, a gyorsítótárban lévő adatok használata esetén a forrás az adatok újbóli betöltéséből helyett.
 

## <a name="data-sources"></a>Adatforrások

Az adatok importálása modullal a következő adatforrásokat támogatja. Részletes útmutatásért és példákért minden adatforráshoz a hivatkozásokra kattintva. 
 
Ha nem biztos abban, hogy hogyan és hol tárolja az adatokat, lásd a jelen útmutató az adatelemzési folyamat a common data jellegű alkalmazási:  [Az Azure Machine Learning speciális elemzési forgatókönyvek](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Adatforrás| Használata|
|-----------|-----------|  
|[A HTTP Protokollon keresztül, a webes URL-címe](./import-from-web-url-via-http.md)|A webes és URL-, amely a HTTP Protokollt használ, amely lett megadva. a CSV, TSV, ARFF vagy SvmLight formátumokban tárolt adatok lekérése|  
|[Importálás az Azure Blob Storage-ból](./import-from-azure-blob-storage.md) |Az Azure blob service-ben tárolt adatok lekérése|  

## <a name="how-to-use-import-data"></a>Adatok importálása használata
 
1. Adja hozzá a **adatok importálása** modult a kísérletvászonra. Ez a modul a annak a **adatok bemeneti és kimeneti** kategória a felületen.

2. Kattintson a **adatok importálása varázsló elindításához** az adatforrás varázsló segítségével konfigurálhatja.

    A varázsló lekéri a fiók nevét és hitelesítő adatokat és egyéb beállítások konfigurálása súgó. Ha egy meglévő konfigurációnak szerkeszti, először betölti az aktuális értékeit.

3. Ha nem szeretné használni a varázslót, kattintson a **adatforrás**, és válassza ki a felhőalapú tárolási, olvasásakor. 

    További beállítások attól függenek, a választja, a tárolás típusát és a storage védett e vagy sem. Előfordulhat, hogy meg kell adnia, a fiók neve, a fájl típusa vagy a hitelesítő adatokat. Egyes források nem igényelnek hitelesítés; egyéb rendszerek esetén szükség lehet tudni, hogy a fiók nevét, a kulcs vagy a tároló neve.

4. Válassza ki a **használja a gyorsítótárazott eredményeket** beállítást, ha az adatkészlet több egymást követő újrahasznosításának gyorsítótárazni szeretné.

    Ha nem történt más modul paraméterek módosítása nélkül, a kísérlet betölti az adatok csak az első idő a modul fut, és ezt követően használja a az adatkészlet egy gyorsítótárazott verziója.

    Törölje ezt a beállítást, ha szeretné újra betölti az adatokat minden alkalommal, amikor a kísérlet futtatásához.

5. Futtassa a kísérletet.

    Adatok importálása a felületen betölti az adatokat, amikor azt kikövetkezteti tartalmaz, értékei alapján minden oszlop adattípusát vagy numerikus vagy kategorikus.

    - Ha egy fejléc található, a fejléc az oszlopokat a kimeneti adatkészlet neve szolgál.

    - Ha az adatok nincsenek meglévő oszlopfejléceket, új oszlopnevek jönnek létre a formátum col1, col2, használatával... , coln *.

## <a name="results"></a>Results (Eredmények)

Ha az importálás befejeződött, kattintson a kimeneti adatkészletet, és válassza ki **Visualize** megtekintheti, ha az adatok importálása sikerült.

Ha ismét az adatokat menteni szeretné, ahelyett, hogy importál egy új adatkészletet a kísérlet futtatásakor, kattintson a jobb gombbal a kimenetet, és válassza ki **mentés adatkészletként**. Válassza ki az adatkészlet nevét. A mentett adatkészlet megőrzi az adatokat a mentés időpontjában, és adatokat nem frissül a kísérlet akkor fut újra, még akkor is, ha módosítja az adatkészletet a kísérletben. Ez akkor lehet hasznos adatok pillanatképek készítése.

Az adatok importálása után, néhány további előkészített modellezési és elemzési lehet szükség:


- Használat [metaadatainak szerkesztése](./edit-metadata.md) oszlopnevek, kezelni a más oszlopot vagy annak jelzése céljából, hogy egyes oszlopok címkéket és a funkciók módosításához.

- Használjon [Select Columns in Dataset](./select-columns-in-dataset.md) átalakításához, és használja a modellezési oszlopok kiválasztásához. Az átalakított vagy eltávolított oszlopokat is könnyen kell csatlakoznia az eredeti adathalmazból használatával a [oszlopok hozzáadása](./add-columns.md) modul.  

- Használat [partíciót és minta](./partition-and-sample.md) az adatkészlet felosztása, hajtsa végre a mintavételi vagy a felső n sorok beolvasása.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 