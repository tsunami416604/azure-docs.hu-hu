---
title: Az IaaS-erőforrások klasszikusból Azure Resource Manager az áttelepítés tervezése |} A Microsoft Docs
description: Az IaaS-erőforrások klasszikusból Azure Resource Manager az áttelepítés tervezése
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 540abeed3587959af5ca229f59343774b824547b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982896"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Az IaaS-erőforrások klasszikusból Azure Resource Manager az áttelepítés tervezése
Azure Resource Manager számos nagyszerű funkciókat biztosít, rendkívül fontos tervezze meg, hogy zökkenőmentességét sure dolgot a migrálási folyamat elkezdésekor. Tervezési idő kiadások biztosítja, hogy nem problémák merülnek fel migrálási tevékenységek végrehajtása közben.

> [!NOTE]
> Az alábbi útmutatót az erősen járult hozzá az Azure Customer Advisory team és a Felhőbeli megoldástervezők dolgozik az ügyfelekkel való áttelepítése nagy méretű környezetekben. Ilyen ebben a dokumentumban formátumverziók jelennek meg új minták létrehozását a siker várakozott továbbra is, ezért ellenőrizze vissza az idő, hogy van-e új javaslatok.

Az áttelepítési út négy általános fázisból áll:<br>

![Áttelepítési fázisok](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Felkészülés

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot kínál a

Attól függően, a műszaki követelményeknek méretű, földrajzi és működési gyakorlatot érdemes figyelembe venni:

1. Azure Resource Manager miért van szükség a szervezet számára?  Mik azok az alapvető üzleti okok felelősek az áttelepítés?
2. Mik azok a technikai okokból az Azure Resource Manager?  Mi (ha vannak) további Azure-szolgáltatások szeretné kihasználni?
3. Melyik alkalmazás (vagy a virtuálisgép-készleteket) tartalmaz a migrálás?
4. Milyen forgatókönyvekre a migrálási API támogatottak?  Tekintse át a [nem támogatott funkciókat és konfigurációk](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. A műveleti csapatoknak mostantól támogatja a alkalmazások vagy virtuális gépek a klasszikus és az Azure Resource Manager?
6. Hogyan (ha egyáltalán) az Azure Resource Manager változik a virtuális gép üzembe helyezésének, kezelési, figyelési és jelentéskészítési folyamatainak?  Szükség van az üzembehelyezési szkriptek frissíteni?
7. Mi a kommunikációs terv figyelmeztet az érdekelt felek (végfelhasználók számára, alkalmazások és infrastruktúrák tulajdonosai)?
8. A környezet összetettségétől függően kell egy karbantartási időszakban, az alkalmazás nem érhető el, a végfelhasználók számára, és az alkalmazástulajdonosok?  Ha igen, mennyi ideig?
9. Mi az a képzési terv az érintettek hozzáértő és az Azure Resource Manager jártassággal biztosítása érdekében?
10. Mi a program felügyeleti vagy az áttelepítés projekt felügyeleti csomag?
11. Mi az Azure Resource Manager-áttelepítést és egyéb megvizsgáltuk kapcsolódó technológia térképszolgáltatásokat?  Vonalban van-e azok optimális?

### <a name="patterns-of-success"></a>A siker minták

Sikeres ügyfelek tervek, ahol a fenti kérdések tárgyalt, dokumentált és szabályozott részletes rendelkeznek.  Győződjön meg arról, a migrálási tervben széles körben kommunikálja az érdekelt felek és a szponzorokkal.  Saját maga a migrálási beállítások; ismereteket a ellátására az alább áttelepítési dokumentum átolvasása erősen ajánlott.

* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager PowerShell-lel](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager áttelepítését segítő közösségi eszközök](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A legtöbb – gyakori kérdések áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

- Nem sikerült megtervezése.  A migrálás technológiai lépéseit bizonyítottan és kiszámítható eredménye.
- Az összes forgatókönyv feltételezi, hogy a platform támogatja-e a migrálás API-t figyelembe veszi. Olvassa el a [nem támogatott funkciókat és konfigurációk](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) megérteni, milyen forgatókönyvek is támogatottak.
- A végfelhasználók számára nem tervezi a potenciális alkalmazáskimaradás.  Tervezze meg megfelelően figyelmeztesse a végfelhasználók számára a potenciálisan nem érhető el alkalmazás idő elegendő puffer.


## <a name="lab-test"></a>Tesztelési labor

**A környezet replikálni, és végezze el a migrálási teszt**
  > [!NOTE]
  > Közösség által biztosított eszközzel, amely Support hivatalosan nem támogatja a meglévő környezet replikációs végrehajtása történik. Ezért egy **választható** a legjobb módszer az éles környezetek érintése nélkül problémák talál, de a lépésben. Ha egy Közösség által biztosított eszközzel nem egy lehetőséget, majd olvassa el az alábbi ellenőrzése és előkészítése/megszakítási tesztfuttatás ajánlás.
  >

  A legjobb módszer a zökkenőmentes áttelepítés a pontos forgatókönyvhöz (számítási, hálózati és tárolási) labor tesztje végző. Ezzel biztosítja:

  - Egy teljesen elkülönített labor- vagy egy meglévő nem éles környezetben tesztelheti. Azt javasoljuk, hogy teljesen elkülönített labor ismételten telepíthető át, és veszélye nélkül módosíthatók.  A valódi előfizetésekből metaadatok gyűjtése és hidrát parancsfájlok alább láthatók.
  - Célszerű a labor létrehozásához a különálló előfizetést. Az oka, hogy a labor lesz lesz bontva ismételten, és a egy külön kellene, elkülönített előfizetés csökkenti annak az esélyét, hogy valami valós véletlenül törlődni fog.

  Ez a AsmMetadataParser eszközzel is elvégezhető. [Tudjon meg többet a ezt az eszközt](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>A siker minták

A következő számos, a nagyobb áttelepítések a felmerült problémák voltak. Ez nem egy kimerítően teljes lista, és hivatkozzon a [nem támogatott funkciókat és konfigurációk](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) további részleteket talál.  Előfordulhat, hogy, vagy nem jelentkezhet be ezeket a műszaki problémákat, de ha mégis megoldására ezeket a migrálás megkezdése előtt biztosítja az egyenletesebb élmény.

- **Hajtsa végre egy ellenőrzése és előkészítése/megszakítási tesztfuttatás** – ez talán az a legfontosabb lépés annak biztosítása érdekében a klasszikus Azure Resource Manager-áttelepítés sikeres. A migrálási API három fő lépésből áll: Ellenőrizze, előkészítése, és hajtsa végre. Rendszer érvényesítése olvassa el a klasszikus környezet állapotáról és kapcsolatos összes problémát a eredményét adja vissza. Azonban bizonyos problémák az Azure Resource Manager veremben előfordulhat, hogy létezik, mert ellenőrzése nem képes mindent. Az áttelepítési folyamat következő lépése, előkészítése segítségével teszi közzé a ezeket a hibákat. Fogja előkészíteni a metaadatok áthelyezése klasszikusból Azure Resource Manager, de nem véglegesíteni az áthelyezés és fog nem távolítsa el vagy a klasszikus oldalon bármin változtatni. A tesztfuttatás magában foglalja a migrálás előkészítése, majd megszakítása (**nem véglegesítése**) az áttelepítés előkészítése. A tesztfuttatás ellenőrzése és előkészítése/megszakítási célja, hogy megjelenik az összes a metaadatokat az Azure Resource Manager veremben, vizsgálja meg, hogy (*programozás útján vagy a portálon*), és győződjön meg arról, hogy minden megfelelően áttelepíti, és haladjon végig a műszaki probléma.  Azt is ad érteni migrálás időtartama, így megtervezheti az állásidő ennek megfelelően.  Egy ellenőrzése és előkészítése/abort nem okoz felhasználói állásidő; Ezért fontos zavart nem okozó való az alkalmazás használatának.
  - Az alábbi elemek kell a tesztfuttatás előtt kell megoldani, de egy tesztfuttatás teszt kiüríti előkészítő lépések ki is biztonságosan azokat a, elmaradt. Vállalati az áttelepítés során azt tapasztaltuk a tesztfuttatás, biztonságos és felbecsülhetetlen értékű mód annak biztosítása érdekében az áttelepítésre való készenlét biztosítására.
  - Amikor előkészítése fut, a vezérlési sík (az Azure felügyeleti műveletek) zárolva lesz a teljes virtuális hálózathoz, így nem is kell módosítás a virtuális gépek metaadatait ellenőrzése és előkészítése vagy megszakítás alatt.  De egyébként bármely alkalmazás függvény (távoli asztali Munkamenetgazda, a virtuális gép használat, stb.) Ez nem érinti.  A virtuális gépek felhasználók addig nem szerez tudomást, hogy a tesztfuttatás folyamatban van.

- **Express Route Kapcsolatcsoportok és virtuális Magánhálózati**. Jelenleg Express Route átjárók engedélyezési hivatkozások az állásidő nélkül nem telepíthetők át. A megoldás lásd: [áttelepítése ExpressRoute-Kapcsolatcsoportok és kapcsolódó virtuális hálózatok a klasszikusból a Resource Manager-alapú üzemi modellbe](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **A Virtuálisgép-bővítmények** – virtuálisgép-bővítmények közé tartoznak vélhetően a legnagyobb akadályát áttelepítése futó virtuális gépekhez. Virtuálisgép-bővítmények szervizelési sikerült upwards of 1-2 napban igénybe, így ennek megfelelően tervezze.  Egy működő Azure-ügynök van szükség a futó virtuális gépek Virtuálisgép-bővítmény állapota jelentést. Ha az állapot vissza hibás futó virtuális gépek származnak, ez időeltérés áttelepítés. Magának az ügynöknek kell lennie a munkát ahhoz, hogy engedélyezze az áttelepítést, de ha bővítményei a virtuális gép létezik, majd mindkét egy működő ügynök kimenő internetkapcsolattal és (a DNS-ben) lesz szükség a folytatáshoz az áttelepítéshez.
  - Ha egy DNS-kiszolgálóval való kapcsolat 1. verziójának BGInfo kivételével minden Virtuálisgép-bővítmények az áttelepítés során elvész. \* először el kell távolítani minden virtuális gép migrálásának előkészítése előtt, és ezt követően újból hozzá újra a virtuális géphez az Azure Resource Manager az áttelepítés után.  **Ez a csak futó virtuális gépek esetében.**  Ha a virtuális gépek felszabadítva leállnak, Virtuálisgép-bővítmények nem kell eltávolítani.

  > [!NOTE]
  > Számos bővítmény, az Azure diagnostics és a security center figyelési lesz újra kell telepítenie, maguk az áttelepítés után így eltávolításával, majd akkor nem probléma.

  - Emellett ellenőrizze, hogy a hálózati biztonsági csoportok nincsenek kimenő internet-hozzáférés korlátozása. Ez akkor fordulhat elő, az egyes hálózati biztonsági csoportok konfigurációk. Az Azure Resource Manager áttelepíteni kívánt Virtuálisgép-bővítmények kimenő internet-hozzáférést (és a DNS-) van szükség.
  - A BGInfo bővítményt két változata létezik, és 1 és 2. verziójának nevezzük.  

      - Ha a virtuális gép az 1. verziójának BGInfo bővítményt használja, ezt a bővítményt, mert hagyhatja. A migrálási API kihagyja ezt a bővítményt. Az áttelepítés után a BGInfo bővítményt adhat hozzá.
      - Ha a virtuális gép nem használja a JSON-alapú BGInfo 2-es verziójú bővítmény, a virtuális gép létrejött, az Azure portal használatával. A migrálási API tartalmazza ezt a bővítményt az áttelepítés az Azure Resource Manager az ügynök működik, és tartalmaz kimenő internet-hozzáférést (és a DNS-).

  - **Szervizelési beállítás 1**. Ha ismeri a virtuális gépek nem fognak hozzáférni, egy működő DNS-szolgáltatás, és a virtuális gépek Azure-ügynökök dolgozik, majd eltávolítja az összes Virtuálisgép-bővítmények előkészítése előtt az áttelepítés részeként kimenő internet, telepítse a Virtuálisgép-bővítmények az áttelepítés után.
  - **Szervizelési beállítás 2**. Ha a Virtuálisgép-bővítmények túl nagy, egy küszöbértéket, egy másik lehetőség, hogy leállítás/felszabadítás minden virtuális gép áttelepítése előtt. A felszabadított virtuális gépek migrálása, majd indítsa újra őket az Azure Resource Manager-oldalán. Itt előnye, hogy a Virtuálisgép-bővítmények telepítse át. A hátránya az, hogy az összes nyilvános virtuális IP-címek irányuló el fog veszni (Ez lehet egy nem alapszintű), és természetesen a virtuális gépek leáll, így sokkal nagyobb hatást működő alkalmazások.

    > [!NOTE]
    > Ha az áttelepítés alatt futó virtuális gépek elleni egy az Azure Security Center-szabályzatok konfigurálásakor a bővítmények eltávolítása előtt le kell állítani a biztonsági házirend van szüksége, ellenkező esetben a biztonságfigyelés bővítmény újratelepítése automatikusan a virtuális gépen az Eltávolítás után.

- **A rendelkezésre állási csoportok** – virtuális hálózat (vNet) az Azure Resource Manager, a klasszikus üzembe helyezési (azaz a felhőszolgáltatás) lévő virtuális gépek minden kell egy rendelkezésre állási csoportban, vagy a virtuális gépek áttelepítése az összes nem lehet minden rendelkezésre állási csoportban. Kellene több rendelkezésre állási csoporthoz a felhőszolgáltatás nem kompatibilis az Azure Resource Manager és leállítják a migrálás.  Ezenkívül nem lehet néhány virtuális gépet egy rendelkezésre állási csoportban, és nem egy rendelkezésre állási csoportban lévő virtuális gépeket. Ennek megoldásához kell kijavítani, vagy a felhőszolgáltatás átütemezésével.  Tervezi ennek megfelelően, mivel ez időigényes lehet.

- **Webes/feldolgozói szerepkör központi telepítések** – Cloud Services webes és feldolgozói szerepköröket tartalmazó nem telepíthetők át az Azure Resource Manager. A webes/feldolgozói szerepkörök el kell távolítani a virtuális hálózat az áttelepítés megkezdése előtt.  Egy tipikus megoldási lehetőség térni webes/feldolgozói szerepkörpéldányok külön klasszikus virtuális hálózat ExpressRoute-kapcsolatcsoport is kapcsolódó, vagy a kód át (az túlmutat a jelen dokumentum az adott vitafórum) újabb PaaS App Services. Volt ismételt üzembe helyezése eset, hozzon létre egy új klasszikus virtuális hálózat, áthelyezés és ismételt üzembe helyezése a webes/feldolgozói szerepkörök, hogy új virtuális hálózatra, majd törölje a központi telepítések áthelyezett virtuális hálózatból. Nincs szükség a kód módosítására. Az új [virtuális hálózatok közötti Társviszony](../../virtual-network/virtual-network-peering-overview.md) képesség is használható együtt a webes/feldolgozói szerepköröket tartalmazó klasszikus virtuális hálózat társviszonyba állítása és egyéb ugyanabban a régióban az Azure például a virtuális hálózat alatt álló virtuális hálózatok migrálása (**, nem telepíthetők át a virtuális társhálózatok virtuális hálózati migrálás befejezése után**), ezért így ugyanazokat a lehetőségeket a teljesítmény adatvesztés nélkül, és nem késés és sávszélesség-büntetések. Igény szerinti hozzáadásával megadott [virtuális hálózatok közötti Társviszony](../../virtual-network/virtual-network-peering-overview.md), webes/feldolgozói szerepkör központi telepítések most már könnyedén enyhíthető, és nem blokkolja a migrálás az Azure Resource Manager.

- **Az Azure erőforrás-kezelő kvótái** – Azure-régiók külön kvóták és korlátokkal rendelkeznek a klasszikus és az Azure Resource Manager. Annak ellenére, hogy az áttelepítési forgatókönyvben új hardver nem vett *(azt Ön felcserélése meglévő virtuális gépek klasszikusból Azure Resource Manager)*, az Azure erőforrás-kezelő kvótái továbbra is kell teljesülniük, amelynek elegendő a kapacitása előtt áttelepítés megkezdéséhez. Az alábbiakban a jelentős korlátokat úgy tapasztaltuk problémákat okozhat.  Emelje a kvóta támogatási jegy megnyitása.

    > [!NOTE]
    > Ezek a korlátok kell áttelepíteni a jelenlegi környezet ugyanabban a régióban kell emelni.
    >

    - Hálózati illesztők
    - Terheléselosztók
    - Nyilvános IP-címek
    - Statikus nyilvános IP-címek
    - Processzormagok
    - Network Security Groups (Hálózati biztonsági csoportok)
    - Útvonaltáblák

    Az aktuális Azure erőforrás-kezelő kvótái, az Azure PowerShell legújabb verzióját a következő parancsokkal ellenőrizheti.
    
    [!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

    **COMPUTE** *(magok, a rendelkezésre állási csoportok)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Hálózati** *(virtuális hálózatok, statikus nyilvános IP-címek, nyilvános IP-címek, hálózati biztonsági csoportok, hálózati adaptereket, terheléselosztókat, útvonaltáblák)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Tárolási** *(Tárfiók)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Az Azure Resource Manager API szabályozási korlátait** – Ha rendelkezik egy megfelelő méretű környezetben (például) > 400 egy virtuális hálózaton található virtuális gépek), előfordulhat, hogy eléri az alapértelmezett API szabályozási korlátait az írási műveletek (jelenleg `1200 writes/hour`) az Azure Resource Manager. Mielőtt megkezdenék az áttelepítést, hoz létre egy támogatási jegyet a az előfizetéshez tartozó korlát növelését.


- **Virtuális gép állapota túllépte az időkorlátot kiépítési** – Ha az összes virtuális gép állapota `provisioning timed out`, ezt manuálisan kell feloldani az áttelepítés előtti lehet. Ehhez az egyetlen módszer az állásidővel által a virtuális gép (törlés, hagyja meg a lemezt, és hozza létre újra a virtuális gép) megszüntetési/reprovisioning.

- **Virtuális gép állapota RoleStateUnknown** – Ha az áttelepítés miatt megszakítja egy `role state unknown` hibaüzenet jelenik meg, vizsgálja meg a virtuális Gépet a portálon, és győződjön meg arról, hogy fut-e. Ez a hiba általában kerül azonnal a (nem kötelező szervizelési) tulajdonosa néhány perc múlva, és gyakran egy átmeneti típus gyakran látható során a virtuális gépek `start`, `stop`, `restart` műveleteket. **Ajánlott eljárás:** áttelepítési újra néhány perc múlva próbálkozzon újra.

- **Fabric-fürt nem létezik** – néhány esetben az egyes virtuális gépek nem telepíthetők át a különböző páratlan okok miatt. Egy ismert ezekben az esetekben ha a virtuális gép nemrég (belül, tehát az elmúlt hét) létrehozott és történt a föld egy Azure-fürtön, amely még nem rendelkezik Azure Resource Manager munkaterhelésekhez.  Megjelenik egy hibaüzenet, amely szerint `fabric cluster does not exist` és a virtuális gép nem migrálható. Várakozás néhány nap alatt fog általában adott probléma megoldásához, a fürt hamarosan megkapja az Azure Resource Manager-kompatibilis. Azonban egy azonnali megoldás az, hogy `stop-deallocate` a virtuális Gépet, majd folytassa a előre a migrálást, és indítsa el a virtuális gép biztonsági mentése az Azure Resource Managerben való migrálás után.

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

- Parancsikonok igénybe vehet, és ne hagyja ki a ellenőrzése és előkészítése/megszakítási tesztfuttatás áttelepítések.
- Legtöbb, ha nem, a potenciális problémákat, a felületen a ellenőrzése és előkészítése/megszakítási lépések során.

## <a name="migration"></a>Migrálás

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot kínál a

Most már készen áll, mert a környezet az ismert problémákat, amelyek dolgozott.

A valódi áttelepítések érdemes figyelembe venni:

1. Tervezze meg és a virtuális hálózathoz (az áttelepítés legkisebb egység) a prioritás növelése ütemezése.  Kezdeti lépések egyszerű virtuális hálózathoz, és halad az egyre összetettebb virtuális hálózatokkal.
2. A legtöbb ügyfél lesz a nem éles és éles környezetekben.  Utolsó ütemezése az éles környezetben.
3. **(NEM KÖTELEZŐ)**  Ütemezése a karbantartási állásidő bőven puffer, abban az esetben, ha váratlan problémák merülnek fel.
4. Kommunikálni, és abban az esetben, ha problémák merülnek fel a támogatási csapatuk igazodva.

### <a name="patterns-of-success"></a>A siker minták

A technikai útmutatást a _Lab Test_ szakaszban kell figyelembe venni, és problémák elhárításáról való migrálás előtt.  A megfelelő tesztek, az áttelepítés akkor valójában nem eseményalapú.  Éles környezetek esetén hasznos lehet további támogatásához, mint például a megbízható Microsoft-partner vagy a Microsoft Premier szintű szolgáltatást.

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

Nincs teljes egészében tesztelés okozhat problémát, és a migrálás késleltetés.  

## <a name="beyond-migration"></a>Áttelepítési túl

### <a name="technical-considerations-and-tradeoffs"></a>Technikai szempontokat és kompromisszumot kínál a

Most, hogy az Azure Resource Manager áll, állítsa teljes méretűre a platform.  Olvassa el a [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md) kapcsolatos további előnyökkel.

Dolgot figyelembe venni:

- A kötegelés az áttelepítés más tevékenységekkel.  A legtöbb ügyfél opt egy alkalmazás karbantartási időszak.  Ha igen, érdemes az üzemszünet használatával engedélyezhető az egyéb Azure Resource Manager-szolgáltatásokat, például a titkosítás és a migrálás a Managed Disks szolgáltatásba.
- Nyissa meg újra a technikai és üzleti okokból az Azure Resource Manager; Engedélyezze a további szolgáltatások csak az Azure Resource Managerrel használható, amelyek a saját környezetének megfelelően.
- Korszerűsítheti a PaaS-szolgáltatások környezetét.

### <a name="patterns-of-success"></a>A siker minták

Lehet, mely szolgáltatásokat szeretné engedélyezni az Azure Resource Managerben a szándékos.  Számos ügyfél az alábbi vonzó az Azure-környezetek esetében:

- [Szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md).
- [Egyszerűbb és több ellenőrzött üzembe Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [A címkék](../../azure-resource-manager/resource-group-using-tags.md).
- [Vezérlési tevékenység](../../azure-resource-manager/resource-group-audit.md)
- [Az Azure-szabályzatok](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

Ne feledje, hogy miért az Azure Resource Manager migrálási folyamat elkezdésekor ez klasszikus-t elindította.  Mi volt az eredeti üzleti okokból? Sikerült elérni az üzleti oka?


## <a name="next-steps"></a>További lépések

* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager PowerShell-lel](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN-átjáró klasszikusról Resource Manager-áttelepítés](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Az ExpressRoute-Kapcsolatcsoportok és a társított virtuális hálózatok migrálása a klasszikusból a Resource Manager-alapú üzemi modellbe](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager áttelepítését segítő közösségi eszközök](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A legtöbb – gyakori kérdések áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
