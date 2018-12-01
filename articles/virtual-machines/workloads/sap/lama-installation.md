---
title: Az Azure SAP LaMa összekötő |} A Microsoft Docs
description: Az Azure-ban az SAP LaMa SAP-rendszerek felügyelete
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 17/07/2018
ms.author: sedusch
ms.openlocfilehash: 2a0934fa3bb46eebba02029a8292b9bee6b12c62
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728225"
---
# <a name="sap-lama-connector-for-azure"></a>Az Azure SAP LaMa összekötő

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Általános támogatási nyilatkozattal: Mindig nyissa meg az incidens az SAP BC-VCM-LVM-Hyper-v összetevő Ha támogatásra van szüksége az SAP LaMa vagy az Azure-összekötő.

SAP LaMa segítségével számos felhasználó működtetéséhez és figyelheti az SAP-rendszeren. SAP LaMa 3.0 SP05 óta letöltésként érhető el egy-egy összekötő alapértelmezés szerint az Azure-bA. Ez az összekötő segítségével szabadítsa fel és virtuális gépek elindítása, másolása és helyezze át a felügyelt lemezek és felügyelt lemezek törlése. Alapszintű ezeket a műveleteket helyezze át, másolja, klónozza és frissítse az SAP LaMa SAP-rendszerekkel.

Ez az útmutató azt ismerteti, hogyan állíthatja be az Azure-összekötő az SAP-LaMa, hozzon létre virtuális gépeket, amelyek adaptív SAP-rendszereit és a konfigurálásukról telepítéséhez használható.

> [!NOTE]
> Az összekötő csak az SAP LaMa Enterprise verzióban érhető el

## <a name="resources"></a>További források

Az alábbi SAP-megjegyzések kapcsolódnak a témakör az SAP-LaMa az Azure-ban:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [2343511] |A Microsoft Azure-összekötő az SAP-rendszeren, Management (LaMa) |
| [2350235] |SAP fekvő felügyeleti 3.0 – Enterprise edition |

