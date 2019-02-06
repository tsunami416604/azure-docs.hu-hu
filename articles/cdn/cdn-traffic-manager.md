---
title: Hozza létre a feladatátvétel több Azure CDN-végpontok Azure Traffic Managerrel |} A Microsoft Docs
description: Ismerje fel az Azure Traffic Manager beállítása az Azure CDN-végpontok.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 4c072ef63c0d4961fba695fc8d9be1d12b4b0e8b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749214"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Hozza létre a feladatátvétel több Azure CDN-végpontok Azure Traffic Managerrel

Amikor konfigurál az Azure Content Delivery Network (CDN), az optimális szolgáltató és a tarifacsomag kiválaszthatja az igényeinek. Az Azure CDN a globálisan elosztott infrastruktúrával alapértelmezés szerint hoz létre a helyi és földrajzi redundancia és a terhelés globális kiegyenlítéséhez szolgáltatás elérhetőségének és teljesítményének javítása érdekében. Ha egy helyen nem érhető el a tartalmat, automatikusan irányítja a kérelmeket az egy másik helyre, és az optimális POP (a kérelmek helyét és a kiszolgáló által generált terhelést, olyan tényezők alapján) használatos minden ügyfél kérelem kiszolgálására. 
 
Ha több CDN-profilt, tovább növelheti rendelkezésre állását és teljesítményét az Azure Traffic Managerrel. Az Azure CDN-nel az Azure Traffic Manager segítségével terheléselosztást, több CDN-végpontok közötti feladatátvétel, georedundáns betöltési terheléselosztási és egyéb forgatókönyvek. Egy tipikus feladatátvételi esetben összes ügyfélkéréseket a rendszer először átirányítja az elsődleges CDN-profil; a profil nem érhető el, ha kérések majd pedig továbbítva lesznek a másodlagos CDN-profilra mindaddig, amíg az elsődleges CDN-profil újra online állapotba kerül. Az Azure Traffic Managerrel ily módon biztosítható a webes alkalmazás mindig elérhető legyen. 

Ez a cikk útmutatást és a példa bemutatja, hogyan állíthatja be a feladatátvételi **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profilok.

## <a name="set-up-azure-cdn"></a>Az Azure CDN beállítása 
Hozzon létre két vagy több Azure CDN-profilok és a végpontokat különböző szolgáltatók.

1. Hozzon létre egy **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profil a lépéseket követve [hozzon létre egy új CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN több profilt](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Minden új profil létrehozásához legalább egy végpontot a lépéseket követve [hozzon létre egy új CDN-végpont](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Állítsa be az Azure Traffic Manager
Hozzon létre egy Azure Traffic Manager-profilt, és terheléselosztást biztosít a CDN-végpontok beállítása. 

1. Az Azure Traffic Manager-profilok létrehozása a lépéseket követve [Traffic Manager-profil létrehozása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    A **útválasztási módszer**válassza **prioritású**.

2. A Traffic Manager-profil a CDN-végpontok lépéseit követve adja hozzá [hozzáadása Traffic Manager-végpontok](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    A **típus**válassza **külső végpontokat**. A **prioritású**, adjon meg egy számot.

    Hozzon létre például *cdndemo101akamai.azureedge.net* -as prioritású *1* és *cdndemo101verizon.azureedge.net* -as prioritású *2*.

   ![CDN traffic manager-végpontot](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Az Azure Traffic Manager és az Azure CDN egyéni tartomány beállítása
Miután beállította a CDN és a Traffic Manager-profilok, kövesse az alábbi lépéseket, adja hozzá a DNS-hozzárendelést, és regisztrálni az egyéni tartomány a CDN-végpontok. Ebben a példában az egyéni tartománynév van *cdndemo101.dustydogpetcare.online*.

1. Az egyéni tartomány, például a GoDaddy, a tartomány szolgáltató nyissa meg a webhely, és hozzon létre két DNS CNAME-bejegyzést. 

    a. Az első CNAME bejegyzés a cdnverify altartománnyal az egyéni tartomány leképezése a CDN-végponthoz. Ez a bejegyzés regisztrálni az egyéni tartomány a CDN-végponthoz, amely a Traffic Manager a 2. lépésben hozzáadott lépésre szükség.

      Példa: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. A második CNAME bejegyzést anélkül, hogy a cdnverify altartománnyal együtt, az egyéni tartomány leképezése a CDN-végponthoz. Ez a bejegyzés leképezi az egyéni tartomány átirányítása a Traffic Managerhez. 

      Példa: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Ha a tartomány jelenleg működőképes, és nem lehet megszakítani, utolsó hajtsa végre ezt a lépést. Ellenőrizze, hogy a CDN-végpontok és a traffic manager tartományok élő az egyéni tartomány DNS, a Traffic Manager frissítése előtt.
    >


2.  Az Azure CDN-profilt válassza ki az első CDN-végpont (Akamai). Válassza ki **egyéni tartomány hozzáadása** és bemeneti *cdndemo101akamai.azureedge.net*. Ellenőrizze, hogy zöld pipa az egyéni tartomány ellenőrzése. 

    Az Azure CDN-t használ a *cdnverify* altartomány érvényesítése a DNS-hozzárendelést a regisztrációs folyamat befejezéséhez. További információkért lásd: [hozzon létre egy CNAME DNS-rekord](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ez a lépés lehetővé teszi, hogy az Azure CDN-t az egyéni tartomány felismerje, hogy a kérelmek válaszolni tud.

3.  Térjen vissza a webhely számára az egyéni tartomány tartományszolgáltatójának, és úgy, hogy az egyéni tartomány le van képezve a második CDN-végpontra létrehozott első DNS-hozzárendelést frissítenie.
                             
    Példa: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Válassza ki a második CDN-végpont (Verizon) az Azure CDN-profilt, és ismételje meg a 2. lépés. Válassza ki **egyéni tartomány hozzáadása**, és a bemeneti *cdndemo101akamai.azureedge.net*.
 
Miután végrehajtotta ezeket a lépéseket, a feladatátvételt is használhat több CDN-t szolgáltatás beállítása az Azure Traffic Managerrel. Fogja tudni elérni a teszt az egyéni tartomány URL-címei. A működésének teszteléséhez tiltsa le az elsődleges CDN-végponthoz, és győződjön meg arról, hogy a kérelem van megfelelően átkerül a másodlagos CDN-végpont. 

## <a name="next-steps"></a>További lépések
Is beállíthat más útválasztási módszerek, például a földrajzi, a különböző CDN-végpontok közötti terhelés kiegyenlítése érdekében. További információkért lásd: [a földrajzi forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



