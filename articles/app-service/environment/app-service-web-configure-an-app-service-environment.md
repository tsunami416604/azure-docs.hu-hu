---
title: Egy App Service konfigurálása környezet v1 – Azure
description: Konfigurációs, felügyeleti és az App Service Environment-környezet v1 figyelése
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 85353b68673ea91711e0c3d93e68bec662f406df
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272133"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurálása az App Service környezet v1

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 szól.  Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Magas szintű az Azure App Service-környezet több fő összetevőből áll:

* Az App Service-környezetben üzemeltetett szolgáltatásban futó számítási erőforrások
* Storage
* Egy adatbázis
* Classic(V1) vagy az erőforrás Manager(V2) Azure virtuális hálózat (VNet) 
* Az App Service-környezetben üzemeltetett szolgáltatás fut, az alhálózat

### <a name="compute-resources"></a>Számítási erőforrások
A négy erőforráskészletek esetében használja a számítási erőforrások.  Minden App Service Environment (ASE) az előtérrendszerek és három lehetséges feldolgozókészletek rendelkezik. Nem kell használnia minden három feldolgozókészletek – Ha azt szeretné, ugyanúgy használhatja egy vagy két.

Az erőforráskészletek (előtérrendszerekből és) lévő gazdagépek nem érhetők el közvetlenül a bérlők számára. Távoli asztal protokoll (RDP) használatával való kapcsolódást, módosítsa a kiépítés nem, vagy rendszergazdai jogosultságokkal azok szerepét.

Erőforrás-készlet mennyisége és mérete állíthatja be. Az ASE környezetben négy mérete lehetőségeket, amelyek P1-P4 szintű keresztül címkével rendelkezik. Méretek és a díjszabás részleteiért lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).
A mennyiség vagy méretének módosítása a skálázási művelet neve.  Egyszerre csak egy skálázási művelet folyamatban lehet.

**Előtér-vége**: Az előtérrendszerek a HTTP/HTTPS-végpontok az üzenetsorokban található az ASE nem alkalmazások. Az előtérrendszerek a számítási feladatok ne futtassa.

* Az ASE két P2s kezdődik, ez elegendő fejlesztési/tesztelési feladatokhoz és alacsony szintű éles számítási feladatokhoz. Javasoljuk, hogy P3s a közepes (nagy erőforrásigényű) éles számítási feladatokhoz.
* Közepes (nagy erőforrásigényű) éles számítási feladatok javasoljuk, hogy rendelkezik-e legalább négy P3s nincsenek futó, ütemezett karbantartás esetén elegendő kezelőfelületek biztosítása érdekében. Ütemezett karbantartási tevékenységek egyszerre egy előtér rendszer leállásához. Ez csökkenti a teljes rendelkezésre álló előtér-kapacitás karbantartásának idejére.
* Az előtérrendszerek órát is igénybe vehet az üzembe helyezni. 
* A további méretezési finomhangolásra, célszerű figyelemmel kísérni a Processzorhasználat (%), a memóriahasználat (%) és az előtérkészlet aktív kérések metrikáit. Ha a CPU és memória százalékos 70 százalék felett P3s futtatásakor, adjon hozzá további előtérrendszerek. Az aktív kérések érték 15 000 előtér 20 000 kérelemre való átlagának kiszámítása, ha több előtér is hozzá kell adnia. A teljes célja, hogy az alábbi CPU és memória százalékos 70 %-os, és átlagosan kevesebb mint az alábbi 15 000 kérések száma előtér aktív kérések fejeződik be, amikor P3s futtatja.  

**Feldolgozók**: A munkavállalók, ahol az alkalmazás ténylegesen fut. Amikor az App Service-csomagok, feldolgozóra a társítva mentést használó.

