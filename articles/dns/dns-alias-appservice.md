---
title: Elosztott terhelésű Azure Web Apps a zóna csúcsán
description: Azure DNS alias rekord használata elosztott terhelésű webalkalmazások üzemeltetéséhez a zóna csúcsán
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 7d20ef750aa4556a73852982631423d3d08271f5
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854116"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Elosztott terhelésű Azure Web Apps a zóna csúcsán

A DNS protokoll megakadályozza, hogy a zóna csúcsán lévő egy vagy AAAA típusú rekord ne legyen hozzárendelve. Egy példa zóna csúcsa a contoso.com. Ez a korlátozás olyan alkalmazás-tulajdonosokkal kapcsolatos problémát jelent, akik Traffic Manager mögötti elosztott terhelésű alkalmazásokkal rendelkeznek. Nem lehetséges a zóna APEX-rekord Traffic Manager profiljára mutatni. Ennek eredményeképpen az alkalmazás tulajdonosai megkerülő megoldást kell használniuk. Az alkalmazási réteg átirányítását át kell irányítani a zóna csúcsáról egy másik tartományba. Ilyen például a contoso.com és a www\.contoso.com közötti átirányítás. Ez a megoldás az átirányítási függvény egyetlen meghibásodási pontját mutatja be.

Alias-rekordokkal ez a probléma már nem létezik. Az alkalmazások tulajdonosai a zóna csúcspont-rekordját egy olyan Traffic Manager-profilra helyezhetik, amely külső végpontokkal rendelkezik. Az alkalmazások tulajdonosai ugyanarra a Traffic Manager-profilra mutatnak, amelyet a DNS-zónán belüli bármely más tartományhoz is használnak.

Például a contoso.com és a www\.contoso.com ugyanarra a Traffic Manager profilra mutathat. Ebben az esetben, ha a Traffic Manager profilhoz csak külső végpontok vannak konfigurálva.

Ebből a cikkből megtudhatja, hogyan hozhat létre alias-rekordot a tartományhoz, és hogyan konfigurálhatja Traffic Manager profiljának végpontját a webalkalmazásokhoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

