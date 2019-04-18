---
title: Az Azure bejárati ajtajának Service Háttérkomponensei és a háttérkiszolgáló készletek |} A Microsoft Docs
description: Ez a cikk bemutatja, milyen háttérkomponensei és a háttérkiszolgáló-készletekre az előtérben ajtó konfiguráció.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879199"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Háttérrendszerek és a háttérkiszolgáló készletek Azure bejárati ajtajának Service-ben
Ez a cikk csatlakoztatásáról, az alkalmazástelepítés Azure bejárati ajtajának szolgáltatással kapcsolatos fogalmakat ismerteti. Ezen kívül ismerteti a különböző feltételek háttéralkalmazások bejárati ajtajának-konfigurációt.

## <a name="backends"></a>Háttérrendszerek
A háttérrendszernek megegyezik az alkalmazás központi telepítési példánya egy régióban. Bejárati ajtajának szolgáltatás támogatja az Azure és az Azure-háttérrendszerek esetében is, így a régió nem csak korlátozott Azure-régióban. Emellett lehet a helyszíni adatközpont vagy egy példányt egy másik felhőben.

Bejárati ajtajának szolgáltatást háttérkiszolgálókon tekintse meg a gazdagép neve vagy a nyilvános IP-címét a alkalmazást, amely is képes kiszolgálni az ügyfélkéréseket. Háttérrendszerek nem tévesztendő össze az adatbázisszint, tárolási szinten, és így tovább. Háttérrendszerek kell kezelni, az alkalmazás háttérrendszere nyilvános végpontjára. Ha egy háttérszolgáltatás bejárati ajtajának háttérkészlet ad hozzá, akkor is fel kell vennie a következő:

- **Háttérbeli gazdagéptípusokkal**. A hozzáadni kívánt erőforrás típusát. Bejárati ajtajának szolgáltatás automatikus felfedezés a háttéralkalmazások, ha az app Service-ben, a felhőalapú szolgáltatás vagy a storage támogatja. Ha azt szeretné, hogy egy másik erőforrás Azure-ban vagy még nem Azure-háttérrendszernek, válassza ki a **egyéni gazdagép**.

    >[!IMPORTANT]
    >Konfigurálása során API-k nem ellenőrzése, ha a háttérrendszer bejárati ajtajának környezetből nem érhető el. Győződjön meg arról, hogy bejárati ajtajának elérje a háttérrendszerhez.

- **Előfizetés és a háttérkiszolgáló állomásnév**. Ha nincs kiválasztva **egyéni gazdagép** háttérrendszer gazdagéptípusokkal, válassza ki a háttérrendszer a felhasználói felületen a megfelelő előfizetést, és a megfelelő háttér-gazdagép nevének kiválasztásával.

