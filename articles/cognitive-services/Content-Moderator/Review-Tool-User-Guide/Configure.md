---
title: A Véleményezés eszköz beállításainak konfigurálása – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: A Véleményezés eszközzel konfigurálhatja vagy lekérheti a csoport, a címkék, az összekötők, a munkafolyamatok és a tartalommoderátor hitelesítő adatait.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220126"
---
# <a name="configure-the-review-tool"></a>A felülvizsgálati eszköz konfigurálása

A [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) számos fontos funkcióval rendelkezik, amelyek et az irányítópult **Beállítások** menüjén keresztül érheti el.

![Tartalommoderátor- ellenőrzés is beállítások menüje](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Csapat- és segédcsapatok kezelése

A **Csapat** lapon kezelheti a csapatot és a&mdash;felhasználók csoportjait, akik értesítést kaphatnak bizonyos emberi [vélemények](../review-api.md#reviews) indításakor. Csak egy csapata lehet (amelyet akkor hoz létre, amikor regisztrál a Véleményezés eszközzel), de több alcsapatot is létrehozhat. A csoportadminisztrátor meghívhat tagokat, beállíthatja engedélyeiket, és különböző alcsoportokhoz rendelheti őket.

![Az eszközcsapat beállításainak áttekintése](images/settings-2-team.png)

Az alcsoportok akkor hasznosak, ha eszkalációs csoportokat vagy csoportokat hoz létre, amelyek meghatározott tartalomkategóriák áttekintésére szolgálnak. Előfordulhat például, hogy felnőtt tartalmat küld egy másik csapatnak további ellenőrzéscéljából.

Ez a szakasz bemutatja, hogyan hozhat létre alcsoportokat, és hogyan rendelhet gyorsan értékeléseket menet közben. [A Munkafolyamatok](workflows.md) segítségével azonban adott feltételek alapján rendelhet értékeléseket.

### <a name="create-a-subteam"></a>Alcsoport létrehozása

Nyissa meg a **Segédek szakaszt,** és kattintson **az Alcsoport hozzáadása gombra.** Írja be az alcsapat nevét a párbeszédpanelen, majd kattintson a **Mentés gombra.**

![Alcsapat neve](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Csapattársak meghívása

Nem rendelhet hozzá senkit alcsapathoz, ha még nem tagja az alapértelmezett csapatnak, ezért először ellenőrzőket kell hozzáadnia az alapértelmezett csapathoz. Kattintson a **Csapat** lapon a **Meghívás** gombra.

![Felhasználók meghívása](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Csapattársak hozzárendelése az alcsapathoz

A **Tag hozzáadása** gombra kattintva tagokat rendelhet az alapértelmezett csapatból egy vagy több alcsapathoz. Csak meglévő felhasználókat adhat hozzá egy alcsoporthoz. Ha olyan új felhasználókat vesz fel, akik nem szerepelnek a véleményező eszközben, hívja meg őket a Csapatbeállítások lap "Meghívás" gombjával.

![Alcsapattagok hozzárendelése](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Vélemények hozzárendelése segédcsapatokhoz

Miután létrehozta az alcsoportokat és a hozzárendelt tagokat, elkezdheti hozzárendelni a [tartalom-értékeléseket](../review-api.md#reviews) ezekhez az alcsoportokhoz. Ez a webhely **Véleményezés** lapjáról történik.
Ha tartalmat szeretne hozzárendelni egy alcsapathoz, kattintson a jobb felső sarokban lévő három pontra, válassza az **Áthelyezés**ide lehetőséget, és jelöljön ki egy alcsapatot.

![Képellenőrzés hozzárendelése az alcsoporthoz](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Váltás a segédcsapatok között

Ha egynél több alcsapat tagja, válthat az alcsoportok között, hogy megváltoztassa, mely tartalomértékelések jelenjenek meg Az Ön számára. A **Véleményezés** lapon válassza az **Alapértelmezett** feliratú legördülő menüt, és válassza **az Alcsoport kiválasztása lehetőséget.** Megtekintheti a különböző alcsoportok tartalom-értékeléseit, de csak azokat, amelyeknek a tagja.

![Váltás a segédcsapatok között](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Címkék

A **Címkék** lapon egyéni moderálási címkéket&mdash;**isfelnőtt** **(** a ) és **isracy** (**r**) határozhatja meg. Amikor létrehoz egy egyéni címkét, az az alapértelmezett címkék mellett az értékelésekben is elérhetővé válik. A láthatósági beállítások módosításával módosíthatja, hogy mely címkék jelenjenek meg az értékelésekben.

![Címkék nézet, beleértve a "Látható" jelölőnégyzeteket](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Egyéni címkék létrehozása

Új címke létrehozásához meg kell adnia egy rövid kódot, nevet és leírást a megfelelő mezőkben.

- **Rövid kód:** Adjon meg egy kétbetűs kódot a címkéhez. Példa: **cb**
- **Név**: Írjon be egy rövid és leíró címkenevet kisbetűs szóközök nélkül. Példa: **isbullying**.
- **Leírás**: (nem kötelező) Adja meg a címke által megcélozott tartalom típusának leírását. Példa: **Az internetes zaklatás ábrázolásai vagy esetei.**

A Címke hozzáadásához kattintson a **Hozzáadás** gombra, majd a Címkék létrehozása után kattintson a **Mentés** gombra.

![Ellenőrző eszköz Új címke párbeszédpanel létrehozása](images/settings-3-tags.png)

### <a name="delete-tags"></a>Címkék törlése

Az egyéni címkéktörléséhez jelölje ki a Címkék lista bejegyzései melletti kuka ikont, de az alapértelmezett címkéket nem.

## <a name="connectors"></a>Összekötők

Az **Összekötők** lapon kezelheti az összekötőket, amelyek szolgáltatásspecifikus bővítmények, amelyek a [tartalom-munkafolyamatok](../review-api.md#workflows)részeként különböző módon képesek feldolgozni a tartalmat.

Munkafolyamat létrehozásakor az alapértelmezett összekötő a Tartalommoderátor-összekötő, amely a tartalmat **felnőttként** vagy **pikánsként**jelölheti meg, káromkodásként kereshet, és így tovább. Azonban használhatja az itt felsorolt egyéb összekötőket, feltéve, hogy rendelkezik hitelesítő adatokkal a megfelelő szolgáltatásokhoz (például a Face-összekötő használatához [face-előfizetési](https://docs.microsoft.com/azure/cognitive-services/face/overview) kulcsot kell beszereznie).

A [Felülvizsgálati eszköz](./human-in-the-loop.md) a következő összekötőket tartalmazza:

- Érzelemfelismerés
- Arcfelismerés
- PhotoDNA felhőszolgáltatás
- Szövegelemzés

### <a name="add-a-connector"></a>Összekötő hozzáadása

Összekötő hozzáadásához (és a [tartalom-munkafolyamatokban](../review-api.md#workflows)való használatra való elérhetővé) válassza ki a megfelelő **Csatlakozás** gombot. A következő párbeszédpanelen adja meg az adott szolgáltatás hoz tartozó előfizetési kulcsot. Ha elkészült, az új összekötőnek a lap tetején kell megjelennie.

![Tartalommoderátor-összekötők beállításai](images/settings-4-connectors.png)

## <a name="workflows"></a>Munkafolyamatok

A **munkafolyamatok** lapon kezelheti a [munkafolyamatokat.](../review-api.md#workflows) A munkafolyamatok a tartalom felhőalapú szűrői, és összekötőkkel együttműködve különböző módokon rendezik a tartalmat, és megteszik a megfelelő műveleteket. Itt definiálhatja, szerkesztheti és tesztelheti a munkafolyamatokat. Ennek módjáról a [Munkafolyamatok definiálása és használata](Workflows.md) című témakörben talál útmutatást.

![Tartalommoderátor munkafolyamat-beállításai](images/settings-5-workflows.png)

## <a name="credentials"></a>Hitelesítő adatok

A **Hitelesítő adatok** lap gyors hozzáférést biztosít a tartalommoderátor előfizetési kulcsához, amelyet a MODErálási szolgáltatások bármelyikéhez el kell érnie egy REST-hívásból vagy az ügyfél SDK-ból.

![Tartalommoderátor hitelesítő adatai](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Külső hitelesítő adatok használata munkafolyamatokhoz

A [Felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) egy ingyenes próbakulcsot hoz létre az Azure Content Moderator szolgáltatásokhoz, amikor regisztrál, de beállíthatja azt is, hogy egy meglévő kulcsot használjon az Azure-fiókjából. Ez nagyméretű esetekben ajánlott, mivel az ingyenes próbakulcsok szigorú használati korlátozásokkal ( árképzés és korlátozások )[rendelkeznek.](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Ha létrehozott egy [tartalommoderátor-erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) az Azure-ban, keresse meg azt az Azure Portalon, és válassza ki a **Keys** panelt. Másolja az egyik kulcsát.

![Tartalommoderátor-kulcsok az Azure Portalon](images/credentials-azure-portal-keys.PNG)

A [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) **Hitelesítő adatai** lapján nyissa meg a **Munkafolyamat-beállítások** ablaktáblát, válassza a **Szerkesztés**lehetőséget, és illessze be a kulcsot az **Ocp-Apim-Subscription-Key** mezőbe. Most a moderálási API-kat hívó munkafolyamatok az Azure-hitelesítő adatokat fogják használni.

> [!NOTE]
> A **Munkafolyamat-beállítások** ablaktábla másik két mezője egyéni kifejezés- és képlistákra szolgál. Ezekről az [Egyéni kifejezésekben](../try-terms-list-api.md) vagy az [Egyéni képek](../try-image-list-api.md) útmutatókban olvashat.

### <a name="use-your-azure-account-with-the-review-apis"></a>Az Azure-fiók használata az ellenőrzési API-kkal

Az Azure-kulcs használata a felülvizsgálati API-k, be kell olvasnia az erőforrás-azonosítót. Nyissa meg a tartalommoderátor-erőforrást az Azure Portalon, és válassza ki a **Tulajdonságok** panelt. Másolja az Erőforrás-azonosító értékét, és illessze be a Véleményezés eszköz **Hitelesítő adatai** lapjának **Engedélyezési erőforrás-azonosító(k)** mezőjébe.

![Tartalommoderátor erőforrásazonosítója az Azure Portalon](images/credentials-azure-portal-resourceid.PNG)

Ha mindkét helyen megadta az előfizetési kulcsot, a véleményezési eszközfiókhoz kapó próbakulcs nem lesz használva, hanem elérhető marad.

## <a name="next-steps"></a>További lépések

A [Véleményezés eszköz rövid útmutatójában](../quick-start.md) elkezdheti használni a Véleményezés eszközt a tartalommoderálási forgatókönyvekben.