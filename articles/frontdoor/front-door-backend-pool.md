---
title: Háttérrendszerek és háttérkészletek az Azure Bejárati ajtajában| Microsoft dokumentumok
description: Ez a cikk ismerteti, milyen háttérrendszerek és háttérkészletek vannak a bejárati ajtó konfigurációjában.
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
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293468"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Háttérrendszerek és háttérkészletek az Azure bejárati ajtajában
Ez a cikk ismerteti, hogyan képezheti le az alkalmazás üzembe helyezését az Azure Bejárati ajtajával. Azt is ismerteti a különböző kifejezések et frontdoor konfiguráció körül app háttérrendszerek.

## <a name="backends"></a>Háttérrendszerek
A háttér-háttérszabály egy adott régióban az alkalmazás központi telepítési példánya. A Bejárati ajtó támogatja az Azure-t és a nem Azure-beli háttérrendszereket is, így a régió nem csak az Azure-régiókra korlátozódik. Emellett lehet a helyszíni adatközpont vagy egy alkalmazáspéldány egy másik felhőben.

A bejárati ajtó háttérrendszereinek az alkalmazás állomásnevére vagy nyilvános IP-címére hivatkoznak, amely ügyfélkéréseket képes kiszolgálni. Háttérrendszerek nem szabad összetéveszteni az adatbázisréteg, tárolási szint, és így tovább. Háttérrendszerek kell tekinteni, mint az alkalmazás háttérszámítógép nyilvános végpontja. Ha háttér- egy ház hoz a frontajtó-háttérkészlethez ad hozzá, a következőket is hozzá kell adnia:

- **Háttérállomás típusa**. A hozzáadni kívánt erőforrás típusa. A Bejárati ajtó támogatja az alkalmazás háttérrendszereinek automatikus felfedezését az alkalmazásszolgáltatásból, a felhőszolgáltatásból vagy a tárolóból. Ha egy másik erőforrást szeretne az Azure-ban, vagy akár egy nem Azure-beli háttérrendszer, válassza az **Egyéni állomás**lehetőséget.

    >[!IMPORTANT]
    >A konfiguráció során az API-k nem ellenőrzik, hogy a háttérrendszer nem érhető-e el a Bejárati ajtajával környezetben. Győződjön meg róla, hogy a bejárati ajtó eléri a háttér.

- **Előfizetés és háttérállomás neve**. Ha még nem választotta ki **az egyéni állomást** a háttérállomás típusához, válassza ki a háttérrendszert a megfelelő előfizetés és a megfelelő háttérállomás nevének kiválasztásával a felhasználói felületen.

