---
title: Elosztott terhelésű Azure-alapú webes alkalmazásokat üzemeltethet a zóna legfelső pontján
description: Használja az Azure DNS alias terhelés szempontjából elosztott webappok üzemeltetésére rekord a zóna legfelső pontján
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 11/3/2018
ms.author: victorh
ms.openlocfilehash: 2b14753237e118540da6306fa9f06816f3e58b71
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979779"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Elosztott terhelésű Azure-alapú webes alkalmazásokat üzemeltethet a zóna legfelső pontján

A DNS protokoll megakadályozza, hogy az A vagy AAAA rekord a zóna legfelső pontján csakis a hozzárendelését. Egy példa zóna felső pontja a contoso.com. Ez a korlátozás az elosztott terhelésű alkalmazások Forgalomkezelő mögé rendelkező alkalmazástulajdonosok problémát jelent. Nem lehet a zóna felső pontja rekordból a Traffic Manager-profil mutassanak. Ennek eredményeképpen alkalmazástulajdonosok áthidaló kell használnia. Az ügyfélalkalmazási rétegben átirányítási kell átirányítási a zóna felső pontja a egy másik tartományba. Ilyen például, egy irányítja át a contoso.com www.contoso.com. Ezzel az elrendezéssel fokozott megjelenít egy átirányítási függvényéhez meghibásodási pont.

Alias rekordokat a probléma már nem létezik. Alkalmazástulajdonos most már rendelkezik külső végpontok Traffic Manager-profil is mutat a zóna felső pontja rekord. Alkalmazástulajdonos más tartományban a DNS-zóna használt azonos Traffic Manager-profilt is mutat.

Például a contoso.com és a www.contoso.com is mutasson a Traffic Manager-profilt. Ez a helyzet, amíg a Traffic Manager-profil csak külső végpontokkal konfigurálva van.

Ebből a cikkből megismerheti, hogyan hozzon létre egy alias a domain pontjánál rekordot, és a web apps számára a Traffic Manager profil végpontok konfigurálása.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Ez a tartomány teljes vezérlési jogosultsággal kell rendelkeznie. Teljes hozzáférés lehetővé teszi a névkiszolgáló (NS) rekordjait a tartomány beállítása magában foglalja.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példa tartománya a contoso.com, de a saját tartománynevét használja.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot, amely ebben a cikkben használt összes erőforrást tárolja.

## <a name="create-app-service-plans"></a>App Service-csomagok létrehozása

Hozzon létre két Web App Service-csomagok az erőforráscsoportban, az alábbi táblázat segítségével a konfigurációs adatokat. App Service-csomag létrehozásával kapcsolatos további információkért lásd: [kezelése az Azure App Service-csomag](../app-service/app-service-plan-manage.md).


|Name (Név)  |Operációs rendszer  |Hely  |Tarifacsomag  |
|---------|---------|---------|---------|
|AZ ASP-01     |Windows|USA keleti régiója|A D1-közös fejlesztés és tesztelés|
|AZ ASP-02     |Windows|USA középső régiója|A D1-közös fejlesztés és tesztelés|

## <a name="create-app-services"></a>Hozzon létre az App Services

Két webalkalmazást, egy, az egyes App Service-csomag létrehozása.

1. Kattintson a bal felső sarkában az Azure portal oldalán, **erőforrás létrehozása**.
2. Típus **webalkalmazás** a keresősáv és nyomja le az Enter billentyűt.
3. Kattintson a **Web App**.
4. Kattintson a **Create** (Létrehozás) gombra.
5. Elfogadhatja az alapértelmezett beállításokat, és a következő táblázat segítségével konfigurálja a két webalkalmazást:

   |Name (Név)<br>(egyedinek kell lennie. azurewebsites.net)|Erőforráscsoport |Az App Service-csomag/hely
   |---------|---------|---------|
   |Alkalmazás-01|Meglévő használata<br>Jelölje ki az erőforráscsoportot|Az ASP-01(East US)|
   |Alkalmazás-02|Meglévő használata<br>Jelölje ki az erőforráscsoportot|Az ASP-02(Central US)|

### <a name="gather-some-details"></a>Gyűjtse össze az egyes részletei

Most meg kell jegyezze fel az alkalmazások IP cím és a gazdagépcsoport nevét.

1. Nyissa meg az erőforráscsoportot, és kattintson az első alkalmazás (**App-01** ebben a példában).
2. A bal oldali oszlopban kattintson **tulajdonságok**.
3. Vegye figyelembe az címet a **URL-cím**, majd a **kimenő IP-címek** vegye figyelembe a lista első IP-címét. Később ezt az információt fogja használni, amikor a Traffic Manager-végpontok konfigurálása.
4. Ismételje meg a műveletet **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Traffic Manager-profil létrehozásához az erőforráscsoportban. Használja az alapértelmezett értékeket, és adjon meg egy egyedi nevet a trafficmanager.net névtéren belül.

