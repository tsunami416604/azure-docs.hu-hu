---
title: A háttérrendszer és a háttérbeli készletek az Azure bejárati ajtó szolgáltatásában | Microsoft Docs
description: Ez a cikk ismerteti, hogy milyen háttér-és háttér-készletek találhatók az előtérben lévő konfigurációban.
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
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229033"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>A háttérrendszer és a háttérbeli készletek az Azure bejárati ajtó szolgáltatásában
Ez a cikk bemutatja, hogyan képezhető le az alkalmazás üzembe helyezése az Azure bevezetési szolgáltatásával. Emellett ismerteti a különböző használati feltételeket is az alkalmazási háttérrendszer-konfigurációban.

## <a name="backends"></a>Háttérrendszerek
A háttérrendszer egy adott régióban található alkalmazás telepítési példányával egyenlő. A bejárati ajtós szolgáltatás az Azure-t és a nem Azure-beli háttereket is támogatja, így a régió nem csak az Azure-régiókra korlátozódik. Emellett lehet a helyszíni adatközpont vagy egy másik Felhőbeli alkalmazás-példány is.

A bejárati ajtó szolgáltatásának háttere az alkalmazás állomásneve vagy nyilvános IP-címe, amely az ügyfelek kérelmeit szolgálja ki. A háttérrendszer nem tévesztendő össze az adatbázis szintjével, a tárolási szintjével és így tovább. A háttereket az alkalmazás-háttér nyilvános végpontjának kell tekinteni. Amikor felvesz egy hátteret egy előtérben található háttér-készletbe, a következőket is hozzá kell adnia:

- **Háttérbeli gazdagép típusa** A hozzáadni kívánt erőforrás típusa. A bejárati ajtós szolgáltatás az App Service, a Cloud Service vagy a Storage szolgáltatásból támogatja az alkalmazási hátterek automatikus észlelését. Ha egy másik erőforrást szeretne használni az Azure-ban, vagy akár egy nem Azure-beli hátteret is, válassza az **Egyéni gazdagép**lehetőséget.

    >[!IMPORTANT]
    >A konfiguráció során az API-k nem ellenőrzik, hogy a háttérrendszer nem érhető-e el az ajtós környezetekben. Győződjön meg arról, hogy a bejárati ajtó elérheti a hátteret.

- **Az előfizetés és a háttérbeli gazdagép neve**. Ha nem választotta ki az **Egyéni gazdagép** a háttérbeli gazdagéphez lehetőséget, válassza ki a megfelelő előfizetést, és a megfelelő háttér-gazdagép nevét a felhasználói felületen.

