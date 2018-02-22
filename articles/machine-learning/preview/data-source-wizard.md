---
title: "Az Azure Data Source varázsló az Azure Machine Learning |} Microsoft Docs"
description: "Ismerteti az adatforrás varázsló AML munkaterület"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dd603f07c20811543e07b21683b065bc873786a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="data-source-wizard"></a>Adatforrás varázsló #

Az adatforrás varázsló módja a gyorsan és könnyen elvégezhető dataset vonja Azure ML munkaterület kód nélkül. Fontos, ahol igény szerint kiválaszthatja a DataSet adatkészlet és adattípusok, az egyes oszlopok minta stratégiáját. 

## <a name="step-1-trigger-the-data-source-wizard"></a>1. lépés: Az adatforrás varázsló indítás ## 

Ahhoz, hogy az adatokat az adatforrás varázsló használatával projektbe. Válassza ki a  **+**  mellett a keresőmezőbe a adatnézetben gombra, majd válassza ki az adatforrás hozzáadása. 

![Adja hozzá az adatforrást](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>2. lépés: Válassza ki az adatokat tároló ##
Először adja meg, hogyan az adatokat jelenleg. Egybesimított fájl vagy könyvtár, egy parquet fájlt, egy Excel-fájl vagy egy adatbázis tárolható. További információkért lásd: [támogatott adatforrások](data-prep-appendix2-supported-data-sources.md).

![1. lépés](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>3. lépés: Válassza ki az adatfájlt ##
Fájl vagy könyvtár adja meg a fájl elérési útját. A legördülő listából válassza ki az adatok helye – lehet egy helyi fájl elérési útja vagy az Azure Blob Storage tárolóban. 

Adjon meg az elérési utat beírja azt, vagy kattintson a a **Tallózás...** gombra kattintva tallózással keresse meg azt. Egy könyvtár, vagy egy vagy több fájlt tallózhatja.

Kattintson a **Befejezés** fogadja el az alapértelmezett értéket a további lépéseket, vagy tovább a következő lépésben.


![4. lépés](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>4. lépés: Jelölje ki a fájl paraméterek ##

Az adatforrás varázsló automatikusan észleli a fájl típusa, elválasztók és kódolást. Azt is látható egy példa hogyan fog megjelenni az adatokat. Később is ezek a paraméterek egyikét manuálisan módosíthatja. 

![5. lépés](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>5. lépés: Állítsa be az oszlopok adattípusai ##

Az adatforrás varázsló automatikusan észleli a dataset oszlopok adattípusai. Ha azt a gyorsítótárbeli sikertelen keresések egyik, vagy szeretné kényszeríteni adattípust, manuálisan módosíthatja az adattípus. A **kimeneti MINTAADATOK** oszlop bemutatja, hogyan meg az adatok példát.

Adatok előkészítő kikövetkezteti dátumokat tartalmazó oszlopok kérheti a dátumformátum jelölje ki a hónap és nap sorrendjét. Például 1/2/2013 jelenthet. január 2. (Ehhez jelölje ki a *nap-hónap*) vagy február 1. (válasszon *hónap és nap*).

![6. lépés](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>6. lépés: Az adatok mintavételi stratégia kiválasztása ##

Adja meg az adatkészlet egy vagy több mintavételi stratégiák, és válassza ki, mint amit az aktív stratégia. Alapértelmezés szerint a rendszer a felső 10000 sorok betölteni. Ez a minta kattintva szerkesztheti a **szerkesztése** gomb az eszköztáron, vagy adjon hozzá egy új stratégia ehhez kattintson a + új. A jelenleg támogató stratégia a következő

-     Felső sorok száma
-     Véletlenszerű sorok száma
-     Sorok véletlenszerűen kiválasztott százaléka
-     Teljes fájl

Megadhatja, hogy annyi mintavételi stratégiák, de csak egy, az adatok előkészítésekor beállítható aktív. Bármely olyan stratégia, amelynek a stratégia kiválasztásával az aktív kell beállítani, és kattintson a beállítás aktív, az eszköztáron.

Attól függően, hogy az adatok eredetének néhány minta stratégiák nem támogatott. Mintavételi kapcsolatos további információkért tekintse meg a mintavételi részt [Ez a dokumentum](data-prep-user-guide.md) 

![6. lépés](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>7. lépés: Elérési oszlop kezelése ##

Ha a fájl elérési útját a fontos adatokat tartalmaz, ha szeretné, adja hozzá az első adatkészlet oszlopként. Ez a beállítás hasznos, ha több fájlban vannak állapotba hozása lenne. Ellenkező esetben dönthet úgy, hogy nem tartalmazza.

![7. lépés](media/data-source-wizard/step6.png)

Miután a Befejezés gombra kattint, új adatforrás bekerül a projekthez. Megtalálhatja az adatforrások csoportba tartozó az vagy egy dsource fájlban található, a **fájlnézetéhez**.
