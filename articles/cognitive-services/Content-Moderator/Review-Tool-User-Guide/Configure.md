---
title: Felülvizsgálati eszköz beállításainak - Content Moderator konfigurálása
titlesuffix: Azure Cognitive Services
description: A felülvizsgálati eszköz használatával állítsa be, vagy a csapat, címkék, összekötők, a munkafolyamatok és hitelesítő adatokat lekérni a Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756521"
---
# <a name="configure-the-review-tool"></a>A felülvizsgálati eszköz konfigurálása

A [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com) számos fontos funkcióval keresztül férnek hozzá a **beállítások** menü az irányítópulton.

![Túl Content Moderator felülvizsgálati beállítások menü](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Csoportok és alcsoportok kezelése

A **csapat** lap lehetővé teszi a csoportok és alcsoportok kezelése&mdash;értesítést kaphat, amikor bizonyos felhasználói csoportok [emberi ellenőrző](../review-api.md#reviews) elindulnak. Csak egy team (amely hoz létre, amikor regisztrál a felülvizsgálati eszköz) is rendelkezik, de létrehozhat több alcsoportok. A csapat rendszergazdája tagok meghívása, állítsa be az engedélyeiket, és hozzárendelheti azokat különböző alcsoportok.

![Tekintse át az eszköz team-beállítások](images/settings-2-team.png)

Alcsoportok hasznosak eszkalációs csapatok vagy a megjelölt kategóriákat a tartalom megtekintésével számára kijelölt csoportok létrehozása. Például felnőtt tartalom előfordulhat, hogy küldeni egy külön csapat további ellenőrzésre.

Ez a szakasz ismerteti, hogyan alcsoportok létrehozása és gyorsan hozzárendelése az értékelések menet közben. Használhatja azonban [munkafolyamatok](workflows.md) hozzárendelni a megadott feltételek alapján felülvizsgálatok.

### <a name="create-a-subteam"></a>Hozzon létre egy alcsoportok

Nyissa meg a **alcsoportok** szakaszt, és kattintson a **alcsoportok hozzáadása**. A párbeszédpanelen adja meg a csapaton nevét, és kattintson a **mentése**.

![Alcsoportok neve](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Csapattársai meghívása

Nem rendelhet valaki a csapaton, ha azok még nem az alapértelmezett csapat tagja így felülvizsgálók az alapértelmezett csapatának először hozzá kell. Kattintson a **meghívása** a a **csapat** fülre.

![Felhasználó meghívása](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Rendelje hozzá a subteam csapattársai

Kattintson a **tag hozzáadása** alapértelmezett csapata tagok hozzárendelése egy vagy több alcsoportok gombra. A meglévő felhasználók csak a csapaton adhat hozzá. Új felhasználók, akik nem tagjai a felülvizsgálati eszköz hozzáadásához, felkínáljuk számukra a Team-beállítások lapon a "Meghívás" gomb használatával.

![Alcsoportok tagok hozzárendelése](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Alcsoportok felülvizsgálatok hozzárendelése

Létrehozta az alcsoportok, és a hozzárendelt tagok, elkezdheti tartalom hozzárendelése [felülvizsgálatok](../review-api.md#reviews) , ezek alcsoportok. Az ebben az esetben a **felülvizsgálati** lapot a hely.
Tartalom hozzárendelése a csapaton, kattintson a jobb felső sarokban, válassza a három pontot **áthelyezése**, és válassza ki a csapaton.

![Kép felülvizsgálat subteam hozzárendelése](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alcsoportok közötti váltás

Ha egynél több csapaton tagja, mely tartalom felülvizsgálatok jelennek meg módosítani ezeket alcsoportok között válthat. Az a **felülvizsgálati** lapra, válassza ki a legördülő menü feliratú **alapértelmezett** , és válassza ki **alcsoportok kiválasztása**. Megtekintheti, hogy a tartalom felülvizsgálatának különböző alcsoportok, de csak azok, amelyeknek a tagja.

![Alcsoportok közötti váltás](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Címkék

A **címkék** lapon meghatározhatja a két alapértelmezett moderálás címkék mellett egyéni moderálás címkék&mdash;**isadult** (**egy**) és **isracy**  (**r**). Amikor létrehoz egy egyéni címkével, elérhetővé válik az értékelések mellett az alapértelmezett címkéket. Melyik címkék jelennek meg az értékelések azok oszlopláthatósági beállítások között módosíthatja.

![A címkék megtekintéséhez, többek között a "Látható-e" jelölőnégyzet](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Egyéni címkék létrehozása

Új címke létrehozásához kell megadnia egy kódot rövid, nevet és leírást a megfelelő mezőben.

- **Rövid kód**: Adja meg a címke kétbetűs kódját. Példa: **cb**
- **Név**: Adjon meg egy rövid és informatív címkenév kisbetűs szóközök nélkül. Példa: **isbullying**.
- **Leírás**: (nem kötelező) adjon meg egy leírást, hogy milyen típusú tartalmat, amely a címke célokat. Példa: **Ábrázolása vagy a kibertámadások bullying példányai**.

Kattintson a **Hozzáadás** adjon hozzá egy címkét, és kattintson a **mentése** Amikor végzett, a címkék létrehozása.

![Felülvizsgálati eszköz hozzon létre új címke párbeszédpanel](images/settings-3-tags.png)

### <a name="delete-tags"></a>Címkék törlése

Egyéni címkék mellett azok a címkék listája a bejegyzések a Kuka ikonra kattintva törölheti, de nem lehet törölni az alapértelmezett címkéket.

## <a name="connectors"></a>Összekötők

A **összekötők** lap lehetővé teszi az összekötők, amelyek szolgáltatásspecifikus beépülő modulok, amelyekkel képes feldolgozni különböző módon tartalom tartalom részeként kezelése [munkafolyamatok](../review-api.md#workflows).

Az alapértelmezett összekötő munkafolyamat létrehozásakor a Content Moderator összekötőt, amely a tartalmat, megjelölheti **felnőtt** vagy **pikáns**, keresse meg a vulgáris, és így tovább. Azonban használhat más összekötők, itt felsorolt, amennyiben rendelkezik a megfelelő szolgáltatások hitelesítő adatait (a Face API-összekötők használatához például kell lekérni egy [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview) előfizetési kulcs).

A [vizsgálóeszköz](./human-in-the-loop.md) magában foglalja az alábbi csatlakozók:

- Emotion API
- Face API
- PhotoDNA Cloud Service
- Szövegelemzések API

### <a name="add-a-connector"></a>Adjon hozzá egy összekötőt

Összekötő hozzáadása (és a tartalom elérhetővé teheti [munkafolyamatok](../review-api.md#workflows)), válassza ki a megfelelő **Connect** gombra. A következő párbeszédpanelen adja meg az előfizetési kulcs, hogy a szolgáltatás. Amikor elkészült, az új összekötő meg kell jelennie az oldal tetején.

![Content Moderator összekötők beállításai](images/settings-4-connectors.png)

## <a name="workflows"></a>Munkafolyamatok

A **munkafolyamatok** lapon kezelését teszi lehetővé a [munkafolyamatok](../review-api.md#workflows). Munkafolyamatok felhőalapú szűrők a tartalmat, és azok együttműködését összekötők különböző módon rendezheti a tartalom és a megfelelő műveleteket. Itt meghatározhatja, szerkesztheti, és tesztelje a munkafolyamatokat. Lásd: [definiálása és -felhasználási munkafolyamatok](Workflows.md) ehhez útmutatást.

![Content Moderator munkafolyamat-beállítások](images/settings-5-workflows.png)

## <a name="credentials"></a>Hitelesítő adatok

A **hitelesítő adatok** fülre a Content Moderator előfizetési kulcsot, mert a moderálás szolgáltatások elérését egy REST-hívást vagy ügyféloldali SDK-val kell gyors hozzáférést biztosít.

![A Content Moderator hitelesítő adatok](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Külső hitelesítő adatok használata a munkafolyamatokhoz

A [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com) kulcsot generál egy ingyenes próbaverzió az Azure Content Moderator szolgáltatások, amikor regisztrál, de beállíthatja, hogy egy meglévő kulcs az Azure-fiókjával. Ez ingyenes próbaverziós kulcsok szigorú használati korlátokkal rendelkeznek, ajánlott nagyméretű forgatókönyvek esetén ([díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Ha létrehozott egy [a Content Moderator erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) az Azure-ban keresse meg azt az Azure Portalon, és válassza a **kulcsok** panelen. Másolja a kulcsok egyikét.

![Content Moderator kulcsok az Azure Portalon](images/credentials-azure-portal-keys.PNG)

Az a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com)a **hitelesítő adatok** lépjen a **munkafolyamat-beállítások** ablaktáblán válassza **szerkesztése**, és illessze be a kulcsát a **Ocp-Apim-Subscription-Key** mező. Most munkafolyamatok, amelyek a moderálási API-k hívása az Azure hitelesítő adatokat fogja használni.

> [!NOTE]
> A másik két mezőt a **munkafolyamat-beállítások** ablaktábla időszak és a lemezkép listák készítéséhez egyéni vannak. Tekintse meg a [egyéni használati](../try-terms-list-api.md) vagy [egyéni lemezképek](../try-image-list-api.md) útmutatók ezekről.

### <a name="use-your-azure-account-with-the-review-apis"></a>Az Azure-fiók használata a felülvizsgálati API-k

A felülvizsgálati API-k az Azure-kulcs használata, kell beolvasni az erőforrás-azonosító. Nyissa meg az Azure Portalon, és válassza a Content Moderator erőforrás a **tulajdonságok** panelen. Másolja ki az erőforrás-azonosító értékét, és illessze be azt a **szerepel az engedélyezési listán erőforrás-azonosító(k)** mezőjét, a felülvizsgálati eszköz **hitelesítő adatok** fülre.

![Content Moderator erőforrás-azonosító az Azure Portalon](images/credentials-azure-portal-resourceid.PNG)

Az előfizetési kulcs megadott mindkét helyen, ha a próbaverzió kulcs, amely a felülvizsgálati eszköz fiókjához nem fogja használni, de továbbra is elérhető marad.

## <a name="next-steps"></a>További lépések

Kövesse a [felülvizsgálati eszköz rövid](../quick-start.md) a vizsgálóeszköz használatához a tartalom-jóváhagyás forgatókönyvekben.