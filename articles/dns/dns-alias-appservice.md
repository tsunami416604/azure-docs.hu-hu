---
title: A terheléselosztással elhatárolt Azure-webalkalmazások üzemeltetése a zóna csúcsán
description: Azure DNS-aliasrekord használata a terheléselosztásos webalkalmazások üzemeltetéséhez a zóna csúcsán
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937365"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>A terheléselosztással elhatárolt Azure-webalkalmazások üzemeltetése a zóna csúcsán

A DNS-protokoll megakadályozza, hogy a zóna csúcsán ne adjon le más, mint egy A vagy AAAA rekordot. Egy példa zóna csúcsa contoso.com. Ez a korlátozás problémát jelent az alkalmazástulajdonosok számára, akik terheléselosztásos alkalmazásokkal rendelkeznek a Traffic Manager mögött. A Traffic Manager-profilra nem lehet rámutatni a zóna csúcsrekordjából. Ennek eredményeképpen az alkalmazástulajdonosoknak egy kerülő megoldást kell használniuk. Az alkalmazásrétegátirányításnak át kell irányítania a zóna csúcsáról egy másik tartományba. Erre példa a contoso.com-ról\.a www contoso.com-ra történő átirányítás. Ez a megállapodás egyetlen meghibásodási pontot jelent az átirányítási függvényhez.

Aliasrekordok nál ez a probléma már nem áll fenn. Most antól az alkalmazástulajdonosok a zónacsúcsrekordjukat egy külső végponttal rendelkező Traffic Manager-profilra irányíthatják. Az alkalmazástulajdonosok rámutathatnak arra a Traffic Manager-profilra, amelyet a DNS-zónájuk bármely más tartományához használnak.

Például contoso.com és\.a www contoso.com ugyanarra a Traffic Manager-profilra mutathatnak. Ez a helyzet mindaddig, amíg a Traffic Manager-profil csak külső végpontok konfigurálva.

Ebből a cikkből megtudhatja, hogyan hozhat létre aliasrekordot a tartomány csúcspontjához, és hogyan konfigurálhatja a Traffic Manager-profil végpontjait a webalkalmazásokhoz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de Ön használja a saját tartománynevét.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a cikkben használt összes erőforrás tárolására.

## <a name="create-app-service-plans"></a>App-szolgáltatási csomagok létrehozása

Hozzon létre két Web App Service-sémát az erőforráscsoportban az alábbi táblázat segítségével a konfigurációs információkhoz. Az App Service-csomag létrehozásáról az [App Service-csomag kezelése az Azure-ban](../app-service/app-service-plan-manage.md)című témakörben talál további információt.


|Név  |Operációs rendszer  |Hely  |Tarifacsomag  |
|---------|---------|---------|---------|
|ASP-01     |Windows|USA keleti régiója|Fejlesztés/tesztelés D1-megosztva|
|ASP-02     |Windows|USA középső régiója|Fejlesztés/tesztelés D1-megosztva|

## <a name="create-app-services"></a>Alkalmazásszolgáltatások létrehozása

Hozzon létre két webalkalmazást, egyet-egyet az Egyes App Service-csomagban.

1. Az Azure Portal lap bal felső sarkában válassza az **Erőforrás létrehozása lehetőséget.**
2. Írja be a **Webapp kifejezést** a keresősávba, és nyomja le az Enter billentyűt.
3. Válassza a **Web App lehetőséget**.
4. Kattintson a **Létrehozás** gombra.
5. Fogadja el az alapértelmezett beállításokat, és az alábbi táblázat segítségével konfigurálja a két webalkalmazást:

   |Név<br>(egyedinek kell lennie a .azurewebsites.net)(unique within .azurewebsites.net)|Erőforráscsoport |Futtatókörnyezet verme|Régió|App Service-csomag/hely
   |---------|---------|-|-|-------|
   |Alkalmazás-01|Meglévő használata<br>Az erőforráscsoport kiválasztása|.NET Core 2.2|USA keleti régiója|ASP-01(D1)|
   |Alkalmazás-02|Meglévő használata<br>Az erőforráscsoport kiválasztása|.NET Core 2.2|USA középső régiója|ASP-02(D1)|

### <a name="gather-some-details"></a>Gyűjtsön össze néhány részletet

Most meg kell jegyeznie a webalkalmazások IP-címét és állomásnevét.

1. Nyissa meg az erőforráscsoportot, és válassza ki az első webalkalmazást (Ebben a példában az**App-01).**
2. A bal oldali oszlopban válassza a **Tulajdonságok lehetőséget.**
3. Vegye figyelembe az **URL alatt**található címet, a **Kimenő IP-címek** csoportban pedig jegyezze fel a lista első IP-címét. Ezt az információt később fogja használni, amikor konfigurálja a Traffic Manager végpontjait.
4. Ismételje meg a műveletet a **02-es alkalmazással.**

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt az erőforráscsoportban. Használja az alapértelmezett beállításokat, és írjon be egy egyedi nevet a trafficmanager.net névtérbe.

A Traffic Manager-profilok létrehozásáról további információt a [Rövid útmutató: Traffic Manager-profil létrehozása magas rendelkezésre állású webalkalmazáshoz](../traffic-manager/quickstart-create-traffic-manager-profile.md)című témakörben talál.

### <a name="create-endpoints"></a>Végpontok létrehozása

Most már létrehozhatja a két webalkalmazás végpontjait.

