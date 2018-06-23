---
title: Konfigurálja a tartalom moderátor az eszköz beállítások áttekintése |} Microsoft Docs
description: Konfiguráljon, vagy a csoport, a címkék, a összekötők, a munkafolyamatok és a hitelesítő adatok beszerzése.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346955"
---
# <a name="about-review-tool-settings"></a>Felülvizsgálati eszköz beállításairól #

Használja a beállítások lapon a felülvizsgálati eszköz irányítópulton, akkor is könnyen határozza meg, és számos összetevőből módosítása.

![Tartalom moderátor beállítások áttekintése](images/settings-1.png)

## <a name="team-and-subteams"></a>Csoport és Subteams ## 

A csapat és subteams kezelése ezen a lapon. Csak egy csoportot tartozhat, azonban úgy is [hozzon létre több subteams](subteams.md) és meghívókat jövőbeli tagjai. Miután elküldött kimenő küldték el, figyelheti azokat, a csapattagok engedélyeinek módosítása, és további felhasználók meghívásához. Miután a csoport tagjai fogadták el a meghívást, ezekhez a tagokhoz rendelhet különböző subteams. Beállíthatja, hogy a rendszergazdák vagy a felülvizsgálók csoport tagjai szerepkörök: rendszergazdák más felhasználók is meghívhatja, amíg felülvizsgálók nem.

![Moderátor csoport beállításai](images/settings-2-team.png)

## <a name="tags"></a>Címkék ##

Ahol lehetséges [egyéni címkék megadása](tags.md) írja be a rövid kódot, nevét és leírását, a címkék. Miután létrehozta, azt, érhető el ellenőrzések során. Eltérő címkéket használható különböző értékelést által, hogy ki-és bekapcsolása látható.

![Tartalom moderátor címkék beállításai](images/settings-3-tags.png)

## <a name="connectors"></a>Összekötők ##

Munkafolyamatok funkciókkal kommunikálni a felülvizsgálati eszköz összekötők használatával. A felülvizsgálati eszköz meghívja a tartalom moderátor API-k, az alapértelmezett munkafolyamat moderálás a tartalom. Amikor regisztrál a felülvizsgálati eszközt, az automatikus-rendelkezések moderátor API hitelesítő adatai meg. Azt is támogatja a integrálása más összekötő API-k, mindaddig, amíg az összekötő érhető el. Jelenleg néhány összekötők elérhetővé tett kívül a mezőbe.

A [összekötők lapon](connectors.md) összekötők kezelhetik. Adja hozzá vagy törölje az összekötőt, és az Előfizetés-kulcs megkeresése az adott összekötőt. Kattintson a kapcsolódás hozzá az egyéni munkafolyamatokat. 

![Moderátor összekötők beállításai](images/settings-4-connectors.png)

## <a name="workflows"></a>Munkafolyamatok ##

Munkafolyamatok kezelése a munkafolyamatok fülre. Munkafolyamatok tesztelheti egy fájl feltöltésével. Emellett [határozza meg az egyéni munkafolyamatokat](workflows.md) lemezkép és a szöveg a API összekötőket (az összekötők lapon található) használatával. 

![Moderátor munkafolyamat beállításai](images/settings-5-workflows.png)

## <a name="credentials"></a>Hitelesítő adatok ##

Ezen a lapon a tartalom moderátor előfizetés kulcs, amely tartalmazza az API-k használata tartalom moderátor (kép moderálás, szöveges moderálás, listájának kezelése, a munkafolyamat és felülvizsgálati API-k) kell gyors hozzáférést biztosít.
 
![Tartalom moderátor hitelesítő adatok](images/settings-6-credentials.png)
