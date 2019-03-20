---
title: Az Azure bejárati ajtajának szolgáltatás - háttérrendszerek és Háttérkészletek |} A Microsoft Docs
description: Ez a cikk segít megérteni a háttérrendszer és az háttérkészletek bejárati ajtajának konfigurációban.
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
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123691"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Háttérrendszerek és a háttérkiszolgáló készletek Azure bejárati ajtajának Service-ben
Ez a cikk ismerteti a különböző hogyan leképezhet az alkalmazás központi telepítésének a bejárati ajtajának kapcsolatban. Is ismertetjük a különböző használati elöl ajtó alkalmazás háttér-konfigurációt jelentését.

## <a name="backend-pool"></a>Háttérkészlet
A Front Doorban a háttérkészlet azon egyenértékű háttérrendszereket jelöli, amelyek azonos típusú forgalmat képesek fogadni az alkalmazásuk számára. Más szóval ez azon világszerte jelen lévő alkalmazáspéldányok logikus csoportosítása, amelyek ugyanazt a forgalmat képesek fogadni, és a várt módon reagálnak. Ezek a háttérszolgáltatások általában különböző régiók között, vagy ugyanabban a régióban üzembe helyezett. Ezek a háttérszolgáltatások ezenkívül lehetnek aktív-aktív központi telepítés módban, vagy egyéb sikerült definiálható mint egy aktív/passzív konfigurációt.

Háttérkészlet is határozza meg, hogyan a különböző háttérrendszereket kell az összes kiértékelni az állapot-mintavételei azok állapotát és ennek megfelelően hogyan történjen, a háttérrendszerek közötti terheléselosztás.

### <a name="health-probes"></a>Állapotminták
Bejárati ajtajának egyes a konfigurált háttérrendszerekre HTTP/HTTPS-mintavétel rendszeresen kéréseket küld határozza meg, a közelség és az egyes háttérrendszerek betöltése állapotát a végfelhasználói kérések elosztása. A háttérkészlet állapot-mintavételi beállítások határozzák meg, hogyan tudjuk lekérdezi a háttérkomponensei állapotát a. A terheléselosztás konfigurálása a következő beállítások érhetők el:

1. **Elérési út**: Ha a mintavételi kérések küld a háttérkészletben háttéralkalmazásokat készíthet az URL-címet. Például, ha a háttérkiszolgálókon egyik `contoso-westus.azurewebsites.net` és az elérési út értéke `/probe/test.aspx`, majd a bejárati ajtajának környezetekben, feltéve, hogy a HTTP protokoll van beállítva az egészségügyi mintavételi kérelmeket küld http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protokoll**: Határozza meg, hogy egészségügyi mintavételi érkező kérelmek bejárati ajtó a háttérrendszerekre küld HTTP vagy HTTPS protokollon keresztül.
3. **Időköz (másodperc)**: Ez a mező az állapot-mintavételei gyakorisága a háttérrendszerekre, vagyis az intervallumok, amelyben bejárati ajtajának környezeteket fog küldeni a Hálózatfigyelő határozza meg. Megjegyzés: - keresett gyorsabb feladatátvételi teszteket, ha majd állítsa be ezt a mezőt egy alacsonyabb értékre. Azonban a kisebb az érték nagyobb az állapotvizsgálatot kötetet, amelyet a háttérrendszerekre fog kapni. Mennyi mintavételi kötet bejárati ajtajának hoz létre a a háttérkiszolgálókon képet kapjon, vegyünk egy példát. Tegyük fel, az interval értéke pedig 30 másodperc, és körülbelül 90-es bejárati ajtajának környezetek vagy kapcsolódási globálisan. Így megközelítőleg kapcsolatos fog kapni a háttérrendszerekre mindegyike 3-5 mintavételi kérések száma másodpercenként.

Olvasási [állapotadat-mintavételek](front-door-health-probes.md) részleteiről.

