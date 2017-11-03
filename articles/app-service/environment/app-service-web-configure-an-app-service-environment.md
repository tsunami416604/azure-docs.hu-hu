---
title: "Az App Service konfigurálása környezet 1-es verzió"
description: "Konfigurációs felügyeleti és az App Service Environment-környezet v1 monitorozása"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-an-app-service-environment-v1"></a>Alkalmazás konfigurálása szolgáltatás környezet 1-es verzió

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 tárgya.  Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Magas szinten az Azure App Service-környezetek több fő részből áll:

* Az App Service-környezetben üzemeltetett szolgáltatásban futó számítási erőforrásokat
* Storage
* Egy adatbázis
* Egy Classic(V1) vagy erőforrás Manager(V2) Azure virtuális hálózatot (VNet) 
* Az App Service-környezetben üzemeltetett szolgáltatás benne futó alhálózat

### <a name="compute-resources"></a>Számítási erőforrásokat
A számítási erőforrásokat használ a négy erőforráskészletek.  Minden egyes App Service környezetben (ASE) rendelkezik egy előtér-webkiszolgálóinak és három lehetséges feldolgozókészletek. Nem kell használnia minden három feldolgozókészletek – Ha azt szeretné, használhatja egy vagy két.

Az erőforráskészletek (előtér-webkiszolgálóinak és alkalmazottak) a gazdagépek nem érhetők el közvetlenül a bérlők számára. Remote Desktop Protocol (RDP) használatával csatlakozni hozzájuk, módosítsa a kiépítés nem, vagy működjön, és a rendszergazda az őket.

