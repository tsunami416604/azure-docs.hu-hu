---
title: Hozza létre a feladatátvétel több Azure CDN-végpontok Azure Traffic Managerben |} Microsoft Docs
description: További tudnivalók az Azure CDN-végpontok Azure Traffic Manager mentése értékre.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: b52cad1f32cc3d16cf70bb81640dcb1d9f8614bf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132939"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Hozza létre a feladatátvétel több Azure CDN-végpontok Azure Traffic Manager

Amikor konfigurál az Azure Content Delivery Network (CDN), az optimális szolgáltató és a tarifacsomag kiválaszthatja az igényeinek. Az Azure CDN annak globálisan elosztott infrastruktúra alapértelmezés szerint hoz létre a helyi és földrajzi redundancia és a globális szolgáltatás rendelkezésre állásának és teljesítményének javítása érdekében. Ha egy helyen nem érhető el a tartalmat, rendszer kérést automatikusan átirányítja egy másik helyre, és a (kérelem helyét és a kiszolgáló terhelés tényezőket alapján) a optimális POP használ egyes ügyfél-kérelmek kiszolgálására. 
 
Ha több CDN-profilra, tovább növelhető a rendelkezésre állás és teljesítmény az Azure Traffic Manager. Azure Traffic Manager Azure CDN segítségével terheléselosztásához, több CDN-végpontok közötti feladatátvétel, terheléselosztás és egyéb forgatókönyvek földrajzi-terhelés. Tipikus feladatátvételi konfigurációban összes ügyfélkéréseket a rendszer először átirányítja az elsődleges CDN-profil; Ha a profil nem érhető el, kérelmek majd továbbítódnak a másodlagos CDN-profil csak elsődleges CDN-profil újra online állapotba kerül. Azure Traffic Manager ily módon biztosítja a webes alkalmazás mindig elérhető. 

Ez a cikk nyújt útmutatást és példa bemutatja, hogyan állíthatja be a feladatátvevő **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok.

## <a name="set-up-azure-cdn"></a>Azure CDN beállítása 
Hozzon létre két vagy több Azure CDN-profil és a végpontok különböző szolgáltatók.

1. Hozzon létre egy **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** lépéseit követve profil [hozzon létre egy új CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN több profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Minden egyes új profilt, hozzon létre legalább egy végpontot lépéseit [hozzon létre egy új CDN-végpont](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Állítsa be a Azure Traffic Manager
Hozzon létre egy Azure Traffic Manager-profilt, és a CDN-végpontokat terheléselosztásának beállítása. 

1. A lépések az Azure Traffic Manager-profil létrehozása [Traffic Manager-profil létrehozása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1). 

    A **útválasztási módszer**, jelölje be **prioritás**.

2. A lépéseket követve adja hozzá a CDN-végpontokat a Traffic Manager-profil [adja hozzá a Traffic Manager-végpontok](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    A **típus**, jelölje be **külső végpontok száma**. A **prioritás**, adjon meg egy számot.

    Hozzon létre például *cdndemo101akamai.azureedge.net* prioritással *1* és *cdndemo101verizon.azureedge.net* prioritással *2*.

   ![CDN traffic manager-végpontok](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Az Azure CDN-ről és az Azure Traffic Manager egyéni tartomány beállításához
Miután beállította a CDN és a Traffic Manager-profilokat, kövesse az alábbi lépéseket a DNS-hozzárendelést, és regisztrálhatja a CDN-végpontok egyéni tartományt. Az egyéni tartománynév megadása ehhez a példához *cdndemo101.dustydogpetcare.online*.

1. Nyissa meg a webhely az egyéni tartomány, például a GoDaddy, tartomány-szolgáltatóhoz, és hozzon létre két DNS CNAME bejegyzéseket. 

    a. Az első CNAME-bejegyzés rendelje az egyéni tartomány cdnverify altartomány a CDN-végpont. Ez a bejegyzés szükséges lépése az egyéni tartomány felvétele a Traffic Manager 2. lépésben hozzáadott a CDN-végpont regisztrálása.

      Példa: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. CDN-végpont cdnverify altartomány nélkül az egyéni tartomány leképezése a második CNAME-bejegyzést. Ez a bejegyzés az egyéni tartomány felvétele a Traffic Manager képezi le. 

      Példa: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Ha a tartomány jelenleg élő, és nem lehet megszakítani, ehhez a lépéshez utolsó. Ellenőrizze, hogy a CDN-végpontokat és a traffic manager tartományok élő az egyéni tartomány DNS felvétele a Traffic Manager frissítése előtt.
    >


2.  Azure CDN-profil válassza ki az első CDN-végpont (Akamai). Válassza ki **egyéni tartomány hozzáadása** és bemeneti *cdndemo101akamai.azureedge.net*. Győződjön meg arról, hogy zöld a jelet az egyéni tartomány ellenőrzéséhez. 

    Az Azure CDN használja a *cdnverify* altartomány érvényesítése a DNS-hozzárendelése a regisztrációs folyamat befejezéséhez. További információkért lásd: [hozzon létre egy CNAME DNS rekord](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ez a lépés lehetővé teszi, hogy az Azure CDN ismeri fel az egyéni tartomány, így a kérelmek válaszolhat.

3.  A tartomány-szolgáltató az egyéni tartomány térjen vissza a webhely, és frissítse az, hogy az egyéni tartomány van leképezve a második CDN-végpontot létrehozott első Rendelje hozzá.
                             
    Példa: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Az Azure CDN-profilt válassza ki a második CDN-végpont (Verizon), és ismételje meg a 2. Válassza ki **egyéni tartomány hozzáadása**, és a bemeneti *cdndemo101akamai.azureedge.net*.
 
Miután elvégezte ezeket a lépéseket, a feladatátvételi képességekkel multi-CDN szolgáltatás be van állítva az Azure Traffic Manager. A vizsgálat elérésére képes lesz az egyéni tartomány URL-címei. A működésének teszteléséhez tiltsa le az elsődleges CDN-végpontot, és győződjön meg arról, hogy a kérés van megfelelően átkerül a másodlagos CDN-végpontot. 

## <a name="next-steps"></a>További lépések
Is beállíthat más útválasztási módszerrel, például a földrajzi, különböző CDN-végpontok között a terhelés kiegyenlítése érdekében. További információkért lásd: [konfigurálása a földrajzi forgalom-útválasztási módszert a Traffic Managerrel](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



