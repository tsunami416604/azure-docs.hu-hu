---
title: "Az Azure Mobile Engagement hibaelhárítási útmutatója - elemzés"
description: "Az Azure Mobile Engagement Analytics, a figyelés, a szegmentálási és az irányítópult problémák elhárítása"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: e30c9ac0a8421ffcf4fc3e2548cfd7ac49701900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Az elemzés, a figyelés, a szegmentálási és az irányítópult problémák hibaelhárítási útmutató
A következők: lehetséges problémák merülhetnek fel a hogyan Azure Mobile Engagement az alkalmazások, eszközök és felhasználók adatokat gyűjt.

## <a name="missingdelayed-information"></a>Hiányzó/késleltetett információk
### <a name="issue"></a>Probléma
* Elemzés, Szegmentálás vagy irányítópulton megjelenő adatokat késleltetett.
* Hiányzik az figyelés információ.
* Hiányzik az elemzés, Szegmentálás vagy irányítópult információ.
* Szerezze meg a szegmentálási korlátozza.

### <a name="causes"></a>Okok
* Az elemzés API-hoz, a figyelő API, és szegmensek API használatával adatokat van-e a felhasználói felület hiányoznak az API-k segítségével látható.
* Ha az Azure Mobile Engagement SDK nem megfelelően integrálva van az alkalmazás ezután sem lesz az elemzés, szegmentálását, figyelés vagy irányítópultok információk megjelenítéséhez.
* Szegmensek nem lehet módosítani a létrehozásuk után, szegmensek csak olyan számítógépekre "Klónozott" (másolt) vagy "megsemmisül" (törölve). Szegmensek csak tartalmazhat 10 feltételeket.
* A legjobb módszer a figyelésből objektuminformáció egy vizsgálati eszköz beállítása, távolítsa el és/vagy a vizsgálati eszköz telepítse újra az alkalmazást.
* Információk 24 óránként frissül elemzés, Szegmentálás vagy irányítópultok.
* Új szegmensek található információk csak akkor jönnek létre, akkor is, ha a szegmens előző információin alapuló követő 24 órában nem jeleníthető meg.
* A felhasználói felületen analytics adatok szűrése megjeleníti az összes ilyenek például az alkalmazás verziójától függetlenül (pl. "Lefagy" név szerint szűrve jelennek meg az 1-es és 2-es verziójának az alkalmazásban).
* Az elemzés az adott időszakban, amelynek telefonhoz helytelenül állította be a dátum a felhasználó tudta jelenik meg a megfelelő időszakra vonatkozó a felhasználói eszközbeállítások, a dátumot alapul.
* Nincs a kiszolgálóoldali adatok naplóz, amikor vissza gombját használja a "teszt" a leküldéses értesítések, az adatokat a valódi leküldéses kampányokra csak naplózza.

## <a name="cant-locate-items-in-ui"></a>Nem található elemek a felhasználói felületen
### <a name="issue"></a>Probléma
* Nem hozható létre az egyes beépített alapuló szegmens, vagy egyéni alkalmazásadatok címke feltételek.
* Nem található egyes beépített, vagy egyéni alkalmazásadatok elemzés, figyelés vagy irányítópultok szempontok címkét.
* Az elemzés, figyelés, Szegmentálás vagy irányítópultok adatokat nem tudja értelmezni.

### <a name="causes"></a>Okok
* Néhány elemet a beépített és az app-info címkék csak leküldéses feltételként rendelkezésre állnak, de nem lehetnek felvett egy szegmens vagy látható elemzés, figyelés vagy irányítópult. 
* Beépített elemek és alkalmazás info címkék, amely egy szegmens nem adható hozzá akkor beállítási listája minden egyes megadott célcsoport-kezelési szegmens alapján célzó megegyező művelet végrehajtására.
* Tekintse meg a helyi menük az elemzés, figyelés, Szegmentálás és irányítópultok szakaszok Azure Mobile Engagement a felhasználói felület további segítséget és információt.

## <a name="crash-troubleshooting"></a>Összeomlás-hibaelhárítás
### <a name="issue"></a>Probléma
* Alkalmazás összeomlik elemzés, figyelés és az irányítópult jelenik meg.

### <a name="causes"></a>Okok
* Elhárítása alkalmazás összeomlik elemzés, figyelés vagy irányítópulton látható ellenőrizze, hogy az SDK korábbi verziói szolgáltatással kapcsolatos ismert problémák a kibocsátási megjegyzéseket.
* További hibaelhárításhoz alkalmazás-összeomlásokat hajtsa végre az esemény egy vizsgálati eszköz a telepített alkalmazásokkal rendelkező, keresse meg a az Eszközazonosítót az Azure Mobile Engagement felhasználói felület "Figyelője – események" szakaszában. Végezze el az eseményt, amely okozza az alkalmazás összeomlását, és keresse meg az Azure Mobile Engagement felhasználói felület "Figyelője – összeomlási" részében tájékozódhat. 