* Feldolgozók nem azonnal adhat hozzá. Akkor is igénybe vehet egy óránál üzembe helyezni.
* Skálázás bármilyen a készlet számítási erőforrás méretét veszi frissítési tartományonként < 1 óra. Az ASE 20 frissítési tartományig találhatók. Ha egy feldolgozói készlethez 10 példányra a számítási mérete Feladatütemezőt is igénybe vehet legfeljebb 10 óráig.
* A feldolgozókészletek használt számítási erőforrások méretének módosítása esetén okoz a kiküszöbölik az adott feldolgozókészlet futó alkalmazást.

A feldolgozókészletek nem futtató alkalmazások számítási erőforrás méretének módosítása a leggyorsabb módot kínálni arra, hogy:

* Vertikális leskálázás 2 feldolgozó mennyiségét.  A minimális vertikális leskálázási a portálon mérete: 2. Szabadítsa fel a példányok néhány percet vesz igénybe. 
* Válassza ki az új számítási méret és a példányok számát. Itt akár 2 órát vesz igénybe.

Ha az alkalmazások a nagyobb számítási erőforrás mérete, nem előnyeit a korábbi útmutatókban. Ezeket az alkalmazásokat futtató feldolgozókészletként méretének módosítása helyett töltse ki a kívánt méretű feldolgozók használata egy másik munkavégző készletét, és az alkalmazások helyezhet át a készlet.

* Hozzon létre a szükséges számítási méretű további példányok egy másik munkavégző készletét. Ez tarthat, amíg egy órát végrehajtásához.
* Az App Service-csomagok, amelyek egy nagyobb méretű, az újonnan konfigurált feldolgozói készlethez kell rendelni. Ez az egy gyors művelet, amellyel egy percen belül befejeződik.  
* Az első feldolgozókészlet csökkentheti, ha többé már nincs szüksége a fel nem használt ezekhez a példányokhoz. Ez a művelet néhány percet vesz igénybe.

**Az automatikus skálázás**: Az eszközök, amelyek segítségével kezelheti a számítási erőforrás-használat egyik automatikus skálázást. Használhatja az automatikus skálázás az előtér- vagy feldolgozókészletek. Műveleteket, mint növelje a készlet típustól példányok reggel, és csökkenteni esténként. Vagy esetleg adhat hozzá példányok, amikor egy meghatározott küszöbérték alá csökken, elérhető egy feldolgozókészletben száma.

Ha szeretné körül számítási erőforrás készletekkel kapcsolatos metrikák az automatikus skálázási szabályok, akkor a tartsa szem előtt szükséges időt. Automatikus méretezés App Service Environment-környezetek kapcsolatos további információkért lásd: [konfigurálása az automatikus méretezés App Service Environment-környezetben][ASEAutoscale].

### <a name="storage"></a>Storage
Minden egyes ASE 500 GB-os tárhelyet van konfigurálva. Ez a hely szolgál az ASE összes alkalmazásában. Ez a tárolóhely egy része az ASE-t, és jelenleg nem állítható át tárhely használata. A virtuális hálózati útválasztást vagy a biztonsági beállításainak hajt végre, ha továbbra is engedélyezi a hozzáférést az Azure Storage-ot kell, vagy az ASE nem fog működni.

### <a name="database"></a>Adatbázis
Az adatbázis tartalmazza, amely meghatározza a környezetben, valamint a benne lévő futó alkalmazások adatait. Ez túl egy az Azure-tárolt előfizetés részét képezi. Nem valami, amelyek segítségével kezelheti a közvetlen lehetősége van. A virtuális hálózati útválasztást vagy a biztonsági beállításainak hajt végre, ha továbbra is az SQL Azure – való hozzáférés engedélyezése kell, vagy az ASE nem fog működni.

### <a name="network"></a>Network (Hálózat)
A virtuális hálózattal, az ASE-t használják az ASE létrehozása során végrehajtott vagy a kívánt időben végrehajtott lehet. ASE létrehozása során hozza létre az alhálózatot, ha az ASE-t a virtuális hálózatnak ugyanabban az erőforráscsoportban lenniük arra kényszeríti. Ha a virtuális hálózat, amely eltér az ASE által használt erőforráscsoportot, majd szeretne létrehozni az ASE-t egy resource manager-sablon használatával.

