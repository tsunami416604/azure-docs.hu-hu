---
title: Előre összeállított modellek
titleSuffix: Language Understanding - Azure Cognitive Services
description: Előre összeállított modellek adja meg a tartományok, szándék fog vonatkozni, utterances és entitásokat. Indítsa el az alkalmazást egy előre elkészített tartomány vagy a megfelelő tartomány hozzáadása az alkalmazáshoz később.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 75644fd94e6e00e92547cd9e1d42b26c6a55807f
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910628"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Előre összeállított tartomány, szándék és entitás modellek

Előre összeállított modellek adja meg a tartományok, szándék fog vonatkozni, utterances és entitásokat. Indítsa el az alkalmazást egy előre elkészített tartomány vagy a megfelelő tartomány hozzáadása az alkalmazáshoz később. 

## <a name="types-of-prebuilt-models"></a>Előre összeállított modellek típusa

Nincsenek 3 típusú előre összeállított modellek LUIS biztosít. Minden modell az alkalmazás bármikor lehet hozzáadni. 

|Modell típusa|Tartalmazza|
|--|--|
|Domain|Leképezések, utterances, entitások|
|Leképezések|Leképezések, kimondott szöveg|
|Entitások|Entitások csak| 

## <a name="prebuilt-domains"></a>Előre összeállított tartományok

Language Understanding (LUIS) biztosít *előre összeállított tartományok*, olyan előre összeállított készletei, amelyek [leképezések](luis-how-to-add-intents.md) és [entitások](luis-concept-entity-types.md) , hogy működnek együtt, a tartományok vagy közös kategóriái ügyfélalkalmazások számára. 

Az előre összeállított tartományok a következők: betanított és készen áll a LUIS-alkalmazás hozzáadása. A leképezések és a egy előre elkészített tartományban entitások olyan teljes mértékben testre szabható, miután hozzáadta azokat az alkalmazásba. 

Ha a testreszabás, előre összeállított teljes tartományban indul el, törölje a szándékok és entitások, amelyhez az alkalmazás használatához nincs szükség. A készlethez, amely az előre összeállított tartomány már biztosít néhány leképezések vagy entitások is hozzáadhat. Például, ha használja a **események** sport esemény alkalmazások előre összeállított tartományban is sport csapatok entitások hozzáadása. Amikor elkezd [beszédmódok nyújtó](luis-how-to-add-example-utterances.md) , LUIS, olyan feltételek, amelyek az alkalmazás adott. A LUIS megtanulja felismerni őket, és az előre összeállított tartományban szándékok és entitások az alkalmazás igényeinek megfelelően szokásait. 

> [!TIP]
> A leképezések és a egy előre elkészített tartományban entitások működnek együtt a leghatékonyabban. Érdemes úgy, hogy szándékokat és entitásokat, amikor csak lehetséges ugyanahhoz a tartományhoz.
> A segédprogramok előre összeállított tartományban van, amely testre szabható használható minden olyan tartományban szándékok. Például hozzáadhat `Utilities.Repeat` az alkalmazás és a vonat, felismerni bármilyen műveletek felhasználó szeretné ismételje meg az alkalmazásban. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Egy előre elkészített tartomány szándékot viselkedésének módosítása

Előfordulhat, hogy egy előre elkészített tartományt tartalmaz megjelölésű megjelölésű szeretné engedélyezni a LUIS-alkalmazás a hasonló, de azt szeretné, hogy eltérően viselkednek. Például a **helyek** előre összeállított tartományt biztosít egy `MakeReservation` célja, hogy egy étterem foglalást, de szeretné az alkalmazást, hogy a szállodai foglalások adott szándékot használatához. Ebben az esetben módosíthatja, hogy a leképezés viselkedését utterances azáltal, hogy a LUIS szállodai foglalások tétele és a címkézés őket használatával a `MakeReservation` szándék, így a LUIS újrataníthatók felismerni a `MakeReservation` küldött kérelemben egy szállodai Vendég repülőjáratra szándék .

Az előre összeállított tartományok teljes listáját megtalálhatja az [előre összeállított tartományok referencia](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Előre összeállított leképezések

A LUIS biztosít az előre összeállított leképezések és a kimondott szöveg. A teljes tartomány hozzáadása nélkül leképezéseket adhat hozzá. Megjelölésű hozzáadása nem ad megjelölésű és a kimondott szöveg. A leképezés neve és az utterance (kifejezés) listát is módosíthatók.  

## <a name="prebuilt-entities"></a>Előre összeállított entitások

LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. Előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezete eltérő. A LUIS támogatja, beleértve támogatási kulturális környezet, előre összeállított entitások teljes listáját lásd: a [előre összeállított entitások bemutatása](./luis-reference-prebuilt-entities.md).

Ha az alkalmazás tartalmaz egy előre összeállított entitások, az előrejelzéseket a közzétett alkalmazás szerepelnek. Előre összeállított entitások viselkedését előre betanított és **nem** módosítható. Kövesse az alábbi lépéseket, hogy egy előre összeállított entitások működését:

> [!NOTE]
> **Builtin.DateTime** elavult. A rendszer felülírja [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), dátum és idő címtartományok recognition, valamint továbbfejlesztett elismerését nem egyértelmű dátumok és időpontok biztosítja.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [előre összeállított entitások hozzáadása](luis-prebuilt-entities.md) az alkalmazáshoz.