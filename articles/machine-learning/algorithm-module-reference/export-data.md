---
title: 'Adatexportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adatok exportálása modult a Azure Machine Learning az eredmények, a köztes adatok és a munkafolyamatok munkafolyamatainak a Azure Machine Learningokon kívüli Felhőbeli tárolási célhelyekre való mentéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5badcbe2a76c41bdc1431abae617e35b7555eb88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497905"
---
# <a name="export-data-module"></a>Adatmodul exportálása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal mentheti az eredményeket, a köztes adatok és a munkafolyamatok adatait a Azure Machine Learningon kívüli Felhőbeli tárolási célhelyekre. 

Ez a modul támogatja az adatexportálást a következő felhőalapú adatszolgáltatásokban:

- Azure Blob-tároló
- Azure-fájlmegosztás
- Azure Data Lake
- Azure Data Lake Gen2

Az adatexportálás előtt először regisztrálnia kell egy adattárt a Azure Machine Learning-munkaterületen. További információ: [az adatok elérése](../service/how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Az exportálási beállítások konfigurálása

1. Adja hozzá az **adatexportálási** modult a folyamathoz a tervezőben. Ez a modul a **bemeneti és a kimeneti** kategóriában található.

1. Az exportálni kívánt adategységeket tartalmazó modulba csatlakoztasson **adatexportálást** .

1. Válassza az **adatexportálás** lehetőséget a **Tulajdonságok** ablaktábla megnyitásához.

1. Az **adattár**mezőben válasszon ki egy meglévő adattárt a legördülő listából. Létrehozhat egy új adattárt is. Tekintse meg, hogyan érheti el a- [Access-az adathozzáférést](../service/how-to-access-data.md)

1. Adja meg az adattároló elérési útját az adatbevitelhez. 


1. **Fájlformátum**esetén válassza ki azt a formátumot, amelyben tárolni szeretné az adattárolást.
 
1. A folyamat futtatása.

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 