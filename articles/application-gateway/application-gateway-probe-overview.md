---
title: Az Azure Application Gateway állapotfigyelési áttekintése
description: További tudnivalók az Azure alkalmazás átjáró a megfigyelési lehetőségek
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>Átjáró állapotfigyelő figyelési – áttekintés

Azure Application Gateway alapértelmezés szerint a háttér-készlet összes erőforrások állapotát figyeli, és automatikusan eltávolítja az összes erőforrást, a készlet megfelelő állapotúnak számít. Alkalmazásátjáró továbbra is figyeli a nem megfelelő példányok, és hozzáadja őket a megfelelő háttér-készlet számára, amennyiben az rendelkezésre állására, és állapotteljesítmény válaszolni. Alkalmazásátjáró küldi az állapot-mintavételi csomagjai a meghatározott a háttér-HTTP-beállítások ugyanazt a portot. Ez a konfiguráció biztosítja, hogy a mintavétel ellenőrzi, hogy az ügyfelek akkor használja a háttérrendszerhez való csatlakozáshoz ugyanazt a portot.

![alkalmazás átjáró mintavételi – példa][1]

Mellett alapértelmezett mintavételi állapotfigyelés, testreszabhatja a állapotmintáihoz az alkalmazás követelményeinek megfelelően. Ebben a cikkben alapértelmezett és egyéni állapotteljesítmény tartoznak.

> [!NOTE]
> Ha Alkalmazásátjáró alhálózat egy NSG-t, porttartományok 65503-65534 meg kell nyitni bejövő forgalom az Alkalmazásátjáró alhálózaton. Ezeket a portokat a háttérrendszer állapotfigyelő API működéséhez szükségesek.

## <a name="default-health-probe"></a>Alapértelmezett állapotmintáihoz

Alkalmazásátjáró automatikusan meghatározza, hogy egy alapértelmezett állapotmintáihoz nem állít be egyéni mintavételi beállításra. A figyelési viselkedés HTTP-kérelem a háttér-készlet konfigurált IP-címeit, így működik. Az alapértelmezett mintavételt a backendhttpsettings osztályhoz vannak konfigurálva, a HTTPS-hez, ha a mintavételi HTTPS PROTOKOLLT használ, valamint a háttérkiszolgálókon állapotának ellenőrzéséhez.