- **Háttérbeli állomásfejléc**. A gazdagép fejlécének értéke minden kérelem esetében a háttérbe kerül. További információ: [backend Host header](#hostheader).

- **Prioritás**. Rendeljen prioritásokat a különböző háttérekhez, ha az elsődleges szolgáltatási hátteret szeretné használni az összes forgalomhoz. Adja meg a biztonsági mentést is, ha az elsődleges vagy a biztonsági mentési háttérrendszer nem érhető el. További információ: Priority ( [prioritás](front-door-routing-methods.md#priority)).

- **Súlyozás**. Súlyok hozzárendelésével a különböző háttérrendszer-adatokkal terjesztheti a forgalmat a különféle háttérrendszer-csoportokon belül, akár egyenletesen, akár súlyozási együtthatók alapján. További információ: [súlyok](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Háttérbeli gazdagép fejléce

A háttérben a háttérbe továbbított kérések közé tartozik egy állomásfejléc mező, amelyet a háttér a célként megadott erőforrás lekérésére használ. A mező értéke általában a háttér-URI-ból származik, és a gazdagép és a port.

Például a www\.contoso.com-ra vonatkozó kérelemben a gazdagép fejléce www\.contoso.com lesz. Ha Azure Portalt használ a háttér konfigurálásához, a mező alapértelmezett értéke a háttér állomásneve. Ha a háttérrendszer contoso-westus.azurewebsites.net, a Azure Portal a háttérbeli állomásfejléc automatikusan kitöltött értéke contoso-westus.azurewebsites.net lesz. Ha azonban Azure Resource Manager sablonokat vagy más módszert használ a mező explicit beállítása nélkül, a bejárati ajtó szolgáltatás a beérkező állomásnév értékét a gazdagép fejlécének értékeként fogja elküldeni. Ha a kérés a www\.contoso.com, és a háttér olyan contoso-westus.azurewebsites.net, amely üres fejléc mezővel rendelkezik, a bekapcsolási szolgáltatás a gazdagép fejlécét a www\.contoso.com fogja beállítani.

A legtöbb alkalmazás-háttér (az Azure Web Apps, a blob Storage és a Cloud Services) megköveteli, hogy a gazdagép fejléce megfeleljen a háttér tartományának. Ugyanakkor a háttér-gazdagép, amely a háttérre irányítja, egy másik állomásnevet fog használni, például a www\.contoso.azurefd.net.

Ha a háttérrendszer számára szükséges, hogy a gazdagép fejléce megfeleljen a háttérbeli állomásnévnek, győződjön meg arról, hogy a háttérbeli állomásfejléc tartalmazza az állomásnév-háttér nevet.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>A háttérrendszer-állomásfejléc konfigurálása a háttérrendszer számára

A háttérbeli **gazdagép fejlécének** beállítása a háttérrendszer-készletben:

1. Nyissa meg az előtérben található erőforrást, és válassza ki a háttérrendszer-készletet a konfiguráláshoz.

2. Ha még nem tette meg, vegyen fel egy hátteret, vagy szerkesszen egy meglévőt.

3. Állítsa a háttérbeli állomásfejléc mezőt egyéni értékre, vagy hagyja üresen. A rendszer a bejövő kérelem állomásneveként fogja használni a gazdagép fejlécének értékét.

## <a name="backend-pools"></a>Háttér-készletek
A bejárati ajtón a háttérrendszer-készlet az alkalmazáshoz hasonló adatforgalmat fogadó háttérrendszer-készletre hivatkozik. Ez azt jelenti, hogy az alkalmazás példányainak logikai csoportosítása a világon, amely ugyanazt a forgalmat kapja, és a várt működésre reagál. Ezek a hátterek különböző régiókban vagy ugyanazon a régión belül vannak telepítve. Az összes háttér aktív/aktív üzembe helyezési módban lehet, vagy az aktív/passzív konfigurációként van meghatározva.

A háttér-készletek határozzák meg, hogy a különböző háttérrendszer milyen módon legyen kiértékelve az állapot-ellenőrzéseken keresztül. Azt is meghatározza, hogyan történik a terheléselosztás a közöttük.

### <a name="health-probes"></a>Állapotminták
A bejárati ajtó szolgáltatás rendszeres HTTP/HTTPS mintavételi kérelmeket küld az egyes konfigurált háttérrendszer-példányokhoz. A mintavételi kérelmek meghatározzák az egyes háttérrendszer közelségét és állapotát a végfelhasználói kérelmek elosztása érdekében. A háttérbeli készlet állapot-mintavételi beállításai határozzák meg, hogy az alkalmazás-háttérrendszer állapotának lekérdezése hogyan történjen. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Elérési út**. A háttér-készletben található összes háttérrendszer mintavételi kérelméhez használt URL-cím. Ha például az egyik háttérrendszer a contoso-westus.azurewebsites.net, és az elérési út a/Probe/test.aspx értékre van állítva, akkor a bevezető üzemi környezetek, feltéve, hogy a protokoll HTTP-értékre van állítva, az állapot-mintavételi kérelmeket a http\://contoso-westus.azurewebsites.net/probe/test.aspx küldi.

- **Protokoll**. Meghatározza, hogy a rendszer elküldje-e az állapot-mintavételi kérelmeket az első ajtós szolgáltatástól a háttérben HTTP vagy HTTPS protokollal.

- **Időköz (másodperc)** . Meghatározza az állapot-mintavételek gyakoriságát a háttérrendszer számára, illetve azokat az intervallumokat, amelyekben az egyes bejárati környezetek egy mintavételt küldenek.

    >[!NOTE]
    >A gyorsabb feladatátvétel érdekében állítsa az intervallumot alacsonyabb értékre. Minél alacsonyabb az érték, annál nagyobb az állapot mintavételi mennyisége, amelyet a hátterek kapnak. Ha például az intervallum 30 másodpercre van beállítva, és 90 bejárati ajtót, vagy globálisan jelenik meg, az egyes háttérrendszer a 3-5 mintavételi kérelmek másodpercenkénti számát fogja kapni.

További információ: [Health](front-door-health-probes.md)mintavétel.

### <a name="load-balancing-settings"></a>Terheléselosztási beállítások
A háttér-készlet terheléselosztási beállításai határozzák meg, hogyan értékeljük ki az állapotadatok kiértékelését. Ezek a beállítások határozzák meg, hogy a háttér állapota Kifogástalan-e. Azt is bemutatják, hogyan kell terheléselosztást alkalmazni a különböző háttérrendszer-adatforgalom között a háttér-készletben. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

- **Minta mérete** Meghatározza, hogy a háttér-állapot kiértékeléséhez hány mintát kell figyelembe venni.

- A **minta mérete sikeres**. Meghatározza a korábban említett minta méretét, a háttér kifogástalan meghívásához szükséges sikeres minták számát. Tegyük fel például, hogy a bejárati ajtó állapotának mintavételi intervalluma 30 másodperc, a minta mérete 5, a sikeres minta mérete pedig 3. Minden alkalommal, amikor kiértékeljük a háttérrendszer állapotát, megvizsgáljuk az utolsó öt mintát 150 másodpercnél (5 x 30). Legalább három sikeres mintavételre van szükség ahhoz, hogy a hátteret Kifogástalan állapotba lehessen nyilvánítani.

- **Késési érzékenység (további késés)** . Azt határozza meg, hogy a bejárati ajtón szeretné-e elküldeni a kérést a háttérrendszer-mérési érzékenységi tartományon belül, vagy továbbítsa a kérést a legközelebbi háttérbe.

További információ: a [minimális késésen alapuló útválasztási módszer](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>További lépések

- [Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)