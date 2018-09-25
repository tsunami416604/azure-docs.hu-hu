---
title: Az Azure Data Source varázslót az Azure Machine Learning |} A Microsoft Docs
description: Ismerteti az adatforrás varázsló AML-munkaterület
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a3aeb021d5d3726a31b62d66efc44dc29f3dded0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959648"
---
# <a name="data-source-wizard"></a>Adatforrás varázsló #

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Az adatforrás varázsló egy adatkészlet állapotba Azure ML Workbench kód nélkül szeretne gyorsan és egyszerűen elvégezhető. Fontos, ahol is kiválaszthatja-e az egyes oszlopok az adatkészlet és adattípusok minta stratégiáját. 

## <a name="step-1-trigger-the-data-source-wizard"></a>1. lépés: Az adatforrás varázsló aktiválása ## 

Ahhoz, hogy az adatokat egy projektbe az adatforrás varázsló használatával. Válassza ki a **+** mellett a keresőmezőbe az adatnézetben gombra, és válassza ki az adatforrás hozzáadása. 

![Adatforrás hozzáadása](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>2. lépés: Válassza ki az adatok tárolására ##
Először adja meg, hogy az adatok jelenleg. Az egybesimított fájl vagy könyvtár, a parquet-fájlokba, egy Excel-fájl vagy egy adatbázis tárolhatók. További információkért lásd: [támogatott adatforrások](data-prep-appendix2-supported-data-sources.md).

![1. lépés](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>3. lépés: Válassza ki az adatfájlt ##
Fájl vagy könyvtár adja meg a fájl elérési útját. A legördülő listából válassza ki az adatok helye – Ez lehet egy helyi fájl elérési útja vagy az Azure Blob Storage. 

Adja meg az elérési út begépelve, vagy kattintson a **Tallózás...** gombra kattintva keresse meg azt. Egy könyvtár, vagy egy vagy több fájlt tallózhatja.

Kattintson a **Befejezés** elfogadhatja az alapértelmezett beállításokat, a hátralevő lépéseket, és ezután folytassa a következő lépéssel.


![4. lépés](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>4. lépés: Válassza ki a fájl paraméterei ##

Az adatforrás varázsló automatikusan felismeri a fájl típusa, elválasztók és kódolást. Emellett jelenik meg egy példát az adatok fog kinézni. Módosíthatja is ezeket a paramétereket bármelyikét manuálisan. 

![5. lépés](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>5. lépés: Állítsa be az oszlopok adattípusai ##

Az adatforrás varázsló automatikusan észleli az adatkészlet oszlopok adattípusát. Ha azt a gyorsítótárbeli sikertelen keresések egyet, vagy akinél szeretné kényszeríteni adattípust, manuálisan módosíthatja az adattípust. A **kimeneti MINTAADATOK** oszlop példákat mutat be, hogyan meg az adatokat.

Data Prep kikövetkezteti dátumokat tartalmazó oszlopok előfordulhat, hogy kéri, válassza ki a hónap és nap sorrendje a dátum formátuma. Például 1/2/2013. január 2. lehet helynév (Ehhez jelölje ki a *nap hónapos*) vagy február 1. (válassza ki *hónap és nap*).

![6. lépés](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>6. lépés: Válassza ki a mintavételezési stratégia az adatok ##

Adjon meg egy vagy több mintavételezési stratégia az adatkészlet, és válasszon egy, az aktív stratégia. Alapértelmezés szerint a rendszer az első 10 000 sorok betöltésére. Ez a minta kattintva szerkesztheti a **szerkesztése** gombra az eszköztáron, vagy adjon hozzá egy új stratégia szerint kattintson a + új. A stratégiák jelenleg támogató

-     Felső sorok száma
-     Véletlenszerű sorok száma
-     Sorok véletlenszerű százaléka
-     A teljes fájl

Megadhatja, de csak egy, az adatok előkészítésekor beállítható aktív annyi mintavételi stratégiák. Állítsa be az aktív stratégia kiválasztásával is kapcsolódó összes stratégiában, és kattintson a beállítás aktív, az eszköztáron.

Attól függően, ahol az adatok származnak néhány minta stratégiát nem támogatott. Tudnivalók a mintavételezésről további információkért tekintse meg a mintavétel szakaszában [Ez a dokumentum](data-prep-user-guide.md) 

![6. lépés](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>7. lépés: Elérési út oszlop kezelése ##

Ha a fájl elérési útját a fontos adatokat tartalmaz, válassza ki az első oszlop az adatkészletben, adja meg. Ez a beállítás hasznos, ha több fájlt a rendszer állapotba hozása lenne. Ellenkező esetben ha szeretné, nem vonja.

![7. lépés](media/data-source-wizard/step6.png)

A Befejezés gombra kattintva egy új adatforrást a hozzáadja a projekthez. Megtalálja a adatforrásokhoz tartozó az vagy egy dsource fájlban található, a **Fájlnézetben**.
