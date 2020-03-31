---
title: Az ASE 1-es v1-es állapotának konfigurálása
description: Az App Service-környezet 1-es számú konfigurációja, kezelése és figyelése. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687259"
---
# <a name="configuring-an-app-service-environment-v1"></a>App Service-környezet konfigurálása v1

> [!NOTE]
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól.  Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Magas szinten az Azure App Service Environment több fő összetevőből áll:

* Az App Service-környezet ben üzemeltetett szolgáltatásban futó számítási erőforrások
* Storage
* Adatbázis
* Klasszikus(V1) vagy Erőforrás-kezelő(V2) Azure virtuális hálózat (VNet) 
* Alhálózat, amelyben az App Service-környezet üzemeltetett szolgáltatás fut benne

### <a name="compute-resources"></a>Számítási erőforrások
A számítási erőforrásokat a négy erőforráskészlethez használhatja.  Minden App Service-környezet (ASE) egy sor előtér-és három lehetséges feldolgozókészletek. Nem kell mindhárom feldolgozókészletet használnia – ha szeretné, csak egyet vagy kettőt használhat.

Az erőforráskészletekben lévő állomások (előtér- és dolgozók) nem érhetők el közvetlenül a bérlők számára. A Távoli asztali protokoll (RDP) használatával nem csatlakozhat hozzájuk, nem módosíthatja a kiépítésüket, és nem léphet fel rendszergazdaként.

