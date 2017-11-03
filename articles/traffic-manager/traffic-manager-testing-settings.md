---
title: "Ellenőrizze az Azure Traffic Manager-beállítások |} Microsoft Docs"
description: "Ez a cikk segít a Traffic Manager beállításainak ellenőrzése"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="verify-traffic-manager-settings"></a>A Traffic Manager beállításainak ellenőrzése

A Traffic Manager beállítások teszteléséhez szüksége több ügyfelet, hogy különböző helyeken, ahol a tesztek futtatása. Ezt követően is betöltheti a végpontok a Traffic Manager-profil egyenként le.

* Állítsa be a DNS-élettartam értéke alacsony, hogy módosításának propagálása gyorsan (például az 30 másodperc).
* Az IP-címek az Azure felhőszolgáltatások és webhelyek a tesztelni kívánt profil tudja.
* Eszközök, amelyek lehetővé teszik a feloldani egy DNS-nevet egy IP-címre, és megjeleníti ezt a címet használja.

Rendszer ellenőrzi, hogy, hogy a DNS-nevek a profil végpontjainak IP-címeket feloldani. A nevek oldja fel a forgalom-útválasztási módszert a Traffic Manager-profilban megadott konzisztens módon. Az eszközök, például a **nslookup** vagy **átrágniuk** DNS-nevek feloldására.

Az alábbi példák segítséget nyújtanak a Traffic Manager-profil tesztelése.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Ellenőrizze az nslookup és ipconfig használatát Windows Traffic Manager-profil

1. Nyissa meg rendszergazdaként egy parancs vagy a Windows PowerShell parancssorába.
2. Típus `ipconfig /flushdns` kiüríteni a DNS-gyorsítótárban.
3. Gépelje be: `nslookup <your Traffic Manager domain name>`. Például a következő parancs ellenőrzi a tartomány nevét a előtaggal *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Általános eredményt a következő információkat tartalmazza:

    + A DNS-nevét és IP-címet a DNS-kiszolgáló férnek hozzá a megoldásához a Traffic Manager szolgáltatásbeli tartománynevére.
    + A Traffic Manager szolgáltatásbeli tartománynevére írta be a parancssorba "nslookup" és az IP-címet, amelyre a Traffic Manager-tartományra mutat. A második IP-cím, a fontos egy ellenőrzéséhez. Meg kell felelnie a felhőszolgáltatások és webhelyek teszteli a Traffic Manager-profil az egyik nyilvános virtuális IP-cím (VIP) cím.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>A feladatátvételi forgalom-útválasztási módszert tesztelése

1. Hagyja végpontjai fel.
2. DNS-feloldás a vállalata tartománynevét, nslookup vagy egy hasonló segédprogram segítségével az egyetlen ügyfél használatával kérelmet.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik-e az elsődleges végpont.
4. Állítsa le az elsődleges végpont, vagy távolítsa el a figyelési fájlt úgy, hogy a Traffic Manager úgy értelmezi, hogy az alkalmazás nem működik.
5. Várjon, amíg a DNS idő élettartamát (TTL) a Traffic Manager-profil, valamint további két percet. Például ha a DNS-élettartam 300 másodpercen (5 percen), meg kell várnia a hét perc.
6. Ürítse ki a DNS-ügyfél gyorsítótárába, a kérelem DNS névfeloldásban nslookup parancs segítségével. A Windows rendszerben ürítse ki a DNS-gyorsítótár az ipconfig/flushdns paranccsal.
7. Győződjön meg arról, hogy a feloldott IP-cím megegyezik-e a másodlagos végponti.
8. Ismételje meg a folyamat minden egyes végpont pedig leáll. Győződjön meg arról, hogy a DNS-ben az IP-cím a következő végpont a lista adja vissza. Minden végpontok nem működnek, ha az IP-cím, az elsődleges végpont újra kell beszerezniük.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>A súlyozott forgalom-útválasztási módszert tesztelése

1. Hagyja végpontjai fel.
2. DNS-feloldás a vállalata tartománynevét, nslookup vagy egy hasonló segédprogram segítségével az egyetlen ügyfél használatával kérelmet.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik a végpont közül.
4. Ürítse ki a DNS-ügyfél gyorsítótárába, és ismételje meg a 2. és 3 végpontok. Megtekintheti az adott vissza az egyes a végpontok különböző IP-címeket.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>A teljesítmény forgalom-útválasztási módszert tesztelése

A teljesítmény forgalom-útválasztási módszert hatékonyan teszteléséhez a világ különböző pontjain található ügyfelek kell rendelkeznie. Ügyfelek, amelyek segítségével tesztelheti a szolgáltatások különböző Azure régiókban hozhat létre. Ha egy globális hálózata, távolról jelentkezzen be a világ többi részén lévő ügyfelek és a tesztek futtatása onnan.

Azt is megteheti, nincsenek ingyenes, webalapú DNS-címkeresés és elmerülne a rendszer az elérhető szolgáltatások. Egyes eszközök biztosítanak ellenőrizhető a DNS-név feloldása a világ különböző helyekről. Hajtsa végre a Keresés a "DNS-címkeresés" példákat. Külső szolgáltatásokat, mint a Gomez vagy Előadásbeli segítségével győződjön meg arról, hogy a profilok terjeszti forgalom várt módon.

## <a name="next-steps"></a>Következő lépések

* [A Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md)
* [A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
* [A Traffic Manager csökkentett teljesítményének elhárítása](traffic-manager-troubleshooting-degraded.md)
