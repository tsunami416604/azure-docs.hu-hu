---
title: "Egy többszintes Azure Site Recovery segítségével az IIS-alapú webes alkalmazás replikálása |} Microsoft Docs"
description: "Útmutató: az IIS webkiszolgáló farm virtuális gépek Azure Site Recovery segítségével replikálni."
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Egy többszintes IIS-alapú webes alkalmazás replikálása

Alkalmazás Ez a motor a szervezet az üzleti hatékonyságot. Különböző webalkalmazások más célt szolgálhat egy szervezet. Előfordulhat, hogy egyes alkalmazások, például a kérelmek feldolgozását, pénzügyi alkalmazásokat és ügyfélkapcsolati webhelyek, a szervezetek számára fontos. A termelékenység csökkenését megelőzése érdekében fontos a szervezet szeretné, hogy ezek az alkalmazások folyamatosan naprakész, és fut. Ennél is fontosabb hogy a folyamatosan elérhető alkalmazásokat megóvja a márka vagy a szervezet képe.

Kritikus webalkalmazások általában be, a többrétegű alkalmazások: a webes, az adatbázis és az alkalmazás nincs a különböző rétegeket. Mellett különböző rétegek elosztva alatt, az alkalmazások is használhatják az több kiszolgáló egyes rétegekben a forgalom. Ezenkívül a leképezések különböző fizetősökbe, és a webkiszolgálón lehet, hogy alapul statikus IP-címeket. A feladatátvétel a leképezések némelyike frissítenie kell, különösen akkor, ha több webhely vannak konfigurálva a webkiszolgálón. Ha webes alkalmazásokat az SSL használatához frissítenie kell a tanúsítványok kötései.

Hagyományos helyreállítási módszer, amely nem a replikációs alapján tartalmaz, amely különböző konfigurációs fájlokat, beállításjegyzék-beállítások, kötések, egyéni összetevők (COM vagy .NET), tartalom és a tanúsítványok biztonsági mentéséről. Fájlok manuális lépések keresztül helyreállítás. A hagyományos helyreállítási biztonsági mentését, és manuálisan a fájlok helyreállítása módszereket nehézkessé, hibákhoz vezethet, és nem méretezhető. Például előfordulhat, hogy könnyen elfelejti biztonsági másolatot készíteni a tanúsítványokat. A feladatátvétel után még nem választható, de a kiszolgáló új tanúsítványok vásárlása balra.

Egy jó vész-helyreállítási megoldást támogatja a modellezési helyreállítási tervek összetett alkalmazási architektúrákban. Meg kell is testre szabott lépések hozzáadása a helyreállítási tervbe alkalmazástársítások rétegek közötti kezelésére. Ha egy olyan vészhelyzet esetén, alkalmazás-hozzárendelések, amelyek segítségével, előfordulhat, hogy egy alacsonyabb RTO egyetlen kattintással, meg arról, hogy hibaüzenetet megoldást biztosítanak a.

Ez a cikk ismerteti, hogyan védi meg a webes alkalmazás, amely az Internet Information Services (IIS) használatával [Azure Site Recovery](site-recovery-overview.md). A cikk ismerteti a háromrétegű, az IIS-alapú webalkalmazások Azure, tegye a vész-helyreállítási részletezési, és hogy áthelyezze a feladatokat az alkalmazás az Azure-bA replikál ajánlott eljárásai.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan hajtsa végre a következő feladatokat:

* [A virtuális gépek replikálása Azure-bA](vmware-azure-tutorial.md)
* [Egy helyreállítási hálózathoz. terv](site-recovery-network-design.md)
* [Végezzen feladatátvételi tesztet az Azure-bA](site-recovery-test-failover-to-azure.md)
* [Hajtsa végre a feladatátvételt az Azure-bA](site-recovery-failover.md)
* [A tartományvezérlő replikálja.](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Központi telepítés minták
Az IIS-alapú webalkalmazás általában a következő egyike annak a következő központi telepítési:

**Telepítési minta 1**

Az IIS-alapú webfarm alkalmazás alkalmazáskérelmek útválasztása (ARR), az IIS-kiszolgáló és az SQL Server.

![Az IIS-alapú webfarm, amely rendelkezik a három réteg ábrája](./media/site-recovery-iis/deployment-pattern1.png)

**Telepítési minta 2**

Az IIS-alapú webfarm ARR, az IIS-kiszolgáló, egy alkalmazás, vagy az SQL Server.

![Az IIS-alapú webfarm négy rétegek rendelkező ábrája](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

Az ebben a cikkben szereplő példák a VMware virtuális gépeket az IIS 7.5 a Windows Server 2012 R2 Enterprise használjuk. Site Recovery replikációs nem az alkalmazás-specifikus, mert az ebben a cikkben szereplő ajánlásokhoz várt a felsorolt az alábbi táblázatban, és az IIS különböző verzióihoz esetekben alkalmazza.

### <a name="source-and-target"></a>Forrása és célja

Forgatókönyv | Egy másodlagos helyre | To Azure
--- | --- | ---
Hyper-V | Igen | Igen
VMware | Igen | Igen
Fizikai kiszolgáló | Nem | Igen
Azure|NA|Igen

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Indítsa el az összes az IIS webkiszolgáló farm virtuális gépek replikálása Azure-ba, kövesse az ismertetés [az Azure Site Recovery a feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

Egy statikus IP-címet használ, ha azt szeretné, hogy a virtuális gépen ahhoz, hogy IP-cím is megadhat. Az IP-cím megadásához keresse fel **számítási és hálózati beállításai** > **cél IP-címet**.

![A cél IP-címet beállítani a Site Recovery számítási és hálózati ablaktáblán képernyőkép](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja a különböző rétegek alkalmazás-előkészítés során egy többrétegű alkalmazást egy feladatátvétel során. Alkalmazás-előkészítés segítségével biztosítja az egységességet alkalmazás. A helyreállítási terv többrétegű webalkalmazás létrehozásakor teljes lépéseit ismerteti [helyreállítási terv létrehozása a Site Recovery segítségével](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok hozzáadása
Egy tipikus többrétegű IIS webes alkalmazás a következő összetevőkből áll:
* Adatbázis-rétegből, amely rendelkezik az SQL-virtuális gépek.
* A webes réteg, amely az IIS-kiszolgáló és egy alkalmazás réteget. 

Virtuális gépek felvétele a réteg alapján különböző csoportok:

1. Helyreállítási terv létrehozása. Adja hozzá az 1. csoport adatbázis réteg virtuális gépeket. Ez biztosítja, hogy adatbázis réteg virtuális gépein utolsó leállítása és állapotba helyezése.
1. Adja hozzá a 2-es csoportot az alkalmazás réteg virtuális gépeket. Ez biztosítja, hogy alkalmazás réteg virtuális gépein kerülnek sorra, miután az adatbázis-rétegből állapotba nem kerül.
1. 3. csoport adja hozzá a webes réteg virtuális gépein. Ez biztosítja, hogy webes réteg virtuális gépein kerülnek sorra, miután az alkalmazás réteg leállását.
1. Csoport 4 load balance virtuális gépek hozzáadása. Ez biztosítja, hogy load balance virtuális gépek kerülnek sorra, miután a webes réteg állapotba nem kerül.

További információkért lásd: [testre szabhatja a helyreállítási terv](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>A helyreállítási terv parancsfájl hozzáadása
IIS webfarm megfelelő működéséhez szükség lehet néhány műveleteket az Azure virtuális gépek feladatátvételt követően vagy egy feladatátvételi tesztje közben. Néhány feladatátvételt követően művelet automatizálható. Például a DNS-bejegyzés frissítéséhez, módosítsa a hely kötése, vagy módosítsa egy kapcsolati karakterláncot adja hozzá a megfelelő parancsfájlokat a helyreállítási tervbe. [A VMM-parancsfájl hozzáadása egy helyreállítási terv](site-recovery-how-to-add-vmmscript.md) ismerteti, hogyan állíthatja be az automatikus feladatok parancsfájl használatával.

#### <a name="dns-update"></a>DNS-frissítési
Ha a DNS dinamikus DNS-frissítési konfigurált, virtuális gépek általában frissítse a DNS-az új IP-címmel indításakor. Ha hozzá szeretne adni egy explicit lépés DNS frissítése a virtuális gépeket az új IP-címekkel rendelkező, adja hozzá a [parancsprogramot a DNS-ben IP frissítéséhez](https://aka.ms/asr-dns-update) a feladatátvételt követően műveletek a helyreállítási terv csoportok.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Az alkalmazás web.config kapcsolati karakterlánc
A kapcsolati karakterláncot adja meg az adatbázis, amely a webhely kommunikál. Ha az adatbázis-virtuálisgép neve a kapcsolati karakterláncot, további lépésekre nincs szükség feladatátvételt követően. Az alkalmazás automatikusan képes kommunikálni az adatbázissal. Az IP-cím, az adatbázis-virtuális gép megmarad, ha az nem is frissítenie kell a kapcsolati karakterláncot. 

Ha a kapcsolati karakterlánc hivatkozik az adatbázis-virtuális gép IP-cím használatával, kell lennie a frissített feladatátvételt követően. Például a következő kapcsolati karakterlánc pontokat az adatbázisba az IP-cím 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

A kapcsolati karakterláncot a webes réteg frissítéséhez hozzáadása egy [IIS kapcsolat frissítő parancsfájlt](https://aka.ms/asr-update-webtier-script-classic) a helyreállítási tervben 3 csoport után.

#### <a name="site-bindings-for-the-application"></a>Az alkalmazás hely kötései
Minden webhely kötési információ áll. A kötési információ kötés, az IP-cím, amelyen az IIS-kiszolgálót figyeli a kérelmekre a hely, a portszámot és a hely az állomás nevét tartalmazza. A feladatátvétel során előfordulhat, hogy módosítania ilyen kötést tartozik hozzájuk társított IP-cím megváltozása esetén.

> [!NOTE]
>
> Ha a hely kötése **összes ki nem osztott**, nem kell frissíteni a kötés feladatátvételt követően. Is ha egy helyhez társított IP-címét nem módosított feladatátvételt követően, nem kell frissíteni a hely kötése. (A megőrzési az IP-cím a hálózati architektúra és az elsődleges és a másodlagos helyekhez rendelt alhálózatok is függ. Létrehoztak nem feltétlenül valósítható meg, hogy a szervezet.)

![Képernyőkép az SSL-kötés beállítása](./media/site-recovery-iis/sslbinding.png)

Az IP-cím társított egy helyet, ha frissíti az összes hely kötései az új IP-cím. Ha módosítani szeretné a hely kötései, adja hozzá egy [IIS webes réteg frissítő parancsfájlt](https://aka.ms/asr-web-tier-update-runbook-classic) a helyreállítási tervben 3 csoport után.

#### <a name="update-the-load-balancer-ip-address"></a>A load balancer IP-címének frissítése
Ha az IP-címének frissítése az ARR virtuális gép hozzáadása egy [IIS ARR feladatátvételi parancsfájl](https://aka.ms/asr-iis-arrtier-failover-script-classic) csoport 4 után.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-tanúsítvány kötés egy HTTPS-kapcsolat számára
A webhely rendelkezhet társított SSL-tanúsítvány, amely segít biztosítani a webkiszolgáló és a felhasználó böngészője közötti biztonságos kommunikációhoz. Ha a webhely HTTPS-kapcsolatot, és is van egy társított HTTPS hely kötése az IP-cím, az IIS-kiszolgáló SSL-tanúsítvány kötést, hozzá kell adnia egy új hely kötése a tanúsítvány az IIS virtuális gép feladatátvételt követően az IP-címével.

Ezek az összetevők elleni ki lehet adni az SSL-tanúsítvány:

* A webhely teljesen minősített tartománynév.
* A kiszolgáló nevét.
* A tartománynév helyettesítő tanúsítványt.  
* An IP address. Ha az SSL-tanúsítvány jelenik meg az IIS-kiszolgáló IP-címe alapján, egy másik SSL-tanúsítványt kell az IP-cím, az IIS-kiszolgáló az Azure site állították ki kell. A tanúsítvány további SSL-kötést kell létrehozni. Ebből kifolyólag javasoljuk nem egy SSL-tanúsítványt az IP-cím állították ki. Ez a beállítás kevésbé széles körben használt, és hamarosan megszűnnek új tanúsítvány hatóság/böngésző fórum módosítások megfelelően.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>A webes réteg és az alkalmazás szint között függőség frissítése
Ha egy alkalmazás-specifikus függőség a virtuális gépek IP-címe alapján, frissítenie kell a függőségi feladatátvételt követően.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Az Azure-portálon válassza ki a Recovery Services-tároló.
2. Válassza ki a helyreállítási tervet, amelyet az IIS webfarm hozott létre.
3. Válassza ki **feladatátvételi teszt**.
4. A teszt feladatátvételi folyamat elindításához válassza ki a helyreállítási pont és az Azure virtuális hálózat.
5. A másodlagos környezet esetén érvényesítést végezheti el.
6. Ellenőrzés be nem fejeződik, a teszt feladatátvételi környezet tisztítása kijelölése **érvényesítést végrehajtani**.

További információkért lásd: [az Azure Site Recovery a feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. Az Azure-portálon válassza ki a Recovery Services-tároló.
1. Válassza ki a helyreállítási tervet, amelyet az IIS webfarm hozott létre.
1. Válassza ki **feladatátvételi**.
1. A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információkért lásd: [a Site Recovery feladatátvételi](site-recovery-failover.md).

## <a name="next-steps"></a>További lépések
* További információ [replikál a többi alkalmazás](site-recovery-workload.md) Site Recovery segítségével.