A tartomány Azure DNSban történő üzemeltetésével kapcsolatos utasításokért [lásd: oktatóanyag: A tartomány üzemeltetése Azure DNS](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de Ön használja a saját tartománynevét.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, amely az ebben a cikkben használt összes erőforrást tárolni fogja.

## <a name="create-app-service-plans"></a>App Service csomagok létrehozása

Hozzon létre két webes App Service sémát az erőforráscsoporthoz az alábbi táblázat alapján a konfigurációs információkhoz. App Service terv létrehozásával kapcsolatos további információkért lásd: [app Service-terv kezelése az Azure-ban](../app-service/app-service-plan-manage.md).


|Name (Név)  |Operációs rendszer  |Location  |Tarifacsomag  |
|---------|---------|---------|---------|
|ASP-01     |Windows|East US|Fejlesztési/tesztelési D1 – közös|
|ASP-02     |Windows|USA középső régiója|Fejlesztési/tesztelési D1 – közös|

## <a name="create-app-services"></a>App Services létrehozása

Hozzon létre két webalkalmazást, egyet az egyes App Service-csomagokban.

1. Az Azure Portal lap bal felső sarkában kattintson az **erőforrás létrehozása**elemre.
2. Írja  be a webalkalmazás kifejezést a keresősávba, majd nyomja le az ENTER billentyűt.
3. Kattintson a webalkalmazás elemre.
4. Kattintson a **Create** (Létrehozás) gombra.
5. Fogadja el az alapértelmezett értékeket, és a következő táblázat segítségével konfigurálja a két webalkalmazást:

   |Name (Név)<br>(a. azurewebsites.net belül egyedinek kell lennie)|Erőforráscsoport |App Service csomag/hely
   |---------|---------|---------|
   |App-01|Meglévő használata<br>Válassza ki az erőforráscsoportot|ASP-01 (az USA keleti régiója)|
   |App-02|Meglévő használata<br>Válassza ki az erőforráscsoportot|ASP-02 (USA középső régiója)|

### <a name="gather-some-details"></a>Részletek összegyűjtése

Most fel kell jegyeznie az alkalmazások IP-címét és állomásnevét.

1. Nyissa meg az erőforráscsoportot, és kattintson az első alkalmazásra (ebben a példában az**app-01** elemre).
2. A bal oldali oszlopban kattintson a **Tulajdonságok**elemre.
3. Jegyezze fel az **URL**-cím alatti címet, és a **kimenő IP-címek** területen jegyezze fel a lista első IP-címét. Ezt az információt később a Traffic Manager végpontok konfigurálásakor fogja használni.
4. Ismételje meg az **app-02-** et.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt az erőforráscsoporthoz. Használja az alapértelmezett értékeket, és adjon meg egy egyedi nevet a trafficmanager.net névtérben.

Traffic Manager profil létrehozásával kapcsolatos információkért lásd: gyors [útmutató: Hozzon létre egy Traffic Manager profilt egy magasan elérhető webalkalmazáshoz](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Végpontok létrehozása

Most már létrehozhatja a két webalkalmazáshoz tartozó végpontokat.

1. Nyissa meg az erőforráscsoportot, és kattintson a Traffic Manager profilra.
2. A bal oldali oszlopban kattintson a **végpontok**elemre.
3. Kattintson a **Hozzáadás**lehetőségre.
4. A végpontok konfigurálásához használja a következő táblázatot:

   |Type  |Name (Név)  |Target  |Location  |Egyéni fejléc beállításai|
   |---------|---------|---------|---------|---------|
   |Külső végpont     |End-01|Az App-01-ben rögzített IP-cím|East US|gazdagép:\<az App-01-hez rögzített URL-cím\><br>Példa: **Host: app-01.azurewebsites.net**|
   |Külső végpont     |End-02|Az App-02-hez rögzített IP-cím|USA középső régiója|gazdagép:\<az App-02-hez rögzített URL-cím\><br>Példa: **Host: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zóna létrehozása

Egy meglévő DNS-zónát is használhat teszteléshez, vagy létrehozhat egy új zónát is. Új DNS-zóna létrehozásához és delegálásához az Azure [-ban: oktatóanyag: A tartomány üzemeltetése Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Az alias-rekord hozzáadása

Ha a DNS-zóna elkészült, hozzáadhat egy alias-rekordot a zóna csúcsához.

1. Nyissa meg az erőforráscsoportot, és kattintson a DNS-zónára.
2. Kattintson a **Rekordhalmaz** gombra.
3. Adja hozzá a rekordot a következő táblázat használatával:

   |Name (Név)  |Type  |Alias-rekord készlete  |Alias típusa  |Azure-erőforrás|
   |---------|---------|---------|---------|-----|
   |@     |A|Igen|Azure-erőforrás|Traffic Manager – a profil|

## <a name="add-custom-hostnames"></a>Egyéni állomásnevek hozzáadása

Egyéni állomásnév hozzáadása a web appshez.

1. Nyissa meg az erőforráscsoportot, és kattintson az első webalkalmazásra.
2. A bal oldali oszlopban kattintson az **Egyéni tartományok**elemre.
3. Kattintson az **állomásnév hozzáadása**lehetőségre.
4. A hostname (állomásnév) területen írja be a tartomány nevét. Például: contoso.com.

   A tartománynak át kell adnia az érvényesítést, és zöld pipa jeleket kell megadnia az **állomásnév elérhetősége** és a **tartomány tulajdonjoga**mellett.
5. Kattintson az **állomásnév hozzáadása**lehetőségre.
6. Ha meg szeretné tekinteni az új gazdagépet a **helyhez rendelt állomásnevek**alatt, frissítse a böngészőt. A lapon lévő frissítés nem mindig azonnal mutatja a módosításokat.
7. Ismételje meg ezt az eljárást a második webalkalmazás esetében.

## <a name="test-your-web-apps"></a>Webes alkalmazások tesztelése

Most már ellenőrizheti, hogy elérhető-e a webalkalmazás, és hogy terheléselosztás alatt áll-e.

1. Nyisson meg egy webböngészőt, és keresse meg a tartományt. Például: contoso.com. Ekkor megjelenik az alapértelmezett webalkalmazás lap.
2. Állítsa le az első webalkalmazást.
3. Zárjunk be egy webböngészőt, és várjon néhány percet.
4. Indítsa el a böngészőt, és keresse meg a tartományt. Az alapértelmezett webalkalmazás lap továbbra is megjelenik.
5. Állítsa le a második webalkalmazást.
6. Zárjunk be egy webböngészőt, és várjon néhány percet.
7. Indítsa el a böngészőt, és keresse meg a tartományt. A 403-es hiba jelenik meg, amely azt jelzi, hogy a webalkalmazás leáll.
8. Indítsa el a második webalkalmazást.
9. Zárjunk be egy webböngészőt, és várjon néhány percet.
10. Indítsa el a böngészőt, és keresse meg a tartományt. Ekkor meg kell jelennie az alapértelmezett webalkalmazás-oldalnak.

## <a name="next-steps"></a>További lépések

Az alias-rekordokkal kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Oktatóanyag: Alias-rekord konfigurálása egy Azure nyilvános IP-címre való hivatkozáshoz](tutorial-alias-pip.md)
- [Oktatóanyag: Alias-rekord konfigurálása a APEX tartománynevek támogatásához Traffic Manager](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Az aktív DNS-nevek áttelepítésének megismeréséhez tekintse meg [az aktív DNS-név](../app-service/manage-custom-dns-migrate-domain.md)áttelepítését Azure app Servicere című témakört.