- **Háttérbeli állomásfejléc**. A küldött állomásfejléc-érték, a háttérkiszolgáló az egyes kérések. További információkért lásd: [háttérrendszer állomásfejléc](#hostheader).

- **Prioritás**. A különböző háttérrendszerekre prioritásokat lehet kiosztani, ha azt szeretné, a szolgáltatás elsődleges-háttéralkalmazás használatára az összes forgalom. Biztonsági másolatok továbbá adja meg, ha az elsődleges vagy a biztonsági mentési háttérrendszerek sem érhető el. További információkért lásd: [prioritású](front-door-routing-methods.md#priority).

- **Súly**. Rendelje hozzá a különböző háttérrendszerekre a forgalom elosztását-háttérrendszerek esetében több egyenletesen vagy megfelelően súly együttható súlyok. További információkért lásd: [súlyok](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Háttérbeli állomásfejléc

A háttérrendszernek bejárati ajtajának által továbbított kérések tartalmazza a gazdagép fejléc mezőt, a háttérrendszert használó a célként megadott erőforrás lekérése. Ez a mező értéke általában a háttérrendszer URI származik, és állomás és port.

Például a www kérelem\.contoso.com fog rendelkezni a gazdagép fejléc www\.contoso.com. Az Azure portal használatával a háttérbeli konfigurálása, ha az alapértelmezett Ez a mező értéke a háttérrendszer állomásneve. Contoso-westus.azurewebsites.net, az Azure Portalon, a háttér-e töltve háttérrendszer az állomásfejléc értéke lesz a contoso-westus.azurewebsites.net. Azonban használhatja az Azure Resource Manager-sablonok vagy valamilyen más módszerre van adva ez a mező, bejárati ajtajának szolgáltatás elküldi a bejövő állomásnév értékeként a gazdagép-fejléc. Ha a kérés érkezett a www\.contoso.com és a háttérbeli, amely rendelkezik egy üres fejlécmezőt contoso westus.azurewebsites.net, bejárati ajtajának szolgáltatás fogja az állomásfejléc állítja www\.contoso.com.

A legtöbb háttéralkalmazások (az Azure Web Apps, a Blob storage és a Cloud Services) az állomásfejlécnek egyeznie a háttérrendszer tartományát. Azonban a frontend gazdagép, amely a háttérbeli irányítja www például egy eltérő állomásnevet használ\.contoso.azurefd.net.

Ha a háttérrendszer igényel az állomásfejlécnek egyeznie kell a háttér-gazdagép nevével, ügyeljen arra, hogy a háttérrendszer állomásfejlécet a gazdagép neve háttérrendszert.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>A háttérrendszer a háttérrendszer állomásfejlécét konfigurálása

Konfigurálása a **háttérrendszer állomásfejléc** egy háttérrendszer a háttérbeli címkészlet szakaszban mezőjét:

1. Nyissa meg a bejárati ajtajának erőforrást, és válassza ki a háttérkészlet konfigurálása a háttérrendszerrel.

2. Ha még nem tette meg, vagy meglévő szerkesztésekor-integrációs háttérkomponensek felvétele.

3. A háttérrendszer gazdagép fejlécmezőt egyéni értékre beállítva, vagy hagyja üresen a mezőt. Az állomásnév a bejövő kérelem állomásfejléc-érték lesz.

## <a name="backend-pools"></a>Háttérkészletek
Háttérkészlet elöl ajtó szolgáltatás határozza meg, amelyek az alkalmazás hasonló forgalom fogadására. Más szóval, logikus csoportosításai app-példányon, amely ugyanazt a forgalmat, és az elvárt viselkedés válaszolhatnak világszerte. Ezek a háttérszolgáltatások vannak üzembe helyezve, eltérő régióban vagy ugyanazon a régión belül. Az összes háttérrendszerek aktív/aktív üzembe helyezési mód vagy mi számít, ha aktív/passzív konfigurációt is lehet.

Háttérkészlet határozza meg, hogyan a különböző háttérrendszereket kell kiértékelni, állapot-mintavételei keresztül. Azt is meghatározza, hogyan terheléselosztás megtörténik-e közöttük.

### <a name="health-probes"></a>Állapotminták
Bejárati ajtajának szolgáltatás a konfigurált háttérrendszerekre mindegyike HTTP/HTTPS-mintavétel rendszeresen kéréseket küld. Mintavételi kérések határozza meg, a közelség és az egyes háttérrendszerek betöltése állapotát a végfelhasználói kérések elosztása. A háttérkészlet állapot-mintavételi beállítások határozzák meg, hogyan tudjuk lekérdezni a háttéralkalmazások állapotát. A következő beállítások érhetők el a terheléselosztó konfigurációban:

- **Elérési út**. A mintavételi kérések a háttérkészletben háttéralkalmazásokat készíthet a használt URL-cím. Például, ha a háttérkiszolgálókon egyik contoso-westus.azurewebsites.net, és az elérési út /probe/test.aspx értékre van állítva, majd bejárati ajtajának Service-környezetek, feltéve, hogy a HTTP-re, a protokoll értéke küld állapot-mintavételi kérések http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokoll**. Határozza meg, hogy az állapot-mintavételi kérések küldése bejárati ajtajának szolgáltatásból a háttérrendszerekre HTTP vagy HTTPS protokollal.

- **Időköz (másodperc)**. Határozza meg az állapot-mintavételei gyakorisága a háttérrendszerek vagy az intervallumok mintavétel bejárati ajtajának környezeteket küld.

    >[!NOTE]
    >A gyorsabb feladatátvételi teszteket adjon meg alacsonyabb értéket beállítani az időközt. A kisebb az érték, annál egészségügyi mintavételi kötet a háttérrendszerekre kapnak. Például, ha a időköze 90 bejárati ajtajának környezetek vagy kapcsolódási 30 másodperces globálisan, az egyes háttérrendszerek kap kapcsolatos 3-5 mintavételi kérések száma másodpercenként.

További információkért lásd: [állapotadat-mintavételek](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Terheléselosztó beállításai
Terheléselosztási beállítások háttérkészlete számára határozzák meg, hogyan kiértékeljük állapot-mintavételei. Ezek a beállítások határozzák meg, ha a háttérrendszer állapota megfelelő-e. Emellett ellenőrizze hogyan terheléselosztás forgalmat a háttérkészlet különböző háttérrendszereket között. A következő beállítások érhetők el a terheléselosztó konfigurációban:

- **Minta mérete**. Azonosítja az állapot-mintavételei hány minták kell figyelembe venni háttérrendszer állapotának kiértékelését.

- **A sikeres mintanagyság**. A mintanagyság mint már említettük, kifogástalan állapotú a háttéralkalmazás hívásához szükséges sikeres minták számát határozza meg. Tegyük fel például, hogy egy bejárati ajtajának állapot-mintavételi időköz érték 30 másodperc, a minta mérete 5 másodperc és a sikeres minta mérete 3 másodperc. Minden alkalommal, amikor az egészségügyi kiértékeljük a háttérbeli mintavételei, nézzük meg azt az utolsó öt minták több mint 150 másodpercig (5 x 30). Legalább három sikeres mintavételek szükséges deklarálja a háttérrendszer állapota kifogástalan.

- **Késés érzékenységi (további késleltetés)**. Meghatározza, hogy kívánja-e a kérés küldése a háttérrendszerek késés mérési érzékenységi tartományon belül, vagy továbbítja a kérést a legközelebbi háttérrendszere bejárati ajtajának.

További információkért lásd: [legkisebb késés esetén használt útválasztási módszer alapján](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>További lépések

- [Bejárati ajtajának profil létrehozása](quickstart-create-front-door.md)
- [Hogyan működik a bejárati ajtó](front-door-routing-architecture.md)