További információ a Traffic Manager-profil létrehozása: [a rövid útmutató: a Traffic Manager-profil egy magas rendelkezésre állású webes alkalmazás létrehozása](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Végpontok létrehozása

Most már a végpontokat a két webalkalmazást hozhat létre.

1. Nyissa meg az erőforráscsoportot, és kattintson a Traffic Manager-profil.
2. A bal oldali oszlopban kattintson **végpontok**.
3. Kattintson a **Hozzáadás** parancsra.
4. Használja az alábbi táblázat a végpontok konfigurálása:

   |Típus  |Name (Név)  |Cél  |Hely  |Egyéni fejléc beállításai|
   |---------|---------|---------|---------|---------|
   |Külső végpont     |Záró-01|Az alkalmazás-01-es rögzített IP-cím|USA keleti régiója|gazdagép:\<az URL-cím, az alkalmazás-01-es rögzített\><br>Példa: **állomás: az alkalmazás-01.azurewebsites.net**|
   |Külső végpont     |Záró-02|Az alkalmazás-02 rögzített IP-cím|USA középső régiója|gazdagép:\<az URL-cím, az alkalmazás-02 rögzített\><br>Példa: **állomás: az alkalmazás-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zóna létrehozása

Használhatja a meglévő DNS-zóna tesztelési, vagy létrehozhat egy új zóna. Hozzon létre, és a egy új DNS-zónát az Azure-ban delegálása, lásd: [oktatóanyag: üzemeltessen saját tartományt az Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Az alias rekordhalmaz hozzáadása

Amikor készen áll a DNS-zónát, hozzáadhat egy aliast rekord a zóna felső pontja.

1. Nyissa meg az erőforráscsoport, és kattintson a DNS-zónát.
2. Kattintson a **Rekordhalmaz** gombra.
3. Adja hozzá a rekord a következő táblázat használatával:

   |Name (Név)  |Típus  |Alias rekordhalmaz  |Alias típusa  |Azure-erőforrás|
   |---------|---------|---------|---------|-----|
   |@     |A|Igen|Azure-erőforrás|A TRAFFIC Manager - profilját|

## <a name="add-custom-hostnames"></a>Adja hozzá az egyéni állomásnevek

Adja hozzá az egyéni állomásnevet mindkét web Apps szolgáltatásban.

1. Nyissa meg az erőforráscsoportot, és kattintson az első webalkalmazását.
2. A bal oldali oszlopban kattintson **egyéni tartományok**.
3. Kattintson a **gazdagépnév hozzáadása**.
4. A gazdagépnév írja be a tartomány nevét. Ha például a contoso.com.

   A tartományban kell érvényesíteni, és megjelenítése mellett a zöld pipák **állomásnév elérhetősége** és **tartomány tulajdonjogának**.
5. Kattintson a **gazdagépnév hozzáadása**.
6. Megtekintheti az új gazdanév alapján **állomásnevek rendelt hely**, frissítse a böngészőt. Az oldalon a frissítés nem mindig jelenik módosítások azonnal.
7. Ismételje meg ezt az eljárást a második webes alkalmazás.

## <a name="test-your-web-apps"></a>A web apps tesztelése

Most tesztelheti, hogy elérheti a webalkalmazást és a terhelés alatt álló elosztott terhelésű.

1. Nyisson meg egy webböngészőt, és tallózással keresse meg a tartományt. Ha például a contoso.com. Az alapértelmezett webes alkalmazás lapon kell megjelennie.
2. Az első webalkalmazás leállítása.
3. Zárja be a webböngészőt, és várjon néhány percet.
4. Indítsa el a webböngészőt, és tallózással keresse meg a tartományt. Továbbra is megtekintheti az alkalmazás az alapértelmezett weblapot.
5. A második webalkalmazás leállítása.
6. Zárja be a webböngészőt, és várjon néhány percet.
7. Indítsa el a webböngészőt, és tallózással keresse meg a tartományt. Hiba 403-as, amely azt jelzi, hogy a webalkalmazás leállt kell megjelennie.
8. Indítsa el a második webes alkalmazást.
9. Zárja be a webböngészőt, és várjon néhány percet.
10. Indítsa el a webböngészőt, és tallózással keresse meg a tartományt. Ismét megjelenik az alapértelmezett weblapot alkalmazás.

## <a name="next-steps"></a>További lépések

További információt az alias rekordok, tekintse meg a következő cikkeket:

- [Oktatóanyag: Egy aliast rekord tekintse meg az Azure nyilvános IP-cím konfigurálása](tutorial-alias-pip.md)
- [Oktatóanyag: Egy aliasrekordot támogatásához apex-tartománynevek a Traffic Manager konfigurálása](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
