---
title: LUIS app együttműködés - Azure megértése |} Microsoft Docs
description: Egy tulajdonoshoz és választható közreműködők LUIS alkalmazásokhoz.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265065"
---
# <a name="collaborating"></a>Együttműködés

LUIS biztosít együttműködést másokkal ahhoz, hogy az alkalmazások engedélyezéséhez.

## <a name="luis-account"></a>LUIS fiók
Egy LUIS fiókkal társítva egyetlen [Microsoft Live](https://login.live.com/) fiók. Minden LUIS fiókhoz megadott van egy ingyenes [kulcs szerzői](luis-concept-keys.md#authoring-key) való használatra az LUIS alkalmazások készítése a fiókjának van hozzáférési joga. 

Előfordulhat, hogy egy LUIS fiók sok LUIS alkalmazás.

## <a name="luis-app-owner"></a>LUIS alkalmazás tulajdonosa
A fiókot, amely létrehoz egy alkalmazást a tulajdonosa. Minden alkalmazás rendelkezik egy tulajdonoshoz. A tulajdonos szerepel app  **[beállítások](luis-how-to-collaborate.md)**. Azt a fiókot, amelyet az alkalmazás törlése. Ez akkor is a fiókot, amely e-mailt kap, a végpont kvóta elérésekor 75 %-a havi korlátot. 

## <a name="transfer-ownership"></a>Tulajdonjogok átadása
LUIS tulajdonjogának, nem biztosít, azonban bármely közreműködő exportálhatja az alkalmazást, és majd importálja azt az alkalmazás létrehozása. Vegye figyelembe az új alkalmazás rendelkezik egy másik azonosítót. Az új app kell képezni, közzétett, és a használt új végpont.

## <a name="luis-app-collaborators"></a>LUIS app közreműködők
Az alkalmazás tulajdonosa közreműködők is hozzáadhat egy alkalmazást. A tulajdonosa hozzá kell a közreműködő e-mail címet az alkalmazás  **[beállítások](luis-how-to-collaborate.md)**. A közreműködő az alkalmazás teljes hozzáféréssel rendelkezik. A közreműködő törölheti az alkalmazást, ha az alkalmazás a közreműködő fiók törlődik, de marad, a tulajdonos fiók. 

Ha azt szeretné, több alkalmazás megosztása közreműködők, a közreműködő e-mail hozzá kell minden alkalmazást. 

## <a name="managing-multiple-authors"></a>Több szerző kezelése
A [LUIS] [ LUIS] webhely jelenleg nem kínál tranzakció szintű szerzői. Engedélyezheti a szerzők alap verziójából származó független verzióin működik. Két különböző módszereket a következő szakaszok ismertetik.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Több verzió belül ugyanaz az alkalmazás kezelése
Kezdje az [Klónozás](luis-how-to-manage-versions.md#clone-a-version), az Alapverzió, minden egyes Szerző. 

Minden egyes Szerző módosítást hajt végre az alkalmazás a saját verzióját. Ha elégedett a modellel minden Szerző, JSON fájlokba exportálását az új verziókat.  

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások hasonlíthatók össze. A fájlokat, hogy az új verzió egy JSON-fájl össze. Módosítsa a **versionId** tulajdonság az új, egyesített verzió jelölésére JSON-ban. Verzió importálnia kell az eredeti alkalmazást. 

Ez a módszer lehetővé teszi egy aktív verzió van, egy szakaszban verziót, és egy közzétett verziót. Összehasonlíthatja az eredményeket a interaktív tesztelési panelen a három verziók között.

### <a name="manage-multiple-versions-as-apps"></a>Több verzió mint alkalmazások kezelése
[Exportálás](luis-how-to-manage-versions.md#export-version) Alapverzió. Minden egyes Szerző importálja a verzióra. A személy, amellyel az alkalmazás különféle verzió tulajdonosa. Miután befejezte a verzió módosítása az alkalmazás exportálása. 

Exportált alkalmazások olyan JSON-formátumú fájlokat, amelyek a módosítások az alap exportálás hasonlíthatók össze. A fájlokat, hogy az új verzió egy JSON-fájl össze. Módosítsa a **versionId** tulajdonság az új, egyesített verzió jelölésére JSON-ban. Verzió importálnia kell az eredeti alkalmazást.

## <a name="next-steps"></a>További lépések

Megértéséhez [versioning](luis-concept-version.md) fogalmakat. 

Lásd: [Alkalmazásbeállítások](luis-how-to-collaborate.md) megtudhatja, hogyan kezelheti az alkalmazás LUIS közreműködők.

Lásd: [adja hozzá a e-mail hozzáférés listához](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) a szerzői API-khoz.

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website