---
title: Mi az a beszélgetés tanuló? -Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a beszélgetési tanuló és annak működéséről.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9cbf0e60382ef17d68aab47cf5f24ea9b8434f13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348611"
---
# <a name="what-is-conversation-learner"></a>Mi az a beszélgetés tanuló?

Beszélgetés tanuló felépítéséhez és mutatja meg, hogy megtudjuk példa kapcsolati conversational felületek teszi lehetővé. 

Hagyományos megközelítés, eltérően beszélgetés tanuló úgy ítéli meg, a párbeszéd válaszok javításához, és nagyobb felhasználói élmény biztosításához a végpont keretében. Feladatközpontú széles körét átfedés használati esetekben, beszélgetés tanuló gép tanulási botok és intelligens kevésbé valószínű, hogy azoknak a felhasználóknak, további ügyfél-szolgáltatási költségei fel Önnek ügynökök a háttérben, és intuitívabb együttműködhet vonatkozik.

A kezdéshez a fejlesztői szeretnék bátorítaná prototypical párbeszédpanelek kerül. További párbeszédpanelek van-e megadva, a modell folyamatosan frissül, és fejlesztői láthatja, milyen mértékben a modell normalizálása-e. Ha a modell jól működik, a botot is telepíthető a végfelhasználók számára. A fejlesztői is tekintsék meg őket, és beszélgetés tanuló beszélgetések naplózza a felhasználóival. Hibák vannak pedig a pöttyös, ha a fejlesztői tehet egy a helyszíni javítása, és a modell retrained és használható azonnal.

Ez a megközelítés csökkenti a manuális kódolási párbeszéd vezérlő logikáját, és lehetővé teszi, hogy a vállalakozások tulajdonosai vagy a tartomány szakértők hozzájárul, hogy egy conversational felület nélkül előzetes gépi tanulási a Tudásbázis. Telepítve van egy botot, intelligens eszköz vagy egy intelligens ügynök részeként, hogy a beszélgetés tanuló gyorsan többször az új képességek, viselkedések vagy szakértelem, és gyorsan a minőségének javítása. 

Beszélgetés tanuló lehetővé teszi a fejlesztők sebességű piaci és a meghajtó sikeres párbeszéd növeléséhez több conversational csatornákon keresztül a Microsoft Botot keretrendszer, vagy a saját infrastruktúrája önálló között.

Összegzés és emeli ki:

- Beszélgetés tanuló a feladatközpontú botok kialakításának AI-első módszerrel.

- Ez egy végpontok közötti ismétlődő Neurális hálózat (LSTM) alapul, és megtanulja közvetlenül a beszélgetés példák többszörös kapcsolja. 

- Lehetővé teszi a tervezők, fejlesztők, üzleti felhasználók, és hívás center munkavállalók felépítéséhez és botok karbantartása. 

- Üzleti szabályok és a józan kódot az express teszi lehetővé.

- Során oktatási munkamenetek, a Neurális hálózat modellt használja a következő set várható műveleteket a téma pontozása céljából. A botot fejlesztői ezután válassza ki a megfelelő műveletet, és a hálózatra, hogy biztosítsák a megfelelő választ betanításához.
 
- Képzési befejezése után a fejlesztői segítségével a felhasználói kapcsolatok napló párbeszédpaneleket botot válaszok a javításokat, és a modell működik. 

- Meghívhatja tartományspecifikus és külső API-k feladatok elvégzéséhez.

