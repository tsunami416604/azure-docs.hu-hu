---
title: Egyéni mintavétel létrehozása a portál használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt az Application Gateway számára a portál használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074607"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Egyéni mintavétel létrehozása az Application Gateway számára a portál használatával

> [!div class="op_single_selector"]
> * [Azure-portál](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni állapotminta hozzáadása egy meglévő alkalmazásátjáró az Azure Portalon keresztül. Az állapotmintak használatával az Azure Application Gateway figyeli a háttérkészletben lévő erőforrások állapotát.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik alkalmazásátjáróval, látogasson el [az Alkalmazásátjáró létrehozása](application-gateway-create-gateway-portal.md) elemre, és hozzon létre egy alkalmazásátjárót a munka érdekében.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Mintavétel létrehozása az Application Gateway v2 Termékváltozathoz

A mintavételek kétlépésben vannak konfigurálva a portálon keresztül. Az első lépés a mintavételi konfigurációhoz szükséges értékek megadása. A második lépésben tesztelje a háttérrendszer állapotát ezzel a mintavételkonfigurációval, és mentse a mintavételt. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Mintavételtulajdonságainak megadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Ha még nem rendelkezik fiókkal, regisztrálhat egy [ingyenes egyhónapos próbaverzióra](https://azure.microsoft.com/free)

2. Az Azure Portal Kedvencek panelén kattintson az Összes erőforrás elemre. Kattintson az alkalmazásátjáróra a Minden erőforrás panelen. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a partners.contoso.net nevet a Szűrés név alapján... mezőbe.

3. Válassza **az Állapotminta,** majd **a Hozzáadás** lehetőséget egy új állapotminta hozzáadásához.

   ![Új mintavétel hozzáadása][4]

4. Az **Állapotminta hozzáadása** lapon töltse ki a mintavételhez szükséges adatokat, és ha a teljes művelet befejeződött, válassza az **OK gombot.**

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Név**|egyéniProbe|Ez az érték egy rövid nevet adott a mintavétel, amely elérhető a portálon.|
   |**Protocol (Protokoll)**|HTTP VAGY HTTPS | Az állapotminta által használt protokoll. |
   |**Állomás**|Azaz contoso.com|Ez az érték az alkalmazáskiszolgálón futó virtuális állomás neve (eltér a virtuális gép állomásnevétől). A mintavételt a rendszer a (protokoll)://(állomásnév):(port a httpsetting)/urlPath címre küldi.  Ez akkor alkalmazható, ha többhelyes van konfigurálva az Application Gateway.This is applicable when multi-site is configured on Application Gateway. Ha az Application Gateway egyetlen helyhez van konfigurálva, adja meg a "127.0.0.1" értéket.|
   |**Állomásnév választása háttérrendszer HTTP-beállításai közül**|Igen vagy nem|A *mintavételben* lévő állomásfejlécet a háttér-erőforrás állomásnevére állítja be a HTTP-beállításhoz társított háttérkészletben, amelyhez a mintavétel társítva van. Speciálisan szükség van a több-bérlős háttérrendszerek, például az Azure app service esetén. [További információ](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Elérési út**|/ vagy egy másik utat|Az egyéni mintavétel teljes URL-címének fennmaradó része. Az érvényes elérési út '/' kezdetű. A http alapértelmezett elérési\/útja: /contoso.com csak használja a '/' |
   |**Időköz (mp)**|30|Milyen gyakran fut a szonda, hogy ellenőrizze az állapot. Nem ajánlott 30 másodpercnél alacsonyabbra állítani.|
   |**Időeltetés (mp)**|30|A szonda várakozási ideje az időtúllépés előtt. Ha ezen időtúlidőszakon belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve. Az időbeli időköznek elég magasnak kell lennie ahhoz, hogy http-hívás kezdeményezhető legyen annak biztosítása érdekében, hogy a háttérrendszer állapotlapja elérhető legyen. Vegye figyelembe, hogy az időtúllépések értéke nem lehet nagyobb, mint a mintavételi beállításban használt "Interval" érték vagy a "Kérelem időtúllépések" értéke a HTTP-beállításban, amely ehhez a mintavételhez lesz társítva.|
|**Nem kifogástalan állapot küszöbértéke**|3|Az egymást követő sikertelen próbálkozások száma, amelyek nem megfelelő állapotúnak tekinthetők. A küszöbérték 1 vagy több.|
   |**A mintavételezési feltételek használata**|Igen vagy nem|Alapértelmezés szerint a 200 és 399 közötti állapotkóddal rendelkező HTTP(S) válasz kifogástalannak minősül. Módosíthatja a háttér-válaszkód vagy a háttér-választörzs elfogadható tartományát. [További információ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP-beállítások**|kijelölés legördülő menüből|A mintavétel az itt kiválasztott HTTP-beállítás(ok)hoz lesz társítva, ezért figyeli a kiválasztott HTTP-beállításhoz társított háttérkészlet állapotát. Ugyanazt a portot fogja használni a mintavételi kérelemhez, mint a kiválasztott HTTP-beállításban használt port. Csak azokat a HTTP-beállításokat választhatja ki, amelyek nincsenek más egyéni mintavételhez társítva. <br>Vegye figyelembe, hogy csak azok a HTTP-beállítások érhetők el a társításhoz, amelyek ugyanazt a protokollt rendelkezik, mint az ebben a mintavételi konfigurációban kiválasztott protokoll, és ugyanaz az állapotuk a *Háttérrendszerből származó állomásnév* beállításkapcsolóhoz.|
   
   > [!IMPORTANT]
   > A mintavétel csak akkor figyeli a háttérrendszer állapotát, ha egy vagy több HTTP-beállításhoz van társítva. Figyelni fogja azon háttérkészletek háttér-erőforrásait, amelyek ahhoz a HTTP-beállítás(ok)hoz vannak társítva, amelyhez ez a mintavétel társítva van. A mintavételi kérelmet a rendszer a http://(állomásnév):(port címre küldi a httpsetting)/urlPath.The probe request will be sent to http://(host name)):(port from httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Háttérrendszer állapotának tesztelése a szondával

A mintavételi tulajdonságok megadása után tesztelheti a háttér-erőforrások állapotát, ellenőrizheti, hogy a mintavételkonfiguráció helyes-e, és hogy a háttérerőforrások a várt módon működnek-e.

1. Válassza **a Teszt lehetőséget,** és jegyezze fel a mintavétel eredményét. Az alkalmazásátjáró a mintához használt HTTP-beállításokhoz társított háttérkészletek összes háttér-erőforrásának állapotát teszteli. 

   ![Háttérrendszer állapotának tesztelése][5]

2. Ha vannak nem megfelelő állapotú háttér-erőforrások, majd ellenőrizze a **Részletek** oszlop az erőforrás nem megfelelő állapotának megértéséhez. Ha az erőforrás helytelen mintavételi konfiguráció miatt nem kifogástalanállapotúnak van megjelölve, válassza a Visszalépés a **mintavételi kapcsolatra** lehetőséget, és szerkeszti a mintavételkonfigurációt. Ellenkező esetben, ha az erőforrás a háttérrendszer problémája miatt nem kifogástalanállapotúnak van megjelölve, majd oldja meg a háttér-erőforrással kapcsolatos problémákat, majd tesztelje újra a háttérrendszer tesztelését a Visszalépés a **mintavételre** hivatkozás kiválasztásával, és válassza a **Teszt**lehetőséget.

   > [!NOTE]
   > Választhat, hogy mentse a mintavételt még a nem megfelelő háttér-erőforrások, de nem ajánlott. Ennek az az oka, hogy az Application Gateway eltávolítja azokat a háttér-erőforrásokat a háttérkészletből, amelyek a mintavétel által nem megfelelőnek minősülnek. Abban az esetben, ha nincsenek kifogástalan állapotú erőforrások egy háttérkészletben, nem fogja tudni elérni az alkalmazást, és 502-es hibaüzenetet kap.

   ![Mintavétel eredménynek megtekintése][6]

3. A mintavétel mentéséhez válassza a **Hozzáadás** lehetőséget. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Mintavétel létrehozása az Application Gateway v1 Termékváltozathoz

A mintavételek kétlépésben vannak konfigurálva a portálon keresztül. Az első lépés a szonda létrehozása. A második lépésben adja hozzá a mintavételt az alkalmazásátjáró háttér-http-beállításaihoz.

### <a name="create-the-probe"></a><a name="createprobe"></a>A szonda létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Ha még nem rendelkezik fiókkal, regisztrálhat egy [ingyenes egyhónapos próbaverzióra](https://azure.microsoft.com/free)

2. Az Azure Portal Kedvencek ablaktábláján válassza az **Összes erőforrás** lehetőséget. Jelölje ki az alkalmazásátjárót a **Minden erőforrás** lapon. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a partners.contoso.net nevet a Szűrés név alapján... mezőbe.

3. Válassza **a Mintavételek,** majd a **Hozzáadás** lehetőséget a mintavétel hozzáadásához.

   ![Szondapanel hozzáadása kitöltött adatokkal][1]

4. Az **Állapotminta hozzáadása** panelen töltse ki a mintavételhez szükséges adatokat, és ha a teljes állapot, válassza az **OK gombot.**

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Név**|egyéniProbe|Ez az érték egy rövid nevet adott a mintavétel, amely elérhető a portálon.|
   |**Protocol (Protokoll)**|HTTP VAGY HTTPS | Az állapotminta által használt protokoll. |
   |**Állomás**|Azaz contoso.com|Ez az érték az alkalmazáskiszolgálón futó virtuális állomás neve (eltér a virtuális gép állomásnevétől). A mintavételt a rendszer a (protokoll)://(állomásnév):(port a httpsetting)/urlPath címre küldi.  Ez akkor alkalmazható, ha többhelyes van konfigurálva az Application Gateway.This is applicable when multi-site is configured on Application Gateway. Ha az Application Gateway egyetlen helyhez van konfigurálva, adja meg a "127.0.0.1" értéket.|
   |**Állomásnév választása háttérrendszer HTTP-beállításai közül**|Igen vagy nem|A *mintavételben* lévő állomásfejlécet a háttér-erőforrás állomásnevére állítja be a HTTP-beállításhoz társított háttérkészletben, amelyhez a mintavétel társítva van. Speciálisan szükség van a több-bérlős háttérrendszerek, például az Azure app service esetén. [További információ](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Elérési út**|/ vagy egy másik utat|Az egyéni mintavétel teljes URL-címének fennmaradó része. Az érvényes elérési út '/' kezdetű. A http alapértelmezett elérési\/útja: /contoso.com csak használja a '/' |
   |**Időköz (mp)**|30|Milyen gyakran fut a szonda, hogy ellenőrizze az állapot. Nem ajánlott 30 másodpercnél alacsonyabbra állítani.|
   |**Időeltetés (mp)**|30|A szonda várakozási ideje az időtúllépés előtt. Ha ezen időtúlidőszakon belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve. Az időbeli időköznek elég magasnak kell lennie ahhoz, hogy http-hívás kezdeményezhető legyen annak biztosítása érdekében, hogy a háttérrendszer állapotlapja elérhető legyen. Vegye figyelembe, hogy az időtúllépések értéke nem lehet nagyobb, mint a mintavételi beállításban használt "Interval" érték vagy a "Kérelem időtúllépések" értéke a HTTP-beállításban, amely ehhez a mintavételhez lesz társítva.|
|**Nem kifogástalan állapot küszöbértéke**|3|Az egymást követő sikertelen próbálkozások száma, amelyek nem megfelelő állapotúnak tekinthetők. A küszöbérték 1 vagy több.|
   |**A mintavételezési feltételek használata**|Igen vagy nem|Alapértelmezés szerint a 200 és 399 közötti állapotkóddal rendelkező HTTP(S) válasz kifogástalannak minősül. Módosíthatja a háttér-válaszkód vagy a háttér-választörzs elfogadható tartományát. [További információ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Az állomásnév nem egyezik meg a kiszolgálónévvel. Ez az érték az alkalmazáskiszolgálón futó virtuális állomás neve. A mintavétel a http://(host name):(port címre kerül a httpsetting)/urlPath webhelyről.

### <a name="add-probe-to-the-gateway"></a>Mintavétel hozzáadása az átjáróhoz

Most, hogy a mintavétel létrejött, itt az ideje, hogy hozzáadja az átjáróhoz. A mintavételi beállítások az alkalmazásátjáró háttér-http-beállításaiban vannak beállítva.

1. Kattintson a **HTTP-beállítások** az alkalmazás átjáró, hogy a konfigurációs panel kattintson az aktuális háttér-http beállítások az ablakban felsorolt.

   ![https beállítások ablak][2]

2. Az **appGatewayBackEndHttpSettings** beállítások lapon jelölje be az **Egyéni mintavétel használata** jelölőnégyzetet, és válassza ki az Egyéni **mintavétel** legördülő alkalmazás [Mintavétel létrehozása](#createprobe) szakaszában létrehozott mintavételt.
   Ha elkészült, kattintson a **Mentés** gombra, és alkalmazza a beállításokat.

## <a name="next-steps"></a>További lépések

Tekintse meg a háttérerőforrások állapotát a mintaalkalmazás által meghatározott állapotban a [háttérállapot-nézet használatával.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
