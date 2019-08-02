---
title: Verziószámozás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-verziók a hagyományos programozási verziókhoz hasonlóak. Minden verzió egy pillanatkép az alkalmazás időpontjában. Mielőtt módosításokat hajt végre az alkalmazásban, hozzon létre egy új verziót. Könnyebben visszatérhet a pontos alkalmazáshoz, majd megpróbálhatja leválasztani a lehámozás és az alkalmazás szándékát, és hosszúságú kimondott szöveg egy korábbi állapotba.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2e13efa70d0344defeb306a92ac405439635e929
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619705"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>A LUIS-verzió használatának ismertetése

A LUIS-verziók a hagyományos programozási verziókhoz hasonlóak. Minden verzió egy pillanatkép az alkalmazás időpontjában. Mielőtt módosításokat hajt végre az alkalmazásban, hozzon létre egy új verziót. Könnyebb visszatérni a pontos verzióra, majd megpróbáljuk eltávolítani a leképezéseket, és hosszúságú kimondott szöveg egy korábbi állapotba.

Hozzon létre különböző modelleket ugyanazzal az [](luis-how-to-manage-versions.md)alkalmazással a verziókkal. 

## <a name="version-id"></a>Verzióazonosító
A verzióazonosító karakterből, számjegyből vagy "." áll, és nem lehet hosszabb 10 karakternél.

## <a name="initial-version"></a>Kezdeti verzió
A kezdeti verzió (0,1) az alapértelmezett aktív verzió. 

## <a name="active-version"></a>Aktív verzió
Ha a verziót aktívként szeretné [beállítani](luis-how-to-manage-versions.md#set-active-version) , a rendszer jelenleg szerkeszti és teszteli a [Luis](luis-reference-regions.md) webhelyén. Állítsa be az aktív verziót az adathozzáféréshez, a frissítésekhez, valamint a teszteléshez és közzétételhez.

A jelenleg aktív verzió neve az alkalmazás neve után a felső, a bal oldali panelen jelenik meg. 

[![Aktív verzió módosítása](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Verziók és közzétételi pontok
Közzéteszi a szakasz és a termék tárolóhelyeit. Az egyes tárolóhelyek eltérő verziójúak vagy azonos verziójúak lehetnek. Ez akkor lehet hasznos, ha ellenőrizni szeretné a modell verziói közötti módosításokat a végponton keresztül, amely a botok vagy más LUIS hívó alkalmazások számára érhető el. 

## <a name="clone-a-version"></a>Klónozza a verzió
Egy verzió klónozásával létrehozhatja egy meglévő verzió másolatát, és mentheti új verzióként. A verzió klónozásával a meglévő verzió azonos tartalmát használja az új verzió kiindulási pontjaként. Miután klónozott egy verziót, az új verzió lesz az **aktív** verzió. 

## <a name="import-and-export-a-version"></a>Verzió importálása és exportálása
Az alkalmazás szintjén is importálhat egy verziót. Ez a verzió lesz az aktív verzió, és a verziószámot használta az alkalmazás "versionId" tulajdonságában. A verziót a verzió szintjén is importálhatja egy meglévő alkalmazásba. Az új verzió lesz az aktív verzió. 

Az alkalmazás szintjén exportálhat egy verziót, vagy exportálhat egy verziót a verziószáma alatt. Az egyetlen különbség, hogy az alkalmazás-szintű exportált verzió a jelenleg aktív verzió a verzió szintjén, a **[Beállítások](luis-how-to-manage-versions.md)** lapon bármilyen verziót kiválaszthat az exportáláshoz. 

Az exportált fájl nem tartalmaz géppel megismert adatokat, mert az alkalmazás a importálása után újra be lett tanítva. Az exportált fájl nem tartalmaz közreműködőket – ezeket a verziónak az új alkalmazásba való importálása után újra fel kell vennie.

## <a name="export-each-version-as-app-backup"></a>Minden verzió exportálása alkalmazás-biztonsági másolatként
A LUIS-alkalmazás biztonsági mentéséhez exportálja az egyes verziókat a **[Beállítások](luis-how-to-manage-versions.md)** lapon.

## <a name="delete-a-version"></a>Verzió törlése
Az aktív verzió kivételével az összes verziót törölheti a beállítások lap verziók listájában. 

## <a name="version-availability-at-the-endpoint"></a>A verzió elérhetősége a végponton
A betanított verziók nem érhetők el automatikusan [](luis-glossary.md#endpoint)az alkalmazás-végponton. Egy verziót [](luis-how-to-publish-app.md) kell közzétennie vagy újból közzétennie ahhoz, hogy elérhető legyen az alkalmazás végpontján. Közzéteheti az **előkészítést** és a **gyártást**, így az alkalmazás két verziója érhető el a végponton. Ha egy végponton elérhetővé kell tennie az alkalmazás több verzióját, exportálnia kell a verziót, és újra importálnia kell egy új alkalmazásba. Az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik.

## <a name="collaborators"></a>Közreműködők
A tulajdonos és az összes [közreműködő](luis-how-to-collaborate.md) teljes hozzáféréssel rendelkezik az alkalmazás összes verziójához.

## <a name="next-steps"></a>További lépések

Lásd: [verziószámozás](luis-how-to-manage-versions.md) hozzáadása az Alkalmazásbeállítások lapon. 

Megtudhatja, [](luis-concept-intent.md) hogyan tervezhet meg leképezéseket a modellbe.