A virtuális hálózaton, az ASE használt bizonyos korlátozások vonatkoznak:

* A virtuális hálózat regionális virtuális hálózat kell lennie.
* Szükség van egy 8 vagy több címmel rendelkező alhálózatot az ASE telepítési helyét.
* Miután egy alhálózatot az ASE üzemeltetéséhez, az alhálózat címtartománya nem lehet módosítani. Ezért azt javasoljuk, hogy az alhálózat legalább 64 címeket ASE jövőbeli növekedésének tartalmaz.
* Lehet semmi mást az alhálózaton, de az ASE-t.

Ellentétben az üzemeltetett szolgáltatás, amely tartalmazza az ASE-t a [virtuális hálózat] [ virtualnetwork] és alhálózati felhasználói felügyelete alatt állnak.  A virtuális hálózat a virtuális hálózati felhasználói felületén vagy a Powershellen keresztül felügyelheti.  Az ASE is üzembe helyezhetők a klasszikus vagy Resource Manager virtuális hálózatot.  A portál és API-élmény némileg eltérőek a hagyományos és erőforrás-kezelői virtuális hálózatok között, de az ASE ebben az esetben ugyanúgy.

A virtuális hálózattal, az ASE tárolására használt használhatja bármelyik privát RFC1918 IP-címeket vagy nyilvános IP-címek használhat.  Ha egy IP-címtartományt, amely nem vonatkozik az RFC1918 használni kívánt (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) akkor kell létrehozni a virtuális hálózatot és alhálózatot az ASE létrehozása előtt ASE által használható.

Mivel ez a képesség az Azure App Service helyezi el a virtuális hálózat, az azt jelenti, hogy az alkalmazások az ASE környezetben üzemeltetett most erőforrásokhoz is hozzáférhet, elérhetővé válnak az ExpressRoute- vagy helyek közötti virtuális magánhálózatok (VPN) keresztül közvetlenül. Az alkalmazásokat, amelyek az App Service-környezetben nincs szükség további hálózati funkciókat a virtuális hálózat, amelyen az App Service-környezet számára elérhető erőforrások eléréséhez. Ez azt jelenti, hogy nem kell erőforrásokhoz való beolvasása, illetve azok a virtuális hálózathoz csatlakoztatott VNET-integráció vagy hibrid kapcsolatok használatával. Továbbra is használhatja ezeket a funkciókat is, ha az erőforrások eléréséhez hálózatokban, amelyek nem csatlakoznak a virtuális hálózathoz.

VNET-integráció segítségével például egy virtuális hálózat az előfizetésben, de nincs csatlakoztatva a virtuális hálózathoz, amely az ASE integrálása. Hibrid kapcsolatok továbbra is használhatja, ha más hálózatokhoz, ugyanúgy, mint a szokásos módon is lévő erőforrások eléréséhez.  

