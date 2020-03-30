---
title: Mi a Tesztverzió? | Azure Piactér
description: A Piactér tesztmeghajtó funkciójának magyarázata
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bdfadf359195464c4024b28c5e597c571305481a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278229"
---
<a name="what-is-test-drive"></a>Mi a Tesztverzió?
===================

Test Drive egy nagyszerű módja annak, hogy bemutassa az \'ajánlatot a\'potenciális ügyfelek azáltal, hogy nekik a lehetőséget, hogy próbálja meg, mielőtt vásárolni , ami a megnövekedett átalakítás és a generációs magasan képzett vezet.

Az elérhetőségi adataik megadása után az ügyfelek hozzáférhetnek az Előre elkészített Test Drive-élményhez: a termék\'legfontosabb funkcióinak gyakorlati, saját irányítású kipróbálásához, és a valós megvalósítási forgatókönyv ben is bemutatható előnyök.

Test Drive lehetővé teszi, hogy a termék életre, és létrehoz magasan képzett vezet a folyamatban.

<a name="how-does-a-test-drive-work"></a>Hogyan működik a Test Drive?
---------------------------

Egy potenciális ügyfél felderíti az alkalmazást a Marketplace-en, bejelentkezik, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezetet, hogy megpróbálja a meghatározott számú órát, miközben ön kap egy magasan képzett érdeklődőt, hogy kövesse nyomon.

![Első lépés. Marketplace-ajánlat jelenik meg](./media/what-is-test-drive/step1.png)

![Második lépés. Marketplace ajánlat bejelentkezési képernyő](./media/what-is-test-drive/step1andahalf.png)

![Harmadik lépés. Marketplace-ajánlat közzétevői szerződésének képernyője](./media/what-is-test-drive/step2.png)

![Negyedik lépés. A Tesztmeghajtó beállítási képernyője](./media/what-is-test-drive/step3.png)

Az alábbi példa pedig bemutatja, hogyan néz ki egy ajánlat, amikor időre van szüksége az üzembe helyezéshez:

![A Marketplace-ajánlat üzembe helyezése](./media/what-is-test-drive/step4.png)

![Marketplace Test Drive kész képernyő](./media/what-is-test-drive/step5.png)

![Marketplace Test Drive teljes képernyő](./media/what-is-test-drive/step6.png)

Függetlenül attól, hogy milyen összetett az alkalmazás, a Microsoft Test Drive segít életre a terméket az ügyfél számára. Ma három különböző típusú tesztmeghajtót kínálunk a termék, a forgatókönyv és a piactér típusa alapján.

- **[Azure Resource Manager:](./azure-resource-manager-test-drive.md)** Az Azure Resource Manager testdrive egy központi telepítési sablon, amely tartalmazza az összes Azure-erőforrások, amelyek tartalmazzák a megoldás a kiadó által készített. A forgatókönyvnek megfelelő termékek csak Azure-erőforrásokat használnak.
- **[Logic App](./logic-app-test-drive.md)**: A Logic App Test Drive egy központi telepítési sablon, amely célja, hogy magában foglalja az összes összetett megoldás architektúrák. Minden Dynamics-alkalmazásnak vagy egyéni terméknek ilyen típusú tesztmeghajtót kell használnia.
- **Power BI**: A Power BI testdrive egyszerűen egy beágyazott hivatkozás egy egyéni tervezésű irányítópultra. Minden olyan terméknek, amely csak egy interaktív Power BI-vizualizációt szeretne bemutatni, az ilyen típusú tesztvezetést kell használnia.
    Itt mindössze a beágyazott Power BI URL-címét kell feltöltenie.

<a name="what-goes-on-in-the-background"></a>Mi folyik a háttérben?
-------------------------------

A Test Drive szolgáltatás úgy készült, hogy folyamatosan támogassa és kiszolgálja ügyfeleit anélkül, hogy bármilyen manuális erőfeszítést igényelne Öntől. Kiadóként a Tesztmeghajtó beállításainak kezelése és konfigurálása a Cloud Partner Portal on, majd ez a beállítás közvetlenül elérhető lesz az ügyfelek számára.

Ennek az az oka, hogy a tesztmeghajtó konfigurációinak beállítása után minden tesztmeghajtó felügyelt példánysá válik, amelyet az azt kérő ügyfél igény szerint telepít. A Test Drive-példány hozzárendelése után a Tesztmeghajtó a beállított ideig használható, majd törlődik, hogy helyet hozzon létre egy másik ügyfél számára.

<a name="next-steps"></a>További lépések
----------

Most, hogy már tudja, miről szól a Tesztmeghajtó, látogasson el az adott Tesztmeghajtó-típusra, amelyet közzé szeretne tenni, hogy mindent megtudjon a szükséges mezőkről.

- **[Azure erőforrás-kezelő](./azure-resource-manager-test-drive.md)**
- **[Logikai alkalmazás](./logic-app-test-drive.md)**

Ha további kérdései vannak, hibaelhárítási tanácsokat keres, vagy szeretné sikeresebbé tenni a Tesztmeghajtót, olvassa el a [GYIK, hibaelhárítás & gyakorlati tanácsok című témakört.](./marketing-and-best-practices.md)
