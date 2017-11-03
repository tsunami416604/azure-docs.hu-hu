---
title: "Az adatszolgáltatás-ajánlat a piactér tesztelése |} Microsoft Docs"
description: "Megtudhatja, hogyan tesztelheti az adatszolgáltatás-ajánlat a Azure piactérről."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>Az adatszolgáltatás ajánlatot átmeneti tesztelése
> [!IMPORTANT]
> **Jelenleg dolgozunk már nem bevezetési bármely új adatszolgáltatás közzétevők. Új dataservices nem get jóváhagyott listaelem.** Ha egy SaaS-üzleti AppSource a közzétenni kívánt alkalmazás további információt talál [Itt](https://appsource.microsoft.com/partners). Ha egy infrastruktúra-szolgáltatási alkalmazások vagy fejlesztői szolgáltatás szeretne közzétenni az Azure piactérről, további információt talál [Itt](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Az első két lépések végrehajtását követően [a Microsoft Developer-fiók létrehozása](marketplace-publishing-accounts-creation-registration.md) és [a szolgáltatás-ajánlatot létre közzétételi portálon](marketplace-publishing-data-service-creation.md) , hogy készen áll az ajánlatot az Azure-ban elérhetővé tétele Piactér. Ez a témakör végigvezeti az első, köztes, a "Tesztelés" nevű lépés

Átmeneti azt jelenti, hogy a "védőfal", ahol tesztelése és funkciókat ellenőrzése előtt az üzemi környezetben privát ajánlatát telepítése. Az ajánlat volna egy felhasználói, akik már telepítették az átmeneti fog megjelenni.

## <a name="step-1-pushing-your-offer-to-staging"></a>1. lépés A lefokozásra szolgáló átmeneti küldését
Kérdez le az átmeneti tesznek lehetővé teszi, hogy az ajánlat tesztelni, mielőtt jövőbeli előfizetők számára elérhetővé válik.  Láthatja, hogyan ajánlatát fog jelenik meg, és azok az adatok előfizetés működik.  

  ![rajz](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. Bejelentkezés a a [Portal közzététele](https://publish.windowsazure.com)
2. Válassza ki **adatszolgáltatások** a bal oldali navigációs ablakban
3. Válassza ki az átmeneti leküldése kívánt ajánlatot. A fenti képernyő jelenik meg.
4. Kattintson a **leküldéses átmeneti** gombra.  
5. Az ajánlat, amelyek végrehajtása előtt kérdez le, hogy átmeneti problémák vannak, ha megjelenik egy lista jelenik meg.  Javítsa ki ezeket az elemeket a lista összes elemére kattintva. Amikor végzett, az összes korrekció kattintson **átmeneti leküldése** újra gombra.

Ha nincsenek a ajánlat problémák látni fogja az alábbi előugró ablak.  

Ha még nem tervezési/nem jóvá az Azure portálon ajánlatot illesztésének (jelenleg korlátozott kapacitás), majd bezárja az előugró ablak.

A szolgáltatás teszteléséhez az Azure portálon (mellett a DataMarket portál), szüksége lesz egy Azure-előfizetési Azonosítót tesztelni.  Az előfizetés-azonosító azonosítja a fiókot, amelyet a rendszer engedélyezi-e az ajánlatot tesztelése.  

Kivágás, és illessze be az előfizetés-Azonosítóval, és kattintson a pipa jelre a folytatáshoz.

  ![rajz](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> Ezek az Azure-előfizetések azonosítói csak tesztelési és átmeneti előkészítő a szükséges a [Azure felügyeleti portálon](https://manage.windowsazure.com). Ezek nem szükségesek tesztelése az Azure piactéren.
> 
> 

A következő képernyőn megjelenő mutat be, hogy a közzététel van folyamatban a "folyamatban" ikonra megjelenítésével jelzi ezt az alábbi sárga kiemelve. Kérdez le, hogy átmeneti közötti 10 – 15 percet vesz igénybe.  Ha hosszabb ideig tart, először frissítse a böngésző (nyomja le az F5 IE-ben).  Bizonyos ritkán előforduló esetekben, ahol az ajánlatot továbbra is küld egy óra múlva átmeneti, kattintson a lépjen kapcsolatba velünk csatolása ossza meg velünk, hogy probléma van.

  ![rajz](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

A "folyamatban" ikonra a leküldéses átmeneti befejeződésekor stop áthelyezése és az állapot lesz frissíti "Előkészített".  Most már készen áll az ajánlatot teszteléséhez.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>2. lépés Az előkészített ajánlatot DataMarket tesztelése
A szöveg a következő hivatkozásra kattintva **"tekintse meg a szolgáltatás következő kínálnak..."** megjelenítéséhez a képernyőn, hogy az előfizető megjelenik, amikor az ajánlat éles kerül, és DataMarket fog megjelenni.

  ![rajz](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Tesztelje, vagy ellenőrizze a 12 elemeket megjelölve, fent győződjön meg arról, minden emblémák, árak/tranzakciók, szöveg, képek, dokumentáció, és hivatkozások helyességét, és működik megfelelően.  Ez az egy időben teszt értékeket az ajánlat létrehozásakor megadott helyett tényleges értékeket biztosításához.

1. Az ajánlat embléma
2. Az ajánlat neve
3. Közzétevő neve/hivatkozásra a vállalati webhely
4. Az ajánlat kategóriák keresése
5. Az ajánlat támogatási hivatkozás előfizetők segít
6. Környezetfüggő leírást az előfizetéshez
7. Az ajánlat terv végzett ügyfélellenőrzések számlázási részletek
8. Végrehajtási kód csatolása
9. Minta képek, mely ajánlat adatok használata
10. Az ajánlat belül minden egyes szolgáltatás bemeneti/kimeneti metaadatok
11. Ajánlat használati feltételek
12. Az ajánlat adatok megtekintése

Végül ellenőrizze a szolgáltatás "MEGISMERKEDHET a DATASET" hivatkozásra kattintva a Datamarket keresztül fog működni.  Egy új ablakban nyílik meg az eszköz a közvetlen telepítésnek "Szolgáltatás Explorer", egy lekérdezés eredményét megtekintheti a szolgáltatásra.  Ebben az ablakban adja meg a szükséges paramétereket, és tekintse meg a szolgáltatás egy lekérdezés által megjelenített eredményekre.   Megjelenik a, az URL-címet a lekérdezésben.  

> [!NOTE]
> Győződjön meg arról, hogy tekintse át a szolgáltatás a lap tetején megjelenik a szöveges leírása.  És ha az ajánlatot több szolgáltatás hívja, kattintson a lap alján váltson át a következő szolgáltatás tekintse át és tesztelje a lappal.
> 
> 

## <a name="next-step"></a>Következő lépés
Ha problémát tapasztal, és segítségre van szüksége megoldása kérdéseivel forduljon [Azure közzétevő támogatási csoportjához](http://go.microsoft.com/fwlink/?LinkId=272975).

Ha-e meg, és készen állnak a közzététele az ajánlatot, olvassa el a [vonatkozó kérés jóváhagyása leküldéses üzemi](marketplace-publishing-push-to-production.md) dokumentációját.

## <a name="see-also"></a>Lásd még:
* [Első lépések: Hogyan ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

