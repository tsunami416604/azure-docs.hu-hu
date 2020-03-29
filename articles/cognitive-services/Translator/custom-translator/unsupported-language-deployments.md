---
title: Nem támogatott nyelvi telepítések – Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan telepítheti a nem támogatott nyelvi párokat az Azure Cognitive Services egyéni fordítójában.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837449"
---
# <a name="unsupported-language-deployments"></a>Nem támogatott nyelvű környezetek

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

A Microsoft Translator Hub közelgő nyugdíjba vonulásával a Microsoft a Hubon keresztül jelenleg telepített összes modell tanusítsa a központi telepítést. Sokan vannak olyan modellek üzembe helyezése a hubon, amelyek nyelvi párnem támogatja az egyéni fordító.  Nem akarjuk, hogy az ilyen helyzetben lévő felhasználók ne folyamodjanak a tartalmuk fordításához.

Most már van egy folyamat, amely lehetővé teszi, hogy a nem támogatott modellek az egyéni fordítón keresztül.  Ez a folyamat lehetővé teszi a tartalom lefordítását a legújabb V3 API használatával.  Ezek a modellek lesznek üzemeltetve, amíg úgy dönt, hogy undeploy őket, vagy a nyelvi pár elérhetővé válik az egyéni fordító.  Ez a cikk a nem támogatott nyelvi párokkal rendelkező modellek üzembe helyezésének folyamatát ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy modelljei pályázzanak a telepítésre, a következő feltételeknek kell megfelelniük:
* A modellt tartalmazó projektet át kell telepíteni a központból az egyéni fordítóba az áttelepítési eszközzel.  A projektek és munkaterületek áttelepítésének folyamata [itt](how-to-migrate.md)található.
* A modellnek üzembe helyezett állapotban kell lennie az áttelepítés során.  
* A modell nyelvi párjának nem támogatott nyelvpárnak kell lennie az egyéni fordítóban.  Azok a nyelvpárok, amelyekben egy nyelvet angolvagy angol nyelv támogat, de maga a pár nem tartalmazza az angolnyelvet, nem támogatott nyelvi telepítésekre jelöltek.  Például egy francia-német nyelvpár Hub modellje nem támogatott nyelvpárnak minősül, még akkor is, ha a francia-angol és az angol-német nyelv támogatott nyelvpár.

## <a name="process"></a>Folyamat
Miután áttelepítette a központi modelleket, amelyek a telepítésre jelöltek, megtalálhatja őket a munkaterület **Beállítások** lapján, és görgetve a lap végéig, ahol megjelenik egy **nem támogatott Fordítóközpont-tanfolyamok** szakasz.  Ez a szakasz csak akkor jelenik meg, ha olyan projektjei vannak, amelyek megfelelnek a fent említett előfeltételeknek.

![Az áttelepítés a Hubról](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

A **nem támogatott Fordítóközpont-betanítások** választólapon a **nem kért edzések** lap olyan modelleket tartalmaz, amelyek alkalmasak a telepítésre.  Válassza ki a telepíteni kívánt modelleket, és küldje el a kérelmet.   Az április 30-i üzembe helyezési határidő előtt annyi modellt választhat ki, amennyit csak szeretne az üzembe helyezéshez.
 
![Az áttelepítés a Hubról](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

A beküldést követően a modell a továbbiakban nem lesz elérhető a **Nem kért betanítások** lapon, és ehelyett megjelenik a **Kért betanítások** lapon.  A kért edzéseket bármikor megtekintheti.

![Az áttelepítés a Hubról](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>A következő lépések

A központi telepítéshez kiválasztott modellek mentésre kerülnek, miután a Hub levan szerelve, és az összes modell telepítése nem történik meg.  Május 24-ig van ideje a nem támogatott modellek telepítésére vonatkozó kérelmek benyújtására.  Ezeket a modelleket június 15-én telepítjük, ekkor lesznek elérhetők a Translator V3 API-n keresztül.  Ezenkívül július 1-ig elérhetők lesznek a V2 API-n keresztül.  

A Hub-ellenőrzés egesztésének fontos időpontjaival kapcsolatos további információkért [itt olvashat](https://www.microsoft.com/translator/business/hub/).
A telepítés után normál üzemeltetési díjak at kell alkalmazni.  A [részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) lásd az árakban.  

A szabványos egyéni fordítómodellekkel ellentétben a Hub-modellek csak egy régióban lesznek elérhetők, így a több régióra kiterjedő üzemeltetési díjak nem érvényesek.  Üzembe helyezés után bármikor lehetővé teszi a Hub-modell központi telepítésének lefektusát az áttelepített egyéni fordító projekten keresztül.

## <a name="next-steps"></a>További lépések

- [Modell betanítása](how-to-train-model.md).
- Kezdje el használni a telepített egyéni fordítási modellt a [Microsoft Translator Text API V3-on](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)keresztül.
