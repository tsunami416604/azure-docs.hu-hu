---
title: 'SAP az Azure-ban: Támogatott forgatókönyvek az Azure virtuális gépekkel'
description: Az Azure Virtuális gépek által támogatott forgatókönyvek AZ SAP-munkaterheléssel
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137628"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-munkaterhelés az Azure virtuális gépen támogatott forgatókönyvekben
Az SAP NetWeaver, Business `Hybris` one vagy S/4HANA rendszerarchitektúra tervezése az Azure-ban számos különböző lehetőséget nyit meg a különböző architektúrák és eszközök számára, hogy egy méretezhető, hatékony és magas rendelkezésre állású telepítést kapjon. Bár a használt operációs rendszertől vagy ADATBÁZIS-rendszertől függ, vannak korlátozások. Emellett nem minden helyszíni támogatott forgatókönyvek ugyanúgy támogatott az Azure-ban. Ez a dokumentum a támogatott, nem magas rendelkezésre állású konfigurációkon, valamint a magas rendelkezésre állású konfigurációkon és architektúrákon keresztül vezet kizárólag az Azure virtuális gépek használatával. A [HANA nagy példányokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)támogatott forgatókönyvek esetén tekintse meg a [hana nagy példányok támogatott forgatókönyvei című cikket.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario) 


