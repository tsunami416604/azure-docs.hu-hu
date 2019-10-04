---
title: 'Az adatexportálás: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja az adatok exportálása modult a Azure Machine Learning szolgáltatásban az eredmények, a köztes adatok és a munkaadatoknak a kísérletekből a Azure Machine Learningn kívüli Felhőbeli tárolási célhelyekre való mentéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128761"
---
# <a name="export-data-module"></a>Adatmodul exportálása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal mentheti az eredményeket, a köztes és a munkaadatokat a kísérletekből a Azure Machine Learningon kívüli felhőalapú tárolási célhelyekre.

Ez a modul támogatja a következő felhőalapú adatszolgáltatások exportálását vagy mentését:


- **Exportálás az Azure Blob Storageba**: Az Azure-beli Blob serviceba menti az adatvesztést. A Blob serviceban tárolt adatmennyiség megoszthatók nyilvánosan, vagy menthetők biztonságos alkalmazás-adattárakban.

  
## <a name="how-to-configure-export-data"></a>Az exportálási beállítások konfigurálása

1. Adja hozzá az adatexportálási modult a kísérlethez az illesztőfelületen. Ez a modul a **bemeneti és a kimeneti** kategóriában található.

2. Az exportálni kívánt adategységeket tartalmazó modulba csatlakoztasson adatexportálást.

3. Kattintson duplán az adatexportálás elemre a **Tulajdonságok** ablaktábla megnyitásához.

4. Az **adatcél**mezőben válassza ki a felhőalapú tárolás típusát, ahová menteni szeretné az adatait. Ha módosítja ezt a beállítást, az összes többi tulajdonság alaphelyzetbe áll. Ügyeljen arra, hogy először ezt a lehetőséget válassza.

5. Adja meg a megadott Storage-fiók eléréséhez szükséges fióknevet és hitelesítési módszert.

    A privát előzetes verzióban csak az **Azure Blob Storageba való exportálás** lehetséges. Az alábbiakban bemutatjuk, hogyan állíthatja be a modult.
    1. Az Azure Blob Service nagy mennyiségű, például bináris adatmennyiségű adattárolásra szolgál. A blob Storage két típusa létezik: nyilvános blobok és Blobok, amelyek bejelentkezési hitelesítő adatokat igényelnek.

    2. A **Hitelesítés típusa**beállításnál válassza a **nyilvános (SAS)** lehetőséget, ha tudja, hogy a tároló támogatja az SAS URL-címen keresztüli hozzáférést.

          Az SAS URL-cím olyan speciális URL-cím, amely egy Azure Storage-segédprogrammal hozható létre, és csak korlátozott ideig érhető el.  A hitelesítéshez és a letöltéshez szükséges összes információt tartalmazza.

        **URI**esetén írja be vagy illessze be a fiókot és a nyilvános blobot meghatározó teljes URI-t.

        Fájlformátum esetén a CSV és a TSV formátum támogatott.

    3. Privát fiókok esetében válassza a **fiók**lehetőséget, és adja meg a fiók nevét és a fiók kulcsát, hogy a kísérlet a Storage-fiókba írjon.

         - **Fiók neve**: Írja be vagy illessze be annak a fióknak a nevét, ahová menteni kívánja az adatgyűjtést. Ha például a Storage-fiók `http://myshared.blob.core.windows.net`teljes URL-címe, akkor gépelje be `myshared`a következőt:.

        - **Fiók kulcsa**: Illessze be a fiókhoz társított Storage-elérési kulcsot.

        -  **Tároló, könyvtár vagy blob elérési útja**: Írja be annak a blobnak a nevét, ahová az exportált adatmennyiséget tárolni kívánja. Ha például egy **results01. csv** nevű új blobba szeretné menteni a kísérlet eredményét egy **mymldata**nevű fiókban , a blob teljes URL-címe a következő lesz `http://mymldata.blob.core.windows.net/predictions/results01.csv`:.

            Ezért a **tároló, könyvtár vagy blob mező elérési útján**a következő módon kell megadnia a tárolót és a blob nevét:`predictions/results01.csv`

        - Ha olyan blob nevét adja meg, amely még nem létezik, az Azure létrehozza a blobot.

       -  Egy meglévő blobba való íráskor megadhatja, hogy felülírja-e a blob aktuális tartalmát a tulajdonság, az **Azure Blob Storage írási mód**beállításával. Alapértelmezés szerint ez a tulajdonság a **hiba**értékre van állítva, ami azt jelenti, hogy hiba jön létre, amikor egy azonos nevű meglévő blob-fájl található.


    4. A **blob**fájl formátuma beállításnál válassza ki azt a formátumot, amelyben az adattárolást tárolni szeretné.

        - **CSV**: A vesszővel tagolt értékek (CSV) az alapértelmezett tárolási formátum. Ha az oszlop fejléceit az adatsorokkal együtt szeretné exportálni, válassza ki a beállítást, majd írja be a **blob fejlécét**.  A Azure Machine Learningben használt vesszővel tagolt formátummal kapcsolatos további információkért lásd: [Konvertálás CSV](./convert-to-csv.md)-re.

        - **TSV**: A tabulátorral tagolt értékek (TSV) formátuma számos gépi tanulási eszközzel kompatibilis. Ha az oszlop fejléceit az adatsorokkal együtt szeretné exportálni, válassza ki a beállítást, majd írja be a **blob fejlécét**.  

 
    5. **Gyorsítótárazott eredmények használata**: Akkor válassza ezt a lehetőséget, ha el szeretné kerülni az eredmények a blob-fájlba való újraírását minden alkalommal, amikor futtatja a kísérletet. Ha a modul paramétereinek nincsenek más módosításai, a kísérlet csak a modul első futtatásakor, vagy az adatok módosításakor írja az eredményeket.

    6. Futtassa a kísérletet.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 