Beállíthatja az erőforrás-készlet mennyiségétől és méretétől. Service-környezetben lehetősége van négy mérete, amelyek tartalma P1 P4 keresztül. Ezen méretét és tarifacsomagját kapcsolatos részletekért lásd: [App Service díjszabás](https://azure.microsoft.com/pricing/details/app-service/).
A mennyiség vagy méretének módosítása a méretezési művelet neve.  Egyszerre csak egy skálázási művelet folyamatban lehet.

**Előtérkiszolgáló-végpontok**: az előtér-webkiszolgálóinak azok a HTTP/HTTPS-végpontok az alkalmazások, amelyek a ASE vannak használatban. Nem futtathatja a munkaterheléseket az előtér-webkiszolgálóinak.

* Egy ASE kezdődik-e két P2s, amely is elegendő fejlesztési és tesztelési célú alkalmazásokat és az alacsony szintű termelési számítási feladatokhoz. Erősen ajánlott P3s a közepes való nehéz termelési számítási feladatokhoz.
* Mérsékelt való nehéz termelési számítási feladatokhoz javasoljuk, hogy rendelkezik-e legalább négy P3s annak érdekében, hogy van elegendő előtér-webkiszolgálóinak ütemezett karbantartás bekövetkeztekor. Ütemezett karbantartás tevékenységek egyszerre egy előtér le fogja hozni. Ez csökkenti a teljes elérhető előtér-kapacitás karbantartási tevékenységek során.
* Előtér-webkiszolgálóinak kiépítését egy óráig is eltarthat. 
* A további méretezési pontosabb beállításra, célszerű figyelemmel kísérni a processzor, memória százalékos és aktív kérelmek metrikák az előtér-készlet. Ha a CPU és memória százalékos 70 százalék felett P3s futtatásakor, vegyen fel több előtér-webkiszolgálóinak. Az aktív kérések érték a 15 000 a 20 000 kérelmek előtér / átlaga szerepel, ha több előtér-webkiszolgálóinak is fel kell. Végső célja az adatok megőrzése CPU és memória százalékos aránya az alábbi 70 %, és a átlagosan az alábbi 15 000 kérelmek száma az első aktív kérelmek fejeződik be, amikor P3s futtatja.  

**Feldolgozók**: A munkavállalók, az alkalmazások ténylegesen futtatják. Az App Service-csomagokról növelheti, ha a társított feldolgozókészletek munkavállalók mentése használó.

* Azonnal munkavállalók vehető fel. Ezek kiépítését egy óráig is tarthat.
* A készlet számítási erőforrás mérete skálázás lépnek frissítés tartományonként < 1 óra. Nincsenek 20 frissítési tartományok Service-környezetben. Ha átméretezi a feldolgozókészleten 10 osztályt számítási méretét, eltarthat, legfeljebb 10 óra szükséges.
* Ha módosítja a számítási erőforrásokat, amelyek szerepelnek a feldolgozókészleten méretét, az adott munkavégző készletét. a futó alkalmazások hidegindítások miatt.

A leggyorsabban a feldolgozókészletek nem minden alkalmazás futó számítási erőforrás méretének módosítása, hogy:

* Csökkentheti a dolgozók 2 mennyiségét.  A minimális méret a portálon vertikális: 2. A példányok felszabadítani néhány percig tart. 
* Válassza ki az új számítási mérete és a példányok száma. Itt tart legfeljebb 2 óra szükséges.

Ha az alkalmazások egy nagyobb számítási erőforrás mérete van szükség, akkor tudják kihasználni az előző útmutatás. Helyett a feldolgozókészleten, amelyen ezek az alkalmazások méretének módosítása, egy másik munkavégző címkészlet, amely a kívánt méretű munkavállalók feltöltéséhez, és az alkalmazások átvitele készlethez.

* Hozzon létre egy másik munkavégző készletét. a további példányait a szükséges számítási méretét. A rendszer tarthat egy órával befejezéséhez.
* Az App Service-csomagokról üzemeltető az alkalmazásokat, amelyek az újonnan konfigurált munkavégző készletbe nagyobb méretű kell rendelni. Ez az egy gyors műveletet hajtson végre egy percen belül befejeződik.  
* Az első feldolgozókészletek csökkentheti, ha azokat nem használt példányait többé nem szükséges. Ez a művelet néhány percet is igénybe vehet.

**Automatikus skálázás**: az eszközök, amelyek segítségével kezelheti a számítási erőforrás-felhasználás egyik automatikus skálázást. Használhatja az automatikus skálázás előtér- vagy feldolgozókészletek. Vezérelhesse növelje a készlet bármilyen példányai reggel, és csökkenteni a este a. Vagy esetleg adhat hozzá példányok, ha a meghatározott küszöbérték alá esik, a feldolgozókészleten az elérhető száma.

Ha be szeretné állítani a számítási erőforrás készlet metrikák körül automatikus skálázási szabályok, majd vegye figyelembe a kiépítés szükséges idő. App Service Environment-környezetek automatikus skálázás kapcsolatos további tudnivalókért lásd: [automatikus skálázás beállítása az App Service-környezetek][ASEAutoscale].

### <a name="storage"></a>Storage
Minden egyes ASE 500 GB tárhelyet van konfigurálva. Ez a terület a ASE összes alkalmazást minden használatosak. Ezt a tárolóhelyet a ASE része, és jelenleg nem állítható át a tárhely használja. Ha módosításának hajt végre a virtuális hálózati útválasztási és a biztonság, továbbra is az Azure Storage – hozzáférési engedélyeznie kell a, vagy a ASE nem működik.

### <a name="database"></a>Adatbázis
Az adatbázis tartalmazza, amely meghatározza a környezet, valamint a benne futó alkalmazások adatait. Ez túl része az Azure-tárolt előfizetés. Nincs valamit, hogy a közvetlen számítógép kezelése céljából. Ha módosításának hajt végre a virtuális hálózati útválasztási és a biztonság, továbbra is hozzáférjenek az SQL Azure--kell, vagy a ASE nem működik.

### <a name="network"></a>Network (Hálózat)
A virtuális hálózat, amely a mértékéig szolgál egy, a ASE létrehozása után végrehajtott vagy egy időben végrehajtott lehet. ASE létrehozásakor hozza létre az alhálózatot, ha a virtuális hálózatnak ugyanahhoz az erőforráscsoporthoz tartozik találhatók ASE arra kényszeríti. Ha az erőforráscsoport, használja a ASE eltér a virtuális hálózat van szüksége, akkor szüksége a resource manager-sablon használatával ASE létrehozásához.

Nincsenek a virtuális hálózat egy ASE használt bizonyos korlátozások vonatkoznak:

* A virtuális hálózat regionális virtuális hálózatot kell lennie.
* Kell adni egy alhálózat 8 vagy több címekkel a ASE telepítési helyét.
* Miután az alhálózat egy ASE futtatására szolgál, az alhálózat címtartománya nem módosítható. Ezért azt javasoljuk, hogy az alhálózat ASE jövőbeli növekedésének legalább 64 címeket tartalmaz.
* Lehet semmi mást az alhálózat, de a ASE.

Az üzemeltetett szolgáltatás, amely tartalmazza a ASE eltérően a [virtuális hálózati] [ virtualnetwork] és alhálózati felhasználói felügyelete alatt áll.  A virtuális hálózat a virtuális hálózati felhasználói felületén vagy a Powershellen keresztül felügyelheti.  Egy ASE klasszikus és Resource Manager virtuális hálózat is telepíthető.  A portál és API lép némileg eltérő klasszikus és Resource Manager Vnetek között, de a ASE felhasználói.

A virtuális hálózat, amely egy ASE állomásaként vagy saját RFC1918 IP-címek használhatók, vagy telepítheti a nyilvános IP-címeket.  Ha szeretné használni az IP-tartomány, amely nem fedi RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), majd a VNet és alhálózat előre ASE létrehozása a ASE által használandó létrehozásához szükséges.

