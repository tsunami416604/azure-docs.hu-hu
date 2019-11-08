---
title: Mi a Tesztverzió? | Azure piactér
description: A Marketplace Test Drive funkció magyarázata
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a6826657a4af74276e363acd585e80d101cc1459
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828143"
---
<a name="what-is-test-drive"></a>Mi a Tesztverzió?
===================

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfeleknek, mivel lehetővé teszi számukra, hogy a\'megvásárlása előtt \'próbálkozzon, ami nagyobb átalakítást és magas minősítésű érdeklődők létrehozását eredményezi.

A kapcsolattartási adatok megadása után az ügyfelek hozzáférhetnek az előre elkészített tesztelési élményhez: a termék gyakorlati, saját irányítású próbaverziója\'s legfontosabb funkciói, valamint a valós megvalósítási forgatókönyvben bemutatott előnyök.

A test Drive lehetővé teszi a termék életbe lépését, és a folyamat során kiválóan minősített érdeklődőket hozhat.

<a name="how-does-a-test-drive-work"></a>Hogyan működik a test Drive?
---------------------------

Egy potenciális ügyfél felfedi az alkalmazást a piactéren, bejelentkezik, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezetet, hogy rögzített számú órát próbáljon megállapítani, miközben nagy teljesítményű vezetőt kap a követéshez.

![Első lépés. Marketplace-ajánlat megjelenítve](./media/what-is-test-drive/step1.png)

![Második lépés. Piactéri ajánlat bejelentkezési képernyője](./media/what-is-test-drive/step1andahalf.png)

![Harmadik lépés. Marketplace ajánlat kiadói szerződés képernyője](./media/what-is-test-drive/step2.png)

![Negyedik lépés Tesztelési meghajtó beállítása képernyő](./media/what-is-test-drive/step3.png)

Alább látható egy példa arra, hogyan néz ki egy ajánlat, amikor az üzembe helyezés ideje szükséges:

![Marketplace-ajánlat üzembe helyezése](./media/what-is-test-drive/step4.png)

![Marketplace Test Drive – kész képernyő](./media/what-is-test-drive/step5.png)

![Piactér Test Drive – teljes képernyő](./media/what-is-test-drive/step6.png)

Függetlenül attól, hogy mennyire összetett az alkalmazása, a Microsoft Test Drive segít a termék életbe juttatásában az ügyfél számára. Jelenleg három különböző típusú tesztelési meghajtót kínálunk a termék, a forgatókönyv és a piactér típusától függően.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)** : a Azure Resource Manager Test Drive egy központi telepítési sablon, amely tartalmazza az összes olyan Azure-erőforrást, amely a közzétevő által létrehozott megoldást tartalmaz. A forgatókönyvnek megfelelő termékek csak az Azure-erőforrásokat használják.
- **[Logikai alkalmazás](./logic-app-test-drive.md)** : a Logic app Test Drive egy központi telepítési sablon, amely az összes összetett megoldási architektúrát magában foglalja. Az összes Dynamics-alkalmazásnak vagy egyéni terméknek ilyen típusú tesztelési meghajtót kell használnia.
- **Power bi**: a Power bi Test Drive egyszerűen egy egyéni beépített irányítópultra mutató beágyazott hivatkozás. Minden olyan termék, amely egy interaktív Power BI vizualizációt szeretne bemutatni, ezt a típusú tesztelési meghajtót kell használnia.
    Itt a beágyazott Power BI URL-címére kell feltöltenie.

<a name="what-goes-on-in-the-background"></a>Mi kerül a háttérben?
-------------------------------

A test Drive szolgáltatás úgy van felépítve, hogy folyamatosan támogassa és szolgálja ügyfelei segítségét anélkül, hogy manuális beavatkozásra lenne szükség. Közzétevőként a feladata, hogy felügyelje és konfigurálja a tesztvezetés beállításait a theCloud partner portálról, majd ezt a beállítást közvetlenül az ügyfelei számára is elérhetővé teszi.

Ennek az az oka, hogy a tesztelési meghajtó konfigurációjának beállítása után mindegyik tesztvezetés olyan felügyelt példány lesz, amely igény szerint lesz telepítve az ügyfél számára. Miután hozzárendelte a test Drive-példányt, a rendszer elérhetővé teszi a tesztelési meghajtót a beállított időtartamra, majd törli, hogy helyet hozzon létre egy másik ügyfél számára.

<a name="next-steps"></a>További lépések
----------

Most, hogy már tudja, mi az a tesztvezetés, látogasson el arra a konkrét tesztvezetés-típusra, amelyet közzé szeretne tenni, és ismerkedjen meg a szükséges mezőkkel.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Logikai alkalmazás](./logic-app-test-drive.md)**

Ha további kérdései vannak, hibaelhárítási tanácsokat talál, vagy ha szeretné, hogy a tesztvezetés sikeres legyen, tekintse meg a [Gyakori kérdések, hibaelhárítás és & ajánlott eljárásokat](./marketing-and-best-practices.md).
