---
title: Az Azure Traffic Manager beállításainak ellenőrzése
description: Ez a cikk segítséget nyújt a Traffic Manager beállításainak ellenőrzése.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: b37022f79feafc2110366446752675e7c42188f3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054027"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager-beállítások ellenőrzése

Traffic Manager-beállítások teszteléséhez szüksége lesz a több ügyfél különböző helyeken, amelyből a tesztek futtatása. A Traffic Manager-profil le egyenként, majd betöltheti a végpontok.

* Állítsa be a DNS-Élettartamot érték alacsony, hogy a módosítások propagálása gyorsan (Ha például 30 másodperc).
* Hogy az IP-címét az Azure cloud services és websites teszteli a profilban.
* Eszközök, amelyekkel egy DNS-név feloldása IP-címhez, és megjeleníti ezt a címet használja.

Ellenőrzi, hogy a profil végpontjainak IP-címeket feloldani a DNS-nevek megtekintéséhez. A neveket a forgalom-útválasztási módszert a Traffic Manager-profilban megadott konzisztens módon kell feloldhatónak lennie. Az eszközök, például **nslookup** vagy **dig** DNS-nevek feloldására.

Az alábbi példák segítenek a Traffic Manager-profil teszteléséhez.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Ellenőrizze az nslookup és IP-konfiguráció használata a Windows a Traffic Manager-profilt

1. Nyisson meg egy parancs vagy a Windows PowerShell-parancssort rendszergazdaként.
2. Típus `ipconfig /flushdns` kiüríteni a DNS-gyorsítótárban.
3. Gépelje be: `nslookup <your Traffic Manager domain name>`. Például az a következő parancs ellenőrzi a tartomány nevét a előtaggal *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Egy tipikus eredményt jeleníti meg a következő információkat:

    + A DNS-nevet és megoldása érdekében a Traffic Manager szolgáltatásbeli tartománynévre hozzáfér a DNS-kiszolgáló IP-címét.
    + A Traffic Manager szolgáltatásbeli tartománynévre írta be a parancssorba "nslookup" és az IP-cím, amelyhez a Traffic Manager-tartományra feloldása után. Ellenőrizze, hogy fontos a második IP-cím lesz. Az egyik vagy a webhely a Traffic Manager-profil teszteli a nyilvános virtuális IP-cím (VIP) címet meg kell egyeznie.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>A feladatátvételi forgalom-útválasztási módszer tesztelése

1. Hagyja meg az összes végpontok fel.
2. DNS-feloldás az nslookup vagy egy hasonló eszköz használatával a vállalata tartománynevét egyetlen ügyfél használja, kérhet.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik-e az elsődleges végpontot.
4. Állítsa le az elsődleges végpontot, vagy távolítsa el a figyelési fájlt, így a Traffic Manager fenyegetésként észlel, hogy az alkalmazás le van-e.
5. Várjon, amíg a DNS idő-az-élettartam (TTL) a Traffic Manager-profil és a egy további két percet tevődik össze. Például ha a DNS-Élettartamot 300 másodpercig (5 perc), meg kell várnia hét perc.
6. Kiüríti a DNS ügyfél gyorsítótára és a kérés DNS-feloldás nslookup parancs segítségével. A Windows kiürítheti a DNS-gyorsítótár a ipconfig/flushdns paranccsal paranccsal.
7. Győződjön meg arról, hogy a feloldott IP-cím megegyezik-e a másodlagos végpontra.
8. Ismételje meg a folyamatot, viszont így minden végpont le. Győződjön meg arról, hogy a DNS-ben a következő végpont IP-címét a lista adja vissza. Minden végpontjai működnek, ha újra kell beszerezni az elsődleges végpont IP-címét.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Súlyozott forgalom-útválasztási módszer tesztelése

1. Hagyja meg az összes végpontok fel.
2. DNS-feloldás az nslookup vagy egy hasonló eszköz használatával a vállalata tartománynevét egyetlen ügyfél használja, kérhet.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik a végpontok egyikét.
4. A DNS-ügyfél gyorsítótára ürítése, és ismételje meg a 2. és 3 végpontok. Minden végpont visszaadott különböző IP-címeket kell megjelennie.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>A teljesítmény forgalom-útválasztási módszer tesztelése

A teljesítmény forgalom-útválasztási módszert hatékonyan teszteléséhez rendelkeznie kell a világ különböző pontjain található ügyfelek számára. Az ügyfelek különböző Azure-szolgáltatásait teszteléséhez használható régióban hozhat létre. Ha egy globális hálózata, távolról jelentkezzen be a világ más részein lévő ügyfelek és a tesztek futtatása onnan.

Azt is megteheti, ingyenes webes DNS-címkeresés és vannak további elérhető szolgáltatások. Egyes eszközök lehetővé teszik, ellenőrizze a DNS-névfeloldás világszerte különböző helyekről. Példák a "DNS-címkeresés" Keresés végrehajtása. Külső szolgáltatások, például Gomez vagy Nyitóelőadást, győződjön meg arról, hogy a profilok terjeszti forgalom a várt módon használható.

## <a name="next-steps"></a>További lépések

* [Tudnivalók a Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)
* [A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
* [A Traffic Manager csökkentett teljesítményének elhárítása](traffic-manager-troubleshooting-degraded.md)
