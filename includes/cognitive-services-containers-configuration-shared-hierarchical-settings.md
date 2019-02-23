---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741506"
---
A tároló beállításait hierarchikus, és a gazdagépen az összes tárolót a megosztott hierarchiába.

Adja meg a beállítások a következők egyikét használhatja:

* [Környezeti változók](#environment-variable-settings)
* [Parancssori argumentumok](#command-line-argument-settings)

A környezeti változó értékeit felülbírálhatja parancssori argumentumok értékeit, amelyek viszont felülírják az alapértelmezett értékeit a tároló rendszerképét. Ha eltérő értékeket egy környezeti változó és a egy parancssori argumentum ugyanazon konfigurációs beállítás megadása esetén a környezeti változó értékét a példányosított tároló használják.

|Sorrend|Helyének beállítása|
|--|--|
|1|Környezeti változó| 
|2|Parancssor|
|3|Tároló-lemezképet alapértelmezett érték|

### <a name="environment-variable-settings"></a>Környezeti változók beállításai

A környezeti változók használatának előnyei a következők:

* Több beállítások konfigurálhatók.
* Több tároló használhatja ugyanazokat a beállításokat.

### <a name="command-line-argument-settings"></a>Parancssori argumentumok beállításai

Parancssori argumentumok használatával előnye, hogy a tárolók használható különböző beállításokat.