## <a name="2-tier-configuration"></a>Kétrétegű konfiguráció
Az SAP 2 rétegű konfiguráció az SAP DBMS és az ugyanazon a kiszolgálón vagy virtuális gép egységen futó alkalmazásréteg együttes rétegeiből épül fel. A második réteg a felhasználói felület rétege. Kétrétegű konfiguráció esetén a DBMS és az SAP alkalmazásréteg megosztja az Azure virtuális gép erőforrásait. Ennek eredményeképpen úgy kell konfigurálnia a különböző összetevőket, hogy azok ne versenyezzenek az erőforrásokért. Emellett óvatosnak kell lennie, hogy ne iratkozzon túl a virtuális gép erőforrásait. Egy ilyen konfiguráció nem biztosít magas rendelkezésre állást, a különböző Azure-összetevők [Azure szolgáltatási szintre vonatkozó megállapodásain](https://azure.microsoft.com/support/legal/sla/) kívül.

Egy ilyen konfiguráció grafikus ábrázolása így nézhet ki:

![Egyszerű, kétrétegű konfiguráció](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Ezeket a konfigurációkat a Windows, a Red Hat, a SUSE és az Oracle Linux támogatja az SQL Server, Oracle, Db2, maxDB és SAP ASE DB rendszerekkel az éles és nem éles esetekben. Az SAP HANA dbms-ként, az ilyen típusú konfigurációk csak nem éles esetekben támogatott. Ez magában foglalja az [Azure HANA nagy példányok üzembe helyezésének esetét](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) is.
Az Azure-ban támogatott összes OS/DBMS-kombináció esetében az ilyen típusú konfiguráció támogatott. Azonban kötelező, hogy állítsa be a konfigurációt a DBMS és az SAP-összetevők oly módon, hogy a DBMS és az SAP-összetevők nem versenyeznek a memória és a CPU-erőforrások, és ezáltal meghaladja a fizikai rendelkezésre álló erőforrásokat. Ezt a DBMS által lefoglalni engedélyezett memória korlátozásával kell elvégezni. Az SAP extended memory alkalmazáspéldányokon is korlátoznia kell. Emellett a virtuális gép teljes processzorfogyasztását is figyelnie kell, hogy az összetevők ne maximalizálják a CPU-erőforrásokat. 

> [!NOTE]
> Éles SAP-rendszerek esetén további magas rendelkezésre állású és esetleges vész-helyreállítási konfigurációkat javasoljuk a jelen dokumentum későbbi részében leírtak szerint.


## <a name="3-tier-configuration"></a>Háromrétegű konfiguráció
Az ilyen konfigurációkban az SAP-alkalmazásréteget és a DBMS-réteget különböző virtuális gépekre bontja. Általában ezt a nagyobb rendszerek és az okok miatt, hogy rugalmasabb az SAP alkalmazásréteg erőforrásait. A legegyszerűbb beállítás ban nincs magas rendelkezésre állás az [Azure szolgáltatási szint revonatkozó szerződésén](https://azure.microsoft.com/support/legal/sla/) kívül a különböző Azure-összetevők érintett. 

A grafikus ábrázolás így néz ki:

![Egyszerű, kétrétegű konfiguráció](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Ez a konfigurációtípus támogatott Windows, Red Hat, SUSE és Oracle Linux az SQL Server, Oracle, Db2, SAP HANA, maxDB és SAP ASE adatbázis-rendszerében éles és nem éles esetekben. Ez az [Azure HANA nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)alapértelmezett központi telepítési konfigurációja. Az egyszerűsítés érdekében nem különböztettünk meg az SAP központi szolgáltatások és az SAP-párbeszédpanel-példányok között az SAP alkalmazásrétegben. Ebben az egyszerű háromrétegű konfigurációban nem lenne magas rendelkezésre állású védelem az SAP központi szolgáltatások számára.

> [!NOTE]
> Éles SAP-rendszerek esetén további magas rendelkezésre állású és esetleges vész-helyreállítási konfigurációkat javasoljuk a jelen dokumentum későbbi részében leírtak szerint.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Több DBMS-példány virtuális gépenként vagy HANA nagy példányegységenként
Ebben a konfigurációs típusban több DBMS-példányt üzemeltet Az Azure Virtuális gép vagy a HANA nagy példány egységenként. A motiváció lehet, hogy kevesebb operációs rendszerek fenntartása és ezzel a csökkentett költségeket. Egyéb motivációk lehet, hogy nagyobb rugalmasságot és nagyobb hatékonyságot egy nagyobb virtuális gép vagy HANA nagy példány egység erőforrásainak megosztása több DBMS-példányok között. Eddig ezek a konfigurációk jelentek meg leginkább a nem termelési rendszerek.

Egy ilyen konfiguráció így nézhet ki:

![Több DBMS-példány egy egységben](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Az ilyen típusú DBMS-telepítés a következő célokra támogatott:

- SQL Server windowsrendszeren
- IBM Db2. Részletek keresése a cikkben [Több példány (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Az Oracle-nek. További részletek: [SAP támogatási megjegyzés #1778431](https://launchpad.support.sap.com/#/notes/1778431) és a kapcsolódó SAP-megjegyzések
- Az SAP HANA, egy virtuális gép több példánya, az SAP meghívja ezt a telepítési módszert MCOS, támogatott. További részletekért lásd az SAP cikk [Több SAP HANA systems on One Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Több adatbázis-példány futtatása egy gazdagépen, győződjön meg arról, hogy a különböző példányok nem versengenek az erőforrásokért, és ezáltal meghaladja a virtuális gép fizikai erőforrás-korlátait. Ez különösen igaz a memória, ahol meg kell, hogy a memória minden a virtuális gép megosztásának példányai lefoglalhatja. Ez is lehet, hogy igaz a CPU-erőforrások a különböző adatbázispéldányok is kihasználhatja. Az összes említett DBMS olyan konfigurációval rendelkezik, amely lehetővé teszi a memóriafoglalás és a CPU-erőforrások korlátozását egy példány szintjén.
Annak érdekében, hogy az Azure virtuális gépek ilyen konfigurációjának támogatása érdekében várható, hogy az adatokhoz használt lemezek vagy kötetek, valamint a különböző példányok által kezelt adatbázisok napló-/ismétlési naplófájljai elkülönülnek. Más szóval a különböző DBMS-példány által kezelt adatbázisok adatai vagy napló-/ismétlése nem feltétlenül ugyanazt a lemezt vagy kötetet osztja meg. 

A HANA nagy példányok lemezkonfigurációja konfigurálva van, és a [HANA nagy példányok támogatott forgatókönyveiben részletezi.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) 

> [!NOTE]
> Éles SAP-rendszerek esetén további magas rendelkezésre állású és esetleges vész-helyreállítási konfigurációkat javasolunk a jelen dokumentum későbbi részében leírtak szerint. Több DBMS-példánysal rendelkező virtuális gépek nem támogatottak a dokumentum későbbi részében ismertetett magas rendelkezésre állású konfigurációkkal.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Több SAP-párbeszédpanel-példány egy virtuális gépben
Sok esetben több párbeszédpéldány tértek üzembe a csupasz fémkiszolgálókon, vagy akár a privát felhőkben futó virtuális gépeken. Az ilyen konfigurációk oka az volt, hogy bizonyos SAP-párbeszédpanel-példányokat bizonyos munkaterheléshez, üzleti funkciókhoz vagy munkaterhelés-típusokhoz igazítson. Oka, hogy nem elkülöníti ezeket a példányokat külön virtuális gépekre volt az operációs rendszer karbantartásának és működésének erőfeszítése. Vagy számos esetben a költségek abban az esetben, ha a virtuális gép üzemeltetője vagy üzemeltetője havi díjat kér a virtuális gépen működő és adminisztrált virtuális gépenként. Az Azure-ban egy olyan forgatókönyv, amelyben több SAP-párbeszédpanel-példány tapad egyetlen virtuális gépen belül, amelyet éles és nem éles célokra támogatunk a Windows, a Red Hat, a SUSE és az Oracle Linux operációs rendszerén. Az SAP kernel paraméter PHYS_MEMSIZE, elérhető a Windows és a modern Linux kernelek, be kell állítani, ha több SAP Application Server példányok futnak egyetlen virtuális gépen. Azt is javasoljuk, hogy korlátozza az SAP Extended Memory bővítését az operációs rendszereken, például a Windows rendszeren, ahol az SAP kiterjesztett memória automatikus növekedése valósul meg. Ezt az SAP profil paraméterrel `em/max_size_MB`teheti meg.

A háromrétegű konfiguráció, ahol több SAP-párbeszédpanel-példányok futnak az Azure virtuális gépeken belül így nézhet ki:

![Több DBMS-példány egy egységben](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Az egyszerűsítés érdekében nem különböztettünk meg az SAP központi szolgáltatások és az SAP-párbeszédpanel-példányok között az SAP alkalmazásrétegben. Ebben az egyszerű háromrétegű konfigurációban nem lenne magas rendelkezésre állású védelem az SAP központi szolgáltatások számára. Éles rendszerek esetén nem ajánlott az SAP központi szolgáltatások védelem nélkül hagyni. Az SAP központi példányok és az ilyen több-SID-konfigurációk magas rendelkezésre állású, úgynevezett több-SID-konfigurációk sajátosságait a dokumentum későbbi szakaszaiban.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Magas rendelkezésre állásvédelem az SAP DBMS-réteghez
Az SAP éles rendszerek üzembe helyezéséhez érdemes figyelembe venni a magas rendelkezésre állású konfigurációk készenléti típusát. Különösen az SAP HANA, ahol az adatokat be kell tölteni a memóriába, mielőtt képes a teljes teljesítmény és a méretezhetőség vissza, az Azure szolgáltatás gyógyulása nem ideális intézkedés a magas rendelkezésre állás. 

A Microsoft általában csak a magas rendelkezésre állású konfigurációkat és szoftvercsomagokat támogatja, amelyek et az DOCS.MICROSOFT.COM SAP számítási feladatok szakasza ismerteti. Ugyanezt a kijelentést elolvashatja az SAP note [#1928533.](https://launchpad.support.sap.com/#/notes/1928533) A Microsoft nem nyújt támogatást más, magas rendelkezésre állású, külső gyártótól származó szoftverkeretrendszerekhez, amelyeket a Microsoft nem dokumentált az SAP-munkaterheléssel együtt. Ilyen esetekben a magas rendelkezésre állású keretrendszer külső szállítója a magas rendelkezésre állású konfiguráció támogató fele, akit ön nek kell igénybe vennie, mint ügyfélnek a támogatási folyamatba. Kivételek fognak említeni ebben a cikkben. 

A Microsoft általában támogatja az Azure virtuális gépek en vagy hana nagy példányok egységein rendelkezésre álló magas rendelkezésre állású konfigurációk korlátozott készletét. A hana nagy példányok támogatott forgatókönyvei, olvassa el a dokumentum [támogatott forgatókönyvek HANA nagy példányok.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)

Az Azure virtuális gépek esetében a következő magas rendelkezésre állású konfigurációk támogatottak a DBMS szintjén:

- SAP HANA rendszer replikáció linuxos pacemaker alapján a SUSE és a Red Hat. Lásd a részletes cikkeket:
    - [Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Az SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken a Red Hat Enterprise Linuxon](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA horizontális felskálázásn keresztül n+m konfigurációk [az Azure NetApp-fájlok](https://azure.microsoft.com/services/netapp/) suse-on és a Red Hat használatával. A részleteket a következő cikkek sorolják fel:
    - [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával a SUSE Linux Enterprise Server} használatával}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával Red Hat Enterprise Linux on Red Hat Enterprise Linux on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- SQL Server feladatátvételi fürt a Windows kibővített fájlszolgáltatások alapján. Bár az éles rendszerek rejleési javaslata az SQL Server Always On használata a fürtözés helyett. Az SQL Server Always On jobb rendelkezésre állást biztosít külön tárolóhasználatával. A részleteket ebben a cikkben ismertetjük: 
    - [SQL Server feladatátvételi fürtpéldány konfigurálása Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- Az SQL Server Always On az Azure-beli SQL Server operációs rendszerrel támogatott. Ez az alapértelmezett javaslat az azure-beli éles SQL Server-példányok hoz. A részleteket a következő cikkek ismertetik:
    - [Bemutatjuk az SQL Server Always On rendelkezésre állási csoportokat az Azure virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
    - [Konfiguráljon egy mindig rendelkezésre állási csoportot a különböző régiókban lévő Azure virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
    - [Konfiguráljon egy terheléselosztót egy mindig elérhető helyen lévő csoporthoz az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)
- Oracle Data Guard Windows és Oracle Linux. Az Oracle Linux részletei ebben a cikkben találhatók:
    - [Az Oracle Data Guard megvalósítása Egy Azure Linux-alapú virtuális gépen](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR a SUSE-ról és RHEL A SUSE és rhel pacemaker használatával kapcsolatos részletes dokumentációitt található:
    - [Az IBM Db2 LUW magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server en pacemakerrel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Az IBM Db2 LUW magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux Serveren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Az SAP ASE és az SAP maxDB konfigurációja az alábbi dokumentumokban részletezett módon:
    - [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [SAP MaxDB, liveCache és Content Server központi telepítése az Azure virtuális gépein](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Hana nagy példányok magas rendelkezésre állású forgatókönyvek részletesen:
    - [Hana Large Instances támogatott forgatókönyvei – HSR STONITH-val a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Hana nagypéldányok támogatott forgatókönyvei – Állomás automatikus feladatátvétele (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> A fent leírt forgatókönyvek egyike sem támogatjuk több DBMS-példány konfigurációját egy virtuális gépen. Eszközök minden esetben csak egy adatbázis-példány telepíthető virtuális gépenként, és a leírt magas rendelkezésre állási módszerekkel védett. Több DBMS-példány védelme ugyanazon Windows vagy Pacemaker feladatátvételi fürt alatt **jelenleg nem** támogatott. Az Oracle Data Guard is csak virtuális gép telepítési esetenként egy példányban támogatott. 

A különböző adatbázisrendszerek lehetővé teszik több adatbázis üzemeltetését egy DBMS-példány alatt. Az SAP HANA-hoz hasonlóan több adatbázis-tárolóban (MDC) is üzemeltethető. Azokban az esetekben, amikor ezek a többadatbázisos konfigurációk egy feladatátvevő fürterőforráson belül működnek, ezek a konfigurációk támogatottak. A nem támogatott konfigurációk olyan esetek, amikor több fürterőforrásra lenne szükség. Ami azokat a konfigurációkat illeti, ahol több SQL Server-rendelkezésre állási csoportot definiálna, egyetlen SQL Server-példány alatt.


![DBMS HA konfiguráció](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

A DBMS an/vagy operációs rendszerektől függően az olyan összetevők, mint az Azure terheléselosztó, előfordulhat, hogy nem szükségesek a megoldásarchitektúra részeként. 

Kifejezetten a maxDB esetében a tárolási konfigurációnak másnak kell lennie. A maxDB-ben az adatoknak és a naplófájloknak megosztott tárolón kell elhelyezkedniük a magas rendelkezésre állású konfigurációkhoz. Csak abban az esetben, maxDB, megosztott tároló támogatott a magas rendelkezésre állás. Az összes többi DBMS külön tárolóhalom csomópontonként az egyetlen támogatott lemezkonfigurációk.

Más magas rendelkezésre állású keretrendszerek ismert, hogy létezik, és ismert, hogy fut a Microsoft Azure-ban is. A Microsoft azonban nem tesztelte ezeket a keretrendszereket. Ha ezekkel a keretrendszerekkel szeretné felépíteni a magas rendelkezésre állású konfigurációt, akkor a szoftver szolgáltatójával együtt kell működnie a következők érdekében:

- Telepítési architektúra kialakítása
- Az architektúra telepítése
- Az architektúra támogatása

> [!IMPORTANT]
> A Microsoft Azure Piactér számos olyan puha készüléket kínál, amelyek tárolási megoldásokat kínálnak az Azure natív tárhelyén felül. Ezek a soft appliances nfs-megosztások létrehozásához is használható, amelyek elméletileg felhasználhatók az SAP HANA kibővített telepítésekben, ahol készenléti csomópontra van szükség. Különböző okok miatt egyik ilyen tárolási soft appliances sem támogatja a Microsoft és az SAP az Azure-ban a DBMS-telepítések egyikét sem. A DBMS smb-megosztásokon történő telepítése egyáltalán nem támogatott. A DBMS NFS-megosztásokon történő telepítése az Azure NetApp Files NFS 4.1-es megosztására [korlátozódik.](https://azure.microsoft.com/services/netapp/)


## <a name="high-availability-for-sap-central-service"></a>Az SAP központi szolgáltatás magas rendelkezésre állása
Az SAP központi szolgáltatások az SAP-konfiguráció második egyetlen meghibásodási pontja. Ennek eredményeképpen ezeket a központi szolgáltatási folyamatokat is meg kell védenie. Az SAP-munkaterheléstámogatott és dokumentált ajánlata a következőképpen szól:

- Windows feladatátvevőfürt-kiszolgáló, amely a Windows kibővített fájlszolgáltatásokat használja a sapmnt és a globális átviteli könyvtárhoz. A részleteket a cikk ismerteti:
    - [SAP ASCS/SCS-példány fürtjének fürtje Windows feladatátvevő fürtön fájlmegosztás használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Az Azure-infrastruktúra előkészítése az SAP magas rendelkezésre állására egy Windows feladatátvételi fürt és fájlmegosztás használatával SAP ASCS/SCS-példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Windows feladatátvevő fürtkiszolgáló SMB-megosztáshasználatával az [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) sapmnt és globális átviteli könyvtár alapján. A részletek a cikkben találhatók:
    - [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken Windows rendszeren, SAP-alkalmazásokhoz készült Azure NetApp-fájlok (SMB)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows feladatátvevő fürtkiszolgáló sios `Datakeeper`alapú . Bár a Microsoft dokumentálta, a SIOS-szel támogatási kapcsolatra van szüksége, így a megoldás használatakor kapcsolatba léphet a SIOS-támogatással. A részleteket a cikk ismerteti:
    - [SAP ASCS/SCS-példány fürtjének fürtje egy Windows feladatátvételi fürtön az Azure-ban megosztott fürt használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Az Azure-infrastruktúra előkészítése az SAP HA számára egy Windows feladatátvételi fürt és az SAP ASCS/SCS megosztott lemezhasználatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- A SUSE operációs rendszer pacemakere egy magas rendelkezésre állású `drdb` NFS-megosztás létrehozásával két SUSE virtuális gép használatával és fájlreplikációhoz. A részleteket a cikk dokumentálja
    - [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server SAP-alkalmazásokhoz szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Pacemaker SUSE operációs rendszer kihasználva NFS-megosztások által biztosított [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). A részleteket a
    - [Az SAP NetWeaver magas rendelkezésre állása az Azure virtuális gépeken a SUSE Linux Enterprise Server en az SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker a Red Hat operációs rendszer NFS megosztással egy `glusterfs` fürtön. Részletek találhatók a cikkekben
    - [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`Az Azure virtuális gépeken a Red Hat Enterprise Linux sap NetWeaver-hez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker a Red Hat operációs rendszer NFS megosztásházigazdája [az Azure NetApp Files](https://azure.microsoft.com/services/netapp/). A részleteket a cikk ismerteti
    - [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on Azure NetApp Files SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

A felsorolt megoldások közül támogatási kapcsolatra van szüksége a `Datakeeper` SIOS-szel, hogy támogassa a terméket, és problémák esetén közvetlenül kapcsolatba lépjen a SIOS-szel. A Windows, a Red Hat és/vagy a SUSE operációs rendszer licencmódjától függően előfordulhat, hogy támogatási szerződést kell kötnie az operációs rendszer szolgáltatójával, hogy teljes mértékben támogassa a felsorolt magas rendelkezésre állású konfigurációkat.

A konfiguráció is megjeleníthető, mint:

![DBMS és ASCS HA konfiguráció](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

A grafika jobb oldalán a magas rendelkezésre állású SAP central services jelenik meg. Amellett, hogy az SAP Központi szolgáltatások olyan feladatátvevő fürtkeretrendszerrel vannak védve, amely probléma esetén feladatátvételt tesz lehetővé, szükség van egy magas rendelkezésre állású NFS- vagy SMB-megosztásra, vagy egy Windows megosztott lemezre, hogy megbizonyosodjon arról, hogy a sapmnt és a globális átviteli könyvtár függetlenül attól, hogy létezik-e egyetlen virtuális gép. További megoldások, például a Windows feladatátvevő fürtkiszolgáló és a pacemaker lesz szükség egy Azure-terheléselosztó közvetlen vagy átirányítja a forgalmat egy kifogástalan csomópont.

A listában látható, nincs említés az Oracle Linux operációs rendszer. Az Oracle Linux nem támogatja a Pacemaker t fürtkeretrendszerként. Ha sap-rendszerét Oracle Linuxra szeretné telepíteni, és magas rendelkezésre állási keretrendszerre van szüksége az Oracle Linux számára, külső beszállítókkal kell együttműködnie. Az egyik szállító a SIOS a Protection Suite for Linux, amely az SAP által támogatott az Azure-ban. További információkért olvassa el az SAP [note #1662610 - A SIOS Protection Suite for Linux támogatási adatait](https://launchpad.support.sap.com/#/notes/1662610) további részletekért.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Támogatott tárolás a fent felsorolt SAP központi szolgáltatási forgatókönyvekkel
Mivel az Azure storage-típusoknak csak egy részhalmaza biztosít magas rendelkezésre állású NFS- vagy SMB-megosztásokat, amelyek minőséget biztosítanak az SAP Központi Szolgáltatások fürtforgatókönyveiben való használathoz, a támogatott tárolótípusok listáját

- A Windows feladatátvevő fürtkiszolgáló és a Windows kibővített fájlkiszolgáló minden natív Azure-tárhelytípusra telepíthető, kivéve az Azure NetApp-fájlokat. Azonban az a javaslat, hogy a prémium szintű storage-t az átviteli és iops-os magasabb szintű szolgáltatásiszint-szerződések nek köszönhetően használja.
- Az Azure NetApp-fájlok smb-vel rendelkező Windows feladatátvevő fürtkiszolgálója támogatott. Az Azure File-szolgáltatások SMB-megosztások **jelenleg nem** támogatottak.
- A Windows feladatátvevő fürtkiszolgáló sios-alapú Windows feladatátvételi fürtkiszolgálója sios-alapú `Datakeeper` Windows feladatátvevő fürtkiszolgálóval az Azure NetApp-fájlok kivételével minden natív Azure-tárhelytípusra telepíthető. Azonban az a javaslat, hogy a prémium szintű storage-t az átviteli és iops-os magasabb szintű szolgáltatásiszint-szerződések nek köszönhetően használja.
- Az Azure NetApp-fájlok nfs-megosztásokat használó SUSE vagy Red Hat pacemaker támogatott. 
- A két virtuális `drdb` gép közötti konfigurációt használó SUSE pacemaker t natív Azure-tárolási típusok használatával támogatja, kivéve az Azure NetApp-fájlokat. Azonban az a javaslat, hogy a prémium szintű storage-t az átviteli és iops-os magasabb szintű szolgáltatásiszint-szerződések nek köszönhetően használja.
- Red Hat `glusterfs` pacemaker segítségével nfs-megosztás tanonc az Azure Storage-típusok, kivéve az Azure NetApp-fájlok használata. Azonban az a javaslat, hogy a prémium szintű storage-t az átviteli és iops-os magasabb szintű szolgáltatásiszint-szerződések nek köszönhetően használja.

> [!IMPORTANT]
> A Microsoft Azure Piactér számos olyan puha készüléket kínál, amelyek tárolási megoldásokat kínálnak az Azure natív tárhelyén felül. Ezek a soft appliances lehet használni NFS vagy SMB megosztások, valamint, hogy elméletileg lehet használni a feladatátvevő fürtözött SAP központi szolgáltatások is használható. Ezeket a megoldásokat a Microsoft nem támogatja közvetlenül az SAP-munkaterheléshez. Ha úgy dönt, hogy egy ilyen megoldás segítségével hozza létre az NFS vagy SMB-megosztás, az SAP központi szolgáltatás konfigurációját kell biztosítania a harmadik fél tulajdonában lévő szoftver a tároló puha készülék.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>TöbbSID-szintű SAP központi szolgáltatások feladatátvételi fürtjei
A nagy SAP-környezetekben szükséges virtuális gépek számának csökkentése érdekében az SAP lehetővé teszi több különböző SAP-rendszer SAP központi szolgáltatások példányainak futtatását feladatátvételi fürtkonfigurációban. Képzeljen el olyan eseteket, amikor 30 vagy több NetWeaver vagy S/4HANA termelési rendszerrel rendelkezik. Több SID-fürtözés nélkül ezek a konfigurációk 30 vagy több Windows vagy Pacemaker feladatátvételi fürtkonfigurációban 60 vagy több virtuális gépet igényelnének. A szükséges DBMS feladatátvételi fürtök mellett. Több SAP központi szolgáltatás üzembe helyezése két csomópont között egy feladatátvevő fürt konfigurációjában jelentősen csökkentheti a virtuális gépek számát. Azonban több SAP központi szolgáltatási példányok üzembe helyezése egyetlen két csomópont fürtkonfigurációis van néhány hátránya is. A fürtkonfigurációban lévő egyetlen virtuális gép körüli problémák több SAP-rendszerre vonatkoznak. A fürtkonfigurációban futó vendég operációs rendszer karbantartása nagyobb koordinációt igényel, mivel több éles SAP-rendszer érintett. Az OLYAN eszközök, mint az SAP LaMa nem támogatják a több SID-fürtözést a rendszer klónozási folyamatában.

Az Azure-ban az ENSA1 és AZ ENSA2 operációs rendszer többSID-fürtkonfigurációja támogatott. Az ajánlás nem a régebbi enqueue replikációs szolgáltatás architektúrájának (ENSA1) és az új architektúra (ENSA2) egyetlen többSID-fürtön való egyesítése. Az ilyen architektúrával kapcsolatos részleteket a cikkek dokumentálják

- [Az SAP ASCS/SCS példány több SID-példány magas rendelkezésre állása windows Server feladatátvételi fürtözéssel és megosztott lemezzel az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Az SAP ASCS/SCS példány több SID-példány magas rendelkezésre állása windows Server feladatátvételi fürtözéssel és fájlmegosztással az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

A SUSE esetében a Pacemaker alapú több-SID-fürt is támogatott. Eddig a konfiguráció támogatott:

- Legfeljebb öt SAP ASCS/SCS-példány
- A régi enqueue replikációs kiszolgáló jégarchitektúrája (ENSA1)
- Két csomópont pacemakerének fürtkonfigurációja

A konfiguráció dokumentálva [az SAP NetWeaver magas rendelkezésre állású az Azure-beli virtuális gépeken az Sap Enterprise Server for SAP alkalmazások több SID-útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

A több SID-fürt enqueue replikációs kiszolgáló samatikusan néz ki

![DBMS és ASCS HA konfiguráció](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA horizontális felskálázási forgatókönyvek
Az SAP HANA horizontális felskálázási forgatókönyvek az [SAP HANA hardverkönyvtárában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)felsorolt HANA-tanúsítvánnyal rendelkező Azure-virtuális gépek egy részhalmaza számára támogatottak. A "Fürtözés" oszlopban "Igen" jellel jelölt összes virtuális gép használható OLAP vagy S/4HANA horizontális felskálázáshoz. A készenlétnélküli konfigurációkat az Azure Storage-típusok támogatják: 

- Azure Premium Storage, beleértve az Azure Write gyorsítót a /hana/log kötethez
- [Ultralemez](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Az OLAP vagy az S/4HANA készenléti csomópontokkal rendelkező SAP HANA kibővített konfigurációit kizárólag az Azure NetApp-fájlokban üzemeltetett NFS támogatja.

A készenléti csomós sal rendelkező vagy a nélküli pontos tárolási konfigurációkról további információt a következő cikkekben talál:

- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával a SUSE Linux Enterprise Server en](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [SAP HANA horizontális felskálázási rendszer üzembe helyezése készenléti csomókkal az Azure virtuális gépein az Azure NetApp-fájlok használatával Red Hat Enterprise Linux on Red Hat Enterprise Linux on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [SAP támogatási megjegyzés #2080991](https://launchpad.support.sap.com/#/notes/2080991)

A HANA nagy példányok által támogatott HANA kibővített konfigurációk részleteiért a következő dokumentáció vonatkozik:

- [Támogatott forgatókönyvek hana nagy példányok horizontális felskálázás a készenléti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Támogatott forgatókönyvek hana nagy példányok horizontális felskálázás nélkül készenléti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Vész-helyreállítási forgatókönyv
Számos vész-helyreállítási forgatókönyvek támogatottak. A katasztrófa-architektúrákat olyan architektúrákként definiáljuk, amelyek kompenzálják a teljes Azure-régiókat, amelyek lekerülnek a hálózatról. Ez azt jelenti, hogy a vész-helyreállítási cél egy másik Azure-régióban, mint a cél az SAP-környezet futtatásához. A módszereket és konfigurációkat dbms rétegben és nem DBMS rétegben különítjük el. 

### <a name="dbms-layer"></a>DBMS-réteg
A DBMS-réteg esetében a DBMS natív replikációs mechanizmusait használó konfigurációk, például az Always On, az Oracle Data Guard, a Db2 HADR, az SAP ASE Always-On vagy a HANA system replication támogatottak. Az ilyen esetekben kötelező, hogy a replikációs adatfolyam aszinkron, ahelyett, hogy szinkron, mint a tipikus magas rendelkezésre állású forgatókönyvek, amelyek egyetlen Azure-régióban telepített. Egy ilyen támogatott DBMS vész-helyreállítási konfiguráció egy tipikus példa az [SAP HANA rendelkezésre állása az Azure-régiók között.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions) A szakasz második ábrája egy hana-val példaként leírt forgatókönyvet. Az SAP-alkalmazások által támogatott fő adatbázisok egy ilyen forgatókönyvben telepíthetők.

A vész-helyreállítási régióban egy kisebb virtuális gép használata a vész-helyreállítási régióban, mivel a virtuális gép nem tapasztalja a teljes számítási terhelést. Ezzel a következő szempontokat kell szem előtt tartania:

- A kisebb virtuálisgép-típusok nem teszik lehetővé, hogy a kisebb virtuális gépeknél sok lemez
- A kisebb virtuális gépek kevesebb hálózati és tárolási átviteli
- A virtuálisgép-családok közötti méretezés problémát okozhat, ha a különböző virtuális gépeket egy Azure rendelkezésre állási csoportban gyűjtik, vagy ha az újraméretezés nek az M sorozatú család és a virtuális gépek Mv2 családja között kell megtörténnie
- Az adatbázis-példány processzor- és memóriafelhasználása, amely minimális késleltetéssel és elegendő CPU- és memóriaerőforrással képes fogadni a változások adatfolyamát, hogy ezeket a módosításokat minimális késleltetéssel alkalmazza az adatokra  

További részletek a korlátozások a különböző virtuális gép méretek megtalálható [itt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

A DR-cél központi telepítésének egy másik támogatott módszere, hogy egy második DBMS-példány telepítve van egy virtuális gépre, amely egy nem éles SAP-példány nem éles ADATBÁZIS-példányát futtatja. Ez egy kicsit nagyobb kihívást jelenthet, mivel ki kell találnia, hogy mi van szükség a memóriában, a PROCESSZOR-erőforrásokon, a hálózati sávszélességen és a tárolási sávszélességen az adott célpéldányok esetében, amelyek nek fő példányként kell működniük a VÉSZ-forgatókönyvben. Különösen a HANA erősen ajánlott, hogy konfigurálja a példányt, amely vész-érték célként működik egy megosztott gazdagépen, hogy az adatok nem előre betöltött a VÉSZ-célpéldány.

Hana nagy példány ra d. forgatókönyvek ellenőrizze ezeket a dokumentumokat:

- [Egyetlen csomópont a DR-rel a tárolóreplikáció használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Egyetlen csomópont DR-rel (többcélú) tárolóreplikációval](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Egyetlen csomópont DR-rel (többcélú) tárolóreplikációval](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Magas rendelkezésre állás hsr és DR tároló replikációval](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Horizontális felskálázás a DR-rel a tárolóreplikáció használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Egyetlen csomópont DR-rel hsr használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Egycsomópontos HSR–DR (költségoptimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Magas rendelkezésre állás és vészhelyreállítás a HSR-rel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Magas rendelkezésre állás és vészhelyreállítás a HSR-rel (költségoptimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Horizontális felskálázás dr-rel hsr használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Az [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) használata nem tesztelt a DBMS-telepítések sap-munkaterhelés alatt. Ennek eredményeképpen jelenleg nem támogatott az SAP-rendszerek ADATBÁZIS-rétege. A Microsoft és az SAP más, nem felsorolt replikációs módszerei nem támogatottak. A szoftver szállítójának támogatnia kell az SAP-rendszerek ADATBÁZIS-rétegének replikálására szolgáló harmadik féltől származó szoftvert a különböző Azure-régiók közötti replikálására, és a Microsoft és az SAP támogatási csatornáin keresztül nem támogatott. 
 
## <a name="non-dbms-layer"></a>Nem DBMS-réteg
Az SAP-alkalmazásréteg és a szükséges esetleges megosztások vagy tárolási helyek esetében a két fő forgatókönyvet az ügyfelek használják ki:

- A vész-helyreállítási célokat a második Azure-régióban nem használják semmilyen éles vagy nem éles célokra. Ebben a forgatókönyvben a vész-helyreállítási célként működő virtuális gépek ideális esetben nem üzembe helyezhetők, és a rendszerkép és az éles SAP-alkalmazásréteg rendszerképváltozásai replikálódnak a vész-helyreállítási régióba. Egy ilyen feladat végrehajtására alkalmas funkció az [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). Az Azure Site Recovery támogatja az Azure-to-Azure replikációs forgatókönyvek, mint ez. 
- A vész-helyreállítási célok olyan virtuális gépek, amelyek ténylegesen nem éles rendszerek által használt. A teljes SAP-környezet két különböző Azure-régióban van elosztva, ahol az éles rendszerek általában egy régióban és egy másik régióban nem éles rendszerek. Sok ügyfél-telepítésesetén az ügyfél rendelkezik egy nem éles rendszerrel, amely egyenértékű a termelési rendszerrel. A vevő éles alkalmazáspéldányok előre telepítve az alkalmazásréteg nem éles rendszerek. Feladatátvétel esetén a nem éles példányok leállnának, az éles virtuális gépek virtuális nevei áthelyezve a nem éles virtuális gépekre (miután új IP-címeket adtak hozzá a DNS-hez), és az előre telepített éles példányok elindulnak

### <a name="sap-central-services-clusters"></a>SAP központi szolgáltatások fürtjei
A megosztott lemezeket (Windows), SMB-megosztásokat (Windows) vagy NFS-megosztásokat használó SAP központi szolgáltatások fürtjeit kissé nehezebb replikálni. A Windows oldalán a Windows storage replikáció egy lehetséges megoldás. Linux on rsync egy életképes megoldás.



## <a name="non-supported-scenario"></a>Nem támogatott forgatókönyv
Van egy forgatókönyv, amely nem támogatott az Azure-architektúrák SAP-számítási feladatok. **Nem támogatott** azt jelenti, hogy az SAP és a Microsoft nem fogja tudni támogatni ezeket a konfigurációkat, és el kell halasztania egy esetleges harmadik fél, amely az ilyen architektúrák létrehozásához szoftvert biztosított. A következő kategóriák közül kettő:

- Soft appliances tárolása: Számos tároló puha készülékek az Azure piactéren kínálnak. Egyes gyártók saját dokumentációt kínálnak arról, hogyan használhatja ezeket a tárolópuha készülékeket az Sap-szoftverekhez kapcsolódóAn az Azure-ban. Az ilyen tárolópuha berendezéseket érintő konfigurációk vagy üzembe helyezések támogatását a tárolópuha készülékek forgalmazójának kell biztosítania. Ez a tény az [SAP támogatási megjegyzésében](https://launchpad.support.sap.com/#/notes/2015553) is megnyilvánul #2015553
- Magas rendelkezésre állású keretrendszerek: Csak pacemaker és a Windows Server feladatátvételi fürt támogatott magas rendelkezésre állású keretrendszerek SAP számítási feladatok az Azure-ban. Mint korábban említettük, a `Datakeeper` SIOS megoldását a Microsoft ismerteti és dokumentálja. Mindazonáltal a SIOS `Datakeeper` összetevőit a SIOS-on keresztül kell támogatni, mint az összetevőket biztosító szállítót. Az SAP más, minősített, magas rendelkezésre állású keretrendszereket is felsorolt a különböző SAP-jegyzetekben. Ezek közül néhányat a külső gyártó is tanúsított az Azure-hoz. Mindazonáltal az ezeket a termékeket használó konfigurációkat a termék szállítójának kell biztosítania. A különböző szállítók különböző integrációval rendelkeznek az SAP támogatási folyamataiba. Tisztázza, hogy melyik támogatási folyamat működik a legjobban az adott szállító számára, mielőtt úgy dönt, hogy a terméket az Azure-ban telepített SAP-konfigurációkban használja.
- Azok a megosztott lemezfürtök, amelyek adatbázisfájljai a megosztott lemezeken találhatók, a maxDB kivételével nem támogatottak. Az összes többi adatbázis esetében a támogatott megoldás az, hogy smb- vagy NFS-megosztás vagy megosztott lemez helyett külön tárolóhelyekkel kell rendelkeznie a magas rendelkezésre állású forgatókönyvek konfigurálásához.

Más forgatókönyvek, amelyek nem támogatottak, olyan forgatókönyvek, mint:

- Üzembe helyezési forgatókönyvek, amelyek nagyobb hálózati késést vezetnek be az SAP alkalmazásréteg és az SAP DBMS-szint között `Hybris`az SAP közös architektúrájában, ahogy azt a NetWeaver, az S/4HANA és például . Az érintett műveletek közé tartoznak az alábbiak:
    - Az egyik szint üzembe helyezése a helyszínen, míg a másik szint az Azure-ban van telepítve
    - A rendszer SAP-alkalmazásszintjének üzembe helyezése a DBMS-rétegtől eltérő Azure-régióban
    - Üzembe helyezése egy réteg adatközpontokban, amelyek az Azure-ban és a másik réteg az Azure-ban, kivéve, ha egy ilyen architektúra minták által biztosított Egy Azure natív szolgáltatás
    - Hálózati virtuális készülékek üzembe helyezése az SAP alkalmazásréteg és a DBMS-réteg között
    - Az SAP DBMS-csomag vagy az SAP globális átviteli címtárához tartozó, az Azure-adatközpontban található adatközpontokban tárolt tárterület kihasználása
    - A két réteg üzembe helyezése két különböző felhőszállítóval. Például a DBMS-szint üzembe helyezése az Oracle Cloud Infrastructure-ben és az alkalmazásszint az Azure-ban
- Többpéldányos HANA pacemaker-fürt konfigurációi
- Windows fürtkonfigurációk megosztott lemezekkel a SOFS-en vagy az SMB-n keresztül a Windows által támogatott SAP-adatbázisokhoz. Ehelyett azt javasoljuk, hogy az adott adatbázisok natív, magas rendelkezésre állású replikációját használja, és külön tárolóhalmokat használjon
- Sap adatbázisok telepítése Linuxon támogatott adatbázis-fájlok nfs megosztások tetején ANF, kivéve az SAP HANA
- Az Oracle DBMS telepítése a Windows és az Oracle Linux rendszeren kívül bármely más vendég operációs rendszeren. Lásd még: [SAP támogatási megjegyzés #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Forgatókönyv(ek), hogy nem tesztelt, és ezért nincs tapasztalata a lista, mint például:

- Az Azure Site Recovery replikálása DBMS réteg virtuális gépek. Ennek eredményeképpen azt javasoljuk, hogy az adatbázis natív aszinkron replikációs funkcióját kihasználva a potenciális vész-helyreállítási konfigurációhoz
 

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő lépéseket az [Azure virtuális gépek tervezésében és megvalósításában az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



