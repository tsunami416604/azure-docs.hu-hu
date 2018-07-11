---
title: Új alkalmazás létrehozása az intelligens hangfelismerési szolgáltatással |} A Microsoft Docs
description: Létrehozhat és kezelhet az alkalmazások, a Language Understanding (LUIS) weblapon.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 998a85720f5707fbf6ed4c5cfa3ed0dab5d1cc0e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932909"
---
# <a name="create-an-app"></a>Alkalmazás létrehozása
Egy új alkalmazást hoz létre, különböző módon: 

* [Indítsa el](#create-new-app) egy üres alkalmazást és szándék fog vonatkozni, utterances és entitások létrehozásához.
* [Indítsa el](#create-new-app) egy üres alkalmazást, és adja hozzá a [előre összeállított tartomány](luis-how-to-use-prebuilt-domains.md).
* [A LUIS alkalmazás importálása](#import-new-app) , amely már tartalmazza a szándék fog vonatkozni, utterances és entitások JSON-fájlból.

## <a name="what-is-an-app"></a>Mi az alkalmazás
Az alkalmazás tartalmaz [verziók](luis-how-to-manage-versions.md) a modellt, valamint bármely [közreműködők](luis-how-to-collaborate.md) az alkalmazáshoz. Amikor az alkalmazást hoz létre, ki kell választania a kulturális környezet ([nyelvi](luis-supported-languages.md)) amely **később már nem módosítható**. 

Új alkalmazás alapértelmezett verzióját az "0.1". 

Létrehozhat és kezelhet alkalmazásokat a **saját alkalmazások** lapot. Ezen a lapon kattintva mindig elérheti **saját alkalmazások** a navigációs sáv tetején, a [LUIS](luis-reference-regions.md) webhely. 

[![](media/luis-create-new-app/apps-list.png "Képernyőkép az alkalmazások listája")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Új alkalmazás létrehozása

1. A **saját alkalmazások** lapon jelölje be **új alkalmazás létrehozása**.
2. A párbeszédpanelen adjon nevet az alkalmazás "TravelAgent".

    ![Hozzon létre új alkalmazás párbeszédpanel](./media/luis-create-new-app/create-app.png)

3. Válassza ki az alkalmazás kulturális környezet (TravelAgent alkalmazások esetén válassza az angol nyelvű), majd válassza ki **kész**. 

    >[!NOTE]
    >A kulturális környezet nem módosítható, ha az alkalmazás létrejött. 

## <a name="import-new-app"></a>Új alkalmazás importálása
Beállíthatja a nevét (max. 50 karakter), verzió (legfeljebb 10 char) és az alkalmazás leírását a JSON-fájlban. Példák a alkalmazás JSON-fájlok esetén érhető el [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. A **saját alkalmazások** lapon jelölje be **importálása új alkalmazás**.
2. Az a **importálása új alkalmazás** párbeszédpanelen válassza ki a JSON-fájlt a LUIS alkalmazás meghatározása.

    ![Importálja egy új alkalmazás párbeszédpanel](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Alkalmazás exportálása
1. A **saját alkalmazások** lapon, válassza ki a három pontra (***...*** ) az alkalmazások sor végén található.

    [![](media/luis-create-new-app/apps-list.png "App műveletek felugró párbeszédpanel képernyőképe")](media/luis-create-new-app/three-dots.png#lightbox)

2. Válassza ki **exportálási alkalmazás** a menüből. 

## <a name="rename-app"></a>Alkalmazás átnevezése

1. A **saját alkalmazások** lapon, válassza ki a három pontra (***...*** ) az alkalmazások sor végén található. 
2. Válassza ki **átnevezése** a menüből.
3. Adja meg az alkalmazást, és válassza az új neve **kész**.

## <a name="delete-app"></a>Alkalmazás törlése

> [!CAUTION]
> Az alkalmazás összes közreműködők és a tulajdonos törlése folyamatban van. [Exportálás](#export-app) az alkalmazás törlése előtt. 

1. A **saját alkalmazások** lapon, válassza ki a három pontra (***...*** ) az alkalmazások sor végén található. 
2. Válassza ki **törlése** a menüből.
3. Válassza ki **Ok** a megerősítési ablakban.

## <a name="export-endpoint-logs"></a>Végpont naplók exportálása
A naplók tartalmaznak a lekérdezést, UTC-idő és a LUIS JSON-választ.

1. A **saját alkalmazások** lapon, válassza ki a három pontra (***...*** ) az alkalmazások sor végén található. 
2. Válassza ki **végpont naplók exportálása** a menüből.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>További lépések

Az első az alkalmazás feladata [leképezések hozzáadása](luis-how-to-add-intents.md).