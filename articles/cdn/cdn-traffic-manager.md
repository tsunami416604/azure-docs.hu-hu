---
title: Feladatátvétel több Azure CDN végpont között Traffic Manager
description: Ismerje meg, hogyan állíthatja be az Azure Traffic Manager Azure CDN-végpontokkal.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: d557637815036fa49e83f1d11a948f264d493321
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888663"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Feladatátvétel beállítása több Azure CDN végpont között az Azure-ban Traffic Manager

Az Azure Content Delivery Network (CDN) konfigurálásakor kiválaszthatja az igényeinek megfelelő optimális szolgáltatói és díjszabási szintet. A globálisan elosztott infrastruktúrával Azure CDN alapértelmezés szerint helyi és földrajzi redundanciát és globális terheléselosztást hoz létre a szolgáltatás rendelkezésre állásának és teljesítményének javítása érdekében. Ha egy hely nem érhető el a tartalom kiszolgálásához, a rendszer automatikusan átirányítja a kéréseket egy másik helyre, és az optimális POP-ot (a kérelmek helye és a kiszolgáló terhelése alapján) használja az egyes ügyfelek kéréseinek kiszolgálására. 
 
Ha több CDN-profillal rendelkezik, tovább növelheti a rendelkezésre állást és a teljesítményt az Azure Traffic Manager használatával. Az Azure Traffic Manager és az Azure CDN használatával terheléselosztást hajthat végre több CDN-végpont között a feladatátvételhez, a Geo-terheléselosztáshoz és más forgatókönyvekhez. Egy tipikus feladatátvételi forgatókönyvben az összes ügyfél-kérést először az elsődleges CDN-profilra irányítja a rendszer. Ha a profil nem érhető el, akkor a rendszer addig továbbítja a kéréseket a másodlagos CDN-profilhoz, amíg az elsődleges CDN-profil újra online állapotba nem kerül. Az Azure Traffic Manager így biztosíthatja, hogy a webalkalmazás mindig elérhető legyen. 

Ez a cikk útmutatást és példát mutat be arra vonatkozóan, hogyan állíthatja be a **Azure CDN standard szintű** feladatátvételt a Verizon és a **Azure CDN standard Akamai-** profilokból.

## <a name="set-up-azure-cdn"></a>Azure CDN beállítása 
Hozzon létre két vagy több Azure CDN-profilt és-végpontot különböző szolgáltatókkal.

1. Az [új CDN-profil létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-profile)című témakör lépéseit követve hozzon létre egy **Azure CDN standardot a Verizon** és **a Azure CDN standard Akamai** -profilból.
 
   ![CDN több profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Mindegyik új profilban hozzon létre legalább egy végpontot az [új CDN-végpont létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)című rész lépéseit követve.

## <a name="set-up-azure-traffic-manager"></a>Az Azure Traffic Manager beállítása
Hozzon létre egy Azure Traffic Manager-profilt, és állítsa be a terheléselosztást a CDN-végpontok között. 

1. Hozzon létre egy Azure Traffic Manager-profilt a [Traffic Manager profil létrehozása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)című témakör lépéseit követve. 

    Az **útválasztási módszernél**válassza a **Priority (prioritás**) lehetőséget.

2. Adja hozzá a CDN-végpontokat a Traffic Manager profilhoz a Traffic Manager- [végpontok hozzáadása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) című témakör lépéseit követve.

    A **Típus mezőben**válassza a **külső végpontok**lehetőséget. A **Priority (prioritás**) mezőben adjon meg egy számot.

    Hozzon létre például egy *1* és *cdndemo101verizon.azureedge.net* prioritással rendelkező *cdndemo101akamai.azureedge.net* a *2-es*prioritással.

   ![CDN Traffic Manager-végpontok](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Egyéni tartomány beállítása a Azure CDN és az Azure Traffic Manager
A CDN-és Traffic Manager-profilok beállítása után kövesse az alábbi lépéseket a DNS-hozzárendelés hozzáadásához és az egyéni tartomány regisztrálásához a CDN-végpontokon. Ebben a példában az Egyéni tartománynév a *cdndemo101. dustydogpetcare. online*.

1. Nyissa meg az egyéni tartomány (például GoDaddy) tartományi szolgáltatójának webhelyét, és hozzon létre két DNS CNAME-bejegyzést. 

    a. Az első CNAME-bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartományhoz a CDN-végponthoz. Ez a bejegyzés egy szükséges lépés ahhoz, hogy regisztrálja az egyéni tartományt ahhoz a CDN-végponthoz, amelyet a 2. lépésben Traffic Managerhoz adott hozzá.

      Például: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. A második CNAME-bejegyzéshez rendelje hozzá az egyéni tartományt a cdnverify altartomány nélkül a CDN-végponthoz. Ez a bejegyzés az egyéni tartományt az Traffic Managerra képezi le. 

      Például: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Ha a tartomány jelenleg élő, és nem lehet megszakítani, akkor hajtsa végre ezt a lépést. Győződjön meg arról, hogy a CDN-végpontok és a Traffic Manager-tartományok élőben vannak, mielőtt frissíti az egyéni tartomány DNS-ét Traffic Managerra.
    >


2.  Az Azure CDN-profilban válassza ki az első CDN-végpontot (Akamai). Válassza az **egyéni tartomány hozzáadása** és a bemeneti *cdndemo101. dustydogpetcare. online*lehetőséget. Győződjön meg arról, hogy az egyéni tartomány érvényesítése zöld színnel történik. 

    Azure CDN a *cdnverify* altartomány használatával ellenőrzi a DNS-hozzárendelést a regisztrációs folyamat befejezéséhez. További információ: [CNAME DNS-rekord létrehozása](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ez a lépés lehetővé teszi, hogy a Azure CDN felismerje az egyéni tartományt, hogy válaszoljon a kéréseire.
    
    > [!NOTE]
    > Ha engedélyezni szeretné a TLS-t a Akamai-profilokból **származó Azure CDN** , akkor az egyéni tartományt közvetlenül a végponthoz kell CNAME-re feltüntetni. a TLS engedélyezésének cdnverify még nem támogatott. 
    >

3.  Térjen vissza az egyéni tartomány tartományi szolgáltatójának webhelyéhez, és frissítse az elsőként létrehozott DNS-leképezést, hogy az egyéni tartomány a második CDN-végpontra legyen leképezve.
                             
    Például: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A Azure CDN profilban válassza a második CDN-végpontot (Verizon), és ismételje meg a 2. lépést. Válassza az **egyéni tartomány hozzáadása**lehetőséget, majd a bemeneti *cdndemo101. dustydogpetcare. online*elemet.
 
A lépések elvégzése után a többszörös CDN szolgáltatás a feladatátvételi képességekkel együtt be van állítva az Azure Traffic Manager. Elérheti az egyéni tartományának tesztelési URL-címeit. A funkció teszteléséhez tiltsa le az elsődleges CDN-végpontot, és ellenőrizze, hogy a kérés megfelelően át lett-e helyezve a másodlagos CDN-végpontra. 

## <a name="next-steps"></a>További lépések
Más útválasztási módszereket is beállíthat, például a földrajzi, a különböző CDN-végpontok közötti terhelés kiegyenlítéséhez. További információ: [a földrajzi forgalom útválasztási módszerének konfigurálása Traffic Manager használatával](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



