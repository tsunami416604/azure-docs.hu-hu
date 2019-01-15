---
title: A Content Moderator felülvizsgálati eszköz beállításainak - Content Moderator konfigurálása
titlesuffix: Azure Cognitive Services
description: Állítsa be, vagy a csapat, címkék, összekötők, a munkafolyamatok és hitelesítő adatok lekérése.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8717eb58538889e99938164f97f10c010f87d6a5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262948"
---
# <a name="configure-review-tool-settings"></a>Felülvizsgálati eszköz beállításainak konfigurálása

Használja a beállítások lapon a felülvizsgálati eszköz irányítópultján, könnyebbé vált a megadásához, és számos összetevőből módosítása.

![Content Moderator felülvizsgálati beállítások](images/settings-1.png)

## <a name="team-and-subteams"></a>Csoportok és alcsoportok

A csoportok és alcsoportok ezen a lapon lehet kezelni. Csak egy csapat rendelkezhet, de is [hozzon létre több alcsoportok](subteams.md) és a jövőbeli tagjai meghívók küldése. Miután elküldött meghívók ki, megfigyelheti őket, a csapattagok engedélyeinek módosítása, és további felhasználókat meghívni. Miután a csoport tagjai elfogadta a meghívást, ezekhez a tagokhoz rendelhet eltérő alcsoportok. Beállíthatja, hogy a rendszergazdák vagy a felülvizsgálók csapat tagjai szerepkörök: a rendszergazdák meghívhatnak más felhasználókat, míg a felülvizsgálók nem.

![Content Moderator Team-beállítások](images/settings-2-team.png)

## <a name="tags"></a>Címkék

Ez az, ahol [egyéni címkék megadása](tags.md) rövid kódja, nevét és leírását a címkék megadásával. Miután létrehozta, azt, érhető el értékelések során. Eltérő címkéket használható különböző felülvizsgálatok látható-e ki-és bekapcsolása bekapcsolásával.

![A Content Moderator címkék beállításai](images/settings-3-tags.png)

## <a name="connectors"></a>Összekötők

A munkafolyamatok bővítik a felülvizsgálati eszköz kommunikálni összekötők használatával. A felülvizsgálati eszköz szükségessé teszi a tartalom Moderator API-k, az alapértelmezett munkafolyamat moderálása tartalom. A felülvizsgálati eszköz a regisztráláskor, auto-rendelkezések a Moderator API hitelesítő adatokat az Ön számára. Ezenkívül támogatja integrálása más összekötő API-k, mindaddig, amíg az összekötő érhető el. Elérhetővé vált néhány összekötők beépített.

A [összekötők lap](connectors.md) van, amellyel kezelheti az összekötők. Adjon hozzá vagy összekötőkkel törölni, és az előfizetési kulcs keresése egy adott összekötőt. Csatlakozás gombra kattintva adja hozzá ezeket az egyéni munkafolyamatokat. 

![Content Moderator összekötők beállításai](images/settings-4-connectors.png)

## <a name="workflows"></a>Munkafolyamatok

Munkafolyamatok kezelése a munkafolyamatok lapról. A munkafolyamatok, ha feltölt egy mintafájlt tesztelheti. Emellett [meghatározása egyéni munkafolyamatokat](workflows.md) lemezkép és szöveg (az összekötők lapon található) elérhető API-összekötők használatával. 

![Content Moderator munkafolyamat-beállítások](images/settings-5-workflows.png)

## <a name="credentials"></a>Hitelesítő adatok

Ezen a lapon a Content Moderator előfizetési kulcsot, mert szüksége lesz az API-k használata a Content Moderator (képek moderálása, Szövegmoderálás, kezeléssel, a munkafolyamat és felülvizsgálati API-k) gyors hozzáférést biztosít.
 
![A Content Moderator hitelesítő adatok](images/settings-6-credentials.png)