### <a name="load-balancing-settings"></a>Terheléselosztás beállításai
A betöltési terheléselosztási beállítások háttérkészlete számára adja meg, hogyan kiértékeljük a háttérrendszer döntheti megfelelő vagy nem megfelelő állapot-mintavételei és is hogyan ellenőriznünk kell a terheléselosztást alkalmazni a különböző háttérrendszereket háttérkészlet közötti forgalom. A terheléselosztás konfigurálása a következő beállítások érhetők el:

1. **Minta mérete**: Ez a tulajdonság azonosítja az állapot-mintavételei hány minták kell figyelembe venni háttérrendszer állapotának kiértékelését.
2. **A sikeres mintanagyság**: Ez a tulajdonság határozza meg, hogy a minta mérete a fent leírtak hány minták szükségünk van a kifogástalan állapotú, a háttéralkalmazás hívásához sikeres ellenőrzése. 
</br>Például tételezzük fel számára a bejárati ajtajának állított be az állapotminta *időköz* 30 másodperc, *mérete minta* "5" értékre van állítva és *sikeres mintanagyság* "3" értékre van állítva. Mi ez a konfiguráció azt jelenti, hogy minden alkalommal, amikor az állapot-mintavételei biztosítani a háttérbeli kiértékeljük, áttekintjük az utolsó öt mintákat, amelyek az elmúlt 150 másodpercben átfedés lenne, majd (5 * 30 = s), kivéve ha 3 vagy több ilyen mintavételezők sikeres azt deklarálja, a biztonsági és a végfelhasználók nem megfelelő állapotú. Tegyük fel, csak két sikeres mintavételek volt, és így a háttérrendszer állapota nem megfelelőként megjelöli azt. A kiértékelés a futtatott legközelebb találtunk 3 az utolsó öt mintavételek a sikeres, ha ezután azt jelölje meg a háttérrendszer állapota kifogástalan újra.
3. **Késés érzékenységi (további késleltetés)**: A késés érzékenységi mező határozza meg, hogy kívánja-e a kérelem elküldéséhez, amelyek az érzékenységi tartománya késés mérési és a kérést a legközelebbi háttérrendszere tekintetében bejárati ajtajának. Olvasási [legkisebb késés esetén használt útválasztási módszer alapján](front-door-routing-methods.md#latency) bejárati ajtajának további számára.

## <a name="backend"></a>Háttérszolgáltatás
A háttérrendszernek megegyezik egy alkalmazás központi telepítési példánya egy régióban. Bejárati ajtajának támogatja mind az Azure és a nem Azure-beli háttérrendszerek és tehát itt az régió nem csak korlátozott Azure-régióban, de a helyszíni adatközpont vagy más felhőben alkalmazáspéldány is lehet.

-Háttérrendszerek esetében bejárati ajtó, kontextusában hivatkozik a gazdagép neve vagy a nyilvános IP-címet az alkalmazás, amely is képes kiszolgálni az ügyfélkéréseket. Tehát háttérrendszerek nem tévesztendő az adatbázisszint vagy a tárolási réteg stb., de inkább kell kezelni, az alkalmazás háttérrendszere nyilvános végpontjára.

Ha a háttérrendszer a bejárati ajtó háttérkészlet ad hozzá, szüksége lesz adja meg az alábbi adatokat:

1. **Háttérbeli gazdagéptípusokkal**: A hozzáadni kívánt erőforrás típusát. Bejárati ajtajának automatikus felderítését a háttérkomponensei, ha az app Service-ben, a felhőalapú szolgáltatás vagy a storage támogatja. Ha azt szeretné, hogy egy másik erőforrás Azure-ban vagy még nem Azure-háttérrendszernek, válassza az "Egyéni állomás". Megjegyzés: - konfigurálása során az API-k nem ellenőrzik a háttérrendszer érhető el bejárati ajtajának környezetekben ehelyett győződjön meg arról, hogy a háttérrendszer bejárati ajtajának elérhető-e. 
2. **Előfizetés és a háttérkiszolgáló állomásnév**: Nem választott írja be a háttér-gazdagép "egyéni host", akkor a kell hatókörét le, és válassza ki a háttérrendszer a felhasználói felületen a megfelelő előfizetést, és a megfelelő háttér-gazdagép nevének kiválasztásával.
3. **Háttérbeli állomásfejléc**: A küldött állomásfejléc-érték, a háttérkiszolgáló az egyes kérések. Olvasási [háttérrendszer állomásfejléc](#hostheader) részleteiről.
4. **Prioritás**: A különböző háttérrendszerekre is prioritásokat lehet kiosztani, ha szeretne egy elsődleges service háttérrendszer használata minden forgalmat, és adja meg a biztonsági mentések, abban az esetben, ha az elsődleges vagy a biztonsági mentési háttérrendszerek nem érhető el. Tudjon meg többet [prioritású](front-door-routing-methods.md#priority).
5. **Súly**: Ha meg szeretné elosztani a forgalmat egy készletét-háttérrendszerek esetében egyenletesen vagy súly együttható megfelelően, súlyok rendelhet a különböző háttérrendszerekre. Tudjon meg többet [súlyok](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Háttérbeli állomásfejléc

A háttérrendszernek bejárati ajtajának által továbbított kérések rendelkezik egy gazdagép fejlécmezőt, amely a háttéralkalmazás használja a célként megadott erőforrás lekérése. Ez a mező értéke általában a háttérrendszer URI származik, és állomás és port. Ha például kérelem `www.contoso.com` fog rendelkezni az állomásfejléc `www.contoso.com`. Az Azure portal használatával háttérszolgáltatás használatára konfigurál, ha az alapértelmezett érték, amely tölti fel a rendszer a mező a háttérrendszer állomásneve. Például, ha a háttérrendszer `contoso-westus.azurewebsites.net`, akkor az Azure Portalon automatikusan kitölti háttérrendszer állomásfejléc értéke lesz `contoso-westus.azurewebsites.net`. 
</br>Azonban ha a Resource Manager-sablonok használata vagy más módszerrel, és nem állítja ezt a mezőt explicit módon ezután bejárati ajtajának elküldi a bejövő állomásnév állomásfejléc értéke. Például, ha a kérést intéztek `www.contoso.com`, és a háttérrendszer `contoso-westus.azurewebsites.net` gazdagép üresként fejléc háttérrendszer mezővel, majd bejárati ajtajának állítja, az állomásfejléc `www.contoso.com`.

A legtöbb háttérkomponensei (például a Web Apps, a Blob Storage és a Cloud Services) az állomásfejlécnek egyeznie a háttérrendszer tartományát. Azonban a frontend gazdagép, amely a háttérbeli irányítja kell például a www eltérő állomásnévvel\.contoso.azurefd.net. Ha a háttérrendszer állítja be az állomásfejlécnek egyeznie kell a gazdagép nevével, a háttérrendszer igényel, győződjön meg róla, hogy a "háttér-állomásfejléc" is rendelkezik-e a háttérrendszer állomásneve.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>A háttérrendszer a háttérrendszer állomásfejlécét konfigurálása
A "Háttér állomásfejléc" mező a háttérrendszernek a háttérbeli címkészlet szakaszban konfigurálhatók.

1. Nyissa meg a bejárati ajtajának erőforrást, majd kattintson a háttérkészlet, amely rendelkezik a háttérrendszer kell konfigurálni.

2. Ha nem adta hozzá bármelyik, vagy meglévő szerkesztésekor-integrációs háttérkomponensek felvétele. A "Háttér állomásfejléc" mező lehet beállítása egy egyéni értéket vagy üresen marad, ami azt jelenti, hogy a bejövő kérelem gazdagépnevét állomásfejléc-érték lesz.



## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.