1. Nyissa meg az erőforráscsoportot, és válassza ki a Traffic Manager-profilt.
2. A bal oldali oszlopban válassza a **Végpontok**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A végpontok konfigurálásához használja az alábbi táblázatot:

   |Típus  |Név  |Cél  |Hely  |Egyéni fejléc beállításai|
   |---------|---------|---------|---------|---------|
   |Külső végpont     |01. vége|Az App-01-hez rögzített IP-cím|USA keleti régiója|host:\<az App-01-hez rögzített URL-cím\><br>Példa: **állomás:alkalmazás-01.azurewebsites.net**|
   |Külső végpont     |02. vége|Az App-02-hez rögzített IP-cím|USA középső régiója|host:\<az App-02-hez rögzített URL-cím\><br>Példa: **állomás:alkalmazás-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zóna létrehozása

A teszteléshez használhat egy meglévő DNS-zónát, vagy létrehozhat egy új zónát. Új DNS-zóna létrehozásához és delegálásához az [Azure-ban az Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md)című témakörben található.

## <a name="add-a-txt-record-for-custom-domain-validation"></a>TXT rekord hozzáadása egyéni tartomány-érvényesítéshez

Amikor egyéni állomásnevet ad hozzá a webalkalmazásokhoz, egy adott TXT-rekordot fog keresni a tartomány érvényesítéséhez.

1. Nyissa meg az erőforráscsoportot, és válassza ki a DNS-zónát.
2. Válassza a **Rekordhalmaz** elemet.
3. Adja hozzá a rekordkészletet az alábbi táblázat segítségével. Az értékhez használja a korábban rögzített webalkalmazás-URL-címet:

   |Név  |Típus  |Érték|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása

Egyéni tartomány hozzáadása mindkét webalkalmazáshoz.

1. Nyissa meg az erőforráscsoportot, és válassza ki az első webalkalmazást.
2. A bal oldali oszlopban válassza az **Egyéni tartományok**lehetőséget.
3. Az **Egyéni tartományok csoportban**válassza **az Egyéni tartomány hozzáadása**lehetőséget.
4. Az **Egyéni tartomány**csoportban adja meg az egyéni tartománynevét. Például adja meg a contoso.com nevet.
5. Válassza az **Érvényesítés** lehetőséget.

   A tartománynak át kell mennie az ellenőrzésen, és zöld pipákat kell megjelenítenie a **Állomásnév elérhetősége** és **a tartomány tulajdonjoga**mellett.
5. Válassza **az Egyéni tartomány hozzáadása**lehetőséget.
6. Ha az új állomásnevet a **webhelyhez rendelt állomásnevek**között szeretné látni, frissítse a böngészőt. Az oldalon lévő frissítés nem mindig jelenik meg azonnal a módosítások között.
7. Ismételje meg ezt az eljárást a második webalkalmazásban.

## <a name="add-the-alias-record-set"></a>Alias rekordkészlet hozzáadása

Most adjon hozzá egy alias rekordot a zóna csúcsa.

1. Nyissa meg az erőforráscsoportot, és válassza ki a DNS-zónát.
2. Válassza a **Rekordhalmaz** elemet.
3. Adja hozzá a rekordkészletet az alábbi táblázat segítségével:

   |Név  |Típus  |Alias rekordkészlet  |Alias típusa  |Azure-erőforrás|
   |---------|---------|---------|---------|-----|
   |@     |A|Igen|Azure-erőforrás|Traffic Manager - a profil|


## <a name="test-your-web-apps"></a>A webalkalmazások tesztelése

Most tesztelheti, hogy biztosan elérheti-e a webalkalmazást, és hogy a terhelés elosztása folyamatban van.Now you can test to make sure you can reach your web app and that it's being being load balanced.

1. Nyisson meg egy webböngészőt, és keresse meg a tartományt. Például adja meg a contoso.com nevet. Meg kell jelennie az alapértelmezett webalkalmazás-lapnak.
2. Állítsa le az első webalkalmazást.
3. Zárja be a webböngészőt, és várjon néhány percet.
4. Indítsa el a webböngészőt, és keresse meg a tartományt. Továbbra is látnia kell az alapértelmezett webalkalmazás-lapot.
5. Állítsa le a második webalkalmazást.
6. Zárja be a webböngészőt, és várjon néhány percet.
7. Indítsa el a webböngészőt, és keresse meg a tartományt. Meg kell jelennie a 403-as hiba, jelezve, hogy a webalkalmazás le van állítva.
8. Indítsa el a második webalkalmazást.
9. Zárja be a webböngészőt, és várjon néhány percet.
10. Indítsa el a webböngészőt, és keresse meg a tartományt. Újra meg kell jelennie az alapértelmezett webalkalmazás-lapnak.

## <a name="next-steps"></a>További lépések

Az aliasrekordokról az alábbi cikkekben olvashat bővebben:

- [Oktatóanyag: Állítson be egy aliasrekordot egy azure-beli nyilvános IP-címre való hivatkozáshoz](tutorial-alias-pip.md)
- [Oktatóanyag: aliasrekord konfigurálása a hogy, támogassa a legfelső szintű tartományneveket a Traffic Managerrel](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Az aktív DNS-név áttelepítése című témakörben [olvashat: Aktív DNS-név áttelepítése az Azure App Service szolgáltatásba című témakörben.](../app-service/manage-custom-dns-migrate-domain.md)
