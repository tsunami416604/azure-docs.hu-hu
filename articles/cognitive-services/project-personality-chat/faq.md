---
title: Gyakori kérdések – Personality Chat
titlesuffix: Azure Cognitive Services
description: Gyakori kérdések Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: d5f144fa0bf5325bfe2922fdc00c6689d894ff7e
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258579"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Miért nem válaszol minden kérdésre, mint a többi chat-robotok?

A Project személyiségi csevegés a közös kisméretű beszélgetéssel fokozza a robotot, amely a személyiséget mutatja be, és teljesebb felhasználói élményt nyújt. Nem úgy tervezték, hogy hosszú beszélgetéseket hajtson végre olyan témakörökkel kapcsolatban, amelyek nem kapcsolódnak a robot elsődleges funkciójához. Habár az összes beszélgetésre reagálhat, a bétaverzióban korlátozott, a gyakori kisméretű Talk-tartományokra korlátozódik.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Hogyan szabhatom testre a személyiséget a saját márka igényei szerint?

Válassza ki az elérhető alapértelmezett personák közül a legközelebb álló Personát. Napjainkban a szerkesztői függvénytárat és a válaszokat szerkesztheti, hogy jobban megfeleljen a márkájának. A későbbiekben feltöltheti a kiválasztott személyiségből származó hosszúságú kimondott szöveg, és megkeresheti a legközelebbi person ID-verzióját. A modell újratanítására és testreszabására is lehetőség van.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Ez a szolgáltatás olyan meglévő intelligens ügynököket kapcsol be, mint például a zo?

A zo, a Cortana és a Project által nyújtott szolgáltatások Personality Chat megoszthatnak hasonló technikákat, de különböző stackek. A zo és a Cortana tapasztalataiból beszerzett tanulók.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Okozhat a szolgáltatás a vásárlói élményben?

Ha gazdagabb élményt szeretne biztosítani, Personality Chat a szerkesztői adatkészleten kívüli válaszokat is létrehozhat, és az összes felhasználói bevitelt megpróbálja értelmezni. Tehát lehetséges, hogy a válasz nem megfelelő a kontextusban. Számos vezérlő gondoskodik a kedvezőtlen válaszok elkerüléséről a Zóhoz hasonló intelligens ügynököktől származó ismeretekre építve. Alapértelmezés szerint a Personality Chat projekt úgy van beállítva, hogy kizárólag felismert felhasználói szándékokra válaszoljon. Érdemes tesztelnie, hogy a Personality Chat projekt alkalmazható-e az Ön körülményei között. Szívesen vesszük visszajelzését, ha észrevesz valamit, ami további betanítást igényel. Ha a jövőben ezt a szolgáltatást használja az ügyfelekkel, javasoljuk, hogy a névtelen naplózással azonosítsa az élő felhasználói interakciókkal kapcsolatos problémákat.
