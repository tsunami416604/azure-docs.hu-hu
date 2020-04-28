---
title: A klasszikusról az Azure Resource Managerre való áttérés megtervezése
description: Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 10ae2e1a85d5250e4da836c6f57e3619befd9330
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865930"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése

> [!IMPORTANT]
> Ma az IaaS virtuális gépek mintegy 90%-a használja az [Azure Resource Managert.](https://azure.microsoft.com/features/resource-manager/) 2020. február 28-tól a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen nyugdíjba kerülnek. [Tudjon meg többet]( https://aka.ms/classicvmretirement) erről az eprecációról [és arról, hogy ez milyen hatással van Önre.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Bár az Azure Resource Manager számos lenyűgöző funkciót kínál, rendkívül fontos, hogy megtervezze az áttelepítési utat, hogy a dolgok zökkenőmentesen menjenek. A tervezéssel töltött idő biztosítja, hogy az áttelepítési tevékenységek végrehajtása során ne ütközjön problémákba.

A migrációs útnak négy általános szakasza van:<br>

![Áttelepítési fázisok](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Felkészülés

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

A műszaki követelmények méretétől, földrajzi elhelyezkedésétől és működési gyakorlatától függően érdemes lehet a következőket figyelembe venni:

1. Miért van szükség az Azure Resource Manager a szervezet számára?  Mi az üzleti oka az áttelepítésnek?
2. Mik az Azure Resource Manager technikai okai?  Milyen (ha van) további Azure-szolgáltatásokat szeretne kihasználni?
3. Melyik alkalmazás (vagy virtuális gépek készletei) szerepel az áttelepítésben?
4. Mely forgatókönyvek támogatottak az áttelepítési API-val?  Tekintse át a [nem támogatott szolgáltatásokat és konfigurációkat.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)
5. Az operatív csapatok mostantól támogatják az alkalmazásokat/virtuális gépeket a klasszikus és az Azure Resource Managerben egyaránt?
6. Hogyan (ha egyáltalán) az Azure Resource Manager módosítja a virtuális gép üzembe helyezését, felügyeletét és jelentéskészítési folyamatait?  Frissíteni kell a központi telepítési parancsfájlokat?
7. Mi az a kommunikációs terv, amely figyelmezteti az érdekelt feleket (végfelhasználókat, alkalmazástulajdonosokat és infrastruktúra-tulajdonosokat)?
8. A környezet összetettségétől függően kell-e olyan karbantartási időszakot tartani, amelyben az alkalmazás nem érhető el a végfelhasználók és az alkalmazástulajdonosok számára?  Ha igen, mennyi ideig?
9. Mi az a képzési terv, amely biztosítja, hogy az érdekelt felek tájékozottak és jártasak legyenek az Azure Resource Managerben?
10. Mi az áttelepítés program-kezelési vagy projektkezelési terve?
11. Melyek az Azure Resource Manager áttelepítésének és más kapcsolódó technológiai ütemterveknek az idővonalai?  Optimálisan vannak egy vonalban?

### <a name="patterns-of-success"></a>A siker mintái

A sikeres ügyfelek részletes tervekkel rendelkeznek, ahol az előző kérdéseket megvitatják, dokumentálják és szabályozzák.  Annak biztosítása, hogy a migrációs terveket széles körben közöljék a szponzorokkal és az érdekelt felekkel.  Szerelje fel magát a migrációs lehetőségek ismeretével; az alábbi áttelepítési dokumentum on-én való olvasás erősen ajánlott.

* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusról az Azure Resource Managerbe való áttelepítése a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával áttelepítheti az IaaS-erőforrásokat klasszikusról Azure Resource Managerre](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerre való áttelepítéséhez](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Buktatók elkerülése érdekében

- Nem terveznek.  A migráció technológiai lépései bizonyítottak, és az eredmény kiszámítható.
- Feltételezés, hogy a platform által támogatott áttelepítési API figyelembe veszi az összes forgatókönyvet. Olvassa el a [nem támogatott funkciók és konfigurációk,](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) hogy milyen forgatókönyvek támogatottak.
- Nem tervezi a lehetséges alkalmazáskimaradást a végfelhasználók számára.  Tervezzen elegendő puffert ahhoz, hogy megfelelően figyelmeztesse a végfelhasználókat a potenciálisan nem elérhető alkalmazásidejére.


## <a name="lab-test"></a>Laboratóriumi teszt

**A környezet replikálása és tesztáttelepítés**
  > [!NOTE]
  > A meglévő környezet pontos replikációja egy közösség által készített eszközzel történik, amelyet a Microsoft támogatási szolgálata hivatalosan nem támogat. Ezért ez egy **opcionális** lépés, de ez a legjobb módja annak, hogy megtudja, problémák érintése nélkül az éles környezetben. Ha egy közösség által készített eszköz használata nem lehetséges, akkor olvassa el az alábbi Érvényesítés/Előkészítés/Megszakítás száraz futtatása javaslatot.
  >

  A zökkenőmentes áttelepítés biztosításának legjobb módja a pontos forgatókönyv (számítás, hálózatkezelés és tárolás) laboratóriumi vizsgálatának végrehajtása. Ez segít biztosítani:

- Egy teljesen különálló labor vagy egy meglévő, nem éles környezet tesztelése. Javasoljuk, hogy egy teljesen különálló labor, amely többször is áttelepíthető, és lehet roncsian módosítani.  Az alábbiakban felsoroljuk a metaadatok gyűjtésére/hidratálására a valós előfizetésekből származó parancsfájlokat.
- Célszerű a labort külön előfizetésben létrehozni. Ennek az az oka, hogy a labor lesz lebontották többször, és miután egy külön, elszigetelt előfizetés csökkenti annak az esélyét, hogy valami valós lesz véletlenül törlődik.

  Ez az AsmMetadataParser eszközzel valósítható meg. [Tudjon meg többet erről az eszközről itt](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>A siker mintái

A következő problémák at fedezték fel sok a nagyobb migráció. Ez nem teljes körű lista, és további részletekért olvassa el a [nem támogatott szolgáltatásokat és konfigurációkat.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)  Lehet, hogy nem találkozik ezekkel a technikai problémákkal, de ha ezeket az áttelepítési kísérlet előtt megoldja, az zökkenőmentesebb élményt biztosít.

- **Végezze el a validálást/előkészítést/megszakításos száraz futtatást** – Ez talán a legfontosabb lépés az Azure Resource Manager áttelepítésének sikeressággal való biztosításához. Az áttelepítési API három fő lépésből áll: Érvényesítés, Előkészítés és Véglegesítés. Az ellenőrzés leolvassa a klasszikus környezet állapotát, és az összes probléma eredményét adja vissza. Azonban, mert bizonyos problémák előfordulhatnak az Azure Resource Manager veremben, érvényesítése nem fog elkapni mindent. A következő lépés az áttelepítési folyamat, Előkészítés segít felfedni ezeket a problémákat. Az előkészítés áthelyezi a metaadatokat a Klasszikus szolgáltatásból az Azure Resource Managerbe, de nem véglegesíti az áthelyezést, és nem távolít el vagy módosít semmit a klasszikus oldalon. A próbafuttatás magában foglalja az áttelepítés előkészítését, majd az áttelepítés előkészítésének megszakítását **(nem véglegesítését).** A cél a valid/prepare/abort száraz futtatás, hogy az összes metaadatot az Azure Resource Manager verem, vizsgálja meg (*programozott vagy a portálon*), és ellenőrizze, hogy minden megfelelően vándorol át, és a munka technikai problémák.  Ez is ad ön egyfajta migráció időtartama, így a terv állásidő kell.  Az érvényesítés/előkészítés/megszakítás nem okoz felhasználói leállást; ezért nem zavarja az alkalmazások használatát.
  - Az alábbi tételeket meg kell oldani a száraz futtatás előtt, de a szárazfuttatási teszt is biztonságosan kimossa ezeket az előkészítési lépéseket, ha kimaradnak. A vállalati áttelepítés során a próbaüzem biztonságos és felbecsülhetetlen módja a migrációra való felkészültség biztosításának.
  - Előkészítés futtatásakor a vezérlősík (Azure felügyeleti műveletek) zárolva lesz a teljes virtuális hálózatra, így nem lehet módosítani a virtuális gép metaadatait az érvényesítés/előkészítés/megszakítás során.  De egyébként minden alkalmazás függvény (RD, VM használat, stb) lesz érintetlen.  A virtuális gépek felhasználói nem fogják tudni, hogy a próbafuttatás végrehajtása folyamatban van.

- **Express Route áramkörök és VPN**. Jelenleg az engedélyezési kapcsolatokkal rendelkező expressz útvonalátjárók nem telepíthetők át állásidő nélkül. A kerülő megoldásról az [ExpressRoute-áramkörök és a kapcsolódó virtuális hálózatok áttelepítése a klasszikusról az Erőforrás-kezelő telepítési modelljére című](../../expressroute/expressroute-migration-classic-resource-manager.md)témakörben talál.

- **Virtuálisgép-bővítmények** – A virtuálisgép-bővítmények potenciálisan az egyik legnagyobb úttorlasza a futó virtuális gépek áttelepítése. Virtuálisgép-bővítmények szervizelése is eltarthat felfelé 1-2 nap, ezért ennek megfelelően tervezze n.  Egy működő Azure-ügynök szükséges a virtuális gépek virtuális gépek virtuális gépeinek virtuális gép-bővítmény állapotának jelentéséhez. Ha az állapot jön vissza, mint rossz egy futó virtuális gép, ez leállítja az áttelepítést. Maga az ügynök nem kell üzemképes állapotban lennie az áttelepítés engedélyezéséhez, de ha a virtuális gépen léteznek bővítmények, akkor mind a működő ügynökre, mind a kimenő internetkapcsolatra (A DNS-sel) szükség lesz az áttelepítéshez.
  - Ha az áttelepítés során megszakad a DNS-kiszolgálóval való kapcsolat, a BGInfo 1-es verzióján kívül az összes virtuálisgép-bővítmény elvész. \* először el kell távolítani minden virtuális gép ről az áttelepítés előkészítése előtt, majd az Azure Resource Manager áttelepítése után újra hozzá kell adni a virtuális géphez.  **Ez csak a futó virtuális gépek.**  Ha a virtuális gépek levan állítva felszabadított, virtuálisgép-bővítmények nem kell eltávolítani.

  > [!NOTE]
  > Számos bővítmény, például az Azure diagnosztika és a biztonsági központ figyelése újratelepíti magát az áttelepítés után, így eltávolítása nem jelent problémát.

  - Ezenkívül győződjön meg arról, hogy a hálózati biztonsági csoportok nem korlátozzák a kimenő internet-hozzáférést. Ez néhány hálózati biztonsági csoport konfigurációjával fordulhat elő. Kimenő internet-hozzáférés (és DNS) szükséges a virtuálisgép-bővítmények azure Resource Manager áttelepítéséhez.
  - A BGInfo kiterjesztésnek két verziója létezik, és 1-es és 2-es verziónak nevezik őket.  

      - Ha a virtuális gép a BGInfo 1-es verzióját használja, akkor ezt a bővítményt úgy hagyhatja meg, ahogy van. Az áttelepítési API kihagyja ezt a bővítményt. A BGInfo bővítmény az áttelepítés után adható hozzá.
      - Ha a virtuális gép a JSON-alapú BGInfo 2-es verzióbővítményt használja, a virtuális gép az Azure Portal használatával lett létrehozva. Az áttelepítési API tartalmazza ezt a bővítményt az Azure Resource Managerbe való migrálásban, feltéve, hogy az ügynök működik, és kimenő internet-hozzáféréssel (és DNS-sel) rendelkezik.

  - **1. szervizelési lehetőség**. Ha tudja, hogy a virtuális gépek nem rendelkeznek kimenő internet-hozzáféréssel, egy működő DNS-szolgáltatással és működő Azure-ügynökökkel a virtuális gépeken, majd távolítsa el az összes virtuálisgép-bővítményt az áttelepítés előtt, majd telepítse újra a virtuálisgép-bővítményeket az áttelepítés után.
  - **2. szervizelési lehetőség**. Ha a virtuálisgép-bővítmények túl nagy akadály, egy másik lehetőség az összes virtuális gép leállítása/felszabadítása az áttelepítés előtt. Telepítse át a felszabadított virtuális gépeket, majd indítsa újra őket az Azure Resource Manager oldalán. Az előny itt az, hogy a virtuálisgép-bővítmények áttelepülnek. A hátránya az, hogy az összes nyilvános néző virtuális IP-k elvesznek (ez lehet egy nem induló), és nyilvánvalóan a virtuális gépek leáll, ami sokkal nagyobb hatással van a munkaalkalmazások.

    > [!NOTE]
    > Ha egy Azure Security Center-szabályzat van konfigurálva a futó virtuális gépek áttelepítése ellen, a biztonsági szabályzatot le kell állítani a bővítmények eltávolítása előtt, különben a biztonsági figyelési bővítmény automatikusan újratelepítést kap a virtuális gépre eltávolítása után.

- **Rendelkezésre állási készletek** – Egy virtuális hálózat (vNet) az Azure Resource Manager, a klasszikus üzembe helyezés (azaz a felhőszolgáltatás) tartalmazott virtuális gépek kell minden egy rendelkezésre állási készlet, vagy a virtuális gépek nem lehet semmilyen rendelkezésre állási készletben. A felhőszolgáltatásban egynél több rendelkezésre állási készlet nem kompatibilis az Azure Resource Managerrel, és leállítja az áttelepítést.  Emellett nem lehet néhány virtuális gép egy rendelkezésre állási csoportban, és néhány virtuális gép nem egy rendelkezésre állási csoportban. A probléma megoldásához ki kell újítania vagy át kell szerveznie a felhőszolgáltatást.  Ennek megfelelően tervezze meg, mivel ez időigényes lehet.

- **Webes/feldolgozói szerepkör-telepítések** – A webes és feldolgozói szerepköröket tartalmazó felhőszolgáltatások nem tudnak áttérni az Azure Resource Managerbe. A webes és feldolgozói szerepkörök tartalmának áttelepítéséhez magának a kódnak az újabb PaaS App Services szolgáltatásba kell áttelepítenie (ez a vitafórum nem tartozik a dokumentum hatálya alá). Ha azt szeretné, hogy a web/feldolgozó szerepkörök, ahogy van, de migrálni a klasszikus virtuális gépek az Erőforrás-kezelő telepítési modell, a webes/feldolgozói szerepkörök először el kell távolítani a virtuális hálózatból, mielőtt az áttelepítés elindulhat.  Egy tipikus megoldás az, hogy csak helyezze át a web/feldolgozó szerepkör példányait egy külön klasszikus virtuális hálózatba, amely szintén kapcsolódik egy ExpressRoute-kapcsolat. Az előbbi újraüzembe helyezés esetén hozzon létre egy új klasszikus virtuális hálózatot, helyezze át/telepítse újra a webes/feldolgozószerepköröket az új virtuális hálózatra, majd törölje a központi telepítéseket az áthelyezett virtuális hálózatról. Nincs szükség kódmódosításra. Az új [virtuális hálózati társviszony-létesítési](../../virtual-network/virtual-network-peering-overview.md) képesség használható a klasszikus virtuális hálózat, amely a webes/feldolgozói szerepkörök és más virtuális hálózatok ugyanabban az Azure-régióban, például a virtuális hálózat áttelepítése **(miután a virtuális hálózati áttelepítés befejeződött, mint társviszony-létesített virtuális hálózatok nem telepíthető),** így biztosítva ugyanazokat a képességeket teljesítménycsökkenés nélkül, és nem késés/sávszélesség szankciókat. A virtuális [hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md)hozzáadása miatt a webes/feldolgozói szerepkör-telepítések mostantól könnyen csökkenthetők, és nem akadályozhatják az Azure Resource Managerbe való migrálást.

- **Azure Resource Manager kvóták** – Az Azure-régiók külön kvótákat/korlátokat mind a klasszikus, mind az Azure Resource Manager számára külön kvótákkal rendelkeznek. Annak ellenére, hogy egy áttelepítési forgatókönyv ben nincs új hardver felhasználása *(a meglévő virtuális gépekklasszikusról Azure Resource Managerre cseréljük),* az Azure Resource Manager-kvótáknak továbbra is elegendő kapacitással kell lenniük az áttelepítés megkezdése előtt. Az alábbiakban felsoroljuk a főbb határértékeket láttunk problémákat okozni.  Nyisson meg egy kvótatámogatási jegyet a korlátok növeléséhez.

    > [!NOTE]
    > Ezeket a korlátokat ugyanabban a régióban kell növelni, mint az aktuális környezetet, hogy áttelepítsék.
    >

  - Hálózati illesztők
  - Terheléselosztók
  - Nyilvános IP-címek
  - Statikus nyilvános IP-k
  - Cores
  - Network Security Groups (Hálózati biztonsági csoportok)
  - Útvonaltáblák

    A jelenlegi Azure Resource Manager-kvóták at a következő parancsokkal ellenőrizheti az Azure PowerShell legújabb verziójával.



    **Számítási** *adatok (magok, rendelkezésre állási készletek)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Hálózat** *(virtuális hálózatok, statikus nyilvános IP-k, nyilvános IP-k, hálózati biztonsági csoportok, hálózati adapterek, terheléselosztók, útvonaltáblák)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Tárterület** *(tárfiók)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Azure Resource Manager API-szabályozás korlátok** – Ha elég nagy a környezet (pl. > 400 virtuális gép egy virtuális hálózatban), előfordulhat, hogy eléri az `1200 writes/hour`alapértelmezett API-szabályozási korlátok írási (jelenleg) az Azure Resource Managerben. Az áttelepítés megkezdése előtt meg kell emelnie egy támogatási jegyet az előfizetési korlát növeléséhez.


- **Kiépítése idővel rendelkező virtuálisgép állapota** – Ha bármely `provisioning timed out`virtuális gép állapota, ezt meg kell oldani az áttelepítés előtt. Az egyetlen módja ennek az állásidő a virtuális gép deprovisioning/reprovisioning (törölje, tartsa meg a lemezt, és újra a virtuális gép).

- **RoleStateUnknown virtuális gép állapota** – Ha `role state unknown` az áttelepítés hibaüzenet miatt leáll, vizsgálja meg a virtuális gép a portálhasználatával, és győződjön meg arról, hogy fut. Ez a hiba általában néhány perc után megszűnik (nincs szükség szervizelésre), és gyakran átmeneti `start` `stop`típus, `restart` amely gyakran látható a virtuális gép , műveletek során. **Ajánlott eljárás:** néhány perc múlva próbálkozzon újra az áttelepítéssel.

- **Fabric Cluster nem létezik** – Bizonyos esetekben bizonyos virtuális gépek nem telepíthetők át különböző furcsa okok miatt. Az egyik ilyen ismert eset az, ha a virtuális gép nemrég jött létre (az elmúlt egy héten belül, vagy úgy), és történt egy Azure-fürt, amely még nem rendelkezik az Azure Resource Manager számítási feladatok.  Megjelenik egy hiba, `fabric cluster does not exist` amely azt mondja, és a virtuális gép nem telepíthető. A várakozás néhány napig általában megoldja ezt a problémát, mivel a fürt hamarosan engedélyezve lesz az Azure Resource Manager. Azonban egy azonnali megoldás `stop-deallocate` a virtuális gép, majd folytassa az áttelepítés, és indítsa el a virtuális gép biztonsági másolatot az Azure Resource Manager áttelepítése után.

### <a name="pitfalls-to-avoid"></a>Buktatók elkerülése érdekében

- Ne vegyen parancsikonokat, és hagyja ki a valid/prepare/abort száraz futtatás áttelepítését.
- A legtöbb, ha nem az összes, a potenciális problémák felszínre az ellenőrzés / előkészítés / megszakítás lépéseket.

## <a name="migration"></a>Migrálás

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

Most már készen áll, mert dolgozott át az ismert problémák at a környezettel.

A valós áttelepítések, érdemes megfontolni:

1. Tervezze meg és ütemezze a virtuális hálózatot (az áttelepítés legkisebb egységét) növekvő prioritással.  Először az egyszerű virtuális hálózatokat végezze el, és haladj a bonyolultabb virtuális hálózatokkal.
2. A legtöbb ügyfél nem éles és éles környezetben.  A termelés ütemezése utoljára.
3. **(NEM KÖTELEZŐ)** Ütemezze a karbantartási állásidőt bő pufferrel, ha váratlan problémák merülnek fel.
4. Kommunikáció és összehangolja a támogató csapatok, ha problémák merülnek fel.

### <a name="patterns-of-success"></a>A siker mintái

A _laborteszt_ szakasz technikai útmutatását figyelembe kell venni, és enyhíteni kell a tényleges áttelepítés előtt.  A megfelelő tesztelés, a migráció valójában nem esemény.  Éles környezetben hasznos lehet további támogatás, például megbízható Microsoft-partner vagy Premier Microsoft-szolgáltatások.

### <a name="pitfalls-to-avoid"></a>Buktatók elkerülése érdekében

A nem teljes tesztelés problémákat okozhat, és késleltetheti az áttelepítést.  

## <a name="beyond-migration"></a>A migráción túl

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

Most, hogy az Azure Resource Managerben van, maximalizálja a platformot.  Olvassa el [az Azure Resource Manager áttekintését,](../../azure-resource-manager/management/overview.md) hogy megismerje a további előnyöket.

Megfontolandó dolgok:

- A migráció összekapcsolása más tevékenységekkel.  A legtöbb ügyfél az alkalmazáskarbantartási időszakot választja.  Ha igen, érdemes lehet ezt az állásidőt használni, hogy más Azure Resource Manager képességek, például a titkosítás és a felügyelt lemezekre való áttelepítés engedélyezéséhez.
- Az Azure Resource Manager technikai és üzleti okainak újbóli felkeresése; a csak az Azure Resource Manageren elérhető, a környezetre vonatkozó további szolgáltatásokat.
- Modernizálja környezetét a PaaS szolgáltatásokkal.

### <a name="patterns-of-success"></a>A siker mintái

Legyen céltudatos, hogy milyen szolgáltatásokat szeretne most engedélyezni az Azure Resource Managerben.  Sok ügyfél az alábbi lenyűgözőnek találja az Azure-környezeteit:

- [Szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md).
- [Azure Resource Manager-sablonok a könnyebb és ellenőrzöttebb telepítésérdekében.](../../azure-resource-manager/templates/overview.md)
- [Címkék](../../azure-resource-manager/management/tag-resources.md).
- [Tevékenység-ellenőrzés](../../azure-resource-manager/management/view-activity-logs.md)
- [Azure-szabályzatok](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Buktatók elkerülése érdekében

Ne feledje, hogy miért indította el ezt a klasszikus azure Resource Manager áttelepítési utazást.  Mi volt az eredeti üzleti ok? Elérted az üzleti okot?


## <a name="next-steps"></a>További lépések

* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusról az Azure Resource Managerbe való áttelepítése a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával áttelepítheti az IaaS-erőforrásokat klasszikusról Azure Resource Managerre](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN-átjáró klasszikus az Erőforrás-kezelő áttelepítéséhez](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute-áramkörök és a kapcsolódó virtuális hálózatok áttelepítése a klasszikusról az Erőforrás-kezelő telepítési modelljére](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerre való áttelepítéséhez](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