Beállíthatja az erőforráskészlet mennyiségét és méretét. Az ASE-ben négy méretbeállítás van, amelyek P1–P4 címkével vannak ellátva. Ezekről a méretekről és azok díjszabásáról az [App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
A mennyiség vagy a méret módosítását léptékműveletnek nevezzük.  Egyszerre csak egy méretezési művelet lehet folyamatban.

**Előtér-végek:** Az előtér-végpontok a HTTP/HTTPS-végpontok az alkalmazások, amelyek az ASE-ben található. A számítási feladatok nem futnak az előtér-végén.

* Az ASE két P2-vel kezdődik, amely elegendő a fejlesztési és tesztelési számítási feladatokhoz és az alacsony szintű éles számítási feladatokhoz. Erősen javasoljuk, hogy a P3s közepes és nagy éles számítási feladatokhoz.
* Közepesen súlyos és nagy éles számítási feladatok esetén azt javasoljuk, hogy legalább négy P3-assal rendelkezzen, amelyek biztosítják, hogy az ütemezett karbantartás esetén megfelelő előtér-rendszerek fussanak. Az ütemezett karbantartási tevékenységek egyszerre csak egy front-végződést eredményeznek. Ez csökkenti az általános rendelkezésre álló előtér-kapacitást a karbantartási tevékenységek során.
* Az előtér-végződések ellátása akár egy órát is igénybe vehet. 
* További méretezésfinomhangolása, figyelnie kell a CPU-százalék, a memória százalékos és az aktív kérelmek metrikák az előtér-készlet. Ha a PROCESSZOR vagy a memória százaléka meghaladja a 70 százalékot a P3s futtatásakor, adjon hozzá további előtér-végeket. Ha az aktív kérelmek értéke átlagosan ki 15 000-20 000 kérelmek előtér-enként, akkor is hozzá kell adnia további előtér-végek. Az általános cél az, hogy a PROCESSZOR és a memória százalékos aránya 70% alatt maradjon, és az aktív kérelmek átlagosan 15 000 kérelem alatt maradnak előtér-enként, amikor P3-akat futtat.  

**Dolgozók:** A dolgozók azok a hely, ahol az alkalmazások ténylegesen futnak. Az App Service-csomagok skálázásakor, amely felhasználja a dolgozók a társított feldolgozókészletben.

* Nem adhat hozzá azonnal dolgozókat. Az ellátás akár egy órát is igénybe vehet.
* A számítási erőforrások méretének méretezése bármely készlethez frissítési tartományonként < 1 órát vesz igénybe. Az ASE-ben 20 frissítési tartomány található. Ha 10 példánysal méretezte a feldolgozókészlet számítási méretét, akár 10 órát is igénybe vehet.
* Ha módosítja a feldolgozókészletben használt számítási erőforrások méretét, az adott munkavégző készletben futó alkalmazások hideg indításait fogja okozni.

A leggyorsabb módja annak, hogy módosítsa a számítási erőforrás méretét a feldolgozó készlet, amely nem fut semmilyen alkalmazást, hogy:

* Csökkentse a dolgozók mennyiségét 2-re.  A portálon a legkisebb leskálázási méret 2. A példányok felszabadítása néhány percet vesz igénybe. 
* Válassza ki az új számítási méretet és a példányok számát. Innen akár 2 órát is igénybe vehet.

Ha az alkalmazások nagyobb számítási erőforrás-méretet igényelnek, nem használhatja ki az előző útmutatáselőnyeit. Ahelyett, hogy módosítaná az alkalmazásokat tároló munkavégző készlet méretét, feltölthet egy másik munkavégző készletet a kívánt méretű dolgozókkal, és áthelyezheti az alkalmazásokat a készletbe.

* Hozza létre a szükséges számítási méret további példányait egy másik feldolgozókészletben. Ez akár egy órát is igénybe vehet.
* Rendelje hozzá újra az App Service-csomagok, amelyek üzemeltetik a nagyobb méretű alkalmazásokat az újonnan konfigurált feldolgozókészlet. Ez egy gyors művelet, amely kevesebb, mint egy percet vesz igénybe.  
* Csökkentse az első feldolgozókészletet, ha már nincs szüksége a fel nem használt példányokra. A művelet néhány percet vesz igénybe.

**Automatikus skálázás:** Az egyik eszköz, amely segíthet a számítási erőforrás-felhasználás kezelésében az automatikus skálázás. Az előtér- vagy munkavégző készletek automatikus skálázása használható. Meg tudod csinálni a dolgokat, mint például növeli a példányok bármilyen medence típusú reggel, és csökkenti azt az esti órákban. Vagy talán hozzáadhat olyan példányokat, amikor a feldolgozókészletben elérhető dolgozók száma egy bizonyos küszöbérték alá csökken.

Ha azt szeretné, hogy automatikus skálázási szabályok körül számítási erőforráskészlet metrikák, majd tartsa szem előtt az időt, amely a kiépítés szükséges. Az Automatikus skálázás alkalmazásszolgáltatás-környezetek ről az [Automatikus skálázás konfigurálása App Service-környezetben][ASEAutoscale]című témakörben talál további információt.

### <a name="storage"></a>Storage
Minden ASE 500 GB tárhellyel van konfigurálva. Ez a hely az ASE összes alkalmazásában használatos. Ez a tárhely az ASE része, és jelenleg nem lehet átváltani a tárhely használatára. Ha módosítja a virtuális hálózati útválasztást vagy a biztonságot, továbbra is engedélyeznie kell az Azure Storage-hoz való hozzáférést – vagy az ASE nem tud működni.

### <a name="database"></a>Adatbázis
Az adatbázis tartalmazza a környezetet meghatározó információkat, valamint a benne futó alkalmazások részleteit. Ez is az Azure által fenntartott előfizetés része. Ez nem olyasmi, amit közvetlenül manipulálni tudsz. Ha módosítja a virtuális hálózati útválasztást vagy a biztonságot, továbbra is engedélyeznie kell az SQL Azure-hoz való hozzáférést – vagy az ASE nem tud működni.

### <a name="network"></a>Network (Hálózat)
Az ASE-vel használt virtuális hálózat lehet az ase létrehozásakor létrehozott vagy az idő előtt létrehozott virtuális hálózat. Amikor az ASE létrehozása során létrehozza az alhálózatot, az ASE-t a virtuális hálózattal azonos erőforráscsoportba kényszeríti. Ha az ASE által használt erőforráscsoport nak különböznie kell a virtuális hálózattól, akkor létre kell hoznia az ASE-t egy erőforrás-kezelő sablon használatával.

Az ASE-hez használt virtuális hálózatra bizonyos korlátozások vonatkoznak:

* A virtuális hálózatnak regionális virtuális hálózatnak kell lennie.
* Kell lennie egy 8 vagy több címet, ahol az ASE telepítve van egy alhálózat.
* Miután egy alhálózatot ase-t üzemeltet, az alhálózat címtartománya nem módosítható. Ezért azt javasoljuk, hogy az alhálózat legalább 64 címet tartalmazzon a jövőbeli ASE-növekedés hez.
* Az alhálózatban csak az ASE található.

Az ASE-t tartalmazó üzemeltetett szolgáltatással ellentétben a [virtuális hálózat][virtualnetwork] és az alhálózat felhasználói felügyelet alatt áll.  A virtuális hálózat felügyelete a virtuális hálózati felhasználói felületen vagy a PowerShellen keresztül.  Az ASE klasszikus vagy erőforrás-kezelő ivirtuális hálózatban telepíthető.  A portál- és API-élmények némileg eltérnek a klasszikus és az erőforrás-kezelő virtuális hálózatok között, de az ASE-élmény ugyanaz.

Az ASE üzemeltetéséhez használt virtuális hálózat használhatja a privát RFC1918 IP-címeket, vagy használhatja a nyilvános IP-címeket.  Ha olyan IP-tartományt szeretne használni, amely nem tartozik az RFC1918-ba (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), akkor létre kell hoznia a virtuális hálózatot és az alhálózatot, amelyet az ASE az ASE létrehozása előtt használ.

Mivel ez a képesség az Azure App Service-t a virtuális hálózatba helyezi, azt jelenti, hogy az ASE-ben üzemeltetett alkalmazások mostantól hozzáférhetnek az ExpressRoute-on vagy a helyek közötti virtuális magánhálózatokon (VPN) keresztül elérhető erőforrásokhoz. Az App Service-környezetben található alkalmazások nem igényelnek további hálózati funkciókat az App Service-környezetet üzemeltető virtuális hálózat számára elérhető erőforrások eléréséhez. Ez azt jelenti, hogy nem kell vnet-integrációt vagy hibrid kapcsolatokat használnia a virtuális hálózat erőforrásainak elérése vagy a virtuális hálózathoz való csatlakozáshoz. Mindkét szolgáltatást továbbra is használhatja a virtuális hálózathoz nem kapcsolódó hálózatok erőforrásainak eléréséhez.

A VNET-integráció használatával például integrálható egy virtuális hálózattal, amely az előfizetésben van, de nem csatlakozik ahhoz a virtuális hálózathoz, amelyben az ASE található. A hibrid kapcsolatok továbbra is használhatják a más hálózatokban lévő erőforrások elérését, ahogy általában is.  

Ha a virtuális hálózat konfigurálva van egy ExpressRoute VPN, tisztában kell lennie az ASE útválasztási igényeinek néhány. Vannak olyan felhasználó által definiált útvonalkonfigurációk (UDR), amelyek nem kompatibilisek az ASE-vel. Az ASE ExpressRoute-tal rendelkező virtuális hálózaton való futtatásáról további információt az [App Service-környezet futtatása az ExpressRoute-tal rendelkező virtuális hálózaton című témakörben talál.][ExpressRoute]

#### <a name="securing-inbound-traffic"></a>Bejövő forgalom biztonságossá tétele
Két elsődleges módszer az ASE-be irányuló bejövő forgalom szabályozására.  A hálózati biztonsági csoportok (NSG-k) segítségével szabályozhatja, hogy milyen IP-címek férhetnek hozzá az ASE-hez az itt leírtak [szerint Hogyan szabályozhatja a bejövő forgalmat egy App Service-környezetben,](app-service-app-service-environment-control-inbound-traffic.md) és az ASE-t belső terheléselosztóval (ILB) is konfigurálhatja.  Ezek a szolgáltatások együttesen is használhatók, ha az NSG-k használatával korlátozni szeretné a hozzáférést az ILB ASE-re.

AsE létrehozásakor egy VIP a virtuális hálózatban.  Két VIP típus van, külső és belső.  Amikor létrehoz egy ASE-t egy külső VIP-vel, akkor az ASE-ben lévő alkalmazások elérhetők lesznek egy internetes irányítható IP-címen keresztül. Ha a Belső lehetőséget választja, az ASE egy ILB-vel lesz konfigurálva, és nem lesz közvetlenül elérhető az interneten.  Az ILB ASE továbbra is szükség van egy külső VIP, de csak az Azure felügyeleti és karbantartási hozzáféréshez használható.  

Az ILB ASE létrehozása során megadja az ILB ASE által használt altartományt, és a megadott altartományhoz saját DNS-t kell kezelnie.  Mivel beállította az altartomány nevét, a HTTPS-hozzáféréshez használt tanúsítványt is kezelnie kell.  Az ASE létrehozása után a rendszer kéri a tanúsítvány megadását.  Ha többet szeretne megtudni az ILB ASE létrehozásáról és [használatáról: Belső terheléselosztó használata App Service-környezettel.][ILBASE] 

## <a name="portal"></a>Portál
Az App Service-környezet et az Azure Portalon a felhasználói felület használatával kezelheti és figyelheti. Ha van egy ASE, akkor valószínű, hogy az App Service szimbólum az oldalsávon. Ez a szimbólum az App Service-környezetek az Azure Portalon:

![App Service-környezet szimbólum][1]

Az összes App Service-környezetet felsoroló felhasználói felület megnyitásához használja az ikont, vagy válassza ki az oldalsáv alján található sávsávot (">" szimbólumot) az App Service-környezetek kiválasztásához. A felsorolt ase-k kiválasztásával megnyitja a figyeléséhez és kezeléséhez használt felhasználói felületet.

![Felhasználói felület az App Service-környezet figyeléséhez és kezeléséhez][2]

Ez az első panel az ASE néhány tulajdonságát, valamint egy erőforráskészletenkénti metrikadiagramot jelenít meg. Az **Essentials** blokkban látható tulajdonságok némelyike olyan hivatkozások is, amelyek megnyitják a hozzá társított panelt. Például kiválaszthatja a **virtuális hálózat** nevét, hogy megnyissa a virtuális hálózathoz társított felhasználói felületet, amelyben az ASE fut. **Az App Service-csomagok** és **az alkalmazások** mindegyike megnyit egy-egy paneleket, amelyek az ASE-ben található elemeket sorolják fel.  

### <a name="monitoring"></a>Figyelés
A diagramok lehetővé teszik, hogy az egyes erőforráskészletekben különböző teljesítménymutatókat tekintsen meg. Az előtér-készlet, az átlagos CPU és a memória figyelheti. A dolgozói készletek esetében figyelemmel kísérheti a felhasznált mennyiséget és a rendelkezésre álló mennyiséget.

Több App Service-csomagok a dolgozók egy feldolgozókészletben. A számítási feladatok nem úgy oszlanak el, mint az előtér-kiszolgálók, így a PROCESSZOR- és memóriahasználat nem nyújt sok hasznos információt. Sokkal fontosabb, hogy nyomon kövesse, hány alkalmazottat használt és érhető el – különösen akkor, ha ezt a rendszert mások számára kezeli.  

A diagramokban nyomon követhető összes mutatót is használhatja a riasztások beállításához. A riasztások beállítása itt ugyanúgy működik, mint az App Service más részei. Riasztást a **Riasztások** felhasználói felületrészéből vagy bármely metrika felhasználói felületének fúrásából állíthat be, és a **Riasztás hozzáadása**lehetőséget választhatja.

![Metrikák felhasználói felülete][3]

Az imént tárgyalt mérőszámok az App Service-környezet metrikák. Vannak olyan mérőszámok is, amelyek az App Service-csomag szintjén érhetők el. Ez az, ahol a cpu és a memória felügyelete sok értelme tetszika van.

Egy ASE-ben az összes App Service-csomag dedikált App Service-csomagok dedikált. Ez azt jelenti, hogy az adott App Service-csomaghoz rendelt gazdagépeken csak az adott App Service-csomagban lévő alkalmazások futnak. Az App Service-csomag részleteinek megtekintéséhez hozza létre az App Service-csomagot az ASE felhasználói felületének vagy a **Tallózás App Service-csomagok** listájának bármelyikéről (amely az összeset felsorolja).   

### <a name="settings"></a>Beállítások
Az ASE panelen belül van egy **Beállítások** szakasz, amely számos fontos funkciót tartalmaz:

**Beállítások** > **tulajdonságai:** A **Beállítások** panel automatikusan megnyílik, amikor az ASE panelt felnyitja. A tetején a **Tulajdonságok**. Számos olyan elem van itt, amely redundáns ahhoz, amit az **Essentials-ban**lát, de ami nagyon hasznos, az a **virtuális IP-cím**, valamint **a kimenő IP-címek.**

![Beállítások panel és tulajdonságok][4]

**Beállítások** > **IP-címek:** Ha létrehoz egy SSL-alkalmazást az ASE-ben, IP SSL-címre van szüksége. Annak érdekében, hogy egy, az ASE szüksége van az IP SSL-címek, hogy a tulajdonában lévő, hogy kiosztható. AsE létrehozásakor egy IP SSL-címmel rendelkezik erre a célra, de továbbiakat is hozzáadhat. További IP SSL-címek ért díjat számítunk fel, ahogy az [Az App Service díjszabása][AppServicePricing] (az SSL-kapcsolatok szakaszában látható). A kiegészítő ár az IP SSL ár.

**Beállítások** >  / **előkészlet-feldolgozókészletek:** Ezen erőforráskészlet-panelek mindegyike lehetővé teszi, hogy csak az adott erőforráskészleten lévő információkat tekintse meg, amellett, hogy vezérlőket biztosít az erőforráskészlet teljes méretezéséhez.**Front End Pool**  

Az egyes erőforráskészletek alappanelje egy diagramot biztosít az adott erőforráskészlet metrikáival. Csakúgy, mint a diagramok az ASE panel, akkor bemegy a diagram, és állítsa be a riasztásokat a kívánt módon. Egy adott erőforráskészlet ről az ASE-panelriasztás beállítása ugyanazt a dolgot teszi, mint az erőforráskészletből. A munkavégző készlet **beállítások** panel, hozzáférése van az összes apps vagy App Service-csomagok, amelyek ebben a munkavégző készletben futnak.

![Dolgozói készlet beállításainak felhasználói felülete][5]

### <a name="portal-scale-capabilities"></a>Portálméretezési lehetőségek
Három skálázási művelet létezik:

* Az IP SSL-használathoz elérhető IP-címek számának módosítása az ASE-ben.
* Az erőforráskészletben használt számítási erőforrás méretének módosítása.
* Az erőforráskészletben használt számítási erőforrások számának módosítása manuálisan vagy automatikus skálázással.

A portálon háromféleképpen szabályozhatja, hogy hány kiszolgáló van az erőforráskészletekben:

* Skálaművelet a felső ASE fő penge-ből. Az előtér- és a feldolgozókészleteken több méretezési konfigurációs módosítást is végezhet. Ezek mindegyike egyetlen műveletként van alkalmazva.
* Manuális méretezési művelet az egyes erőforráskészlet **skálázási** paneljéből, amely a **Beállítások**csoportban található.
* Automatikus skálázás, amely az egyes erőforráskészlet **méretezési** panelről beállított.

Az ASE panel méretezési műveletének használatához húzza a csúszkát a kívánt mennyiségre, és mentse. Ez a felhasználói felület támogatja a méret módosítását is.  

![Felhasználói felület méretezése][6]

Ha egy adott erőforráskészlet manuális vagy automatikus skálázási képességeit szeretné használni, szükség szerint nyissa meg a **Beállítások** > **előkészlet** / **munkavégző készleteit.** Ezután nyissa meg a módosítani kívánt medencét. Nyissa meg a **Beállítások** > **horizontális felskálázása** vagy a **Beállítások** > **felskálázása lehetőséget.** A **Scale Out** panel lehetővé teszi a példány mennyiségének szabályozását. **A Felskálázás** lehetővé teszi az erőforrások méretének szabályozását.  

![Beállítások méretezése felhasználói felület][7]

## <a name="fault-tolerance-considerations"></a>Hibatűrési szempontok
Az App Service-környezet legfeljebb 55 számítási erőforrás használatára konfigurálható. Ebből az 55 számítási erőforrások, csak 50 használható számítási feladatok üzemeltetéséhez. Ennek oka kettős. Legalább 2 előtér-számítási erőforrás van.  Így legfeljebb 53 marad a dolgozói készlet felosztásának támogatására. A hibatűrés biztosításához egy további számítási erőforrással kell rendelkeznie, amely a következő szabályok szerint van lefoglalva:

* Minden dolgozókészletnek legalább 1 további számítási erőforrásra van szüksége, amely nem érhető el a számítási feladatok hozzárendeléséhez.
* Ha egy munkavégző készletben lévő számítási erőforrások mennyisége egy bizonyos érték fölé megy, akkor egy másik számítási erőforrásra van szükség a hibatűréshez. Nem ez a helyzet az előtér-medencében.

Bármely egyetlen munkavégző készleten belül a hibatűrési követelmények a munkavégző készlethez rendelt X erőforrások adott értékének követelményei:

* Ha X 2 és 20 között van, a számítási feladatokhoz használható használható számítási erőforrások mennyisége X-1.
* Ha X 21 és 40 között van, a számítási feladatokhoz használható használható számítási erőforrások mennyisége X-2.
* Ha X 41 és 53 között van, a számítási feladatokhoz használható használható számítási erőforrások mennyisége X-3.

A minimális lábnyom 2 előtér-kiszolgálóval és 2 dolgozóval rendelkezik.  A fenti állításokat, akkor, itt van néhány példa, hogy tisztázza:  

* Ha egy készletben 30 dolgozó van, akkor 28-at lehet számítási feladatok üzemeltetésére használni.
* Ha egy készletben 2 dolgozó van, akkor 1 használható számítási feladatok üzemeltetéséhez.
* Ha egy készletben 20 dolgozó van, akkor 19 használható számítási feladatok üzemeltetésére.  
* Ha 21 dolgozója van egy készletben, akkor még mindig csak 19 használható számítási feladatok üzemeltetéséhez.  

A hibatűrési szempont fontos, de ezt szem előtt kell tartania, amikor bizonyos küszöbértékek fölé skáláz. Ha azt szeretné, hogy több kapacitást megy 20 példány, majd folytassa a 22 vagy magasabb, mert 21 nem ad hozzá több kapacitást. Ugyanez igaz a 40 feletti szintre, ahol a következő szám, amely növeli a kapacitást 42.  

## <a name="deleting-an-app-service-environment"></a>App Service-környezet törlése
Ha törölni szeretne egy App Service-környezetet, egyszerűen használja a **Delete** műveletet az App Service-környezet panel tetején. Ha ezt teszi, a rendszer kéri, hogy adja meg az App Service-környezet nevét, és erősítse meg, hogy valóban ezt szeretné. Vegye figyelembe, hogy az App Service-környezet törlésekor a benne lévő összes tartalom is törlődik.  

![App Service-környezet felhasználói felületeitörlése][9]  

## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése: [App Service-környezet létrehozása című témakörben.](app-service-web-how-to-create-an-app-service-environment.md)

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
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