Emellett olvassa el a [SAP súgó portál SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Általános megjegyzéseket

* Ne feledje engedélyezni *automatikus csatlakoztatási pont létrehozásához* beállítása -> Beállítások -> motor  
  Ha az SAP LaMa csatlakoztatja a kötetek az adaptív SAP-bővítmények használatával a virtuális gépen, a csatlakoztatási pont léteznie kell, ha ez a beállítás nincs engedélyezve.

* Használjon külön alhálózatot, és nem használja a dinamikus IP-címeket, hogy az IP-cím "lopásának megjelölése" új virtuális gépek üzembe helyezésekor és SAP példányai nem előkészített  
  Ha dinamikus IP-címek hozzárendelését az alhálózaton, SAP LaMa által is használt, az SAP-rendszer előkészítése az SAP-LaMa meghiúsulhat. Ha az SAP-rendszerek nem előkészített, az IP-címek nem fenntartott és előfordulhat, hogy első ki más virtuális gépeknek.

* Ha bejelentkezik a felügyelt gazdagépek, ügyeljen arra, hogy nem blokkolja a leválasztották fájlrendszerek  
  Ha bejelentkezik egy Linux rendszerű virtuális gépek, és módosítsa a munkakönyvtárban címtárhoz a csatlakoztatási pont, például /usr/sap/AH1/ASCS00/exe, a kötet nem lehet leválasztani és áthelyezése vagy unprepare meghiúsul.

## <a name="set-up-azure-connector-for-sap-lama"></a>Az Azure-összekötő az SAP-LaMa beállítása

Az Azure-összekötő az SAP LaMa 3.0 SP05 kezdődően tartalmazza a szükséges. Azt javasoljuk, hogy mindig a legújabb támogatási csomag és a javítás telepítése SAP LaMa 3.0. Az Azure-összekötő engedélyezése ellen a Microsoft Azure egy egyszerű szolgáltatást használ. Kövesse az alábbi lépéseket az SAP-rendszeren, Management (LaMa) egy szolgáltatásnév létrehozásához.

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az Azure Active Directory panel
1. Kattintson az alkalmazásregisztrációk
1. Kattintson a Hozzáadás gombra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például http://localhost) , majd kattintson a létrehozás
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza ki az új alkalmazást, majd kattintson a kulcsok a beállítások lapon
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés
1. Jegyezze fel az értéket. Használatban van a jelszót a szolgáltatásnév
1. Jegyezze fel az alkalmazás azonosítóját. A szolgáltatás egyszerű felhasználónév használatban van

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Kell adni az egyszerű szolgáltatás engedélyeket érheti el őket.

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az erőforráspanelt csoportok
1. Válassza ki a használni kívánt erőforráscsoportot
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson a szerepkör-hozzárendelés hozzáadása
1. Válassza ki a közreműködő szerepkört
1. Adja meg a fent létrehozott alkalmazás neve
1. Kattintson a Save (Mentés) gombra.
1. Ismételje meg a 3 – 8 SAP LaMa használni kívánt összes erőforráscsoportra vonatkozóan

Nyissa meg az SAP LaMa webhelyet, és keresse meg az infrastruktúrához. Nyissa meg felhőalapú kezelők lapot, és kattintson a Hozzáadás gombra. Válassza ki a Microsoft Azure felhőalapú adaptert, és kattintson a Tovább gombra. Adja meg a következő információkat:

* Címke: Válassza ki az összekötő-példány nevét
* Felhasználónév: Egyszerű szolgáltatás Alkalmazásazonosítója
* Jelszó: Egyszerű szolgáltatás kulcs vagy jelszó
* URL-címe: Tartsa alapértelmezett https://management.azure.com/
* Figyelési időköz (másodperc): Legalább 300 kell lennie
* Előfizetés azonosítója: Azure-előfizetés azonosítója
* Az Azure Active Directory-bérlő azonosítója: Az Active Directory-bérlő Azonosítóját
* A proxyállomás: a proxyt, ha SAP LaMa kell az internethez való kapcsolódáshoz proxy állomásnevét
* Proxy portja: TCP-port a proxy

Kattintson a konfiguráció ellenőrzése a. Megtekintheti az

A sikeres kapcsolat: a Microsoft cloud sikerült csatlakozni. 7 erőforráscsoportok található (legfeljebb 10 csoportok kért)

a webhely alján.

## <a name="provision-a-new-adaptive-sap-system"></a>Új adaptív SAP-rendszerek kiépítése

Manuálisan is üzembe új virtuális gépet, vagy használja az Azure-sablonok egyikét a [rövid tárház](https://github.com/Azure/azure-quickstart-templates). A sablonok tartalmaz [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver alkalmazáskiszolgálók](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps), és a [adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-db). Ezek a sablonok használatával új gazdagépek üzembe helyezése során a rendszer másolási/klón stb.

Az összes virtuális gépet, hogy az SAP-LaMa kezelni szeretne, és ne használja a dinamikus IP-címek, hogy az IP cím "lopásának megjelölése" új virtuális gépek üzembe helyezésekor és SAP példányai nem előkészített egy külön alhálózat használatát javasoljuk.

> [!NOTE]
> Ha lehetséges távolítsa el az összes virtuálisgép-bővítmények, akkor előfordulhat, hogy hosszú modulok leválasztása a virtuális gépek lemezeit a.

Győződjön meg arról, hogy a felhasználó \<hanasid > adm, \<sapsid > adm és csoport sapsys a célgépen ilyen Azonosítójú és csoportazonosító létezik, vagy az LDAP-vel. Engedélyezze, és indítsa el az NFS-kiszolgáló a virtuális gépek futtatásához az SAP NetWeaver (A) SCS használandó.

### <a name="manual-deployment"></a>Manuális telepítés

SAP LaMa kommunikál a virtuális gépet az SAP gazdagép-ügynök használatával. Ha a virtuális gépeket manuálisan, vagy nem az Azure Resource Manager-sablon használatával a rövid útmutató adattárból telepít, ügyeljen arra, hogy telepítse a legújabb SAP gazdagép-ügynök és az SAP adaptív bővítmények. Az Azure-ban a szükséges javítási szintekkel kapcsolatos további információkért tekintse meg az SAP-Jegyzetnek [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Manuális központi telepítése egy Linux rendszerű virtuális gép

Hozzon létre egy új virtuális gépet az egyik az SAP-Jegyzetnek felsorolt támogatott operációs rendszereken [2343511]. Adjon hozzá további, a SAP-példányok IP-konfigurációk. Minden példány IP-cím vagy újabb kell, és segítségével egy virtuális állomásnevet kell telepíteni.

Az SAP NetWeaver ASCS példány /sapmnt/ van szüksége a lemezek\<SAPSID >, usr/sap/\<SAPSID >, és /usr/sap/transusr/sap/\<sapsid > adm. Az SAP NetWeaver alkalmazás kiszolgálóknak nem kell további lemezeket. Az SAP-példányhoz kapcsolódó összes elemet kell az ASCS tárolja és NFS keresztül van exportálva. Ellenkező esetben azt jelenleg nem lehet felvenni az SAP LaMa használatával további alkalmazáskiszolgálók.

![SAP NetWeaver ASCS Linux rendszeren](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Kézi telepítés az SAP Hana-hoz

Új virtuális gép létrehozása egy, a támogatott operációs rendszereken az SAP Hana-hoz az SAP-Jegyzetnek felsorolt [2343511]. Adjon hozzá egy további IP-konfiguráció az SAP Hana-hoz és a egy HANA bérlőnként.

SAP HANA van szüksége a lemezek /hana/shared, /hana/backup, /hana/data és /hana/log

![Az SAP HANA Linux rendszeren](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Kézi telepítés linuxon Oracle-adatbázis

Hozzon létre egy új virtuális gépet egy Oracle-adatbázisok esetében a támogatott operációs rendszereken az SAP-Jegyzetnek felsorolt [2343511]. Adjon hozzá egy további IP-konfigurációt az Oracle-adatbázishoz.

Az Oracle-adatbázishoz lemezek /oracle, /home/oraod1 és /home/oracle van szüksége.

![Oracle database Linux rendszeren](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>A Microsoft SQL Server manuális központi telepítése

Új virtuális gép létrehozása egy, a támogatott operációs rendszereken a Microsoft SQL Server az SAP-Jegyzetnek felsorolt [2343511]. Adjon hozzá egy további IP-konfigurációt az SQL Server-példányt.

Az SQL Server adatbázis-kiszolgáló a adatbázis adatait és a naplófájlok és a lemezek c:\usr\sap szüksége van a lemezeket.

![Oracle database Linux rendszeren](media/lama/sap-lama-db-sql.png)

Ellenőrizze, hogy telepítse a támogatott Microsoft ODBC-illesztőprogram SQL Serverhez készült áthelyezheti az SAP NetWeaver alkalmazást az alkalmazáskiszolgálóra, vagy a rendszer másolási/klón céljaként használni kívánt virtuális gépen.

SAP LaMa nem helyezze át az SQL Server magát, így át egy adatbázis-példányt, vagy a rendszer másolási/klón céljaként használni kívánt virtuális gépeket kell előre telepítve az SQL Server.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Azure-sablon használatával a virtuális gép üzembe helyezése

A következő legújabb elérhető archiválja a letöltés a [SAP Software Marketplace](https://support.sap.com/swdc) az operációs rendszer a virtuális gépek:

1. SAPCAR 7.21
1. AZ SAP-GAZDAGÉP-ÜGYNÖK 7.21
1. SAP ADAPTÍV BŐVÍTMÉNY 1.0 EXT

Is letöltheti a következő összetevőit a [Microsoft Download Center](https://www.microsoft.com/download)

1. A Microsoft Visual C++ 2010 újraterjeszthető csomag (x64) (csak Windows)
1. Microsoft ODBC-illesztőprogram SQL Serverhez (csak az SQL Server)

Az összetevők a sablon üzembe helyezéséhez szükségesek. A legegyszerűbben úgy, hogy a sablon számára elérhetővé tenni őket, hogy feltölti őket az Azure storage-fiók és a egy közös hozzáférésű Jogosultságkód (SAS) létrehozása.

A sablonok a következő paraméterekkel rendelkezik:

* sapSystemId: az SAP-rendszer azonosítója. A lemez elrendezése létrehozására használatos (példáulusr/sap/\<sapsid >).

* Számítógépnév: az új virtuális gép számítógépnevét. Ezt a paramétert is használja az SAP LaMa szerint. Használatakor ez a sablon egy új virtuális gép üzembe helyezése a rendszer másolatot részeként, a SAP LaMa megvárja, amíg a gazdagép, az ennél a számítógépnévnél érhető el.

* osType: a telepíteni kívánt operációs rendszer típusát.

* DbType: az adatbázis típusát. Ez a paraméter segítségével határozza meg, hány további IP-konfigurációk hozzá kell adni, és hogy a lemez elrendezése hasonlóan kell kinéznie.

* sapSystemSize: az SAP-rendszer számára telepíteni kívánja a méretét. Ez a virtuálisgép-példányok típusát és méretét meghatározására szolgál.

* adminUsername: a virtuális géphez tartozó felhasználónév.

* adminPassword: a virtuális gép jelszavát. Az SSH nyilvános kulcs is megadhatja.

* sshKeyData: a virtuális gépek nyilvános SSH-kulcsot. Csak Linux operációs rendszerek esetében támogatott.

* subnetId: az azonosító az alhálózat létrehozásához használni szeretne.

* deployEmptyTarget: Ha azt szeretné, a virtuális gép használata a cél-példány áthelyezése vagy hasonló telepíthet egy üres céllal. Ebben az esetben nincs további lemezeket vagy IP-konfigurációk vannak csatolva.

* sapcarLocation: A hely az sapcar alkalmazás, amely megegyezik az operációs rendszer központi telepítése. a többi paraméter meg archívum kibontása sapcar szolgál.

* sapHostAgentArchiveLocation: helyét, a gazdagép-ügynök az SAP-archívumot. SAP-gazdagép-ügynök van telepítve Ez a sablon központi telepítésének részeként.

* sapacExtLocation: az SAP adaptív bővítmények helyét. SAP-Jegyzetnek [2343511] sorolja fel az Azure-hoz szükséges minimálisan előírt biztonsági javítási szintet.

* vcRedistLocation: helyét, a VC futtatókörnyezet, az SAP adaptív bővítmények telepítése szükséges. Ez a paraméter csak akkor szükséges Windows.

* odbcDriverLocation: a telepíteni kívánt ODBC-illesztő helyét. Csak a Microsoft ODBC-illesztőprogram SQL Serverhez használata támogatott.

* sapadmPassword: a sapadm felhasználó jelszava.

* sapadmId: A Linux sapadm felhasználó felhasználói azonosítója. A Windows nem szükséges.

* sapsysGid: A Linux-csoport azonosítója a sapsys csoport. A Windows nem szükséges.

* _artifactsLocation: az alap URI-t, ahol a sablonhoz szükséges összetevők találhatók. A sablon a hozzájuk tartozó parancsprogramok használatával lett telepítve, ha egy privát helyen az előfizetés fogja használni, és ez az érték automatikusan létrejön. Csak akkor szükséges, ha nem telepíti a sablont a Githubból.

* _artifactsLocationSasToken: a sasToken _artifactsLocation eléréséhez szükséges. A sablon a hozzájuk tartozó parancsprogramok használatával lett telepítve, amikor egy sasToken automatikusan jönnek létre. Csak akkor szükséges, ha nem telepíti a sablont a Githubból.

### <a name="sap-hana"></a>SAP HANA

Az alábbi példákban feltételezzük, hogy az SAP HANA telepítése azonosító HN1 és az SAP NetWeaver rendszer azonosító AH1 rendszerrel együtt. A virtuális állomásnevek hn1-db ah1-dB-t a HANA-bérlő, az SAP NetWeaver rendszer ah1 – ascs esetében az SAP NetWeaver ASCS és ah1-di-0 az SAP NetWeaver alkalmazás első kiszolgáló által használt HANA-példány.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>SAP NetWeaver ASCS, az SAP Hana telepítése

A SAP Software kiépítés Manager (SWPM) a Kezdés előtt kell csatlakoztatni a futó ASCS állomásnevét virtuális IP-címét. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

SWPM futtassa, és *ah1 – ascs* számára a *ASCS példány állomásnév*.

![Linux][Logo_Linux] Linux  
A következő profil paraméter hozzáadása a gazdagép-ügynök az SAP-profilt, amely /usr/sap/hostctrl/exe/host_profile helyen található. További információkért tekintse meg az SAP-Jegyzetnek [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Az SAP HANA telepítése

Ha az SAP HANA-t a parancssori eszköz hdblcm telepíteni, használja az paraméter – állomásnevet adjon meg egy virtuális állomásnevet. Az adatbázis virtuális állomásnevét, IP-cím hozzáadásához egy hálózati adapterhez kell. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

Adjon hozzá egy másik virtuális állomásnevet és IP-cím a következő a neve, amelyet az alkalmazáskiszolgálók a HANA-bérlőhöz való kapcsolódáshoz.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Az alkalmazás server virtuális gépen, nem pedig a HANA virtuális gép SWPM adatbázisban példánya telepítése fut. Használat *ah1-db* a a *adatbázis gazdagép* párbeszédpanelen *SAP-rendszerhez tartozó adatbázis*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Az SAP Hana SAP NetWeaver-alkalmazáskiszolgáló telepítése

A SAP Software kiépítés Manager (SWPM) a Kezdés előtt kell csatlakoztatni a virtuális kiszolgáló állomásneve a az alkalmazás IP-címét. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Javasoljuk, hogy az SAP NetWeaver profil paraméter adatbázisok/hdb/hdb_use_ident használatával állítsa be az identitást, amellyel a kulcs található a HDB userstore. A SWPM az adatbázis-példány telepítése után manuálisan adja hozzá ezt a paramétert, vagy futtassa a SWPM

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Ha azt manuálisan, is szüksége új HDB userstore bejegyzéseket hozhat létre.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Használat *ah1-di-0* a a *szolgáltatói CÍMEI példány állomásnév* párbeszédpanelen *elsődleges kiszolgáló alkalmazáspéldány*.

#### <a name="post-installation-steps-for-sap-hana"></a>Telepítés utáni lépéseket az SAP Hana-hoz

Győződjön meg arról, készítsen biztonsági másolatot a SYSTEMDB és az összes bérlői adatbázison, mielőtt egy bérlő lemásolni, move-bérlőben, vagy hozzon létre egy rendszer replikációt.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Az alábbi példákban feltételezzük, hogy az SAP NetWeaver rendszer telepítenie azonosító AS1 rendszerrel. A virtuális állomásnevek as1-db, az SAP NetWeaver rendszer as1 – ascs esetében az SAP NetWeaver ASCS és as1-di-0 az SAP NetWeaver alkalmazás első kiszolgáló által használt SQL Server-példány.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SAP NetWeaver ASCS, az SQL Server telepítése

A SAP Software kiépítés Manager (SWPM) a Kezdés előtt kell csatlakoztatni a futó ASCS állomásnevét virtuális IP-címét. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

SWPM futtassa, és *as1 – ascs* számára a *ASCS példány állomásnév*.

#### <a name="install-sql-server"></a>Az SQL Server telepítése

Az adatbázis virtuális állomásnevét, IP-cím hozzáadásához egy hálózati adapterhez kell. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Futtassa a SWPM adatbázisban példánya telepítése az SQL server virtuális gépen. Használja a SAPINST_USE_HOSTNAME =*as1-db* felül az SQL-kiszolgálóhoz való csatlakozáshoz használt állomásnévvel. Ha a virtuális gépet az Azure Resource Manager-sablon használatával telepítette, ügyeljen arra, hogy állítsa be a könyvtár, az adatok adatbázisfájlok *C:\sql\data* és az adatbázis naplófájlját *C:\sql\log*.

Győződjön meg arról, hogy a felhasználó *NT AUTHORITY\SYSTEM* hozzáfér az SQL Server és a kiszolgálói szerepkörrel rendelkezik *SysAdmin (rendszergazda)*. További információkért tekintse meg az SAP-Jegyzetnek [1877727] és [2562184].

#### <a name="install-sap-netweaver-application-server"></a>SAP NetWeaver-alkalmazáskiszolgáló telepítése

A SAP Software kiépítés Manager (SWPM) a Kezdés előtt kell csatlakoztatni a virtuális kiszolgáló állomásneve a az alkalmazás IP-címét. Az ajánlott módszer, hogy sapacext. Ha az IP-cím használatával sapacext csatlakoztatja, ügyeljen arra, hogy az IP-cím újracsatlakoztathatja az újraindítás után.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Használat *as1-di-0* a a *szolgáltatói CÍMEI példány állomásnév* párbeszédpanelen *elsődleges kiszolgáló alkalmazáspéldány*.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-and-warnings-during-discover"></a>Hibák és figyelmeztetések felderítése során

* A SELECT engedély megtagadva
  * [Microsoft] [ODBC SQL Server-illesztőprogram] [SQL Server] A SELECT engedély megtagadva az objektum "log_shipping_primary_databases", adatbázis "msdb", séma "dbo". [SOAPFaultException]  
  A SELECT engedély megtagadva az objektum "log_shipping_primary_databases", adatbázis "msdb", séma "dbo".
  * Megoldás  
    Győződjön meg arról, hogy *NT AUTHORITY\SYSTEM* férhet hozzá az SQL Server. Tekintse meg az SAP-Jegyzetnek [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Hibák és figyelmeztetések például érvényesítése

* Kivétel a HDB userstore ellenőrzése aktiválása vált szükségessé.  
  * Tekintse meg a naplófájl-megjelenítő  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: kivétel az érvényesítési 'RuntimeHDBConnectionValidator' azonosítójú (érvényesítése: "VALIDATION_HDB_USERSTORE"): nem sikerült beolvasni a hdbuserstore  
    HANA userstore nem szerepel a megfelelő helyre
  * Megoldás  
    Győződjön meg arról, hogy /usr/sap/AH1/hdbclient/install/installation.ini megfelelő

### <a name="errors-and-warnings-during-a-system-copy"></a>Hibák és figyelmeztetések során a rendszer másolása

* Hiba történt, amikor a kiépítés lépésben a rendszer érvényesítése
  * Által okozott: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException hívása "/ usr/sap/hostctrl/exe/sapacext – egy ShowHanaBackups -m HN1 -f 50 - h hn1-db - o szint = 0\;állapot = 5\;port 35013 pf = / usr/sap/hostctrl = / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r "|} /usr/SAP/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o szint = 0\;állapot = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Megoldás  
    HANA rendszer forrás biztonsági másolatot az összes adatbázis is

* Rendszer másolási lépés *Start* adatbázis-példány
  * Gazdagép-ügynök művelete "000D3A282BC91EE8A1D76CF1F92E2944" nem sikerült (OperationException. FaultCode: "127", üzenet: "parancs végrehajtása nem sikerült. : [Microsoft] [ODBC SQL Server-illesztőprogram] [SQL Server] felhasználónak nincs engedélye az alter database "AS2", az adatbázis nem létezik, vagy az adatbázis nem olyan állapotban, amely lehetővé teszi a hozzáférés-ellenőrzéseket. ")
  * Megoldás  
    Győződjön meg arról, hogy *NT AUTHORITY\SYSTEM* férhet hozzá az SQL Server. Tekintse meg az SAP-Jegyzetnek [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Hibák és figyelmeztetések során a rendszer klónozott

* Hiba történt a példány ügynök regisztrálásakor lépésben *kényszerített regisztrálása és a példány ügynök indítása* alkalmazáskiszolgáló vagy ASCS
  * Hiba történt a példány ügynök regisztrálásakor. (RemoteException: "Nem sikerült betölteni a példányok adatait profilból"\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ": nem érhető el profil"\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ": nincs ilyen fájl vagy könyvtár.")
  * Megoldás  
   Győződjön meg arról, hogy az ASC/SCS sapmnt megosztást SAP_AS1_GlobalAdmin a teljes hozzáféréssel rendelkezik

* Hiba történt a lépés *indítási védelem engedélyezése a klón*
  * Nem sikerült megnyitni a fájlt a(z)\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 "OK: nincs ilyen fájl vagy könyvtár
  * Megoldás  
    Az alkalmazáskiszolgáló számítógépfiókjának írási hozzáféréssel a profilhoz van szüksége.

### <a name="errors-and-warnings-during-create-system-replication"></a>Hibák és figyelmeztetések során a rendszer replikációs létrehozása

* A replikáció létrehozása gombra kattintva kivétel
  * Által okozott: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException hívása "/ usr/sap/hostctrl/exe/sapacext – egy ShowHanaBackups -m HN1 -f 50 - h hn1-db - o szint = 0\;állapot = 5\;port 35013 pf = / usr/sap/hostctrl = / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r "|} /usr/SAP/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o szint = 0\;állapot = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Megoldás  
    Tesztelése amennyiben sapacext hajtható végre `<hanasid`> adm

* Ha tárolási lépésben nincs engedélyezve a teljes másolási hiba
  * Hiba történt, amikor a jelentéskészítési IStorageCopyData.storageVolumeCopyList:1 elérési útja és a mező targetStorageSystemId környezeti attribútumot üzenet
  * Megoldás  
    Figyelmen kívül hagyhatja a figyelmeztetéseket. lépésben, és próbálkozzon újra. A probléma egy új támogatási csomag/patch SAP-LaMa javítja.

### <a name="errors-and-warnings-during-relocate"></a>Hibák és figyelmeztetések áthelyezése során

* Elérési út "/ usr/sap/AH1" nfs reexports számára nem engedélyezett.
  * Ellenőrizze az SAP-Jegyzetnek [2628497] részleteiről.
  * Megoldás  
    Adja hozzá a ASCS ASCS HostAgent profil exportálja. Tekintse meg az SAP-Jegyzetnek [2628497]

* A funkció nincs megvalósítva, amikor ASCS áthelyezésével
  * A parancs kimenete: exportfs: állomás: / usr/sap/AX1: függvény nincs megvalósítva
  * Megoldás  
    Győződjön meg arról, hogy az NFS-kiszolgáló szolgáltatás engedélyezve van-e a cél virtuális gépen áthelyezése

### <a name="errors-and-warnings-during-application-server-installation"></a>Hibák és figyelmeztetések alkalmazás kiszolgáló telepítése során

* Hiba történt a végrehajtása SAPinst. lépés: getProfileDir
  * Hiba: (utolsó lépés jelentett hiba: modul hívás történt ESAPinstException: lépés érvényesítő ' |} NW_DI |} ind |} ind |} ind |} ind |} 0 |} 0 |} NW_GetSidFromProfiles |} ind |} ind |} ind |} ind |} getSid |} 0 |} NW_readProfileDir |} ind |} ind |} ind |} ind |} readProfile |} 0 |} getProfileDir "hibát jelentett: csomópont \\\as1-ascs\sapmnt\AS1\SYS\profile nem létezik. A probléma megoldásához interaktív módban SAPinst kezdő)
  * Megoldás  
    Győződjön meg arról, hogy SWPM fut-e egy felhasználóval, amely hozzáfér a profilt. Ez a felhasználó az alkalmazáskiszolgáló telepítése varázslóban konfigurálható

* Hiba történt a végrehajtása SAPinst. lépés: askUnicode
  * Hiba: (utolsó lépés jelentett hiba: modul hívás történt ESAPinstException: lépés érvényesítő ' |} NW_DI |} ind |} ind |} ind |} ind |} 0 |} 0 |} NW_GetSidFromProfiles |} ind |} ind |} ind |} ind |} getSid |} 0 |} NW_getUnicode |} ind |} ind |} ind |} ind |} unicode |} 0 |} askUnicode "hibát jelentett: Start SAPinst interaktív módban a probléma megoldásához)
  * Megoldás  
    Egy újabb SAP-rendszermag használatakor SWPM nem tudja megállapítani, hogy a rendszer egy rendszer unicode többé használatával a ascs rendszerbe fut be, a kiszolgáló. Tekintse meg az SAP-Jegyzetnek [2445033] további részletekért.  
    A probléma egy új támogatási csomag/patch SAP-LaMa javítja.  
    Állítsa be a profil paraméter OS_UNICODE = uc az alapértelmezett profilban a SAP-rendszer a probléma megoldásához.

* Hiba történt a végrehajtása SAPinst. lépés: dCheckGivenServer
  * Hiba történt a végrehajtása SAPinst. lépés: dCheckGivenServer "verzió ="1.0"hiba: (utolsó lépés jelentett hiba: \<p > telepítés a felhasználó megszakította. \</p >
  * Megoldás  
    Győződjön meg arról, hogy SWPM fut-e egy felhasználóval, amely hozzáfér a profilt. Ez a felhasználó az alkalmazáskiszolgáló telepítése varázslóban konfigurálható

* Hiba történt a végrehajtása SAPinst. lépés: checkClient
  * Hiba történt a végrehajtása SAPinst. lépés: checkClient "verzió ="1.0"hiba: (utolsó lépés jelentett hiba: \<p > telepítés a felhasználó megszakította. \</p >)
  * Megoldás  
    Győződjön meg arról, hogy az SQL Serverhez készült Microsoft ODBC-illesztőprogram telepítve van-e a virtuális gépen, amelyen szeretné telepíteni az alkalmazáskiszolgáló

* Hiba történt a végrehajtása SAPinst. lépés: copyScripts
  * Utolsó lépés jelentett hiba: nem sikerült a rendszerhívás. Részletek: 13 (0x0000000d) hiba (engedély megtagadva) végrehajtása során a rendszer hívása "fopenU" paraméterrel (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd w), sor (494) fájl (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/ src/syslib/FileSystem/syxxcfstrm2.cpp), trasování zásobníku:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring konstans & név, args_t konstans & args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (konstans args_t & _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (konstans CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Megoldás  
    Győződjön meg arról, hogy SWPM fut-e egy felhasználóval, amely hozzáfér a profilt. Ez a felhasználó az alkalmazáskiszolgáló telepítése varázslóban konfigurálható

* Hiba történt a végrehajtása SAPinst. lépés: askPasswords
  * Utolsó lépés jelentett hiba: nem sikerült a rendszerhívás. Részletek: Hiba: 5 (0x00000005) (a hozzáférés megtagadva.) végrehajtása során a rendszer hívást "NetValidatePasswordPolicy" paraméterrel (...), sor (359) fájl (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), trasování zásobníku:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring konstans & név, args_t konstans & args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (konstans args_t & _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,***) konstans)
  * Megoldás  
    Adjon hozzá egy gazdagépet a szabály lépésben *elkülönítési* engedélyezi a kommunikációt a virtuális gépről a tartományvezérlő

## <a name="next-steps"></a>További lépések
* [SAP HANA az Azure üzemeltetési útmutató][hana-ops-guide]
* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]