Ez a funkció az Azure App Service helyezi el a virtuális hálózaton, mert az azt jelenti, hogy az alkalmazások, amelyek a ASE érhetők el, amelyeket közzétettek expressroute-on vagy a pont-pont virtuális magánhálózatok (VPN) keresztül közvetlenül erőforrásokat. Az alkalmazásokat, amelyek az App Service-környezet belüli nincs szükség további hálózati funkciókat a virtuális hálózat, amelyen az App Service-környezet számára elérhető erőforrások eléréséhez. Ez azt jelenti, hogy nem kell használnia a virtuális integráció vagy hibrid kapcsolatok a vagy csatlakozik-e a virtuális hálózati erőforrások eléréséhez. Továbbra is használhatja ezeket a szolgáltatásokat is hozzáférését az erőforrásokhoz való hálózatokban, amelyek nem csatlakoznak a virtuális hálózat.

Virtuális integráció segítségével például egy virtuális hálózat előfizetése van, de nem kapcsolódik a virtuális hálózat, amely a ASE integrálható. Hibrid kapcsolatok továbbra is használhatja, ha más hálózatokhoz, ahogy normális esetben is lévő erőforrások eléréséhez.  

Ha a virtuális hálózatot az ExpressRoute VPN, kell ügyelnie, amely rendelkezik egy ASE útválasztási igényeinek részének. Néhány felhasználó által megadott útvonal (UDR) konfigurációkat, amelyek nem kompatibilisek egy mértékéig van. A virtuális hálózatban az ExpressRoute egy ASE futtatásával kapcsolatos további részletekért lásd: [ExpressRoute rendelkező virtuális hálózatban egy App Service Environment-környezet futó][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Bejövő adatforgalom biztonságossá tétele
Bejövő forgalmát a ASE vezérlésére két elsődleges módszere van.  Hálózati biztonsági Groups(NSGs) segítségével szabályozhatja, milyen IP-címek hozzáférhet a ASE Itt [az App Service-környezetek bejövő forgalom vezérlése](app-service-app-service-environment-control-inbound-traffic.md) és a ASE is konfigurálható egy belső terheléselosztási Balancer(ILB).  Ezek a funkciók is együtt is használható, ha szeretné korlátozni a ILB ASE NSG-ket használó hozzáférés.

Amikor létrehoz egy ASE, VIP hoz létre a Vneten belül.  Két VIP típusa, a külső és belső van.  Amikor létrehoz egy ASE egy külső virtuális IP-címre a ASE alkalmazások internet irányítható IP-cím elérhetők lesznek. Ha bejelöli a ASE belső egy ILB van konfigurálva, és nem lesz közvetlenül az interneten érhető el.  Egy ILB ASE továbbra is igényel egy külső virtuális IP, de csak az Azure felügyeleti és a karbantartás használja.  

ILB ASE létrehozásakor adja meg az altartományt, a ILB ASE által használt, és kezelheti az altartomány, megadhatja a saját DNS lesz.  Mivel a tanúsítványt a HTTPS-hozzáféréshez használt kezeléséhez szükséges altartománynév be.  ASE létrehozása után kéri adja meg a tanúsítványt.  További létrehozása, és egy ILB ASE használatáról olvassa el [egy belső terheléselosztó használata az App Service-környezetek][ILBASE]. 

## <a name="portal"></a>Portál
Ön felügyelheti és figyelheti az App Service-környezet az Azure-portálon a felhasználói felület használatával. Ha egy ASE, majd valószínűleg az App Service a jelet látja az oldalsávon. Ez a szimbólum az Azure-portálon az App Service Environment-környezetek képviseli a következőkre használható:

![App Service Environment-környezet szimbólum][1]

A felhasználói felület, amely felsorolja az App Service Environment-környezetek megnyitásához használhatja az ikonra, vagy válassza ki a sávnyíl (">" jel) alsó részén jelölje be az App Service Environment-környezetek az oldalsávon. A felsorolt ASEs közül válassza ki, nyissa meg a felhasználói felület, amellyel figyelheti és kezelheti azt.

![Felhasználói felülete figyelése és az App Service-környezet kezelése][2]

Az első panel a ASE néhány tulajdonságát együtt erőforrás készletenként metrika diagram jeleníti meg. A tulajdonságok, amelyek megjelennek a **Essentials** blokk megtalálhatók hiperhivatkozásokat, ekkor megnyílik a panelt, amely társítva hozzá. Például kiválaszthatja a **virtuális hálózati** kattintva nyissa meg a felhasználói felület nevet a virtuális hálózat, amely a ASE futtatja a társított. **App Service-csomagok** és **alkalmazások** panelt, amely ezeket az elemeket, amelyek a ASE a listában minden egyes nyissa meg.  

### <a name="monitoring"></a>Figyelés
A diagramok lehetővé teszik a számos különböző Teljesítményelemzési mutatón minden erőforráskészlet megjelenítéséhez. Az előtér-készlet figyelheti a átlagos CPU és memória. A feldolgozókészletek figyelheti a használt és a rendelkezésre álló.

Több App Service csomagokban teheti a dolgozók a feldolgozókészleten használatát. A munkaterhelés nem a azonos módon terjesztése, mint az előtér-kiszolgálókkal, így a Processzor- és memóriahasználatról nem rendelkeznek nagy átszállítást megakadályozzák hasznos információkat. Fontos több nyomon követéséhez hány munkavállalók használt és elérhető – különösen akkor, ha az Ön által felügyelt mások számára használja a rendszer.  

Is használhatja az összes követhető nyomon a metrikák a diagramokon riasztások beállítása. Itt riasztások beállításával működik máshol, az App Service-ben. Között állítható be egy riasztás vagy a **riasztások** része, vagy állapotkategóriák vizsgálatát az összes metrikák felhasználói felületén, majd válassza a felhasználói felület **hozzáadása riasztás**.

![Felhasználói felület metrikák][3]

A metrikák csak tárgyalt az App Service Environment-környezet metrikákat. Az alkalmazásszolgáltatási csomag szintjén elérhető metrikák is vannak. Ez azért, ahol figyelési CPU és memória lehetővé teszi a nagy mennyiségű logika.

Service-környezetben az App Service-csomagok összes dedikált App Service-csomagokról. Ez azt jelenti, hogy az állomásokon futó a lefoglalt, hogy-e App Service-csomag az alkalmazásokat az adott App Service-csomag csak alkalmazásokat. Az App Service-csomag a részletek megtekintéséhez válassza az App Service-csomag bármelyik a listák a ASE felhasználói felületén vagy a **Tallózás App Service-csomagok** (amely tartalmazza az összes).   

### <a name="settings"></a>Beállítások
ASE panel belül egy **beállítások** szakasz néhány fontos képességeket tartalmazza:

**Beállítások** > **tulajdonságok**: A **beállítások** panel automatikusan nyitja meg elindítani a ASE panelen. A legfelső **tulajdonságok**. Itt láthatók a redundáns elemeinek több **Essentials**, de mi nagyon hasznos lehet az **virtuális IP-cím**, valamint **kimenő IP-címek**.

![Beállítások panel és tulajdonságai][4]

**Beállítások** > **IP-címek**: a ASE IP Secure Sockets Layer (SSL) alkalmazást hoz létre, ha szüksége van-e az IP SSL-címet. Ahhoz, hogy az beszerzése utólag, a ASE kell IP SSL címek azt birtokló oszthat ki. Egy ASE jön létre, amikor erre a célra egy IP SSL-címmel rendelkezik, de több adhat hozzá. Nincs díj további IP-SSL-címek, ahogy az [App Service díjszabás] [ AppServicePricing] (az SSL-kapcsolatok című szakaszban). A további ár az az IP-SSL ár.

**Beállítások** > **első készlet** / **Feldolgozókészletek**: ezen erőforrás készlet paneleken mindegyikének segítségével elvégezheti a csak az adott erőforráskészlethez, teljesen méretezési az adott erőforráskészletben található vezérlők nyújtása mellett talál információt.  

Az alap panel minden erőforráskészlet metrikákat a diagramot biztosít adott erőforráskészlethez. Csakúgy, mint a ASE paneljéről diagramok is nyissa meg a diagramba és állítsa be a kívánt riasztásokat. Riasztás beállítása adott erőforráskészlet ASE paneljéről funkciója ugyanaz, mint az erőforráskészlet végezze el. A munkavégző készletből **beállítások** panelen rendelkezik hozzáféréssel az alkalmazások vagy az App Service-csomagok, amelyek a feldolgozókészleten.

![Munkavégző Készletbeállítások felhasználói felület][5]

### <a name="portal-scale-capabilities"></a>Portál méretezési képességek
Nincsenek három a skálázási műveletek:

* IP-címek a ASE elérhető IP-SSL használatának számának módosítása.
* A számítási erőforrással használja erőforráskészlet a méretének módosítása.
* Az erőforráskészlet, manuális vagy automatikus skálázás használt számítási erőforrások számának módosítása.

A portál háromféleképpen szabályozásához, hogy rendelkezik az erőforrás-készletek a kiszolgálók számát:

* A skálázási művelet a fő ASE paneljéről tetején. Módosításokat végezheti el több skálázási konfigurációs az előtér- és a feldolgozói készletekbe. Az összes lépésük egyetlen műveletként.
* Az egyes erőforráskészlet egy manuális skálázási művelet **méretezési** panel, amelyen alatt **beállítások**.
* Az egyes erőforráskészlet beállítása automatikus skálázás **méretezési** panelen.

A méretezési művelet a ASE panelen használatához húzza a csúszkát a mennyiség szeretne, és mentse. Ez a felhasználói felület is támogatja a méretének módosítása.  

![Skála felhasználói felület][6]

Szeretne használni a manuális vagy automatikus skálázás képesség adott erőforráskészlethez, Ugrás **beállítások** > **első készlet** / **Feldolgozókészletek** szükség szerint. Majd nyissa meg a módosítani kívánt készlettől. Nyissa meg a **beállítások** > **horizontális Felskálázás** vagy **beállítások** > **vertikális Felskálázás**. A **horizontális Felskálázás** panel segítségével kezelhető példány mennyiség. **Vertikális Felskálázás** lehetővé teszik az erőforrás mérete.  

![Felhasználói felület skálázási beállításokat][7]

## <a name="fault-tolerance-considerations"></a>Hibatűrési szempontok
Az App Service-környezet legfeljebb 55 teljes számítási erőforrások használatára konfigurálhatja. Gazdagép munkaterhelések 55 számítási erőforrások, csak 50 használható. Ennek oka kétszeres. Nincs legalább 2 előtér-számítási erőforrásokat.  Amely hagyja, akár 53-feldolgozókészleten kiosztásáért támogatásához. Biztosítja a hibatűrést, meg kell rendelkeznie egy további számítási erőforrást, amely a következő szabályok szerint történik:

* Minden egyes feldolgozókészletek kell legalább 1 további számítási erőforrás, amely nem érhető el a munkaterhelés hozzárendelni.
* A feldolgozókészleten a számítási erőforrások mennyisége a megadott érték fölé megy, majd egy másik számítási erőforrás szükség a hibatűrés érdekében. Ez nem a helyzet az előtér-készletben.

Bármely egyetlen feldolgozókészletek belül a hibatűrő követelményei, amely a X a feldolgozókészleten rendelt erőforrások megadott érték:

* Ha X 2 és 20 közötti, a munkaterhelések használható használható számítási erőforrások mérete X-1.
* Ha X 21 és 40 közötti, a munkaterhelések használható használható számítási erőforrások mérete X-2.
* Ha X 41-es és 53 között, a munkaterhelések használható használható számítási erőforrások mérete X-3.

A minimális kezdjen 2 előtér- és 2 feldolgozónak van.  A fenti utasítások majd, Íme néhány példa, hogy elmagyarázza:  

* Ha 30 munkavállalók egyetlen tárolókészlet, majd azok 28 segítségével gazdagép munkaterhelések.
* Ha 2 feldolgozónak egyetlen tárolókészlet, majd 1 segítségével gazdagép munkaterhelések.
* Ha 20 munkavállalók egyetlen tárolókészlet, majd 19 segítségével gazdagép munkaterhelések.  
* Ha 21 munkavállalók egyetlen tárolókészlet, majd továbbra is egyetlen 19 gazdagép munkaterhelések is használható.  

A hibatűrő aspektus fontos, de tartsa szem előtt, mivel bizonyos küszöbérték feletti méretezni kell. Ha azt szeretné, 20 példányokból is nagyobb kapacitású, folytassa a 22 vagy magasabb mert 21 nem adja hozzá a további kapacitást. Ugyanez igaz állapotra fent 40, ahol a következő kapacitás hozzáadó értéke 42.  

## <a name="deleting-an-app-service-environment"></a>App Service-környezet törlése
Ha törölni szeretné az App Service-környezetek, egyszerűen használja a **törlése** művelet az App Service Environment-környezet panel tetején. Ha így tesz, kérni fogja annak megerősítéséhez, hogy valóban ehhez az App Service-környezet nevét. Vegye figyelembe, hogy ha töröl egy App Service Environment-környezet, törli összes benne lévő tartalom is.  

![Felhasználói felület App Service-környezet törlése][9]  

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [egy App Service Environment-környezet létrehozása](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
