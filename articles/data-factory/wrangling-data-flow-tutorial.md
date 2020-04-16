---
title: Az Azure Data Factory -ban az adatfolyam kibogozásának első lépései
description: Az azure Data Factory ban a huzavona adatfolyam használatával történő adatok előkészítésének oktatóanyaga
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409014"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Adatok előkészítése vonóadat-áramlással

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Vonagló adatfolyam létrehozása

Az Azure Data Factoryban kétféleképpen hozhat létre egy huzavonaadat-folyamatot. Ennek egyik módja, ha a plusz ikonra kattint, és a gyári erőforrások ablaktáblában az **Adatfolyam** lehetőséget választja.

![Vonagló](media/wrangling-data-flow/tutorial7.png)

A másik módszer a folyamatvászon tevékenységablakában található. Nyissa meg az **Áthelyezés és átalakítás** harmonikát, és húzza az **Adatfolyam-tevékenységet** a vászonra.

Mindkét módszerben a megnyíló oldalsó ablaktáblában jelölje be **az Új adatfolyam létrehozása lehetőséget,** és válassza **a Vonós adatfolyam**lehetőséget. Kattintson az OK gombra.

![Vonagló](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Csörlős adatfolyam készítése

**Forrás-adatkészlet** hozzáadása a vonós adatfolyamhoz. Választhat egy meglévő adatkészletet, vagy létrehozhat egy újat. Kijelölhet egy fogadó adatkészletet is. Egy vagy több forrásadatkészletet is kiválaszthat, de jelenleg csak egy fogadó engedélyezett. A fogadó adatkészlet kiválasztása nem kötelező, de legalább egy forrásadatkészlet szükséges.

> [!NOTE]
> Csak az ADLS Gen 2 tagolt szöveg támogatott a korlátozott előzetes verzióhoz. 

![Vonagló](media/wrangling-data-flow/tutorial4.png)

A Power Query Online mashup-szerkesztőjének megnyitásához kattintson a **Létrehozás** gombra.

![Vonagló](media/wrangling-data-flow/tutorial5.png)

A huzavona adatfolyama kódmentes adatelőkészítéssel készüljön el. Az elérhető függvények listáját az [átalakítási függvények című témakörben](wrangling-data-flow-functions.md)/

![Vonagló](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>A vonós adatfolyam futtatása és figyelése

A csörlős adatfolyam folyamathiba-keresési futtatásának végrehajtásához kattintson a **Hibakeresés gombra** a folyamatvásznon. Az adatfolyam közzététele után **az Eseményindító most** végrehajtja az utolsó közzétett folyamat igény szerinti futtatását. A wrangling adatfolyamok ütemezhetők az összes meglévő Azure Data Factory eseményindítóval.

![Vonagló](media/wrangling-data-flow/tutorial3.png)

A **Figyelő** lapon vizualizálhatja az aktivált vonódási adatfolyam-tevékenység futásának kimenetét.

![Vonagló](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>További lépések

További információ a [leképezési adatfolyam létrehozásáról.](tutorial-data-flow.md)