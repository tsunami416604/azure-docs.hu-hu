---
title: Vészhelyreállítás beállítása egy többszintes Azure Site Recovery használatával az IIS-alapú webes alkalmazás fo |} A Microsoft Docs
description: Ismerje meg, hogyan replikáljon az IIS webkiszolgáló farm virtuális gépek Azure Site Recovery használatával.
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: nisoneji
ms.openlocfilehash: 49435665ae1e99dd2b9696e5e5bb048e438dcc4c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832956"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Vészhelyreállítás beállítása a többrétegű az IIS-alapú webalkalmazás

Alkalmazás szoftver egy szervezet üzleti termelékenység motorját. A webalkalmazások különböző a szervezeten belüli különböző célt szolgálhat ki. Előfordulhat, hogy egyes alkalmazások, például a bérszámfejtési feldolgozását, a pénzügyi alkalmazások és az ügyfelek által használt webhelyeken használt alkalmazásokat a szervezet kritikus fontosságú. Termelékenység az adatvesztés elkerülése érdekében, fontos a szervezet számára, hogy ezek az alkalmazások folyamatosan folyamatosan üzemben rendelkezik. Ami még fontosabb hogy ezek az alkalmazások folyamatosan rendelkezésre álló megakadályozhatja kárt a márkáról vagy a szervezet képe.

Kritikus fontosságú webalkalmazásokat általában beállítása, többrétegű alkalmazások: a webes, az adatbázis és az alkalmazás különböző szinten van. Mellett alatt helyezkednek el különböző szinteken, az alkalmazások is használhatják az több kiszolgálót az egyes szintek a irányuló forgalom terheléselosztásához. Ezenkívül a különböző szintek között, és a webkiszolgálón leképezések előfordulhat, hogy alapján statikus IP-címeket. Feladatátvétel esetén ezeket a leképezéseket kell frissíteni, különösen akkor, ha több webhely vannak konfigurálva a webkiszolgálón. Ha webes alkalmazásokhoz az SSL használatához frissítenie kell a tanúsítvány kötéseit.

Hagyományos helyreállítási módszer, amely a replikáció nem alapján járnak, biztonsági mentése különböző konfigurációs fájlokat, beállításjegyzék-beállítások, kötések, egyéni összetevők (COM vagy .NET), tartalmat és a tanúsítványok. Fájlok állíthatók helyre, manuális lépések készletével. A hagyományos helyreállítási biztonsági mentésére és helyreállítására manuálisan a fájlok módszereket nehézkes, sok hibalehetőséget rejtő és nem méretezhető. Például előfordulhat, hogy könnyen elfelejti biztonsági másolatot készíteni a tanúsítványokat. A feladatátvételt követően nincs választási lehetőség, de a kiszolgáló új tanúsítványok vásárlása van hátra.

Egy jó vész-helyreállítási megoldás támogatja a modellezési helyreállítási tervek összetett architektúrák számára. Azt is tudnia kell testre szabott lépések hozzáadása a helyreállítási tervbe a rétegek közötti alkalmazás-hozzárendelések kezeléséhez. Katasztrófa esetén az alkalmazás-hozzárendelések, amely segít az alacsonyabb RTO egyetlen kattintással, hogy jóváhagyást megoldást nyújtanak.

Ez a cikk azt ismerteti, hogyan védheti meg egy webalkalmazást, amely alapján az Internet Information Services (IIS) használatával [Azure Site Recovery](site-recovery-overview.md). Az a cikk ismerteti az ajánlott eljárások a háromrétegű, az IIS-alapú webes alkalmazás Azure vészhelyreállítási próbát módjáról és hogyan végezhet feladatátvételt az alkalmazás az Azure-ba történő replikálásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan a következő feladatokat végezheti el:

