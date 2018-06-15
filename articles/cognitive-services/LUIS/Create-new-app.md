---
title: Hozzon létre egy új alkalmazást LUIS |} Microsoft Docs
description: Alkalmazások létrehozásához és kezeléséhez a nyelvi ismertetése (LUIS) webhelyen.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 75edd39346995cdef72bb1e1fcb9eaff53d29702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349787"
---
# <a name="create-an-app"></a>Alkalmazás létrehozása
Létrehoz egy új alkalmazást különböző módon: 

* [Start](#create-new-app) üres alkalmazáshoz, és hozzon létre a leképezések, utterances és entitások.
* [Start](#create-new-app) üres alkalmazáshoz, és adja hozzá a [előre elkészített tartomány](luis-how-to-use-prebuilt-domains.md).
* [Importálja a LUIS alkalmazások](#import-new-app) a JSON-fájl, amely már tartalmazza a leképezések, utterances és entitások.

## <a name="what-is-an-app"></a>Mi az az alkalmazást
Az alkalmazás tartalmaz [verziók](luis-how-to-manage-versions.md) a modell, valamint bármely [közreműködők](luis-how-to-collaborate.md) az alkalmazás. Az alkalmazás létrehozásakor bejelöli-e a kulturális környezethez ([nyelvi](luis-supported-languages.md)) amely **később már nem módosítható**. 

Az alapértelmezett egy új alkalmazás verziója "0,1." 

Hozhat létre és kezeli az alkalmazásokat a **saját alkalmazások** lap. Ez a lap választásával mindig elérhető **alkalmazásaimat** a felső navigációs sávján látható a [LUIS](luis-reference-regions.md) webhely. 

[![](media/luis-create-new-app/apps-list.png "Képernyőkép a alkalmazások listája")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Új alkalmazás létrehozása

1. A **saját alkalmazások** lapon jelölje be **hozzon létre új alkalmazás**.
2. A párbeszédpanelen adja meg az alkalmazás "TravelAgent" nevét.

    ![Hozzon létre új alkalmazás párbeszédpanel](./media/luis-create-new-app/create-app.png)

3. Válassza ki az alkalmazás kulturális környezet (TravelAgent alkalmazások esetén válassza az angol), majd válassza ki **végzett**. 

    >[!NOTE]
    >A kulturális környezet nem módosítható, az alkalmazás létrehozása után. 

## <a name="import-new-app"></a>Új alkalmazás importálása
Beállíthatja a nevét (legfeljebb 50 karakter), a verzió (legfeljebb 10 karakter) és az alkalmazás leírása a JSON-fájlban. JSON alkalmazásfájlokhoz példák érhető el [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. A **saját alkalmazások** lapon jelölje be **importálási új alkalmazás**.
2. Az a **importálási új alkalmazás** párbeszédpanelen válassza ki a JSON-fájlt a LUIS app meghatározása.

    ![Importálja az új alkalmazás párbeszédpanel](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Alkalmazás exportálása
1. A **saját alkalmazások** lapon, válassza ki a három pont (…) az alkalmazás sor végén.

    [![](media/luis-create-new-app/apps-list.png "Az alkalmazáson belüli műveletek felugró párbeszédpanel képernyőképe")](media/luis-create-new-app/three-dots.png#lightbox)

2. Válassza ki **exportálási app** a menüből. 

## <a name="rename-app"></a>Nevezze át az alkalmazást

1. A **saját alkalmazások** lapon, válassza ki a három pont (…) az alkalmazás sor végén. 
2. Válassza ki **átnevezése** a menüből.
3. Írja be az alkalmazást, és válassza ki az új nevét **végzett**.

## <a name="delete-app"></a>Alkalmazás törlése

> [!CAUTION]
> Törli az összes közreműködők és a tulajdonos alkalmazása. [Exportálás](#export-app) törlése előtt az alkalmazást. 

1. A **saját alkalmazások** lapon, válassza ki a három pont (…) az alkalmazás sor végén. 
2. Válassza ki **törlése** a menüből.
3. Válassza ki **Ok** a az ablak.

## <a name="export-endpoint-logs"></a>Végpont logs exportálása
A naplók tartalmazza a következő lekérdezést, UTC idő szerint, és LUIS JSON-választ.

1. A **saját alkalmazások** lapon, válassza ki a három pont (…) az alkalmazás sor végén. 
2. Válassza ki **végpont logs exportálása** a menüből.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>További lépések

Az első feladat a alkalmazás [leképezések hozzáadása](luis-how-to-add-intents.md).