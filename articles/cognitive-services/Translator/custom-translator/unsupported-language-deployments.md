---
title: Nem támogatott nyelvet központi telepítések – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Nem támogatott nyelvet párok egyéni a fordítót a telepítésének módjáról.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476506"
---
# <a name="unsupported-language-deployments"></a>Nem támogatott nyelvet központi telepítések

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

A Microsoft Translator Hub kivezetésre, a Microsoft undeploying a hubon keresztül a jelenleg telepített összes modellt fog lehet. A legtöbb felhasználó rendelkezik a modellek üzembe helyezve az agyban, amelynek nyelvi párokat az egyéni a fordítót nem támogatottak.  Szükségünk ebben a helyzetben felhasználók számára, hogy nem fordíthatók le mindkét irányba tartalmaihoz.

Most már van egy folyamat, amely lehetővé teszi, hogy a nem támogatott az egyéni a fordítót a modellek üzembe helyezése.  Ez a folyamat lehetővé teszi, hogy a legújabb V3 API-val tartalmak lefordítása.  Ezek a modellek üzemeltetett úgy dönt, hogy megszüntetheti őket, illetve a nyelvi pár egyéni a fordítót az elérhetővé válik.  Ez a cikk ismerteti a folyamatot nem támogatott nyelvet párokat a modellek üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a modellek jelöltek a központi telepítés rendszer a következő feltételeknek kell megfelelniük:
* A projektet, amely tartalmazza a modellt kell át lettek telepítve a központi menü az egyéni Translator az áttelepítési eszköz használata.  A folyamat a áttelepítése projektek és munkaterületek található [Itt](how-to-migrate.md).
* A modell az üzembe helyezett állapotban kell lennie, ha az áttelepítés történik.  
* A modell nyelvi a két egyéni a fordítót a nem támogatott nyelvet párjának kell lennie.  Nyelvi párok, amelyben, vagy az angol nyelv támogatott, de maga a pár nem tartalmazza a angol nyelven, nem támogatott nyelvet központi telepítések a deduplikációra.  Például egy Eseményközpont modell egy francia, német nyelvű pár nem támogatott nyelvet pár még akkor is, azonban francia angol és német vannak az angol nyelvű támogatott nyelvi párjának tekinthető.

## <a name="process"></a>Feldolgozás
A központi menü, amely alkalmas jelöltek az üzembe helyezési modellek áttelepítette, ha megtalálja a a **beállítások** a munkaterülethez, majd legörget az oldal, ahol láthatja az utolsó oldalán egy **nem támogatott Translator Hub Betanítások** szakaszban.  Ez a szakasz csak akkor jelenik meg, ha rendelkezik, amely megfelel a fenti előfeltételeknek projektek.

![A Hub áttelepítése](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Belül a **nem támogatott a fordító Hub Betanítások** kiválasztása lapon a **nem kért betanítások** lap tartalmaz, amelyek jogosultak az üzembe helyezési modellek.  Válassza ki a modellek üzembe helyezése és a egy kérelmet küldeni szeretné.   A április 30 telepítési határidő előtt annyi modellek üzembe helyezéshez igény szerint is választhat.
 
![A Hub áttelepítése](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Elküldve, ha a modell már nem elérhető a **nem kért betanítások** lapra, és ehelyett jelennek meg a **betanítások kért** fülre.  A kért betanítások bármikor tekintheti meg.

![A Hub áttelepítése](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>A következő lépések

A modellek üzembe helyezéshez kiválasztott lesznek mentve, miután a Hub le van szerelve, és minden modell nem telepített.  Nem támogatott modellek üzembe helyezéséhez kéréseket küldhetnek a május 24-ig rendelkezik.  Ezen modellek június 15 ekkor azok lesz elérhető a Translator V3 API-n keresztül helyezzük üzembe.  Emellett akkor lesz elérhető a V2 API-n keresztül. július 1-ig.  

Fontos dátumokat a Hub ellenőrzés kivonásával kapcsolatban további információért [Itt](https://www.microsoft.com/translator/business/hub/).
Miután az üzembe helyezett, normál szolgáltatási díjakat számolunk fel.  Lásd: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) részleteiről.  

Szabványos, egyéni a fordítót modelleket eltérően Hub modellek csak lesz elérhető régióban, így többrégiós szolgáltatási díjakat számolunk fel nem.  Miután üzembe helyezte lesz a Hub modell az áttelepített egyéni a fordítót projekten keresztül bármikor visszavonhatja annak üzembe helyezését.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
