---
title: Vészhelyreállítás beállítása Egy IIS-webalkalmazáshoz az Azure Site Recovery használatával
description: Ismerje meg, hogyan replikálható az IIS webfarm virtuális gépei az Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: dfed398124ca20771e169f6f9e7d08d4d799ee1e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478292"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Vészhelyreállítás beállítása többrétegű IIS-alapú webalkalmazáshoz

Alkalmazás szoftver a motor az üzleti termelékenység egy szervezetben. A különböző webes alkalmazások különböző célokat szolgálhatnak egy szervezetben. Egyes alkalmazások, például a bérszámfejtéshez használt alkalmazások, a pénzügyi alkalmazások és az ügyfélfelé néző webhelyek kritikus fontosságúak lehetnek a szervezet számára. A termelékenység csökkenésének elkerülése érdekében fontos, hogy a szervezet folyamatosan működjön és működjön. Ennél is fontosabb, hogy ezek az alkalmazások következetesen elérhetők, segíthetnek megelőzni a szervezet márkájának vagy arculatának károsodását.

A kritikus fontosságú webalkalmazások általában többrétegű alkalmazásokként vannak beállítva: a web, az adatbázis és az alkalmazás különböző szinteken vannak. Amellett, hogy különböző szintek között oszlik meg, az alkalmazások is használhatnak több kiszolgálót minden rétegben a forgalom terheléselosztása. Ezenkívül a különböző rétegek és a webkiszolgáló közötti leképezések statikus IP-címeken alapulhatnak. Feladatátvételkor néhány ilyen leképezést frissíteni kell, különösen akkor, ha több webhely van konfigurálva a webkiszolgálón. Ha a webalkalmazások TLS-t használnak, frissítenie kell a tanúsítványkötéseket.

A nem replikáción alapuló hagyományos helyreállítási módszerek különböző konfigurációs fájlok, beállításjegyzék-beállítások, kötések, egyéni összetevők (COM vagy .NET), tartalom és tanúsítványok biztonsági mentését foglalják magukban. A fájlok at manuális lépések sorozata állítja helyre. A fájlok biztonsági mentésének és manuális helyreállításának hagyományos helyreállítási módszerei nehézkesek, hibalehetőségek, és nem méretezhetők. Előfordulhat például, hogy egyszerűen elfelejt biztonsági másolatot tenni a tanúsítványokról. A feladatátvétel után nem marad más választása, mint új tanúsítványokat vásárolni a kiszolgálóhoz.

Egy jó vész-helyreállítási megoldás támogatja a modellezéshelyreállítási tervek összetett alkalmazásarchitektúrák. Emellett testre szabott lépéseket is hozzáadhat a helyreállítási tervhez a rétegek közötti alkalmazásleképezések kezeléséhez. Katasztrófa esetén az alkalmazásleképezések egykattintásos, biztos megoldást biztosítanak, amely alacsonyabb RTO-hoz vezet.

Ez a cikk azt ismerteti, hogy miként védheti meg az Internet Information Services (IIS) szolgáltatáson alapuló webalkalmazásokat az [Azure Site Recovery](site-recovery-overview.md)használatával. A cikk ismerteti a háromrétegű, IIS-alapú webalkalmazás Azure-ba replikálásának ajánlott, és hogyan lehet feladatátvételt az alkalmazás az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan kell elvégezni a következő feladatokat:

* [Virtuális gép replikálása az Azure-ba](vmware-azure-tutorial.md)
* [Helyreállítási hálózat tervezése](site-recovery-network-design.md)
* [Tesztfeladat-átvétel az Azure-ba](site-recovery-test-failover-to-azure.md)
* [Feladatátvétel az Azure-ba](site-recovery-failover.md)
* [Tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Telepítési minták
Az IIS-alapú webalkalmazások általában az alábbi telepítési minták egyikét követik:

**1. telepítési minta**

IIS-alapú webfarm alkalmazáskérelem-útválasztással (ARR), IIS-kiszolgálóval és SQL Server kiszolgálóval.

![Három rétegből álló IIS-alapú webfarm diagramja](./media/site-recovery-iis/deployment-pattern1.png)

**Üzembe helyezési minta 2**

IIS-alapú webfarm ARR-rel, IIS-kiszolgálóval, alkalmazáskiszolgálóval és SQL Server kiszolgálóval.

![Négy rétegből álló IIS-alapú webfarm diagramja](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery támogatása

A jelen cikkben szereplő példákat a Windows Server 2012 R2 Enterprise rendszeren az IIS 7.5-ös virtuális gépekkel rendelkező virtuális gépeket használjuk. Mivel a Site Recovery replikációja nem alkalmazásspecifikus, a jelen cikkben szereplő javaslatok nak az alábbi táblázatban felsorolt esetekben és az IIS különböző verzióiban kell alkalmazniuk.

### <a name="source-and-target"></a>Forrás és cél

Forgatókönyv | Egy másodlagos helyre | Az Azure-ba
--- | --- | ---
Hyper-V | Igen | Igen
VMware | Igen | Igen
Fizikai kiszolgáló | Nem | Igen
Azure|NA|Igen

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az összes IIS-webfarm-virtuális gép Azure-ba történő replikálásának megkezdéséhez kövesse a [Feladatátvétel tesztelése az Azure-ba a Site Recovery szolgáltatásban](site-recovery-test-failover-to-azure.md)című útmutatót.

Ha statikus IP-címet használ, megadhatja azt az IP-címet, amelyet a virtuális gépnek el kell vennie. Az IP-cím beállításához nyissa meg a TARGET**IP** **számítási és hálózati beállításokat.** > 

![Képernyőkép, amely bemutatja, hogyan állítható be a cél IP a Site Recovery Compute and Network ablaktáblán](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja a különböző szintek szekvenálása egy többrétegű alkalmazás feladatátvétel során. A szekvenálás segít fenntartani az alkalmazások konzisztenciáját. Amikor helyreállítási tervet hoz létre egy többrétegű webalkalmazáshoz, hajtsa végre a Helyreállítási terv létrehozása a Site Recovery használatával című részben [ismertetett](site-recovery-create-recovery-plans.md)lépéseket.

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása feladatátvételi csoportokhoz
Egy tipikus többrétegű IIS webalkalmazás a következő összetevőkből áll:
* SQL virtuális gépekkel rendelkező adatbázisszint.
* A webes szint, amely egy IIS-kiszolgálóból és egy alkalmazásszintből áll. 

Virtuális gépek hozzáadása különböző csoportokhoz a réteg alapján:

1. Hozzon létre egy helyreállítási tervet. Adja hozzá az adatbázisréteg virtuális gépeit az 1. Ez biztosítja, hogy az adatbázisrétegvirtuális gépei utolsó leállítása és először a létrehozott.
1. Adja hozzá az alkalmazásréteg virtuális gépeit a 2. Ez biztosítja, hogy az alkalmazásréteg virtuális gépei az adatbázisréteg létrehozása után jelenjenek meg.
1. Adja hozzá a webes réteg virtuális gépeit a 3- as csoportban. Ez biztosítja, hogy a webes rétegbeli virtuális gépek az alkalmazásszint feladása után jelenjenek meg.
1. Terheléselosztási virtuális gépek hozzáadása a 4-es csoportban. Ez biztosítja, hogy a terheléselosztási virtuális gépek a webes szint feladása után jelenjenek meg.

További információt [a Helyreállítási terv testreszabása](site-recovery-runbook-automation.md#customize-the-recovery-plan)című témakörben talál.


### <a name="add-a-script-to-the-recovery-plan"></a>Parancsfájl hozzáadása a helyreállítási tervhez
Az IIS-webfarm megfelelő működéséhez előfordulhat, hogy bizonyos műveleteket kell végeznie az Azure virtuális gépeken a feladatátvétel után vagy egy teszt feladatátvétel során. A feladatátvételt követő egyes műveletek automatizálhatók. Frissítheti például a DNS-bejegyzést, módosíthatja a helykötést, vagy módosíthatja a kapcsolati karakterláncot a megfelelő parancsfájlok helyreállítási tervhez való hozzáadásával. [VMM-parancsfájl hozzáadása a helyreállítási tervhez](site-recovery-how-to-add-vmmscript.md) azt ismerteti, hogyan állíthatók be automatikus feladatok parancsfájl használatával.

#### <a name="dns-update"></a>DNS-frissítés
Ha a DNS dinamikus DNS-frissítésre van konfigurálva, a virtuális gépek általában az indításkor frissítik a DNS-t az új IP-címmel. Ha a DNS új IP-címekkel való frissítéséhez explicit lépést szeretne hozzáadni, adjon hozzá egy parancsfájlt az [IP-cím frissítéséhez a DNS-ben](https://aka.ms/asr-dns-update) a helyreállításiterv-csoportok feladatátvétel utáni műveleteként.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Kapcsolati karakterlánc az alkalmazás web.config fájljában
A kapcsolati karakterlánc azt az adatbázist határozza meg, amelyhez a webhely kommunikál. Ha a kapcsolati karakterlánc az adatbázis virtuális gépének nevét hordozza, nincs szükség további lépésekre a feladatátvétel után. Az alkalmazás automatikusan kommunikálhat az adatbázissal. Továbbá, ha az adatbázis virtuális gép IP-címét megőrzi, nem kell frissíteni a kapcsolati karakterláncot. 

Ha a kapcsolati karakterlánc ip-cím használatával hivatkozik az adatbázis virtuális gépére, azt a feladatátvétel után frissíteni kell. A következő kapcsolati karakterlánc például a 127.0.1.2 IP-címmel rendelkező adatbázisra mutat:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

A webes rétegben lévő kapcsolati karakterlánc frissítéséhez adjon hozzá egy [IIS-kapcsolatfrissítési parancsfájlt](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) a 3.

#### <a name="site-bindings-for-the-application"></a>Helykötések az alkalmazáshoz
Minden oldal kötelező érvényű információból áll. A kötési információ tartalmazza a kötés típusát, azt az IP-címet, amelyen az IIS-kiszolgáló figyeli a hely reklamálását, a portszámot és a hely állomásnevét. A feladatátvétel során szükség lehet frissíteni ezeket a kötéseket, ha van egy változás az IP-cím, amely a hozzájuk társított.

> [!NOTE]
>
> Ha a helykötést az Összes ki nem osztott elemre **állítja,** nem kell frissítenie ezt a kötelező érvényű feladatátvétel után. Továbbá, ha a helyhez társított IP-cím nem módosul a feladatátvétel után, nem kell frissítenie a helykötést. (Az IP-cím megőrzése az elsődleges és helyreállítási helyekhez rendelt hálózati architektúrától és alhálózatoktól függ. Előfordulhat, hogy a frissítésük nem valósítható meg a szervezet számára.)

![Képernyőkép a TLS/SSL kötés beállítását bemutató képernyőkép](./media/site-recovery-iis/sslbinding.png)

Ha az IP-címet egy helyhez társította, frissítse az összes helykötést az új IP-címmel. A helykötések módosításához adjon hozzá egy [IIS webréteg-frissítési parancsfájlt](https://aka.ms/asr-web-tier-update-runbook-classic) a 3.

#### <a name="update-the-load-balancer-ip-address"></a>A terheléselosztó IP-címének frissítése
Ha Rendelkezik ARR virtuális géppel, az IP-cím frissítéséhez adjon hozzá egy [IIS ARR feladatátvételi parancsfájlt](https://aka.ms/asr-iis-arrtier-failover-script-classic) a 4- es csoport után.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>TLS/SSL tanúsítványkötés HTTPS-kapcsolathoz
Előfordulhat, hogy egy webhely hez olyan TLS/SSL-tanúsítvány tartozik, amely biztosítja a biztonságos kommunikációt a webkiszolgáló és a felhasználó böngészője között. Ha a webhely HTTPS-kapcsolattal rendelkezik, és rendelkezik egy Kapcsolódó HTTPS-helykötéssel az IIS-kiszolgáló IP-címéhez TLS/SSL tanúsítványkötéssel, új helykötést kell hozzáadnia a tanúsítványhoz az IIS virtuális gép feladatátvétel utáni IP-címével.

A TLS/SSL tanúsítvány az alábbi összetevőkre adható ki:

* A teljesen minősített domain név a honlapon.
* A kiszolgáló neve.
* Helyettesítő karakter a tartománynévhez.  
* Egy IP-cím. Ha a TLS/SSL tanúsítvány t az IIS-kiszolgáló IP-címével szemben van kiadva, egy másik TLS/SSL-tanúsítványt kell kiállítani az Azure-webhelyen lévő IIS-kiszolgáló IP-címével szemben. Ehhez a tanúsítványhoz létre kell hozni egy további TLS-kötést. Emiatt azt javasoljuk, hogy ne használjon az IP-cím hez kiadott TLS/SSL tanúsítványt. Ez az opció kevésbé széles körben használják, és hamarosan elavult szerint az új hitelesítésszolgáltató / böngésző fórum változásokat.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>A webes réteg és az alkalmazásszint közötti függőség frissítése
Ha olyan alkalmazásspecifikus függősége van, amely a virtuális gépek IP-címén alapul, frissítenie kell ezt a függőséget a feladatátvétel után.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Az Azure Portalon válassza ki a Recovery Services-tároló.
2. Válassza ki az IIS webfarmhoz létrehozott helyreállítási tervet.
3. Kattintson a **Feladatátvétel tesztelése** elemre.
4. A teszt feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot és az Azure virtuális hálózatot.
5. Ha a másodlagos környezet ben, akkor végre érvényesítések.
6. Ha az ellenőrzések befejeződtek, a teszt feladatátvételi környezet tisztításához válassza az **Érvényesítésbefejeződés**lehetőséget.

További információ: [Feladatátvétel tesztelése az Azure-ban a Site Recovery.for](site-recovery-test-failover-to-azure.md)more information, see Test failover to Azure in Site Recovery .

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. Az Azure Portalon válassza ki a Recovery Services-tároló.
1. Válassza ki az IIS webfarmhoz létrehozott helyreállítási tervet.
1. Válassza a **Feladatátvétel** lehetőséget.
1. A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [Feladatátvétel a Site Recovery alkalmazásban.](site-recovery-failover.md)

## <a name="next-steps"></a>További lépések
* További információ a [többi alkalmazás replikálásáról](site-recovery-workload.md) a Site Recovery használatával.
