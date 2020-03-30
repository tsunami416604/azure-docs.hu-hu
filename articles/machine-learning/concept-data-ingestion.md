---
title: Adatbetöltési beállítások
titleSuffix: Azure Machine Learning
description: Ismerje meg a gépi tanulási modellek betanítására vonatkozó adatbetöltési lehetőségeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086879"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Adatok betöltése az Azure Machine Learningben

Ebben a cikkben az Azure Machine Learning ben elérhető alábbi adatbetöltési lehetőségek előnyeit és hátrányait ismerheti meg. 

1. [Azure Data Factory](#use-azure-data-factory) folyamatok
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Az adatok betöltése az a folyamat, amelyben strukturálatlan adatok at egy vagy több forrásból nyernek ki, majd előkészítik a gépi tanulási modellek betanításához. Ez is időigényes, különösen akkor, ha manuálisan történik, és ha nagy mennyiségű adat több forrásból származik. Ennek az erőfeszítésnek az automatizálása erőforrásokat szabadít fel, és biztosítja, hogy a modellek a legfrissebb és a megfelelő adatokat használják.

Az Azure Data Factory (ADF) kifejezetten az adatok kinyerésére, betöltésére és átalakítására készült, azonban a Python SDK segítségével egyéni kódmegoldást fejleszthet az alapvető adatbetöltési feladatokhoz. Ha egyik sem egészen mire van szüksége, az ADF és a Python SDK együttes használatával létrehozhat egy átfogó adatbetöltési munkafolyamatot, amely megfelel az igényeinek. 

## <a name="use-azure-data-factory"></a>Az Azure Data Factory használata

[Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) natív támogatást nyújt az adatforrás-figyeléshez és az adatbetöltési folyamatok eseményindítóihoz.  

Az alábbi táblázat összefoglalja az Azure Data Factory adatbetöltési munkafolyamatokhoz való használatának előnyeit és hátrányait.

|Előnyök|Hátrányok
---|---
Kifejezetten az adatok kinyerésére, betöltésére és átalakítására tervezték.|Jelenleg az Azure Data Factory folyamatkezelési feladatainak korlátozott készletét kínálja 
Lehetővé teszi, hogy adatközpontú munkafolyamatokat hozzon létre az adatmozgatás és az átalakítások nagy méretekben történő vezényléséhez.|Drága megépíteni és karbantartani. További információkért tekintse meg az Azure Data Factory [díjszabási oldalát.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
Integrálva a különböző Azure-eszközökkel, például [az Azure Databricks-szel](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) és az [Azure Functions-szel](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Nem natív módon futtat parancsfájlokat, hanem külön számítási parancsfájlfuttatásokra támaszkodik 
Natív módon támogatja az adatforrás által kiváltott adatbetöltést| 
Az adatok előkészítése és a modellbetanítási folyamatok különállóak.|
Beágyazott adatvonal-funkció az Azure Data Factory adatfolyamaihoz|
Alacsony kódélményt biztosít a [nem](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) parancsfájlalapú megközelítésekhez |

Ezek a lépések és az alábbi ábra az Azure Data Factory adatbetöltési munkafolyamatát szemlélteti.

1. Az adatok lekérése a forrásaiból
1. Az adatok átalakítása és mentése egy kimeneti blobtárolóba, amely az Azure Machine Learning adattárolójaként szolgál
1. Az előkészített adatok tárolásával az Azure Data Factory-folyamat meghívja a betanítási Machine Learning-folyamatot, amely megkapja az előkészített adatokat a modell betanításához


    ![ADF adatok betöltése](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Ismerje meg, hogyan hozhat létre adatbetöltési folyamatot a Machine Learning számára az [Azure Data Factory segítségével.](how-to-data-ingest-adf.md)

## <a name="use-the-python-sdk"></a>A Python SDK használata 

A [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)segítségével az adatok betöltési feladatok at egy [Azure Machine Learning-folyamat](how-to-create-your-first-pipeline.md) lépés.

Az alábbi táblázat összefoglalja az SDK és az adatbetöltési feladatok hibakezelési lépésének profiit és kontráit.

Előnyök| Hátrányok
---|---
Saját Python-parancsfájlok konfigurálása | Nem támogatja natív módon az adatforrások módosítását. Logikai alkalmazás vagy Azure-függvény implementációit igényli
Adatok előkészítése minden modell betanítási végrehajtásának részeként|Az adatbetöltési parancsfájl létrehozásához fejlesztési készségekre van szükség
Támogatja az adat-előkészítési parancsfájlokat a különböző számítási célokon, beleértve az [Azure Machine Learning számítási](concept-compute-target.md#azure-machine-learning-compute-managed) |Nem biztosít felhasználói felületet a betöltési mechanizmus létrehozásához

A következő ábrán az Azure Machine Learning-folyamat két lépésből áll: adatbetöltés és modell betanítása. Az adatbetöltési lépés olyan feladatokat foglal magában, amelyek python-kódtárak és a Python SDK használatával elvégezhetők, például adatok helyi/webes forrásokból történő kinyerését és alapvető adatátalakításokat, például a hiányzó értékimputálást. A betanítási lépés ezután az előkészített adatokat használja a betanítási parancsfájl bemeneteként a gépi tanulási modell betanításához. 

![Azure-folyamat + SDK-adatok betöltése](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre adatbetöltési folyamatot a Machine Learning hez az [Azure Data Factory segítségével](how-to-data-ingest-adf.md)
* Ismerje meg, hogyan automatizálhatja és kezelheti az azure-beli folyamatokkal az adatbetöltési folyamatok fejlesztési [életciklusát.](how-to-cicd-data-ingestion.md)