* [A virtuális gépek replikálása az Azure-bA](vmware-azure-tutorial.md)
* [A helyreállítási hálózat tervezése](site-recovery-network-design.md)
* [Végezzen feladatátvételi tesztet az Azure-bA](site-recovery-test-failover-to-azure.md)
* [Ehhez a feladatátvétel az Azure-bA](site-recovery-failover.md)
* [A tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Üzembe helyezési minták
Az IIS-alapú webes alkalmazás általában a következő a következő üzembe helyezési minták egyikét:

**1. telepítési minta**

Az IIS-alapú rendelkező webfarm alkalmazáskérelmek útválasztása (ARR) egy IIS-kiszolgáló és az SQL Server.

![Az IIS-alapú webfarm, amelynek mindhárom szintet ábrája](./media/site-recovery-iis/deployment-pattern1.png)

**Üzembe helyezési minta 2**

Az ARR, az IIS-kiszolgáló, az alkalmazáskiszolgáló és az SQL Server egy IIS-alapú webes farm.

![Az IIS-alapú webfarm, amely négy szinten rendelkezik ábrája](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery támogatása

Az ebben a cikkben szereplő példák a VMware virtuális gépek és a Windows Server 2012 R2 Enterprise IIS 7.5 használunk. A Site Recovery replikációs nem alkalmazás-specifikus, mert ebben a cikkben szereplő ajánlások várhatóan az alábbi táblázatban és az IIS különböző verzióit a felsorolt forgatókönyvek a alkalmazni.

### <a name="source-and-target"></a>Forrás és cél

Forgatókönyv | Egy másodlagos helyre | Az Azure-ba
--- | --- | ---
Hyper-V | Igen | Igen
VMware | Igen | Igen
Fizikai kiszolgáló | Nem | Igen
Azure|NA|Igen

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az IIS webkiszolgáló farm virtuális gépek replikálása Azure-ba, kövesse az útmutató [az Azure-bA a Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

Statikus IP-címet használja, ha azt szeretné, hogy a virtuális gép IP-címet is megadhat. Az IP-cím megadásához keresse fel **számítás és hálózat beállításlapon** > **cél IP-címet**.

![A cél IP-cím beállítása a Site Recovery számítás és hálózat panelen bemutató képernyőkép](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja az alkalmazás-előkészítés különböző rétegek egy többrétegű alkalmazást, a feladatátvétel alatt. Alkalmazás-előkészítés segít fenntartani a konzisztenciát alkalmazás. Amikor létrehoz egy helyreállítási terv többszintű webalkalmazások, teljes körű lépéseket ismertetett [helyreállítási terv létrehozása a Site Recovery használatával](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok felvétele
Egy tipikus többrétegű IIS webalkalmazást a következő összetevőkből áll:
* Adatbázisréteg, amely rendelkezik az SQL virtuális gépek.
* A webes szint, amely tartalmaz egy IIS-kiszolgáló és a egy alkalmazásrétegbe. 

Virtuális gépeket ad hozzá különböző csoportokhoz a réteg alapján:

1. Helyreállítási terv létrehozásához. Az adatbázis szintű virtuális gépek 1 csoport hozzáadása. Ez biztosítja, hogy az adatbázis csomag virtuális gépein utolsó Leállítás és első kerülnek sorra.
1. Az alkalmazás szintű virtuális gépek 2 csoport hozzáadása. Ez biztosítja, hogy alkalmazás szintű virtuális gépek kerülnek a rendszer visszaállította az adatbázisszint után.
1. Adja hozzá a webes szintű virtuális gépeket a 3 csoport. Ez biztosítja, hogy webes szintű virtuális gépek kerülnek a rendszer visszaállította az alkalmazásrétegek után.
1. Adja hozzá a terhelést a virtuális gépek terheléselosztása a csoport 4. Ez biztosítja, hogy terhelést a virtuális gépek terheléselosztása kerülnek a webes szint leállását követően.

További információkért lásd: [a helyreállítási terv testreszabása](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>A helyreállítási terv parancsfájl hozzáadása
Az IIS webfarm a megfelelő működéshez szüksége lehet bizonyos műveleteket az Azure-beli virtuális gépek feladatátvétel utáni, vagy tesztcélú feladatátvétel alatt. Egyes feladatátvétel utáni műveletek automatizálható. Például a DNS-bejegyzés frissítése, egy hely kötésének módosítása, vagy módosítsa a kapcsolati karakterlánc megfelelő parancsfájlokat hozzáadásával a helyreállítási tervbe. [A VMM-parancsfájl hozzáadása a helyreállítási terv](site-recovery-how-to-add-vmmscript.md) azt ismerteti, hogyan hozhatja létre automatizált feladatok parancsfájl használatával.

#### <a name="dns-update"></a>A DNS-frissítés
DNS dinamikus DNS-frissítési van konfigurálva, ha virtuális gépek általában a DNS frissítése az új IP-cím amikor elindítja. Ha szeretne hozzáadni a DNS frissítése az új IP-címei a virtuális gépek, adjon hozzá egy explicit lépés egy [DNS IP-parancsprogramot](https://aka.ms/asr-dns-update) műveletek a feladatátvétel utáni műveletek a helyreállítási terv csoportok.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Kapcsolati karakterláncát az alkalmazás web.config
A kapcsolati karakterláncot adja meg az adatbázis, amely a webhely kommunikál. Ha a kapcsolati karakterlánc nevét a database virtuális gép végzi, nincs további lépéseket kell szükséges feladatátvétel után. Az alkalmazás automatikusan képes kommunikálni az adatbázissal. Ha az adatbázis-virtuális gép IP-címét őrzi, azt nem is frissítenie kell a kapcsolati karakterláncot. 

Ha a kapcsolati karakterlánc hivatkozik az adatbázis-virtuális gép IP-cím használatával, kell lennie a frissített feladatátvétel után. Például a következő kapcsolódási karakterlánc pontok az adatbázishoz IP-cím 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Frissítse a kapcsolati karakterláncot, a webes szint, adjon hozzá egy [IIS kapcsolat frissítési parancsfájl](https://aka.ms/asr-update-webtier-script-classic) a helyreállítási tervben szereplő 3 csoport után.

#### <a name="site-bindings-for-the-application"></a>Az alkalmazás a hely kötései
Minden webhely kötési információ áll. Kötési információt tartalmaz, az IP-cím, amelyen az IIS-kiszolgáló figyel a kérések a hely, a port számát és a gazdagépek nevének a hely kötésének típusát. A feladatátvétel során szükség lehet frissíteni az ilyen kötést ahhoz, ha az őket társított IP-cím megváltozik.

> [!NOTE]
>
> Ha a hely kötésének **összes ki nem osztott**, nem kell frissíteni a kötés-feladatátvétel után. Emellett ha egy helyhez társított IP-cím nem módosított feladatátvételen átesett, nem kell a webhelykötés frissítése. (Az IP-cím megőrzése a hálózati architektúra és az elsődleges és helyreállítási-helyekhez rendelt alhálózatok is függ. Őket frissítése nem lehetséges a szervezet számára.)

![Képernyőkép az SSL-kötés beállítása](./media/site-recovery-iis/sslbinding.png)

Ha az IP-cím társított hely, frissítse az összes hely kötései az új IP-cím. A hely kötései módosításához adjon hozzá egy [IIS webes rétegbeli frissítési parancsfájl](https://aka.ms/asr-web-tier-update-runbook-classic) a helyreállítási tervben szereplő 3 csoport után.

#### <a name="update-the-load-balancer-ip-address"></a>Frissítés a terheléselosztó IP-címe
Ha az ARR virtuális gépként, az IP-cím frissítéséhez adja hozzá egy [IIS ARR feladatátvételi parancsfájl](https://aka.ms/asr-iis-arrtier-failover-script-classic) csoport 4 után.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-tanúsítvány kötés egy HTTPS-kapcsolat számára
Egy webhely rendelkezhet társított SSL-tanúsítvány, amely biztosítja, hogy a webkiszolgáló és a felhasználó böngészőjében közötti biztonságos kommunikációhoz. Ha a webhely HTTPS-kapcsolatot, és is van egy társított HTTPS hely kötése az IIS-kiszolgáló IP-cím SSL-tanúsítvány kötéssel, hozzá kell adnia egy új webhelykötések a tanúsítvány az IIS virtuális gép feladatátvétel utáni az IP-címmel.

Az SSL-tanúsítvány elleni ezeket az összetevőket adhatnak ki:

* A webhely teljesen minősített tartománynevét.
* A kiszolgáló nevét.
* Egy helyettesítő tanúsítványt a tartomány nevét.  
* An IP address. Az SSL-tanúsítványt az IIS-kiszolgáló IP-címét kiadására kerül, ha egy másik SSL-tanúsítványt kell az Azure webhelyén, az IIS-kiszolgáló IP-címét kell állították. A tanúsítvány további SSL-kötéssel kell létrehozni. Emiatt használatát nem javasoljuk egy SSL-tanúsítványt az IP-cím alapján. Ez a beállítás kevésbé széles körben használt, és fogja a providerhez kiadott új tanúsítvány hatóság és böngésző fórum változások hamarosan elavulttá válik.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Frissítés a webes szint és az alkalmazásrétegek közötti függőség
Ha az alkalmazás-specifikus függ, hogy a virtuális gépek IP-címe alapján, frissítenie kell a függőségi feladatátvételt követően.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Az Azure Portalon válassza ki a helyreállítási tárban.
2. Válassza ki a helyreállítási tervet, amelyet az IIS webfarm létrehozott.
3. Kattintson a **Feladatátvétel tesztelése** elemre.
4. A teszt feladatátvételi folyamat indításához válassza ki a helyreállítási pont és az Azure virtuális hálózat.
5. A másodlagos környezet esetén ellenőrzések is végezhet.
6. Ha ellenőrzés befejeződött, tisztítsa meg a teszt feladatátvételi környezetet válassza **ellenőrzések elvégzéséhez**.

További információkért lásd: [az Azure-bA a Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. Az Azure Portalon válassza ki a helyreállítási tárban.
1. Válassza ki a helyreállítási tervet, amelyet az IIS webfarm létrehozott.
1. Válassza a **Feladatátvétel** lehetőséget.
1. A feladatátvételi folyamat indításához válassza ki a helyreállítási pontot.

További információkért lásd: [feladatátvétel a Site Recoveryben](site-recovery-failover.md).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [más alkalmazások replikálása](site-recovery-workload.md) Site Recovery használatával.
