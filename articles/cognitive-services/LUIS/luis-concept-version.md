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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: c519b030aaee58397766ecb8658e7af08b5986e1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256871"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>A LUIS-verzió használatának ismertetése

A LUIS-verziók a hagyományos programozási verziókhoz hasonlóak. Minden verzió egy pillanatkép az alkalmazás időpontjában. Mielőtt módosításokat hajt végre az alkalmazásban, hozzon létre egy új verziót. Könnyebb visszatérni a pontos verzióra, majd megpróbáljuk eltávolítani a leképezéseket, és hosszúságú kimondott szöveg egy korábbi állapotba.

Hozzon létre különböző modelleket ugyanazzal az alkalmazással a [verziókkal](luis-how-to-manage-versions.md). 

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

Az exportált fájl nem tartalmaz géppel megismert adatokat, mert az alkalmazás a importálása után újra be lett tanítva. Az exportált fájl nem tartalmaz közreműködői adatokat.

## <a name="export-each-version-as-app-backup"></a>Minden verzió exportálása alkalmazás-biztonsági másolatként
A LUIS-alkalmazás biztonsági mentéséhez exportálja az egyes verziókat a **[Beállítások](luis-how-to-manage-versions.md)** lapon.

## <a name="delete-a-version"></a>Verzió törlése
Az aktív verzió kivételével az összes verziót törölheti a beállítások lap verziók listájában. 

## <a name="version-availability-at-the-endpoint"></a>A verzió elérhetősége a végponton
A betanított verziók nem érhetők el automatikusan az alkalmazás- [végponton](luis-glossary.md#endpoint). Egy verziót kell [közzétennie](luis-how-to-publish-app.md) vagy újból közzétennie ahhoz, hogy elérhető legyen az alkalmazás végpontján. Közzéteheti az **előkészítést** és a **gyártást**, így az alkalmazás két verziója érhető el a végponton. Ha egy végponton elérhetővé kell tennie az alkalmazás több verzióját, exportálnia kell a verziót, és újra importálnia kell egy új alkalmazásba. Az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Alkalmazáson belül több verziók kezelése
Első lépésként [Klónozás](luis-how-to-manage-versions.md#clone-a-version), a kiindulási verzióról, mindegyik szerző számára. 

Minden szerző módosítja az alkalmazás saját verzióját. Ha mindegyik Szerző elégedett a modellel, JSON-fájlok exportálása az új verziókat.  

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások hasonlítható. A fájlokat, és hozzon létre egy egyetlen JSON-fájlt az új verzió össze. Módosítsa a **versionId** tulajdonság a JSON az új, egyesített verzió jelölésére. Importálja azt a verziót az eredeti alkalmazásba. 

Ez a módszer lehetővé teszi, hogy egy aktív verzióját, egy szakasz és egy közzétett verziója. Az aktív verzió eredményeit összehasonlíthatja egy közzétett verzióval (fázis vagy éles környezet) az [interaktív tesztelési panelen](luis-interactive-test.md).

## <a name="manage-multiple-versions-as-apps"></a>Alkalmazások több verziók kezelése
[Exportálás](luis-how-to-manage-versions.md#export-version) Alapverzió. Mindegyik Szerző importálja a verziót. A személy, amely az alkalmazás importál a verzió tulajdonosa. Amikor végzett a verzió módosítása az alkalmazás exportálása. 

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások az alap exportálás összehasonlíthatók. A fájlokat, és hozzon létre egy egyetlen JSON-fájlt az új verzió össze. Módosítsa a **versionId** tulajdonság a JSON az új, egyesített verzió jelölésére. Importálja azt a verziót az eredeti alkalmazásba.

## <a name="contributions-from-collaborators"></a>Közreműködők hozzájárulásai

További információ a [közreműködők](luis-how-to-collaborate.md)hozzájárulásainak létrehozásáról.

## <a name="next-steps"></a>További lépések

Lásd: [verziószámozás](luis-how-to-manage-versions.md) hozzáadása az Alkalmazásbeállítások lapon. 

Megtudhatja, hogyan tervezhet meg [leképezéseket](luis-concept-intent.md) a modellbe.
