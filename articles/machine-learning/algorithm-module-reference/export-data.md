---
title: 'Adatexportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja az adatok exportálása modult a Azure Machine Learning szolgáltatásban az eredmények, a köztes adatok és a munkafolyamatok munkafolyamatainak a Azure Machine Learningn kívüli Felhőbeli tárolási célhelyekre való mentéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693718"
---
# <a name="export-data-module"></a>Adatmodul exportálása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal mentheti az eredményeket, a köztes adatok és a munkafolyamatok adatait a Azure Machine Learningon kívüli Felhőbeli tárolási célhelyekre.

Ez a modul támogatja a következő felhőalapú adatszolgáltatások exportálását vagy mentését:


- **Exportálás az azure blob Storageba**: az Azure-ban tárolt blob serviceba menti az adatvesztést. A Blob serviceban tárolt adatmennyiség megoszthatók nyilvánosan, vagy menthetők biztonságos alkalmazás-adattárakban.

  
## <a name="how-to-configure-export-data"></a>Az exportálási beállítások konfigurálása

1. Adja hozzá az **adatexportálási** modult a folyamathoz az illesztőfelületen. Ez a modul a **bemeneti és a kimeneti** kategóriában található.

2. Az exportálni kívánt adategységeket tartalmazó modulba csatlakoztasson **adatexportálást** .

3. Kattintson duplán az **adatexportálás** elemre a **Tulajdonságok** ablaktábla megnyitásához.

4. Az **adatcél**mezőben válassza ki a felhőalapú tárolás típusát, ahová menteni szeretné az adatait. Ha módosítja ezt a beállítást, az összes többi tulajdonság alaphelyzetbe áll. Ügyeljen arra, hogy először ezt a lehetőséget válassza.

5. Adja meg a megadott Storage-fiók eléréséhez szükséges fióknevet és hitelesítési módszert.

    A privát előzetes verzióban csak az **Azure Blob Storageba való exportálás** lehetséges. Az alábbiakban bemutatjuk, hogyan állíthatja be a modult.
    1. Az Azure Blob Service nagy mennyiségű, például bináris adatmennyiségű adattárolásra szolgál. A blob Storage két típusa létezik: nyilvános blobok és Blobok, amelyek bejelentkezési hitelesítő adatokat igényelnek.

    2. A **Hitelesítés típusa**beállításnál válassza a **nyilvános (SAS)** lehetőséget, ha tudja, hogy a tároló támogatja az SAS URL-címen keresztüli hozzáférést.

          Az SAS URL-cím olyan speciális URL-cím, amely egy Azure Storage-segédprogrammal hozható létre, és csak korlátozott ideig érhető el.  A hitelesítéshez és a letöltéshez szükséges összes információt tartalmazza.

        **URI**esetén írja be vagy illessze be a fiókot és a nyilvános blobot meghatározó teljes URI-t.

        Fájlformátum esetén a CSV és a TSV formátum támogatott.

    3. Privát fiókok esetében válassza a **fiók**lehetőséget, és adja meg a fiók nevét és a fiók kulcsát, hogy a folyamat a Storage-fiókba írjon.

         - **Fióknév**: írja be vagy illessze be annak a fióknak a nevét, ahová menteni kívánja az adatgyűjtést. Ha például a Storage-fiók teljes URL-címe `http://myshared.blob.core.windows.net`, akkor `myshared` kell beírnia.

        - **Fiók kulcsa**: illessze be a fiókhoz társított Storage-elérési kulcsot.

        -  A **tároló, a könyvtár vagy a blob elérési útja**: írja be annak a blobnak a nevét, ahová az exportált adatmennyiséget tárolni kívánja. Ha például egy **results01. csv** nevű új blobba szeretné menteni a folyamat eredményeit egy **mymldata**nevű fiókban, akkor a blob teljes **URL-címe** `http://mymldata.blob.core.windows.net/predictions/results01.csv` lesz.

            Ezért a **tároló, könyvtár vagy blob mező elérési útján**adja meg a tárolót és a blob nevét a következő módon: `predictions/results01.csv`

        - Ha olyan blob nevét adja meg, amely még nem létezik, az Azure létrehozza a blobot.

       -  Egy meglévő blobba való íráskor megadhatja, hogy felülírja-e a blob aktuális tartalmát a tulajdonság, az **Azure Blob Storage írási mód**beállításával. Alapértelmezés szerint ez a tulajdonság a **hiba**értékre van állítva, ami azt jelenti, hogy hiba jön létre, amikor egy azonos nevű meglévő blob-fájl található.


    4. A **blob fájl formátuma**beállításnál válassza ki azt a formátumot, amelyben az adattárolást tárolni szeretné.

        - **CSV**: az alapértelmezett tárolási formátum a vesszővel tagolt értékek (CSV). Ha az oszlop fejléceit az adatsorokkal együtt szeretné exportálni, válassza ki a beállítást, majd írja be a **blob fejlécét**.  A Azure Machine Learningben használt vesszővel tagolt formátummal kapcsolatos további információkért lásd: [Konvertálás CSV](./convert-to-csv.md)-re.

        - **TSV**: a tabulátorral tagolt értékek (TSV) formátuma számos gépi tanulási eszközzel kompatibilis. Ha az oszlop fejléceit az adatsorokkal együtt szeretné exportálni, válassza ki a beállítást, majd írja be a **blob fejlécét**.  

 
    5. **Gyorsítótárazott eredmények használata**: akkor válassza ezt a lehetőséget, ha el szeretné kerülni, hogy a folyamat minden egyes futtatásakor ne írja át újra az eredményeket a blob-fájlba. Ha a modul paramétereinek nincsenek más módosításai, a folyamat csak a modul első futtatásakor, vagy az adatok módosításakor írja az eredményeket.

    6. A folyamat futtatása.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 