Ha a virtuális hálózat az ExpressRoute VPN konfigurálva van, néhány útválasztási igényeinek, amely az ASE rendelkezik tisztában kell lennie. Nincsenek néhány felhasználó által megadott útvonal (UDR) konfiguráció az ASE nem kompatibilisek. Az ASE fut a virtuális hálózat az expressroute-tal kapcsolatos további információkért lásd: [egy virtuális hálózat az expressroute-tal futó App Service-környezet][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>A bejövő adatforgalom védelme
A bejövő forgalom átirányítása az ASE két elsődleges módszere van.  Hálózati biztonsági Groups(NSGs) segítségével szabályozhatja, hogy milyen IP címek el tud érni az ASE az itt leírtak szerint [az App Service-környezet bejövő forgalom szabályozása](app-service-app-service-environment-control-inbound-traffic.md) és az ASE konfigurálása belső terheléselosztóval (ILB).  Ezeket a funkciókat is használható együtt, ha szeretné korlátozni a hozzáférést NSG-ket az ILB ASE használatával.

Az ASE létrehozásakor hoz létre egy VIP-címet a virtuális hálózatban található.  Két virtuális IP-cím típusa, a külső és belső van.  Ha egy külső virtuális IP-cím létrehozásához az ASE az ASE környezetben az alkalmazások internetes irányítható IP-cím-en keresztül elérhető lesz. Amikor kiválasztja az ASE belső konfigurálni szeretne egy ILB, és nem fogja közvetlenül elérhető az internetről.  ILB ASE környezetben is szükség van egy külső virtuális IP-cím, de csak az Azure felügyeleti és a karbantartási használható.  

ILB ASE létrehozása során adja meg az altartományt, az ILB ASE által használt, és kezelheti saját DNS-ÉT adja meg, hogy az altartomány lesz szüksége.  Mivel be az altartomány nevével együtt is kell kezelni a HTTPS-hozzáféréshez használt tanúsítvány.  ASE létrehozása után adja meg a tanúsítványt a rendszer kéri.  További létrehozásáról és használatáról az ILB ASE [belső terheléselosztó használata az App Service-környezet][ILBASE]. 

## <a name="portal"></a>Portál
Felügyelheti, és az App Service-környezet figyelése az Azure Portalon a felhasználói felület használatával. Ha egy ASE Környezethez, majd, valószínűleg az App Service-ben szimbólumot az oldalsávon. Ezt a szimbólumot szolgál, amelyek az App Service Environment az Azure Portalon:

![App Service Environment-környezet szimbólum][1]

Nyissa meg a felhasználói felület, amely felsorolja az összes, az App Service Environment-környezetek, hogy az ikon használata vagy válassza ki a francia idézőjelre (">" szimbólum) válassza ki az App Service Environment-környezetek az oldalsáv alján. Az ASE felsorolt egyikének kiválasztásával nyissa meg a felhasználói felület, amellyel monitorozni és kezelni.

![Felhasználói felület felügyeléséhez és kezeléséhez az App Service-környezet][2]

Az első panel együtt egy metrikadiagram erőforrás készletenként az ASE néhány tulajdonságát tartalmazza. Egyes látható tulajdonságok a **Essentials** blokk egyúttal hiperhivatkozásokat, ekkor megnyílik a panel, amely társítva van. Például kiválaszthatja a **virtuális hálózat** kattintva nyissa meg a felhasználói felület neve a virtuális hálózat, amely az ASE-t futtatja a társított. **App Service-csomagok** és **alkalmazások** minden egyes nyisson meg, hogy ezeket az elemeket, amelyek az ASE környezetben a többi panelen.  

### <a name="monitoring"></a>Figyelés
A diagramok lehetővé teszi különböző teljesítmény-mérőszámok egyes erőforráskészlet. Az előtér-készlet esetén figyelheti az átlagos CPU és memória. A feldolgozókészletek figyelemmel kísérheti a használt, illetve a rendelkezésre álló mennyiség.

Több App Service-ben csomagok győződjön meg arról is, a feldolgozóra egy használata. A számítási feladatok nem elosztva a ugyanolyan módon, az előtér-kiszolgálókon, így a CPU és memória kihasználtsága nem rendelkeznek átszállítást megakadályozzák a hasznos információk nagy részét. Fontos több nyomon követésére, hány használt feldolgozók és a rendelkezésre álló--különösen akkor, ha mások számára is a rendszer felügyeli.  

Használhatja az összes, hogy nyomon követhetők a diagramokban riasztások beállítása. Itt riasztások beállítása ugyanúgy működik, míg az App Service-ben. Beállíthatja egy riasztás vagy a **riasztások** egy része vagy részletezve bármely metrikák felhasználói Felületet, és kiválasztja a felhasználói felület **riasztás hozzáadása**.

![Felhasználói felület metrikák][3]

Az imént ismertetett metrikák az App Service Environment-környezet metrikákat. Az App Service csomag szintjén rendelkezésre álló metrikák is vannak. Ez az, ahol teszi párosítása Processzor- és figyelése.

Az ASE környezetben az App Service-csomagok összes dedikált App Service-csomagok. Ez azt jelenti, hogy az csak futó alkalmazások a lefoglalt gazdagépeken App Service-csomag biztosítják az alkalmazások az App Service-csomag. Az App Service-csomag részleteinek megtekintéséhez nyissa meg az App Service-csomag bármelyik a listák az ASE felhasználói felületén vagy a **keresse meg az App Service-csomagok** (amely tartalmazza az összes őket).   

### <a name="settings"></a>Beállítások
Az ASE panelen van egy **beállítások** számos fontos funkciókat tartalmazó szakaszt:

**Beállítások** > **tulajdonságok**: A **beállítások** panel automatikusan megnyitja, ha, az ASE panel megjelenítéséhez. Felső **tulajdonságok**. Számos itt megjelenik a redundáns elemeinek **Essentials**, de mi nagyon hasznos **virtuális IP-cím**, valamint **kimenő IP-címek**.

![Beállítások panel és a Tulajdonságok][4]

**Beállítások** > **IP-címek**: Az ASE környezetben létrehoz egy IP Secure Sockets Layer (SSL) alkalmazást, amikor egy IP SSL-cím szükséges. Annak érdekében, hogy beszerzése utólag, az ASE kell IP SSL-címek azt birtokló oszthat ki. Az ASE létrehozását követően erre a célra egy IP SSL-címmel rendelkezik, de többet is hozzáadhat. Van számítunk fel további IP SSL-címekhez, ahogyan [App Service díjszabását] [ AppServicePricing] (SSL-kapcsolatok szakaszában). A szolgáltatás az IP SSL ár további díja.

**Beállítások** > **előtérkiszolgáló-készlet** / **Feldolgozókészletek**: A készlet erőforráspanelek mindegyike lehetőséget biztosít az információk megtekintéséhez, csak az adott erőforráskészlethez, teljes körűen az adott erőforrás-készlet méretezésére vezérlők biztosítása mellett.  

Az alap panel minden erőforráskészlet készít egy grafikont, metrikákkal adott erőforráskészlethez. Csakúgy, mint az ASE panelen diagramokkal megnyithatja a diagramra és riasztásokat tetszés szerint állíthat be. Ugyanaz, mint az erőforráskészlet ezt beállítást a riasztást az ASE panelen egy adott erőforráskészlet végzi. A feldolgozó készletből **beállítások** panelen hozzáfér minden alkalmazás vagy a feldolgozókészlet futó App Service-csomagok.

![Feldolgozói készlethez beállítások Kezelőfelülete][5]

### <a name="portal-scale-capabilities"></a>Portál méretezési képességeit
Nincsenek három skálázási műveletek:

* IP-címek az ASE IP SSL-használat elérhető számának módosításával.
* Az erőforráskészlet használt számítási erőforrások méretének módosítása.
* Manuális vagy automatikus skálázást egy erőforráskészlet használt számítási erőforrások számának módosításával.

A portálon a rendszer három módon lehet szabályozni az erőforráskészletek rendelkező kiszolgálók számát:

* A skálázási művelet a fő ASE panel tetején. Az előtér- és a feldolgozói tárolókészletekben több skálázási konfigurációs módosításokat végezheti el. Az összes alkalmazásuk egyetlen műveletben.
* Az egyes erőforráskészlet manuális skálázási művelet **méretezési** panel, amely alatt található **beállítások**.
* Automatikus méretezés, amely beállítása az egyes erőforráskészlet **méretezési** panelen.

Szeretné használni a skálázási művelet az ASE panelen, húzza a csúszkát szeretne, majd mentse a mennyiséget. Ez a felhasználói felület is támogatja a megadott méret módosításával.  

![Méretezési csoport felhasználói felület][6]

Használja a manuális vagy automatikus skálázási lehetőségeit egy adott erőforráskészlet, lépjen a **beállítások** > **Front End-készlet** / **Feldolgozókészletek** , a megfelelő. Ezután nyissa meg a készlet, amelyet módosítani szeretne. Lépjen a **beállítások** > **horizontális Felskálázása** vagy **beállítások** > **vertikális Felskálázás**. A **horizontális Felskálázás** panel segítségével szabályozható a példány mennyiség. **Vertikális Felskálázás** segítségével szabályozható az erőforrás mérete.  

![Méretezési beállítások Kezelőfelülete][7]

## <a name="fault-tolerance-considerations"></a>– Hibatűrési szempontok
Beállíthatja, hogy az App Service Environment akár 55 összes számítási erőforrások használatát. 55 számítási erőforrások csak 50 számítási feladatokat futtatni használható. Ennek az az oka dokumentumnak. Nincs legalább 2 előtér számítási erőforrásokat.  Akár 53-as a feldolgozókészlet felosztás támogatása, amely hagyja. Hibatűrés biztosításához, szüksége lesz egy további számítási erőforrás, amely a következő szabályok alapján oszlik:

* Minden feldolgozói készlethez legalább 1 további számítási erőforrás, amely nem lehet hozzárendelni egy munkaterhelés számára elérhető van szüksége.
* Feldolgozókészletek a számítási erőforrások mennyiségét egy bizonyos érték feletti megfelelően, ha egy másik számítási erőforrásra szükség a hibatűrés. Ez nem a helyzet az előtérkészlet.

Minden olyan egyetlen worker készleten belül a hibatűrés követelmények a következők, amely egy adott feldolgozói készlethez hozzárendelt erőforrások X értéke:

* Ha X 2 és 20 közötti, használhatja a számítási feladatokhoz felhasználható számítási erőforrások mennyisége X-1.
* Ha X 21 és 40 között van, használhatja a számítási feladatokhoz felhasználható számítási erőforrások mennyisége X-2.
* Ha X a 41-es és az 53-as között van, használhatja a számítási feladatokhoz felhasználható számítási erőforrások mennyisége X-3.

A minimális erőforrás-igényű 2 előtér- és 2 feldolgozó rendelkezik.  A fenti utasítások az ezt követően az alábbiakban néhány példát, azzal a pontosítással:  

* Ha 30 feldolgozók az egyetlen, majd azok 28 segítségével számítási feladatokat futtatni.
* Ha egyetlen 2 feldolgozónak van, majd 1 segítségével számítási feladatokat futtatni.
* Ha egy készletben 20 feldolgozók, majd 19 segítségével számítási feladatokat futtatni.  
* Ha egyetlen 21 dolgozók is van, majd továbbra is csak 19 segítségével számítási feladatokat futtatni.  

A hibatűrés aspektus azért fontos, de tartsa szem előtt, meghaladja bizonyos megoldást kell. Ha azt szeretné, nagyobb kapacitású példányok 20-ról, folytassa a 22 vagy magasabb szintű mert 21 nem adja hozzá semmilyen további kapacitást. Ugyanez érvényes fog fent 40, a következő számot, amely hozzáadja a kapacitás esetén 42.  

## <a name="deleting-an-app-service-environment"></a>App Service-környezet törlése
Ha szeretné törölni az App Service-környezet, majd egyszerűen használja a **törlése** az App Service Environment-környezet panel tetején lévő művelet. Ha így tesz, a program felszólítja annak ellenőrzéséhez, hogy valóban szeretné ezt az App Service-környezet nevét adja meg. Vegye figyelembe, hogy ha töröl egy App Service Environment-környezet, törölje az összes benne lévő tartalom is.  

![App Service-környezet felhasználói felület törlése][9]  

## <a name="getting-started"></a>Első lépések
App Service Environment-környezetek használatának megkezdéséhez lásd [App Service-környezet létrehozása](app-service-web-how-to-create-an-app-service-environment.md).

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
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
