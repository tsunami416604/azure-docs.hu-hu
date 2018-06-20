---
title: A LUIS - Azure versioning megértése |} Microsoft Docs
description: Megtudhatja, hogyan számára való kezeléséhez a nyelvi ismertetése (LUIS) használata
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: v-geberr
ms.openlocfilehash: dabe7def2766770b686be3c43d4af4f331dd9577
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266073"
---
# <a name="versions"></a>Verziók
Hozzon létre ugyanabban az alkalmazásban másik modelljei [verziók](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Verzió azonosítója
A Verzióazonosító karakterekből áll, számjegy, vagy ".", és nem lehet hosszabb, mint 10 karakter.

## <a name="initial-version"></a>Eredeti verziójára
A kezdeti (0,1) verziója az alapértelmezett active verzió. 

## <a name="active-version"></a>Aktív verzió
A [egy verzió](luis-how-to-manage-versions.md#set-active-version) , az aktív azt jelenti, hogy jelenleg szerkesztés és vizsgálni a [LUIS] [ LUIS] webhelyet. Állítsa be a verziót egy aktív fér hozzá az adatokhoz, ellenőrizze a frissítéseket, valamint tesztelése, és tegye közzé.

A jelenleg aktív verzió neve után jelenik meg a felső, bal oldali panelen az alkalmazás nevére. 

[ ![Aktív verzió módosítása](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verziók és közzétételi tárhelyek
A szakasz és a termék tárhelyek közzétételére. Mindegyik tárolóhely egy másik verzió vagy azonos verziója lehet. Ez akkor hasznos, ellenőrzésének módosítások modellverziók keresztül a végpont, amely elérhető botok vagy más LUIS hívja az alkalmazások között. 

## <a name="clone-a-version"></a>A verzió klónozása
Klónozza a verzióra másolatot készít a meglévő verziója, és mentse egy új verziója. Klónozza a verzióját használja kiindulópontként ugyanahhoz a tartalomhoz, a meglévő verziót, az új verzió. Klónozza a verziója, az új verzió lesz a **aktív** verziója. 

## <a name="import-and-export-a-version"></a>Importálás és exportálás a verzió
Importálhatja egy verziója, az alkalmazás szintjén. Verzió aktív verziója lesz, és a Verzióazonosító szerepel az alkalmazásfájl "versionId" tulajdonságát. Is importálhatja a verzió szinten egy meglévő alkalmazást. Az új verzió aktív verziója lesz. 

Az alkalmazás szintjén is exportálja, vagy exportálhatja a verziószámra egy verzió. Az egyetlen különbség, hogy az alkalmazási szintű exportált verziója a verziószámra, jelenleg aktív verziója, esetén dönthet úgy, bármely verziójáról a exportálása a **[beállítások](luis-how-to-manage-versions.md)** lap. 

Az exportált fájl nem tartalmaz számítógép által ismert információ, mert az alkalmazás van retrained importálás után. Az exportált fájl nem tartalmaz közreműködők--hozzá kell adnia ezeket vissza, a verziót az új alkalmazásba importálása után.

## <a name="export-each-version-as-app-backup"></a>Egyes verzióihoz exportálni alkalmazás biztonsági mentése
Ahhoz, hogy készítsen biztonsági másolatot a LUIS app, exportálja minden verzió a **[beállítások](luis-how-to-manage-versions.md)** lap.

## <a name="delete-a-version"></a>A verzió törlése
Az aktív verzió kivételével az összes verzió törölheti a verziók listájából a beállítások lapon. 

## <a name="version-availability-at-the-endpoint"></a>A végpont verzió elérhetőségének
Betanított verziók nem érhetők el automatikusan, az alkalmazás [végpont](luis-glossary.md#endpoint). Meg kell [közzététele](PublishApp.md) vagy ahhoz, hogy elérhető legyen a következőn az alkalmazás végpontjának verzió közzé. Közzéteheti **átmeneti** és **éles**, felkínálva legfeljebb két verziója érhető el az alkalmazást a végpont. Ha az alkalmazás a végpont elérhető további verzióinak van szüksége, exportálja a verziót, és importálja újra egy új alkalmazásba. Az új alkalmazás rendelkezik egy másik alkalmazás.

## <a name="collaborators"></a>Közreműködők
A tulajdonos és az összes [közreműködők](luis-how-to-collaborate.md) az alkalmazás összes verzió teljes hozzáféréssel rendelkeznek.

## <a name="next-steps"></a>További lépések

Felvételével [versioning](luis-how-to-manage-versions.md) az alkalmazás beállításai oldal. 

Ismerje meg, hogyan tervezhető [leképezések](luis-concept-intent.md) a modellbe.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions