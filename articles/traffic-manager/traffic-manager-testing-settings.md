---
title: Az Azure Traffic Manager beállításainak ellenőrzése
description: Ebből a cikkből megtudhatja, hogyan ellenőrizheti a Traffic Manager beállításait, és tesztelheti a forgalomútválasztási módszert.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938380"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager-beállítások ellenőrzése

A Traffic Manager beállításainak teszteléséhez több ügyfélre van szükség különböző helyeken, ahonnan futtathatja a teszteket. Ezután hozza le a Traffic Manager-profil végpontjait egyenként.

* Állítsa alacsonyan a DNS TTL értékét, hogy a változások gyorsan propagálódhassanak (például 30 másodperc).
* Ismerje meg az Azure felhőszolgáltatásainak és webhelyeinek IP-címét a tesztelt profilban.
* Olyan eszközöket használjon, amelyekkel feloldhatja a DNS-nevet egy IP-címre, és megjeleníti azt a címet.

Ellenőrzi, hogy a DNS-nevek feloldódnak-e a profil végpontjainak IP-címeire. A neveket a Traffic Manager-profilban definiált forgalomútválasztási módszerrel összhangban kell feloldani. A DNS-nevek feloldásához használhatja az olyan eszközöket, mint az **nslookup** vagy **az dig.**

Az alábbi példák segítenek a Traffic Manager-profil tesztelésében.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>A Traffic Manager-profil ellenőrzése az nslookup és az ipconfig használatával a Windows rendszerben

1. Nyisson meg egy parancsot vagy a Windows PowerShell-parancssort rendszergazdaként.
2. Írja `ipconfig /flushdns` be a DNS-feloldó gyorsítótárának kiürítését.
3. Gépelje be: `nslookup <your Traffic Manager domain name>`. A következő parancs például a *myapp.contoso* előtaggal ellenőrzi a tartománynevet.

        nslookup myapp.contoso.trafficmanager.net

    Egy tipikus eredmény a következő információkat jeleníti meg:

    + A Traffic Manager tartománynév feloldásához elért DNS-kiszolgáló DNS-neve és IP-címe.
    + Az "nslookup" után a parancssorba beírt Traffic Manager tartománynév és az az IP-cím, amelyre a Traffic Manager tartomány feloldja. A második IP-cím a fontos, hogy ellenőrizze. Meg kell egyeznie egy nyilvános virtuális IP-cím (VIP) címet az egyik a felhőszolgáltatások vagy a traffic manager profil ban tesztelt.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>A feladatátvevő forgalom útválasztási módszerének tesztelése

1. Hagyja az összes végpontot felfelé.
2. Egyetlen ügyfél használatával kérjen DNS-feloldást a vállalati tartománynévhez az nslookup vagy egy hasonló segédprogram használatával.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik az elsődleges végpont.
4. Hozza le az elsődleges végpontot, vagy távolítsa el a figyelési fájlt, hogy a Traffic Manager úgy gondolja, hogy az alkalmazás nem érhető el.
5. Várjon a Traffic Manager-profil DNS-idő (TTL) és további két perc után. Ha például a DNS TTL 300 másodperc (5 perc), hét percet kell várnia.
6. Ürítse ki a DNS-ügyfél gyorsítótárát, és kérje a DNS-feloldást az nslookup használatával. A Windows rendszerben az ipconfig /flushdns paranccsal kiürítheti a DNS-gyorsítótárat.
7. Győződjön meg arról, hogy a feloldott IP-cím megegyezik a másodlagos végpont.
8. Ismételje meg a folyamatot, és minden végpontot lefelé. Ellenőrizze, hogy a DNS a lista következő végpontjának IP-címét adja-e vissza. Ha az összes végpont nem, be kell szereznie az elsődleges végpont IP-címét.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>A súlyozott forgalom-útválasztási módszer tesztelése

1. Hagyja az összes végpontot felfelé.
2. Egyetlen ügyfél használatával kérjen DNS-feloldást a vállalati tartománynévhez az nslookup vagy egy hasonló segédprogram használatával.
3. Győződjön meg arról, hogy a feloldott IP-cím megegyezik az egyik végpontjával.
4. Ürítse ki a DNS-ügyfél gyorsítótárát, és ismételje meg a 2. Meg kell jelennie a különböző IP-címek vissza az egyes végpontok.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>A teljesítményforgalom-útválasztási módszer tesztelése

A teljesítményforgalom-útválasztási módszer hatékony teszteléséhez a világ különböző részein található ügyfeleknek kell rendelkeznie. A szolgáltatások teszteléséhez használható különböző Azure-régiókban hozhat létre ügyfeleket. Ha globális hálózattal rendelkezik, távolról bejelentkezhet a világ más részein lévő ügyfelekre, és onnan futtathatja a teszteket.

Alternatív megoldásként, vannak szabad pókháló- kiindulópontul szolgáló DNS keres és ás szolgáltatás elérhető. Néhány ilyen eszköz lehetővé teszi a DNS-névfelbontás ellenőrzését a világ különböző pontjairól. Keressen rá a "DNS-keresésre" a példákért. A külső szolgáltatások, például a Gomez vagy a Keynote segítségével ellenőrizhetik, hogy a profilok a várt módon forgalmaznak-e forgalmat.

## <a name="next-steps"></a>További lépések

* [A Traffic Manager forgalomirányítási módszerei](traffic-manager-routing-methods.md)
* [A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
* [Hibaelhárítás a Traffic Manager „Csökkentett teljesítményű” állapota esetén](traffic-manager-troubleshooting-degraded.md)
