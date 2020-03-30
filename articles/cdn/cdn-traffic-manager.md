---
title: Feladatátvétel több Azure CDN-végponton a Traffic Manager rel
description: Ismerje meg, hogyan állíthatja be az Azure Traffic Managert az Azure CDN-végpontokkal.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083002"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Feladatátvétel beállítása több Azure CDN-végpontközött az Azure Traffic Managerrel

Az Azure Content Delivery Network (CDN) konfigurálásakor kiválaszthatja az igényeinek megfelelő optimális szolgáltatót és tarifacsomagot. Az Azure CDN globálisan elosztott infrastruktúrájával alapértelmezés szerint helyi és földrajzi redundanciát és globális terheléselosztást hoz létre a szolgáltatás rendelkezésre állásának és teljesítményének javítása érdekében. Ha egy hely nem érhető el a tartalom kiszolgálására, a kérelmek automatikusan egy másik helyre kerülnek, és az optimális POP-ot (az ügyfélkérelmek helyigénye és kiszolgálóterhelése alapján) használja a rendszer. 
 
Ha több CDN-profillal rendelkezik, az Azure Traffic Manager segítségével tovább javíthatja a rendelkezésre állást és a teljesítményt. Az Azure Traffic Manager és az Azure CDN segítségével több CDN-végpont között töltheti be a feladatátvételt, a geoterhelés-elosztást és más forgatókönyveket. Egy tipikus feladatátvételi forgatókönyv esetén az összes ügyfélkérelem először az elsődleges CDN-profilra lesz irányítva; ha a profil nem érhető el, a kérelmeket a rendszer továbbítja a másodlagos CDN-profilnak, amíg az elsődleges CDN-profil újra online állapotba nem kerül. Az Azure Traffic Manager ily módon biztosítja, hogy a webalkalmazás mindig elérhető legyen. 

Ez a cikk útmutatást és egy példát, hogyan állíthatja be a feladatátvételt az **Azure CDN Standard a Verizon** és az Azure **CDN Standard Akamai profilok.**

## <a name="set-up-azure-cdn"></a>Az Azure CDN beállítása 
Hozzon létre két vagy több Azure CDN-profilt és végpontot különböző szolgáltatókkal.

1. Hozzon létre egy **Azure CDN standardot a Verizonból** és **az Azure CDN Standardot az Akamai-profilból** az Új [CDN-profil létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-profile)című részben leírt lépések végrehajtásával.
 
   ![CDN több profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Az új profilok mindegyikében hozzon létre legalább egy végpontot az [Új CDN-végpont létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)című részben leírt lépéseket követve.

## <a name="set-up-azure-traffic-manager"></a>Az Azure Traffic Manager beállítása
Hozzon létre egy Azure Traffic Manager-profilt, és állítsa be a terheléselosztást a CDN-végpontok között. 

1. Hozzon létre egy Azure Traffic Manager-profilt a [Traffic Manager-profil létrehozása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)című részben leírt lépésekkel. 

    Az **Útválasztás módszer csoportban**válassza a **Prioritás**lehetőséget.

2. Adja hozzá a CDN-végpontokat a Traffic Manager-profilhoz a [Traffic Manager végpontok hozzáadása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) című részben leírt lépések végrehajtásával

    A **Típus mezőben**válassza **a Külső végpontok**lehetőséget. A **Prioritás mezőbe**írjon be egy számot.

    Hozzon létre például *cdndemo101akamai.azureedge.net* *1-es* és *cdndemo101verizon.azureedge.net* prioritással, *2-es*prioritással.

   ![CDN traffic manager végpontok](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Egyéni tartomány beállítása az Azure CDN-en és az Azure Traffic Managerben
A CDN- és Traffic Manager-profilok beállítása után kövesse az alábbi lépéseket a DNS-hozzárendelés hozzáadásához és egyéni tartomány regisztrálásához a CDN-végpontokhoz. Ebben a példában az egyéni domain név *cdndemo101.dustydogpetcare.online*.

1. Nyissa meg az egyéni tartomány tartományszolgáltatójának (például a GoDaddynek a webhelyét), és hozzon létre két DNS CNAME bejegyzést. 

    a. Az első CNAME bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartománnyal a CDN-végponthoz. Ez a bejegyzés egy szükséges lépés az egyéni tartomány regisztrálásához a Traffic Managerhez a 2.

      Példa: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. A második CNAME bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartomány nélkül a CDN-végponthoz. Ez a bejegyzés leképezi az egyéni tartományt a Traffic Manager számára. 

      Példa: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Ha a tartomány jelenleg él, és nem lehet megszakítani, ezt a lépést utoljára. Ellenőrizze, hogy a CDN-végpontok és a forgalomkezelő tartományok élnek-e, mielőtt az egyéni tartomány DNS-ét Traffic Manager re frissítené.
    >


2.  Az Azure CDN-profilból válassza ki az első CDN-végpontot (Akamai). Válassza **az Egyéni tartomány hozzáadása** és a *cdndemo101.dustydogpetcare.online bevitele lehetőséget.* Ellenőrizze, hogy az egyéni tartomány érvényesítéséhez pipa zöld-e. 

    Az Azure CDN a *cdnverify* altartományt használja a DNS-hozzárendelés érvényesítéséhez a regisztrációs folyamat befejezéséhez. További információt a [CNAME DNS-rekord létrehozása](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)című témakörben talál. Ez a lépés lehetővé teszi, hogy az Azure CDN ismerje fel az egyéni tartományt, hogy válaszolhassa meg a kéréseket.
    
    > [!NOTE]
    > Az SSL engedélyezéséhez egy **Akamai-profilokból származó Azure CDN-en** közvetlenül meg kell neveznie az egyéni tartományt a végpontra. az SSL engedélyezéséhez készült cdnverify még nem támogatott. 
    >

3.  Térjen vissza az egyéni tartomány tartományszolgáltatójának webhelyére, és frissítse az első létrehozott DNS-hozzárendelést, hogy az egyéni tartomány le képezve a második CDN-végpontra.
                             
    Példa: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Az Azure CDN-profiljából válassza ki a második CDN-végpontot (Verizon), és ismételje meg a 2. Válassza **az Egyéni tartomány hozzáadása**lehetőséget, és adja meg a *cdndemo101.dustydogpetcare.online parancsot.*
 
Miután elvégezte ezeket a lépéseket, a feladatátvételi képességekkel rendelkező több CDN-szolgáltatás be van állítva az Azure Traffic Manager használatával. A teszt URL-címeit az egyéni tartományból érheti el. A funkció teszteléséhez tiltsa le az elsődleges CDN-végpontot, és ellenőrizze, hogy a kérelem megfelelően került-e át a másodlagos CDN-végpontba. 

## <a name="next-steps"></a>További lépések
Más útválasztási módszereket is beállíthat, például a földrajzi, hogy kiegyensúlyozza a terhelést a különböző CDN-végpontok között. További információ: [A földrajzi forgalomútválasztási módszer konfigurálása a Traffic Manager használatával](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)című témakörben talál.