- **Háttérállomás fejléce**. Az egyes kérelmek háttérrendszerének küldött gazdafejléc-érték. További információ: [Backend host header](#hostheader).

- **Prioritás**. Rendeljen prioritásokat a különböző háttérrendszerekhez, ha elsődleges szolgáltatás háttérszolgáltatást szeretne használni az összes forgalomhoz. Is, biztonsági másolatot, ha az elsődleges vagy a biztonsági mentési háttérrendszerek nem érhetők el. További információ: [Priority](front-door-routing-methods.md#priority).

- **Súly**. Súlyokat rendelhet a különböző háttérrendszerekhez, hogy a forgalmat egyenletesen vagy súlyegyütthatók szerint ossza el a háttérrendszerek között. További információ: [Súlyok](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Háttér-állomás fejléce

A Bejárati ajtó által egy háttérrendszernek továbbított kérelmek tartalmaznak egy állomásfejléc-mezőt, amelyet a háttérrendszer a megcélzott erőforrás lekéréséhez használ. A mező értéke általában a háttérURI-ból származik, és rendelkezik az állomással és a porttal.

Például egy kérelmet `www.contoso.com` a fogadó fejléc www.contoso.com. Ha az Azure Portal segítségével konfigurálja a háttérrendszert, a mező alapértelmezett értéke a háttérrendszer állomásneve. Ha a háttérrendszer contoso-westus.azurewebsites.net, az Azure Portalon a háttérrendszer gazdagépfejlécének automatikusan feltöltött értéke lesz contoso-westus.azurewebsites.net. Ha azonban az Azure Resource Manager-sablonokat vagy más módszert használja anélkül, hogy kifejezetten beállítaná ezt a mezőt, a Bejárati ajtó a bejövő állomásnevet küldi az állomásfejléc értékeként. Ha a kérés a\.www contoso.com, és a háttércontoso-westus.azurewebsites.net, amely egy üres fejléc mező,\.Bejárati ajtó fogja állítani a fogadó fejléc www contoso.com.

A legtöbb alkalmazás-háttérrendszerek (Azure Web Apps, Blob storage és a Cloud Services) megkövetelik a gazdagép fejlécének, hogy megfeleljen a háttérrendszer tartományának. Azonban az előtér-gazdagép, amely a háttérkiszolgálóra irányítja egy másik állomásnevet, például www.contoso.net.

Ha a háttérkiszolgálómegköveteli, hogy az állomásfejléc megfeleljen a háttérállomás nevének, győződjön meg arról, hogy a háttérállomás-fejléc tartalmazza az állomásnév-háttérkiszolgálót.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>A háttérállomás fejlécének konfigurálása a háttérkiszolgálóhoz

A **háttér-állomás fejlécmezőjének** konfigurálása egy háttértartalék-szakaszhoz:

1. Nyissa meg a bejárati ajtaját erőforrást, és válassza ki a konfigurálni kívánt háttérkészletet.

2. Ha még nem tette meg, adjon hozzá egy háttérrendszert, vagy szerkesztsen egy meglévőt.

3. Állítsa be a háttérállomás fejlécmezőjét egyéni értékre, vagy hagyja üresen. A bejövő kérelem állomásneve lesz az állomásfejléc értéke.

## <a name="backend-pools"></a>Háttérmedencék
A háttérkészlet a bejárati ajtajában az alkalmazásukhoz hasonló forgalmat kapó háttérrendszerek készletére utal. Más szóval, ez egy logikai csoportosítása az alkalmazáspéldányok szerte a világon, amelyek ugyanazt a forgalmat kapják, és reagálnak a várt viselkedést. Ezek a háttérrendszerek különböző régiókban vagy ugyanazon a régión belül vannak telepítve. Az összes háttérrendszer lehet aktív/aktív központi telepítési módban, vagy mi az aktív/passzív konfiguráció.

A háttérkészlet határozza meg, hogyan kell kiértékelni a különböző háttérrendszereket állapotminta segítségével. Azt is meghatározza, hogyan történik közöttük a terheléselosztás.

### <a name="health-probes"></a>Állapotminták
Bejárati ajtó küld időszakos HTTP/HTTPS mintavételi kérelmek et a konfigurált háttérrendszerek. A mintavételi kérelmek határozzák meg az egyes háttérrendszer ek közelségét és állapotát a végfelhasználói kérelmek terheléselosztása érdekében. A háttérkészlet állapotminta-beállításai határozzák meg, hogyan végezzük le az alkalmazás háttérrendszerek állapotának lekérdezését. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Elérési**út : A háttérkészlet összes háttér-tartalékának mintavételi kérelmeihez használt URL-cím. Ha például az egyik háttérrendszer contoso-westus.azurewebsites.net, és az elérési út /probe/test.aspx, akkor a Bejárati ajtó környezetek, feltéve, hogy a protokoll HTTP-re van állítva, állapotminta-kérelmeket küld a http\://contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokoll**: Azt határozza meg, hogy az állapotminta-kérelmeket a Bejárati ajtóról küldje-e a háttérrendszereknek HTTP vagy HTTPS protokollal.

- **Metódus**:Az állapotminta küldéséhez használt HTTP-módszer. A lehetőségek közé tartozik a GET vagy a HEAD (alapértelmezett).
    > [!NOTE]
    > Az alacsonyabb terhelés és a költségek a háttérrendszereken, bejárati ajtó azt javasolja, hogy head kérelmek et az egészségügyi szondák.

- **Időköz (másodperc)**: Meghatározza az állapotpróbák gyakoriságát a háttérrendszerekhez, vagy azokat az intervallumokat, amelyekben az egyes bejárati ajtó környezetek egy mintavételt küld.

    >[!NOTE]
    >A gyorsabb feladatátvétel érdekében állítsa az intervallumot alacsonyabb értékre. Minél alacsonyabb az érték, annál nagyobb az állapotminta köteta háttérrendszerek kap. Ha például az időköz 30 másodpercre van állítva, és mondjuk 100 bejárati ajtó POP globálisan, minden háttérrendszer körülbelül 200 mintavételi kérelmet kap percenként.

További információ: [Health probes](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Terheléselosztási beállítások
A háttérkészlet terheléselosztási beállításai határozzák meg, hogyan értékeljük az állapotmintákat. Ezek a beállítások határozzák meg, hogy a háttér-háttérszolgáltatás kifogástalan vagy nem kifogástalan. Azt is ellenőrzik, hogyan terhelheti ki a különböző háttérrendszerek közötti forgalmat a háttérkészletben. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **A minta mérete**. Azonosítja, hogy hány mintát az állapotminták at meg kell vizsgálni a háttér-állapot értékelése.

- **Sikeres mintaméret**. A korábban említett mintaméretet határozza meg, a háttérrendszer kifogástalan hívásához szükséges sikeres minták számát. Tegyük fel például, hogy a Bejárati ajtó állapotpróba-időköze 30 másodperc, a minta mérete 5, a sikeres mintaméret pedig 3. Minden alkalommal, amikor kiértékeljük az állapotszondák a háttér-, megnézzük az utolsó öt minta több mint 150 másodperc (5 x 30). Legalább három sikeres mintavételszükséges a háttérterület kifogástalanállapotúként való deklarálni.

- **Késés érzékenysége (további késés).** Azt határozza meg, hogy a Bejárati ajtó küldje-e a kérést a késésmérési érzékenységi tartományon belüli háttérrendszereknek, vagy továbbítsa a kérelmet a legközelebbi háttérrendszernek.

További információ: [Least latency based routing method](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>További lépések

- [Bejárati ajtó profil létrehozása](quickstart-create-front-door.md)
- [A bejárati ajtó működése](front-door-routing-architecture.md)