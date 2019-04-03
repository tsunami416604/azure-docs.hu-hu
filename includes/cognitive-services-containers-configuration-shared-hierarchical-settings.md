---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887221"
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
