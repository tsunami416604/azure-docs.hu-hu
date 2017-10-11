---
title: "IaaS-erőforrásokra a klasszikus Azure Resource Manager az áttelepítés tervezése |} Microsoft Docs"
description: "IaaS-erőforrásokra a klasszikus Azure Resource Manager az áttelepítés tervezése"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 5db4e5b18ad385e7eba125a1296a9c5054213446
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>IaaS-erőforrásokra a klasszikus Azure Resource Manager az áttelepítés tervezése
Azure Resource Manager nagy mennyiségű elképesztő funkciókat kínál, miközben nagyon fontos tervezze meg az áttelepítési út zökkenőmentesek sure művelet. Tervezési idő költségeik biztosítja, hogy nem tapasztal, az áttelepítési tevékenységek végrehajtása során. 

> [!NOTE] 
> A következő volt fokozottan által közzétett útmutatásokat az az Azure felhasználói Ügyféltanácsadói csapatának és a felhőalapú megoldás fejlesztők használata az ügyfelek áttelepítése nagy enviornments. Ilyen Ez a dokumentum továbbra is frissülnek, sikeres új mintáinak merülnek fel, ezért ellenőrizze újból az idő, ha van-e új javaslatokkal időre.

Az áttelepítési út négy általános fázisból áll:

![Áttelepítési fázis](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Felkészülés

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot

Attól függően, hogy a műszaki követelményeiben méret, földrajzi és üzemeltetési eljárások érdemes figyelembe venni:

1. Miért szükséges a szervezet Azure Resource Manager?  Mik az üzleti okokból az áttelepítéshez?
2. Mik a műszaki oka az Azure Resource Manager?  Mi (ha van ilyen) további Azure-szolgáltatások szeretné használni?
3. Melyik alkalmazás (vagy a virtuális gépek beállítása) szerepel az áttelepítés?
4. Az áttelepítés API mely forgatókönyvek támogatottak?  Tekintse át a [funkció és konfigurációs beállítás nem támogatott](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Az operatív adapterekhez most támogatja alkalmazások/virtuális gépek Azure Resource Manager és klasszikus?
6. Hogyan (ha egyáltalán) Azure Resource Manager változik a virtuális gép telepítési, kezelési, figyelési és jelentési folyamatok?  Az üzembe helyezési parancsfájlok kell frissíteni?
7. Mi az, hogy a kommunikációs terv riasztást küldjön az érdekelt felek (a végfelhasználók alkalmazástulajdonosok és infrastruktúrák tulajdonosai)?
8. Attól függően, hogy a környezet összetettsége kell a karbantartási időszak amikor az alkalmazás nem érhető el, a végfelhasználók számára, és az alkalmazástulajdonosok?  Ha igen, mennyi ideig?
9. Mi az az érdekelt felek tájékozott és biztosításához az Azure Resource Manager jártassággal képzési terv?
10. Mi az a program felügyeleti vagy a projekt felügyeleti csomag az áttelepítés?
11. Mi az Azure erőforrás-kezelő áttelepítésének és más vonatkozóan kapcsolódó technológia közúti maps?  Ezek optimális igazított?

### <a name="patterns-of-success"></a>Sikeres mintái

Sikeres ügyfél terveket, ahol a fenti kérdések tárgyalt, dokumentált és szabályozott részletesen.  Győződjön meg arról, az áttelepítési terveket körben közlik szponzorok és az érdekelt felekkel.  Az áttelepítési beállítások; ismereteket ellátására saját kezűleg egész megadása az alábbi áttelepítési dokumentumban ajánlott.

* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítésének áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager PowerShell használatával](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager parancssori felület használatával](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager áttelepítésének védelmével kapcsolatos közösségi eszközök](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A leggyakrabban feltett kérdésekre áttelepítése IaaS-erőforrásokra a klasszikus Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

- Nem sikerült a megtervezése.  Ez az áttelepítés technológia lépésein bizonyítottan, és előre jelezhető eredménye.
- Mindegyik forgatókönyvben figyelembe veszi a azt feltételezi, hogy a platform támogatja-e az áttelepítés API. Olvassa el a [funkció és konfigurációs beállítás nem támogatott](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) megérteni, hogy milyen forgatókönyvek is támogatottak.
- Nem lehetséges alkalmazás kimaradás tervezése végfelhasználók számára.  Tervezze meg a megfelelő figyelmeztesse a végfelhasználók számára a potenciálisan nem érhető el alkalmazás idő elegendő puffer.


## <a name="lab-test"></a>Tesztelési labor 

**A felhasználásához replikálja, és hajtsa végre a test-áttelepítés**
  > [!NOTE]
  > A meglévő környezet replikációs végrehajtása hivatalosan nem támogatott Microsoft Support közösségi hozzájárultak eszköz használatával. Emiatt egy **választható** a legjobb módszer problémák a termelési környezetben érintése nélkül, de a lépést. Ha egy közösségi hozzájárult eszközzel lehetőség nem érhető el, majd olvassa el az alábbi ellenőrzése/Prepare/megszakítási próbafuttatást ajánlás.
  >
  
  A legjobb módszer a zökkenőmentes áttelepítés végrehajtása egy laboratóriumi tesztelése a pontos forgatókönyv (számítási, hálózati és tárolási). Ezzel biztosítja:

  - Egy teljesen különálló labor- vagy egy meglévő nem éles környezetben történő teszteléséhez. Azt javasoljuk, hogy egy teljesen különálló tesztkörnyezetet, mely ismételten telepíthető át, és a korábbi módosítható.  A valós előfizetések metaadatok gyűjtése/hidrát parancsfájlok alább láthatók.
  - Célszerű a labor létrehozása a különálló előfizetést. A hiba oka, hogy a labor fog kell bontva ismételten, és rendelkezik egy különálló, elkülönített előfizetés csökkenti az esélye, hogy valami valós kap véletlenül törölt.

  Ehhez a AsmMetadataParser eszközzel. [További tudnivalók az eszköz itt](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Sikeres mintái

A következő volt a nagyobb áttelepítések számos felfedezett problémákat. Ez a nem egy lista tartalmazza, és tekintse át a [funkció és konfigurációs beállítás nem támogatott](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) további részletek. Lehet, vagy nem a műszaki problémák merülhetnek, de ha így tesz megoldása a migrálás megkísérlése előtt fog gördülékenyebb élményt nyújtsanak.

- **Ellenőrzés/Prepare/megszakítási próbafuttatást tegye** – Ez lehet, hogy az a legfontosabb lépés annak biztosítása érdekében a klasszikus Azure Resource Manager áttelepítése sikeres. Az áttelepítés API rendelkezik három fő lépésből: Ellenőrizze, előkészítése, és véglegesítse. Fogja érvényesíteni olvassa el a klasszikus környezet állapotáról, és térjen vissza az összes probléma eredménye. Azonban bizonyos problémák előfordulhat, hogy az Azure Resource Manager-készletben található, mert ellenőrzése nem képes mindent. Az áttelepítési folyamat következő lépése, előkészítési segítségével teszi közzé a ismertetünk. Készítse elő a rendszer a metaadatok áthelyezése a klasszikus Azure Resource Managerbe, de fog nem véglegesítse az áthelyezés, és nem eltávolítása vagy a klasszikus oldalon bármit módosíthat. A próbafuttatást magában foglalja az áttelepítés előkészítése, majd megszakítása (**nem véglegesítése**) az áttelepítés előkészítése. A ellenőrzése/előkészítése/megszakítási próbafuttatást célja láthatja az összes, a metaadatok az Azure Resource Manager-készletben, akkor tekintse meg (*programozott módon, vagy a portálon*), és győződjön meg arról, hogy minden helyesen áttelepíti, és a munka – technikai problémák.  Azt is ad bizonyos értelemben az áttelepítés időtartamát, tervezéséhez állásidejét ennek megfelelően.  A validate/előkészítése/abort nem okoz felhasználói leállási; ezért nem-őket az alkalmazáshasználatot.
  - Az alábbi elemek a próbafuttatást előtt megoldandó kell, de próbafuttatást teszt is nyugodtan kiüríti azokat a előkészítő lépések, ha azok kimaradt. Vállalati az áttelepítés során talált azt a próbafuttatást való biztosítható a biztonságos és hasznos információt áttelepítésre való készenlét biztosítására.
  - Ha előkészítése fut, a vezérlő vezérlősík (az Azure felügyeleti műveletek) zárolva lesz a teljes virtuális hálózat, ezért nem módosítható a virtuális gép metaadatainak ellenőrzése/előkészítése/megszakítás alatt.  De egyébként bármely alkalmazás függvény (távoli asztali Munkamenetgazda, a virtuális gép kihasználtsága, stb.) nem érinti.  A virtuális gépek felhasználók nem fogja tudni, hogy a próbafuttatást végrehajtása zajlik.

- **Express Route Kapcsolatcsoportok és a VPN-**. Jelenleg Express Route átjárók engedélyezési hivatkozásokkal állásidő nélkül nem telepíthetők át. A probléma megoldásához tekintse meg [áttelepítése ExpressRoute áramkörök, és a Resource Manager üzembe helyezési modellel klasszikus virtuális hálózatok társított](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Virtuálisgép-bővítmények** -virtuálisgép-bővítmények számára a legnagyobb roadblocks áttelepítése futó virtuális gépek közül. Virtuálisgép-bővítmények szervizelésének sikerült upwards of 1-2 nap igénybe, ezért ennek megfelelően tervezheti.  Egy működő Azure-ügynököt futtató virtuális gépek Virtuálisgép-bővítmény állapotának jelentést van szükség. Az állapot a futó virtuális gépek hibás ismét elérhető lesz, ha ez megáll áttelepítési. Az ügynök önmagát nem kell lenniük ahhoz, hogy áttelepítési, de ha bővítményei a virtuális Gépre, majd mindkét egy működő ügynök AND (DNS) a kimenő internetkapcsolat lesz szükség az áttelepítésre való előre.
  - Ha a DNS-kiszolgálóhoz való csatlakozásra BGInfo v1 kivételével az összes Virtuálisgép-bővítmények az áttelepítés során elvész. \* először el kell távolítani a minden virtuális gép áttelepítésének előkészítése előtt, és ezt követően újból hozzá újra a virtuális gép Azure Resource Manager az áttelepítés után.  **Ez csak futó virtuális gépek esetén.**  Ha a virtuális gépek leállnak felszabadított, Virtuálisgép-bővítmények nem kell eltávolítani. **Megjegyzés:** maguk sok bővítmények, például az Azure diagnostics és a security center figyelési lesz telepítse újra az áttelepítést követően, távolítsa el nincs probléma.
  - Emellett gondoskodjon arról, hogy hálózati biztonsági csoportok nem kimenő internet-hozzáférés korlátozása. Ez akkor fordulhat elő, az egyes hálózati biztonsági csoportok konfigurációk. Kimenő internet-hozzáférés (és a DNS-) szükséges Virtuálisgép-bővítmények az Azure Resource Manager kell áttelepíteni. 
  - A BGInfo bővítményt két verziója van: v1 és v2.  Ha a virtuális gép létrehozása a klasszikus portál vagy a PowerShell használatával, a virtuális gép lesz valószínűleg kiterjesztése a v1 rajta. Ehhez a kiterjesztéshez nem kell őket távolítani, és a rendszer kihagyja (áttelepítése nem) által az áttelepítés API. Azonban ha a klasszikus virtuális Gépet az új Azure-portálon hozták létre, akkor valószínűleg lesz a JSON-alapú BGInfo, amely az Azure Resource Manager áttelepíthető v2 verziója lett megadva az ügynök működik-e, és a kimenő internet-hozzáférés (és a DNS-) rendelkezik. 
  - **1. javítási lehetőséget**. Ha tudja, hogy a virtuális gépek nem fognak hozzáférni, egy működő DNS-szolgáltatás, és a virtuális gépek Azure ügynökök dolgozik, majd el kell távolítani az összes Virtuálisgép-bővítmények előkészítése előtt az áttelepítés részeként kimenő internet, majd újra kell telepítenie a Virtuálisgép-bővítmények az áttelepítés után. 
  - **2. lehetőség szervizelési**. Ha a Virtuálisgép-bővítmények túl nagy, egy a küszöbértéket, egy másik lehetőség egy leállítás és felszabadítás/minden virtuális gép áttelepítése előtt. A felszabadított virtuális gépek, majd indítsa újra őket az Azure Resource Manager oldalán. Az itt előnye, hogy a Virtuálisgép-bővítmények lesz áttelepítve. A hátránya az, hogy az összes nyilvánosan elérhető a virtuális IP-cím el fog veszni (ez nem alapszintű lehet), és nyilvánvalóan a a virtuális gépeket egy sokkal nagyobb hatással vannak a működő alkalmazások, amely le fog állni.

    > [!NOTE] 
    > Ha az Azure Security Center házirend úgy van konfigurálva, az áttelepítés alatt álló futó virtuális gépek elleni, a biztonsági házirendnek kell kiterjesztések eltávolítása előtt le kell állítani, ellenkező esetben a biztonságfigyelés bővítmény újra lesz telepítve automatikusan a virtuális gép eltávolítása után.

- **Rendelkezésre állási készletek** – az Azure Resource Manager, a benne lévő virtuális gépek kell lenniük egy rendelkezésre állási csoport Classic deployment (azaz a felhőalapú szolgáltatás), vagy a virtuális gépeket áttelepíteni egy virtuális hálózatot (vNet) összes nem lehet a rendelkezésre állási csoportot. Egynél több rendelkezésre állási, a felhőalapú szolgáltatás csoport rendelkezik nem kompatibilis az Azure Resource Manager és megáll, és áttelepítési.  Emellett nem lehet néhány virtuális gépek rendelkezésre állási csoportba, és egyes virtuális gépek rendelkezésre állási csoport nem található. A probléma megoldásához, akkor javítsa vagy átütemezésével a felhőalapú szolgáltatás.  Tervezze meg ennek megfelelően, mert ez időigényes lehet. 

- **Webes vagy feldolgozói szerepkör telepítéseket** -Felhőszolgáltatások tartalmazó webes és feldolgozói szerepkörök nem telepíthetők át, az Azure Resource Manager. A webes vagy feldolgozói szerepköröket először el kell távolítani a virtuális hálózati áttelepítés megkezdése előtt.  Egy tipikus megoldás, egyszerűen folytassa a webes/munkavégző szerepkörpéldányokat külön klasszikus virtuális hálózathoz ExpressRoute-kapcsolatcsoportot is kapcsolódó, illetve újabb PaaS alkalmazásszolgáltatások (ismertető már nem tartozik a jelen dokumentum) telepítse át a kódot. Volt eset újratelepíteni, hozzon létre egy új klasszikus virtuális hálózatot, áthelyezés/helyezze üzembe újra az új virtuális hálózat webes/munkavégző szerepkörök, majd törölje a központi telepítések áthelyezett virtuális hálózatról. Nem szükséges kódmódosításokat. Az új [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md) funkció használható együtt a klasszikus virtuális hálózatot, a webes vagy feldolgozói szerepköröket és egyéb ugyanabban a régióban Azure például a virtuális hálózati áttelepítés alatt álló virtuális hálózatok tartalmazó egyenrangú (**, nem telepíthetők át a virtuális hálózatok társítottak, virtuális hálózati áttelepítés befejezése után**), így adatvesztés nélkül teljesítmény és a késés/sávszélesség büntetést nem biztosítható a ugyanazokat a képességeket. Mivel a [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md), webes/munkavégző szerepkör telepítések mostantól egyszerűen kivédhető, és nem blokkolja az áttelepítés az Azure Resource Manager.

- **Az Azure erőforrás-kezelő kvótái** -Azure-régiók Azure Resource Manager és klasszikus külön kvóták/korlátokkal rendelkeznek. Annak ellenére, hogy az áttelepítési forgatókönyvben új hardver nem feldolgozottként *(jelenleg éppen áttelepíteni a forráskörnyezetból meglévő virtuális gépek a klasszikus Azure Resource Manager)*, Azure erőforrás-kezelő kvótái továbbra is szeretné helyen elegendő kapacitással rendelkező az áttelepítés megkezdése előtt kell. Az alábbiakban is láttuk jelentős korlátokat problémákhoz.  Emelje meg a kvóta támogatási jegy megnyitása. 

    > [!NOTE]
    > Ezek a korlátozások kell áttelepíteni az aktuális felhasználásához ugyanabban a régióban kell emelni.
    >

    - Hálózati illesztők
    - Terheléselosztók
    - Nyilvános IP-címek
    - Statikus nyilvános IP-címek
    - Processzormagok
    - Network Security Groups (Hálózati biztonsági csoportok)
    - Útvonaltáblák

    Ellenőrizheti, hogy a jelenlegi Azure erőforrás-kezelő kvótái, az Azure CLI 2.0 a legújabb verzióra a következő parancsok használatával.

    **Számítási** *(mag, Avaiability beállítása)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Hálózati** *(virtuális hálózatok, a statikus nyilvános IP-címek, a nyilvános IP-címek, hálózati biztonsági csoportok, hálózati adapter áll rendelkezésükre, Terheléselosztók, Útvonaltábláit)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Tárolási** *(Tárfiók)*
    
    ```bash
    az storage account show-usage
    ```

- **Az Azure Resource Manager API szabályozás korlátok** – Ha a elég nagy környezetekben (például) > 400 virtuális gépek a VNETEN belül), előfordulhat, hogy kattint az alapértelmezett API korlátozása korlátozza az írási műveletek (jelenleg **1200 írások óránként**) az Azure Resource Manager. Áttelepítés megkezdése előtt kell előléptetése egy támogatási jegy ezt az előfizetéshez tartozó korlát növelése érdekében.

- **Előkészítési virtuális gép állapotát túllépte az időkorlátot** – Ha a virtuális gép állapota **kiépítése túllépte az időkorlátot**, ez kell lennie a feloldott áttelepítés előtti. A csak ehhez, állásidővel megszüntetés/reprovisioning a virtuális gép (törlés, a lemez megőrzése, és hozza létre újra a virtuális gép). 

- **RoleStateUnknown virtuális gép állapotát** – Ha az áttelepítés miatt leáll egy **szerepkör állapota ismeretlen** hibaüzenet jelenik meg, vizsgálja meg a virtuális Gépet a portálon, és ellenőrizze, fut-e. Ez a hiba általában eltűnik majd a (nem kötelező szervizelési) tulajdonosai néhány perc múlva, és gyakran egy átmeneti típus gyakran látható során a virtuális gép **start**, **leállítása**, **újraindítása** műveletek. **Ajánlott eljárás:** próbálja meg újból az áttelepítési újra néhány perc múlva. 

- **Fabric-fürt nem létezik** – néhány esetben egyes virtuális gépek nem telepíthetők át a különböző páratlan okokból. Egy ismert ezekben az esetekben ha a virtuális gép nemrégiben jött létre (a múlt héten vagy így) és történt a megnyílik egy Azure-fürttel, amely még nem rendelkezik Azure Resource Manager munkaterhelésekhez.  Egy hiba, amely szerint jelenik **fabric-fürt nem létezik** és a virtuális gép nem telepíthető át. Néhány nap Várakozás fog általában adott probléma megoldásához, a fürt hamarosan megkapja az Azure Resource Manager engedélyezve van. Azonban egy azonnali kerülő megoldás lehet `stop-deallocate` a virtuális Gépet, majd áttelepítés előre folytatásához, és indítsa el a virtuális gép biztonsági mentése az Azure Resource Manager áttelepítése után.

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

- Parancsikonok igénybe vehet, és ne hagyja el a ellenőrzése/előkészítése/megszakítási próbafuttatást áttelepítéseket.
- Legtöbb, ha nem, a potenciális problémák fog surface a ellenőrzése/előkészítése/megszakítási lépések során.

## <a name="migration"></a>Migrálás

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot

Most már készen áll, mert a környezet keresztül az ismert problémák dolgozott.

A valódi áttelepítések érdemes figyelembe venni:

1. Tervezze meg, és a virtuális hálózathoz (az áttelepítés legkisebb egység) prioritás növelése ütemezni.  Az egyszerű virtuális hálózatok kezdeti lépések, és a bonyolultabb virtuális hálózatokat előrehaladás.
2. A legtöbb ügyfél nem éles és éles környezetben fog rendelkezni.  Éles utolsó ütemezni.
3. **(VÁLASZTHATÓ)**  Tervezze egy karbantartási bőven puffer abban az esetben, ha váratlan problémák merülhetnek fel.
4. Kommunikálni, és a támogatási csoportokkal igazodnak, abban az esetben, ha problémák merülnek fel.

### <a name="patterns-of-success"></a>Sikeres mintái

A műszaki útmutatót a Lab Test részből legyen tekinthető, és a tényleges áttelepítése előtt problémák elhárításáról.  A megfelelő tesztek, az áttelepítés az ténylegesen nem esemény.  Éles környezetben hasznos lehet további támogatásához, például egy megbízható Microsoft-partnerével vagy Microsoft Premier szolgáltatások.

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

Nincs teljesen tesztelése problémákat okozhat, és az áttelepítés késleltetés.  

## <a name="beyond-migration"></a>Áttelepítési túl

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot

Most, hogy az Azure Resource Manager, maximalizálhatja a platform.  Olvassa el a [áttekintése Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) regisztrációval kapcsolatos további előnyökkel is jár.

Vegye figyelembe a következőkre:

- Az áttelepítés más tevékenységek kötegelése.  A legtöbb ügyfél opt egy alkalmazás karbantartási időszak.  Ha igen, előfordulhat, hogy használni kívánt üzemszünet engedélyezése az egyéb Azure Resource Manager-szolgáltatásokat, például a titkosítás és a felügyelt lemezek áttelepítés.
- Le újra a műszaki és üzleti okokból az Azure Resource Manager; a további szolgáltatások csak az Azure Resource Manager érhető el, amelyek a saját környezetének engedélyezése.
- A környezet PaaS szolgáltatásokkal korszerűsítésére.

### <a name="patterns-of-success"></a>Sikeres mintái

Kell a mely szolgáltatásokat szeretné engedélyezni az Azure Resource Manager szándékos.  Sok ügyfél található a alatt az Azure környezetben vonatkozóan:

- [Szerepköralapú hozzáférés-vezérlés](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Az Azure Resource Manager-sablonok egyszerűbbé és több ellenőrzött telepítéshez](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Címkék](../../azure-resource-manager/resource-group-using-tags.md).
- [Tevékenység-vezérlő](../../azure-resource-manager/resource-group-audit.md)
- [Erőforrás-házirendekkel](../../azure-resource-manager/resource-manager-policy.md)

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

Ne feledje, hogy miért használatba a klasszikus – Azure Resource Manager áttelepítési út.  Mi volt az eredeti üzleti okokból? Sikerült elérni az üzleti okokból?


## <a name="next-steps"></a>Következő lépések

* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítésének áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager PowerShell használatával](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager áttelepítésének védelmével kapcsolatos közösségi eszközök](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A leggyakrabban feltett kérdésekre áttelepítése IaaS-erőforrásokra a klasszikus Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
