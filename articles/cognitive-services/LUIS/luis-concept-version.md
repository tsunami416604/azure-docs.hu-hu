---
title: Verziókezelés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Verziók, LUIS, a verziók a hagyományos programozási hasonlóak. Minden egyes verziója egy pillanatképet az alkalmazás időben. Az alkalmazás módosítása előtt hozzon létre egy új verziója. Egyszerűbb legyen a lépjen vissza a pontos alkalmazást, majd próbálja meg unpeel és az alkalmazás szándéka és utterances korábbi állapotába.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: e71fc5bb0bd8ce54b471829df20336a9dea0ccda
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217084"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Megismerheti, hogyan és mikor a LUIS-verzió használatához

Verziók, LUIS, a verziók a hagyományos programozási hasonlóak. Minden egyes verziója egy pillanatképet az alkalmazás időben. Az alkalmazás módosítása előtt hozzon létre egy új verziója. Lépjen vissza a pontos verzióra, majd próbálja meg eltávolítani a leképezések és a egy korábbi állapotba utterances könnyebbé válik.

Ugyanezt az alkalmazást a különböző modellek létrehozásának eljárásait [verziók](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Verzió azonosítója
A Verzióazonosító karakterek, számjegyek áll, vagy ".", és nem lehet hosszabb 10 karakter hosszúságú.

## <a name="initial-version"></a>Eredeti verziójára
A kezdeti verzióhoz (0,1) az alapértelmezett active verzió. 

## <a name="active-version"></a>Aktív verziója
A [-verzió beállítása](luis-how-to-manage-versions.md#set-active-version) , az aktív jelenti, hogy a jelenleg szerkesztett és tesztelése a [LUIS](luis-reference-regions.md) webhelyén. Az adatok eléréséhez, hajtsa végre a frissítéseket, is tesztelheti, és tegye közzé az active-verzió beállítása.

A jelenleg aktív verzió neve a felső, a bal oldali panelen jelenik meg az alkalmazás neve után. 

[ ![Aktív verzió módosítása](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verziók és közzétételi tárolóhely
A szakaszt, és a termék bővítőhelyek közzétételére. Mindegyik tárolóhely lehet egy másik verzió vagy ugyanazt a verziót. Ez hasznos annak ellenőrzésére a módosításokat a végpont elérhető robotokat vagy más hívó alkalmazásoknak LUIS modell verziói között. 

## <a name="clone-a-version"></a>Klónozza a verzió
Klónozza a másolatot készít egy meglévő verziója, és mentse egy új verzió verziója. Klónozza a meglévő verziót ugyanazon tartalmát használja kiindulási pontként az új verzióhoz tartozó verziója. Klónozza a verzió, miután az új verzió válik a **aktív** verzió. 

## <a name="import-and-export-a-version"></a>Importálás és exportálás-verzió
A verzió, az alkalmazás szintjén importálhatja. Azt a verziót az aktív-verzió lesz, és használja a Verzióazonosító az alkalmazásfájl "versionId" tulajdonságában. Is importálhatja a verzió szintjén egy meglévő alkalmazást. Az új verzió válik, az aktív verziót. 

Az alkalmazás szintjén is exportálja, vagy exportálhatja egy verziót, a verzió szintjén. Az egyetlen különbség, hogy az alkalmazásszintű exportált verziót a jelenleg aktív verziót, a verziószámra, kiválaszthatja a exportálása bármely verziójáról a **[beállítások](luis-how-to-manage-versions.md)** lapot. 

Az exportált fájl nem tartalmaz a gép megismert információk, mert az alkalmazás van retrained importálás után. Az exportált fájl nem tartalmaz közreműködő – kell hozzáadnia ezeket a biztonsági, a verzió az új alkalmazásba importálása után.

## <a name="export-each-version-as-app-backup"></a>Alkalmazás biztonsági másolatának minden verzió exportálása
Annak érdekében, hogy az alkalmazás biztonsági mentése a LUIS, exportálás minden verzió a **[beállítások](luis-how-to-manage-versions.md)** lapot.

## <a name="delete-a-version"></a>A verzió törlése
Minden verzió, kivéve az aktív verzió törölheti a verziók listából a beállítások lapon. 

## <a name="version-availability-at-the-endpoint"></a>A végpont verzió rendelkezésre állásáról
Betanított verziók nem érhetők el automatikusan, az alkalmazás [végpont](luis-glossary.md#endpoint). Meg kell [közzététele](luis-how-to-publish-app.md) vagy újra közzétennie ahhoz, hogy az alkalmazás-végpont elérhető legyen egy verziót. Is lehet publikálni **átmeneti** és **éles**, így Ön a végponton legfeljebb két verziója érhető el az alkalmazást. Ha az alkalmazás a végpont elérhető további verziói, a verzió exportálása, és importálja újra egy új alkalmazásba. Az új alkalmazás rendelkezik egy másik alkalmazás.

## <a name="collaborators"></a>Közreműködők
A tulajdonos és az összes [közreműködők](luis-how-to-collaborate.md) teljes hozzáférés az alkalmazás összes verziójára.

## <a name="next-steps"></a>További lépések

Tekintse meg, hogyan adhat hozzá [versioning](luis-how-to-manage-versions.md) alkalmazás beállítások oldalán. 

Ismerje meg, hogyan tervezhet [leképezések](luis-concept-intent.md) a modellbe.