Például: az alkalmazás átjárót háttérkiszolgálók A, B és C használja a 80-as porton, HTTP-hálózati forgalom fogadására konfigurálja. A három kiszolgáló az alapértelmezett állapotfigyelés tesztek 30 másodpercenként kifogástalan HTTP-választ. Rendelkezik a megfelelő HTTP-válasz egy [állapotkód](https://msdn.microsoft.com/library/aa287675.aspx) 200 és 399 között.

Ha a kiszolgáló az alapértelmezett mintavételi ellenőrzése sikertelen, az Alkalmazásátjáró automatikusan eltávolítja a háttér-készlet, és a hálózati forgalom leállítja a kiszolgáló halad. Az alapértelmezett mintavétel továbbra is ellenőrizze a kiszolgáló egy 30 másodpercenként. Amikor a kiszolgáló válaszol sikeresen egy kérést a egy alapértelmezett állapotmintáihoz, kerül ismét kifogástalan a háttér-készlet, és forgalom elindul, a kiszolgáló ismét halad.

### <a name="probe-matching"></a>Mintavételi megfelelő

Alapértelmezés szerint egy HTTP (S) 200-as állapotkóddal tekinthető kifogástalan. Egyéni állapotteljesítmény emellett támogatja a két folyamatmegfeleltetési feltételek. Feltételeknek megfelelő segítségével szükség esetén módosítsa az alapértelmezett értelmezésének milyen consititutes megfelelő választ.

A következő vannak feltételeknek: 

- **HTTP válasz állapot kód egyezés** - mintavétel elfogadása az adott feltételnek megfelelő felhasználó által megadott http válasz kódja vagy válasz kód tartományokat. Egyes vesszővel elválasztott állapotkódok vagy állapotkód számos támogatott.
- **HTTP-válasz törzsében egyezés** – HTTP-válasz törzsében és megfelel a felhasználó a következőhöz megadott karakterlánc adott feltételnek megfelelő mintavétel. Vegye figyelembe, hogy az egyeztetés keres a felhasználó jelenléte megadott karakterlánc adott válasz törzsének, és nincs teljes reguláris kifejezéssel egyező.

Feltételek egyeznek meg lehet adni a `New-AzureRmApplicationGatewayProbeHealthResponseMatch` parancsmag.

Példa:

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Ha a feltételek megadása esetén az csatolható mintavétel konfigurálása egy `-Match` PowerShell paramétere.

### <a name="default-health-probe-settings"></a>Alapértelmezett állapotfigyelő mintavételi beállításai

| Mintavételi tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Teszt URL-címe |http://127.0.0.1:\<port\>/ |URL-cím |
| Időköz |30 |Mintavételi időköz másodpercben |
| Időtúllépés |30 |Mintavételi időkorlátja másodpercben |
| Sérült küszöbérték |3 |Mintavételi újrapróbálkozások maximális számát. A háttér-kiszolgálófiók van megjelölve, miután az egymást követő mintavételi hiba száma eléri a sérült küszöbérték. |

> [!NOTE]
> A port, a háttér-HTTP-beállítások ugyanazt a portot.

Az alapértelmezett vizsgálat ellenőrzi, hogy az csak http://127.0.0.1:\<port\> állapot meghatározásához. Ha az állapotfigyelő mintavétel nyissa meg egy egyéni URL-címre, vagy egyéb beállításait módosítani kell, a következő lépésekben leírtaknak egyéni mintavételt kell használnia:

## <a name="custom-health-probe"></a>Egyéni állapotmintáihoz

Egyéni mintavételt lehetővé teszik az állapotfigyelés részletesebb vezérlésére. Egyéni mintavételt használata esetén konfigurálhatja a mintavételi időköznek, az URL-cím és elérési útja tesztelése és fogadja el, hogy megsérült a háttér-készlet példányhoz való megjelölése előtt hány hibás válaszokat.

### <a name="custom-health-probe-settings"></a>Egyéni állapotfigyelő mintavételi beállítások

A következő táblázat a definíciók tulajdonságait a egyéni állapotmintáihoz.

| Mintavételi tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A mintavétel neve. Ez a név segítségével tekintse meg a mintavétel a háttér-HTTP-beállításait. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttér-HTTP-beállítások között megadott protokollt használ. |
| Gazdagép |A mintavétel küldendő állomásnevet. Alkalmazandó csak akkor, ha több hely van beállítva az alkalmazás-átjárón, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét. |
| Útvonal |A mintavétel relatív elérési útja. Az érvényes elérési utat elindítja a "/". |
| Időköz |Mintavételi időköz másodpercben. Ez az érték pedig az időköz, két egymást követő mintavételek menüpontban között. |
| Időtúllépés |Mintavételi időtúllépés másodpercben. Egy érvényes válasz nem érkezett meg a megadott időn belül, ha a mintavételi hibás jelölést.  |
| Sérült küszöbérték |Mintavételi újrapróbálkozások maximális számát. A háttér-kiszolgálófiók van megjelölve, miután az egymást követő mintavételi hiba száma eléri a sérült küszöbérték. |

> [!IMPORTANT]
> Ha Alkalmazásátjáró egy adott hely van konfigurálva, a gazdagépen alapértelmezés szerint nevét kell megadni a "127.0.0.1", kivéve, ha az egyéni tesztműveleti egyéb konfigurálni.
> Referenciaként egy egyéni mintavételt küldött \<protokoll\>://\<állomás\>:\<port\>\<elérési\>. A háttér-HTTP-beállítások között megadott használt port válik, ugyanazt a portot.

## <a name="next-steps"></a>További lépések
Alkalmazásátjáró állapotfigyelés megismerését követően konfigurálhatja a [egyéni állapotmintáihoz](application-gateway-create-probe-portal.md) az Azure portálon vagy egy [egyéni állapotmintáihoz](application-gateway-create-probe-ps.md) PowerShell és az Azure Resource Manager használatával üzembe helyezési modellben.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
