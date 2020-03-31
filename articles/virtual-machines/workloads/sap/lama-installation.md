---
title: SAP LaMa-összekötő az Azure-hoz | Microsoft dokumentumok
description: SAP-rendszerek kezelése az Azure-ban az SAP LaMa használatával
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293950"
---
# <a name="sap-lama-connector-for-azure"></a>Az Azure SAP LaMa összekötője

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Általános támogatási nyilatkozat: Mindig nyisson meg egy incidenst az SAP-val a BC-VCM-LVM-HYPERV összetevőn, ha az SAP LaMa vagy az Azure-összekötő támogatására van szüksége.

Az SAP LaMa-t számos ügyfél használja az SAP-környezet működtetéséhez és figyeléséhez. Az SAP LaMa 3.0 SP05 óta alapértelmezés szerint egy összekötővel szállítja az Azure-t. Ezzel az összekötővel felszabadíthatja és elindíthatja a virtuális gépeket, átmásolhatja és áthelyezheti a felügyelt lemezeket, és törölheti a felügyelt lemezeket. Ezekkel az alapvető műveletekkel áthelyezheti, másolhatja, klónozhatja és frissítheti az SAP-rendszereket az SAP LaMa használatával.

Ez az útmutató ismerteti, hogyan állítsa be az Azure-összekötőt az SAP LaMa, hozzon létre virtuális gépeket, amelyek segítségével az adaptív SAP-rendszerek telepítéséhez, és hogyan konfigurálhatja őket.

> [!NOTE]
> Az összekötő csak az SAP LaMa Enterprise Edition

## <a name="resources"></a>Források

A következő SAP-megjegyzések az SAP LaMa azure-beli témaköréhez kapcsolódnak:

| Megjegyzés száma | Cím |
| --- | --- |
| [2343511] |Microsoft Azure-összekötő sap tájrendszerezéshez (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Nagyvállalati kiadás |

Olvassa el az [SAP Súgóportált is az SAP LaMa számára.](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)

## <a name="general-remarks"></a>Általános megjegyzések

* Győződjön meg arról, hogy engedélyezi *az automatikus csatlakoztatási pont létrehozását* a telepítőben -> Settings -> Engine  
  Ha az SAP LaMa az SAP Adaptive Extensions használatával csatlakoztatja a köteteket egy virtuális gépen, a csatlakoztatási pontnak léteznie kell, ha ez a beállítás nincs engedélyezve.

* Használjon külön alhálózatot, és ne használjon dinamikus IP-címeket az IP-címek "lopásának" megakadályozására az új virtuális gépek telepítésekor, és az SAP-példányok nincsenek felkészülve  
  Ha dinamikus IP-címfoglalást használ az alhálózatban, amelyet az SAP LaMa is használ, az SAP-rendszer előkészítése az SAP LaMa-val sikertelen lehet. Ha egy SAP-rendszer nem előkészített, az IP-címek nincsenek lefoglalva, és előfordulhat, hogy más virtuális gépek számára lefoglaljuk.

* Ha felügyelt állomásokra jelentkezik be, ügyeljen arra, hogy a fájlrendszerek leválasztása ne legyen letiltva  
  Ha bejelentkezik egy Linux virtuális gépre, és a munkakönyvtárat egy csatlakoztatási pont könyvtárává módosítja, például /usr/sap/AH1/ASCS00/exe, a kötet nem választható le, és az áthelyezés vagy a kicsomagolás sikertelen.

* Győződjön meg arról, hogy letiltja a CLOUD_NETCONFIG_MANAGE SUSE SLES Linux virtuális gépeken. További részletek: [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Azure-összekötő beállítása az SAP LaMa számára

Az Azure-összekötő az SAP LaMa 3.0 SP05-höz csatlakozik. Javasoljuk, hogy mindig telepítse a legújabb támogatási csomagot és javítást az SAP LaMa 3.0-hoz.

Az Azure-összekötő az Azure Resource Manager API-t használja az Azure-erőforrások kezeléséhez. Az SAP LaMa egy egyszerű szolgáltatás vagy egy felügyelt identitás használatával hitelesítheti az API-t. Ha az SAP LaMa egy Azure-beli virtuális gépen fut, javasoljuk, hogy az [Azure API-hoz való hozzáférés hez való hozzáférés hez](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d)az Azure API-hoz való hozzáférés hez a felügyelt identitás használata című fejezetben leírtak szerint felügyelt identitás használata. Ha egyszerű szolgáltatást szeretne használni, kövesse az Egyszerű szolgáltatás használata az [Azure API-hoz való hozzáférés hez](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e)című fejezetlépéseit.

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Egyszerű szolgáltatás használata az Azure API eléréséhez

Az Azure-összekötő használhatja a Service Principal a Microsoft Azure-on elleni engedélyezéshez. Az alábbi lépésekkel hozzon létre egy egyszerű szolgáltatás az SAP tájrendszer-kezelés (LaMa) hozzon létre.

1. Nyissa meg a következőt: https://portal.azure.com
1. Az Azure Active Directory panel megnyitása
1. Kattintson az alkalmazásregisztrációkra
1. Kattintson az Új regisztráció gombra
1. Adjon meg egy nevet, és kattintson a Regisztráció gombra
1. Válassza ki az új alkalmazást, és kattintson a Tanúsítványok & titkos kulcsok a Beállítások lapon
1. Hozzon létre egy új ügyféltitkot, adjon meg egy leírást egy új kulcshoz, válassza ki, hogy mikor járjon le a titkos kulcs, és kattintson a Mentés gombra
1. Írja le az Érték. A szolgáltatásegyszerű jelszóként szolgál.
1. Írja le az alkalmazásazonosítót. A szolgáltatásegyszerű felhasználóneveként használják.

A szolgáltatásnév alapértelmezés szerint nem rendelkezik az Azure-erőforrások eléréséhez szükséges engedélyekkel. A rendszerhez meg kell adnia a szolgáltatásnévengedélyeket a hozzáféréshez.

1. Nyissa meg a következőt: https://portal.azure.com
1. Az Erőforráscsoportok panel megnyitása
1. A használni kívánt erőforráscsoport kijelölése
1. Kattintson a Hozzáférés-vezérlés (IAM) elemre
1. Kattintson a Szerepkör-hozzárendelés hozzáadása gombra
1. A közreműködő szerepkör kiválasztása
1. Adja meg a fent létrehozott alkalmazás nevét.
1. Kattintson a Save (Mentés) gombra.
1. Ismételje meg a 3–8.

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Felügyelt identitás használata az Azure API eléréséhez

Felügyelt identitás használatához az SAP LaMa-példánynak egy olyan Azure-beli virtuális gépen kell futnia, amely rendelkezik egy rendszerrel vagy felhasználóval hozzárendelt identitással. A felügyelt identitásokról további információt a [Mi az Azure-erőforrások felügyelt identitásai?](../../../active-directory/managed-identities-azure-resources/overview.md) és [konfigurálja az Azure-erőforrások felügyelt identitásait egy virtuális gépen az Azure Portalon.](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

A felügyelt identitás alapértelmezés szerint nem rendelkezik az Azure-erőforrások eléréséhez szükséges engedélyekkel. Meg kell adni a hozzáférést.

1. Nyissa meg a következőt: https://portal.azure.com
1. Az Erőforráscsoportok panel megnyitása
1. A használni kívánt erőforráscsoport kijelölése
1. Kattintson a Hozzáférés-vezérlés (IAM) elemre
1. Kattintson a -> szerepkör-hozzárendelés hozzáadása gombra
1. A közreműködő szerepkör kiválasztása
1. Válassza a "Virtuális gép" lehetőséget a "Hozzáférés hozzárendelése"
1. Válassza ki azt a virtuális gépet, amelyen az SAP LaMa-példány fut
1. Kattintson a Save (Mentés) gombra.
1. Ismételje meg a lépéseket az SAP LaMa-ban használni kívánt összes erőforráscsoporthoz

Az SAP LaMa Azure-összekötő konfigurációjában válassza a "Felügyelt identitás használata" lehetőséget a felügyelt identitás használatának engedélyezéséhez. Ha rendszerhez rendelt identitást szeretne használni, hagyja üresen a Felhasználónév mezőt. Ha felhasználóhoz rendelt identitást szeretne használni, írja be a felhasználó hozrendelt identitásazonosítót a Felhasználónév mezőbe.

### <a name="create-a-new-connector-in-sap-lama"></a>Új összekötő létrehozása az SAP LaMa-ban

Nyissa meg az SAP LaMa webhelyet, és keresse meg az infrastruktúrát. Lépjen a Felhőkezelők lapra, és kattintson a Hozzáadás gombra. Válassza ki a Microsoft Azure felhőadaptert, és kattintson a Tovább gombra. Adja meg a következő információkat:

* Címke: Válasszon nevet az összekötő példány
* Felhasználónév: Egyszerű szolgáltatásalkalmazás-azonosító vagy a virtuális gép felhasználó által hozzárendelt identitásának azonosítója. További információ a [Rendszer vagy felhasználó által rendelt identitás használata] című témakörben
* Jelszó: Egyszerű szolgáltatáskulcs/jelszó. Ezt a mezőt üresen hagyhatja, ha rendszerhez vagy felhasználóhoz rendelt identitást használ.
* URL: Az alapértelmezett megtartás`https://management.azure.com/`
* Megfigyelési időköz (másodperc): Legalább 300-nak kell lennie
* Felügyelt identitás használata: Az SAP LaMa egy rendszer vagy a felhasználó által hozzárendelt identitás t az Azure API-n keresztül hitelesítheti. Lásd: [Felügyelt identitás használata az Azure API-hoz való hozzáférés ebben az](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) útmutatóban.
* Előfizetésazonosító: Azure-előfizetés azonosítója
* Azure Active Directory-bérlői azonosító: az Active Directory-bérlő azonosítója
* Proxy host: A proxy állomásneve, ha az SAP LaMa-nak proxyra van szüksége az internethez való csatlakozáshoz
* Proxyport: A proxy TCP-portja
* A tárolási típus módosítása a költségek megtakarításához: Engedélyezze ezt a beállítást, ha az Azure Adapternek módosítania kell a felügyelt lemezek tárolási típusát, hogy költségeket takarítson meg, ha a lemezek nincsenek használatban. Az SAP-példánykonfigurációban hivatkozott adatlemezek esetében az adapter a lemez típusát normál tárolóra módosítja egy példány előkészítése során, majd egy példány előkészítése során vissza az eredeti tárolótípusra. Ha leállít egy virtuális gépet az SAP LaMa-ban, az adapter megváltoztatja az összes csatlakoztatott lemez tárolási típusát, beleértve az operációsrendszer-lemezt is, a Standard Storage szolgáltatásra. Ha virtuális gépet indít az SAP LaMa-ban, az adapter visszaváltoztatja a tárolási típust az eredeti tárolási típusra.

A bevitel érvényesítéséhez kattintson a Konfiguráció tesztelése gombra. Látnia kell, hogy

A kapcsolat sikeres volt: A Microsoft felhővel való kapcsolat sikeres volt. 7 erőforráscsoport található (csak 10 csoport kért)

a honlap alján.

## <a name="provision-a-new-adaptive-sap-system"></a>Új adaptív SAP-rendszer kiépítése

Manuálisan telepíthet egy új virtuális gépet, vagy használhatja az Azure-sablonok egyikét a [rövid útmutató tárházban.](https://github.com/Azure/azure-quickstart-templates) Az [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [AZ SAP NetWeaver alkalmazáskiszolgálók](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)és az [adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database)sablonjait tartalmazza. Ezeket a sablonokat is használhatja új állomások kiépítéséhez a rendszer másolása/klónozása stb.

Azt javasoljuk, hogy az SAP LaMa-val kezelni kívánt összes virtuális géphez használjon külön alhálózatot, és ne használjon dinamikus IP-címeket az IP-címek "lopásának" megakadályozására az új virtuális gépek telepítésekor, és az SAP-példányok nincsenek felkészülve.

> [!NOTE]
> Ha lehetséges, távolítsa el az összes virtuálisgép-bővítményt, mivel azok hosszú futási időt okozhatnak a lemezek virtuális gépről való leválasztásához.

Győződjön meg \<arról, hogy a \<felhasználó hanasid>adm, sapsid>adm és csoportos sapsys létezik a célgépen az azonos azonosítóés gid vagy használja LDAP. Engedélyezze és indítsa el az NFS-kiszolgálót azSAP NetWeaver (A)SCS futtatásához használandó virtuális gépeken.

### <a name="manual-deployment"></a>Kézi üzembe helyezés

Az SAP LaMa kommunikál a virtuális géppel az SAP gazdaügynök használatával. Ha a virtuális gépeket manuálisan telepíti, vagy nem használja az Azure Resource Manager sablont a gyorsútmutató tárházból, győződjön meg arról, hogy telepítse a legújabb SAP-ügynök és az SAP Adaptive Extensions. Az Azure-hoz szükséges javítási szintekről az SAP [2343511]megjegyzésében talál további információt.

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Linux os virtuális gép manuális üzembe helyezése

Hozzon létre egy új virtuális gépet az SAP [2343511.] Adjon hozzá további IP-konfigurációkat az SAP-példányokhoz. Minden példánynak legalább az IP-címen szüksége van, és virtuális állomásnév használatával kell telepíteni.

Az SAP NetWeaver ASCS-példánynak lemezekre van\<szüksége a /sapmnt/ SAPSID>, /usr/sap/\<SAPSID>, /usr/sap/trans és /usr/sap/\<sapsid>adm számára. Az SAP NetWeaver alkalmazáskiszolgálóknak nincs szükségük további lemezekre. Az SAP-példányhoz kapcsolódó minden kapcsolatot az ASCS-en kell tárolni, és NFS-en keresztül kell exportálni. Ellenkező esetben jelenleg nem lehet további alkalmazáskiszolgálókat hozzáadni az SAP LaMa használatával.

![SAP NetWeaver ASCS Linuxon](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Az SAP HANA manuális üzembe helyezése

Hozzon létre egy új virtuális gépet az SAP HANA egyik támogatott operációs rendszerével az SAP [2343511]megjegyzésében felsoroltak szerint. Adjon hozzá egy további IP-konfigurációt az SAP HANA és egy HANA-bérlőhöz.

Az SAP HANA lemezeket igényel a /hana/shared, /hana/backup, /hana/data és /hana/log

![SAP HANA Linuxon](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuális üzembe helyezés az Oracle Database linuxos adatbázisához

Hozzon létre egy új virtuális gépet az SAP [2343511]megjegyzésében felsorolt Oracle-adatbázisok egyik támogatott operációs rendszerével. Adjon hozzá egy további IP-konfigurációt az Oracle adatbázishoz.

Az Oracle adatbázisnak lemezekre van szüksége az /oracle, /home/oraod1 és /home/oracle

![Oracle adatbázis Linuxon](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>A Microsoft SQL Server manuális telepítése

Hozzon létre egy új virtuális gépet a Microsoft SQL Server egyik támogatott operációs rendszerével az SAP [2343511]megjegyzésében felsoroltak szerint. Adjon hozzá egy további IP-konfigurációt az SQL Server-példányhoz.

Az SQL Server adatbázis-kiszolgálónak lemezekre van szüksége az adatbázis-adatokhoz, valamint a c:\usr\sap adatbázis-adatokhoz és naplófájlokhoz.

![Oracle adatbázis Linuxon](media/lama/sap-lama-db-sql.png)

Győződjön meg arról, hogy egy támogatott Microsoft ODBC illesztőprogramot telepít az SQL Server-hez egy olyan virtuális gépen, amelyet az SAP NetWeaver alkalmazáskiszolgáló áthelyezéséhez vagy rendszermásolat/klónozási célként kíván használni.

Az SAP LaMa nem tudja áthelyezni magát az SQL Servert, így elő kell telepíteni e virtuális gépet, amelyet egy adatbázispéldány áthelyezéséhez vagy rendszermásolási/klónozási célként kíván használni.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Virtuális gép üzembe helyezése Azure-sablon használatával

Töltse le a következő legújabb elérhető archívumokat az [SAP Software Marketplace-ről](https://support.sap.com/swdc) a virtuális gépek operációs rendszeréhez:

1. SAPCAR 7,21
1. SAP GAZDAÜGYNÖK 7,21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

Töltse le a következő összetevőket is a [Microsoft letöltőközpontjából](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 terjeszthető csomag (x64) (csak Windows)
1. Microsoft ODBC illesztőprogram SQL Server hez (csak SQL Server esetén)

Az összetevők szükségesek a sablon üzembe helyezéséhez. A legegyszerűbb módja annak, hogy elérhetővé a sablon, hogy töltse fel őket egy Azure-tárfiókba, és hozzon létre egy közös hozzáférési aláírás (SAS).

A sablonok a következő paraméterekkel rendelkeznek:

* sapSystemId: Az SAP rendszer azonosítója. A lemezelrendezés létrehozásához szolgál (például /usr/sap/\<sapsid>).

* computerName: Az új virtuális gép számítógépneve. Ezt a paramétert az SAP LaMa is használja. Ha ezt a sablont használja egy új virtuális gép kiépítéséhez egy rendszerpéldány részeként, az SAP LaMa megvárja, amíg az ezzel a számítógépnévvel rendelkező állomás elérhető lesz.

* osType: A telepíteni kívánt operációs rendszer típusa.

* dbtype: Az adatbázis típusa. Ez a paraméter határozza meg, hogy hány további IP-konfigurációt kell hozzáadni, és hogyan kell kinéznie a lemezelrendezésnek.

* sapSystemSize: A telepíteni kívánt SAP-rendszer mérete. A virtuálisgép-példány típusának és méretének meghatározására szolgál.

* adminUsername: A virtuális gép felhasználóneve.

* adminPassword: Jelszó a virtuális géphez. Az SSH-hoz nyilvános kulcsot is megadhat.

* sshKeyData: Nyilvános SSH kulcs a virtuális gépekhez. Csak Linux operációs rendszerekesetén támogatott.

* alhálózati azonosító: A használni kívánt alhálózat azonosítója.

* deployEmptyTarget: Egy üres célt telepíthet, ha a virtuális gépet egy példány áthelyezése vagy hasonló célként szeretné használni. Ebben az esetben nincs további lemez vagy IP-konfiguráció csatlakoztatva.

* sapcarLocation: A sapcar alkalmazás helye, amely megfelel a telepített operációs rendszernek. sapcar használják kivonat az archívumban nyújt más paramétereket.

* sapHostAgentArchiveLocation: Az SAP-állomásügynök archívumának helye. Az SAP gazdaügynök a sablon központi telepítésének részeként van telepítve.

* sapacExtLocation: Az SAP Adaptive Extensions helye. Az SAP [2343511] az Azure-hoz szükséges minimális javítási szintet sorolja fel.

* vcRedistLocation: Az SAP Adaptive Extensions telepítéséhez szükséges VC runtime helye. Ez a paraméter csak a Windows rendszerhez szükséges.

* odbcDriverLocation: A telepíteni kívánt ODBC illesztőprogram helye. Csak az SQL Server microsoft ODBC illesztőprogramja támogatott.

* sapadmPassword: A sapadm felhasználó jelszava.

* sapadmId: A sapadm felhasználó Linux felhasználói azonosítója. Nem szükséges a Windows.

* sapsysGid: A sapsys csoport Linux-csoportazonosítója. Nem szükséges a Windows.

* _artifactsLocation: Az alap URI, ahol a sablon által igényelt összetevők találhatók. Ha a sablon a kísérő parancsfájlok használatával van telepítve, a rendszer egy privát helyet fog használni az előfizetésben, és ezt az értéket a rendszer automatikusan létrehozza. Csak akkor szükséges, ha nem telepíti a sablont a GitHubról.

* _artifactsLocationSasToken: A sasToken eléréséhez szükséges _artifactsLocation. Amikor a sablon a kísérő parancsfájlok használatával van telepítve, a rendszer automatikusan létrehoz egy sasToken-t. Csak akkor szükséges, ha nem telepíti a sablont a GitHubról.

### <a name="sap-hana"></a>SAP HANA

Az alábbi példákban feltételezzük, hogy telepíti az SAP HANA rendszerazonosítóhn1 és az SAP NetWeaver rendszer azonosító AH1 telepítésével. A virtuális állomásnevek hn1-db a HANA-példány, ah1-db az SAP NetWeaver rendszer által használt HANA-bérlő, ah1-ascs az SAP NetWeaver ASCS és ah1-di-0 az első SAP NetWeaver alkalmazáskiszolgáló.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Az SAP NetWeaver ASCS sap HANA telepítése az Azure felügyelt lemezei használatával

Az SAP szoftverkiépítési kezelő (SWPM) megkezdése előtt csatlakoztatnia kell az ASCS virtuális állomásnevének IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

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

Futtassa az SWPM-et, és használja az *ah1-ascs-ot* az *ASCS-példány állomásnevéhez.*

![Linux][Logo_Linux] Linux  
Adja hozzá a következő profilparamétert az SAP host agent profilhoz, amely a /usr/sap/hostctrl/exe/host_profile helyen található. További információ: SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Az SAP NetWeaver ASCS SAP HANA telepítése az Azure NetAppFiles (ANF) BETA programra

> [!NOTE]
> Ez a funkció még nem GA. További információkért lásd az SAP Note [2815988] (csak az ügyfelek előnézetét látható).
Nyisson meg egy SAP-incidenst a BC-VCM-LVM-HYPERV összetevőn, és kérjen engedélyt az Azure NetApp Files előzetes verziójához való LaMa tárolóadapterhez való csatlakozásra

Az ANF NFS-t biztosít az Azure-hoz. Az SAP LaMa környezetében ez leegyszerűsíti az ABAP központi szolgáltatások (ASCS) példányok létrehozását és az alkalmazáskiszolgálók későbbi telepítését. Korábban az ASCS-példánynak NFS-kiszolgálóként is kellett működnie, és az acosprep/nfs_paths paramétert hozzá kellett adni az SAP Hostagent host_profile.

#### <a name="anf-is-currently-available-in-these-regions"></a>Az ANF jelenleg a következő régiókban érhető el:

Kelet-Ausztrália, USA középső része, USA keleti régiója, USA keleti régiója 2, Észak-Európa, USA délnyugati középső régiója, Nyugat-Európa és USA nyugati régiója 2.

#### <a name="network-requirements"></a>Hálózati követelmények

Az ANF-nek delegált alhálózatra van szüksége, amelynek ugyanannak a virtuális hálózatnak a részét kell tennie, mint az SAP-kiszolgálóknak. Íme egy példa egy ilyen konfigurációra.
Ez a képernyő a virtuális hálózat és az első alhálózat létrehozását mutatja:

![Az SAP LaMa virtuális hálózatot hoz létre az Azure ANF számára ](media/lama/sap-lama-createvn-50.png)

A következő lépés létrehozza a Microsoft.NetApp/volumes delegált alhálózatát.

![SAP LaMa delegált alhálózat hozzáadása ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa alhálózatok listája ](media/lama/sap-lama-subnets.png)

Most létre kell hozni egy NetApp-fiókot az Azure Portalon belül:

![SAP LaMa hozzon létre NetApp-fiókot ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp fiók létrehozva ](media/lama/sap-lama-netappaccount.png)

A NetApp-fiókon belül a kapacitáskészlet határozza meg az egyes készletek lemezeinek méretét és típusát:

![Az SAP LaMa létrehozza a NetApp kapacitáskészletét ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp kapacitáskészlet létrehozva ](media/lama/sap-lama-capacitypool-list.png)

Az NFS-kötetek most már definiálhatók. Mivel egy készletben több rendszerkötetek is lesznek, önmagyarázó elnevezési sémát kell választani. A biztonsági azonosító hozzáadása segít a kapcsolódó kötetek csoportosításához. Az ASCS és az AS-példány esetében a következő csatlakoztatásokra van szükség: */sapmnt/\<\>SID*, */usr/sap/\<SID\>* és */home/\<sid\>adm*. Opcionálisan a */usr/sap/trans* kapcsoló szükséges a központi átviteli könyvtárhoz, amelyet legalább egy táj összes rendszere használ.

> [!NOTE]
> A BÉTA fázisban a kötetek nevének egyedinek kell lennie az előfizetésen belül.

![Az SAP LaMa 1 kötetet hoz létre ](media/lama/sap-lama-createvolume-80.png)

![AZ SAP LaMa 2 kötetet hoz létre ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa hozzon létre egy kötetet 3 ](media/lama/sap-lama-createvolume3-80.png)

Ezeket a lépéseket a többi kötetesetében is meg kell ismételni.

![SAP LaMa létrehozott kötetek listája ](media/lama/sap-lama-volumes.png)

Most ezeket a köteteket kell csatlakoztatni a rendszerekhez, ahol a kezdeti telepítés az SAP SWPM kerül végrehajtásra.

Először létre kell hozni a csatlakoztatási pontokat. Ebben az esetben a SID AN1, így a következő parancsokat kell végrehajtani:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Ezután az ANF-kötetek a következő parancsokkal lesznek felszerelve:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
A csatlakoztatási parancsok a portálról is származtathatók. A helyi csatlakoztatási pontokat módosítani kell.

Az ellenőrzéshez használja a df -h parancsot.

![SAP LaMa csatlakoztatási pontok operációs rendszer szinten ](media/lama/sap-lama-mounts.png)

Most az SWPM-mel történő telepítést kell végrehajtani.

Ugyanazokat a lépéseket legalább egy AS-példányesetében kell végrehajtani.

A sikeres telepítés után a rendszert fel kell deríteni az SAP LaMa-n belül.

A csatlakoztatási pontok így kell kinézniük az ASCS és az AS-példány esetében:

![SAP LaMa csatlakoztatási ](media/lama/sap-lama-ascs.png) pontok LaMa (Ez egy példa. Az IP-címek és az exportálási útvonal eltér nek a korábban használt)


#### <a name="install-sap-hana"></a>Az SAP HANA telepítése

Ha az SAP HANA telepítése a hdblcm parancssori eszközzel, használja a --hostname paramétert egy virtuális állomásnév megadásához. Hozzá kell adnia az adatbázis virtuális állomásnevének IP-címét egy hálózati adapterhez. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

Adjon hozzá egy másik virtuális állomásnevet és IP-címet az alkalmazáskiszolgálók által a HANA-bérlőhöz való csatlakozáshoz használt névhez.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Az SWPM adatbázispéldány-telepítését az alkalmazáskiszolgáló virtuális gépen, ne a HANA virtuális gépen futtassa. Használja *az ah1-db-ot* az *Adatbázis-állomáshoz* az *SAP-rendszer párbeszédadatbázisában.*

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Sap NetWeaver application server sap hana telepítésével

Az SAP Szoftverkiépítési Kezelő (SWPM) megkezdése előtt csatlakoztatnia kell az alkalmazáskiszolgáló virtuális állomásnevének IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

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

Javasoljuk, hogy az SAP NetWeaver profil paraméter dbs/hdb/hdb_use_ident használatával állítsa be a kulcs megkereséséhez használt identitást a HDB userstore-ban. Ezt a paramétert manuálisan is hozzáadhatja az adatbázispéldány SWPM-mel való telepítése után, vagy futtathatja az SWPM-et

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Ha manuálisan állítja be, új HDB userstore bejegyzéseket is létre kell hoznia.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Használja *az ah1-di-0-t* a *PAS-példány példánynevéhez* az *elsődleges alkalmazáskiszolgáló-példány*párbeszédpanelen.

#### <a name="post-installation-steps-for-sap-hana"></a>Az SAP HANA telepítés utáni lépései

Győződjön meg arról, hogy biztonsági másolatot készíteni a SYSTEMDB és az összes bérlői adatbázisok, mielőtt megpróbál egy bérlői másolatot, bérlő áthelyezése vagy hozzon létre egy rendszer replikáció.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Az alábbi példákban feltételezzük, hogy telepíti az SAP NetWeaver rendszert az AS1 rendszerazonosítóval. A virtuális állomásnevek as1-db az SAP NetWeaver rendszer által használt SQL Server-példányhoz, az SAP NetWeaver ASCS és az as1-di-0 az első SAP NetWeaver alkalmazáskiszolgálóhoz as1-db.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Az SAP NetWeaver ASCS sql serverhez telepítése

Az SAP szoftverkiépítési kezelő (SWPM) megkezdése előtt csatlakoztatnia kell az ASCS virtuális állomásnevének IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Futtassa az SWPM-et, és használja az *ascs-asc-okat* az *ASCS-példány állomásnevéhez.*

#### <a name="install-sql-server"></a>Az SQL Server telepítése

Hozzá kell adnia az adatbázis virtuális állomásnevének IP-címét egy hálózati adapterhez. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Futtassa az SWPM adatbázispéldány-telepítését az SQL-kiszolgáló virtuális gépen. A SAPINST_USE_HOSTNAME=*as1-db használatával* felülbírálhatja az SQL Server kiszolgálóhoz való csatlakozáshoz használt állomásnevet. Ha a virtuális gépet az Azure Resource Manager sablonhasználatával telepítette, ügyeljen arra, hogy az adatbázis-adatfájlokhoz használt könyvtárat *C:\sql\data* és adatbázisnapló-fájlra állítsa *a C:\sql\log*értékre.

Győződjön meg arról, hogy az *NT AUTHORITY\SYSTEM* felhasználó hozzáfér az SQL Server kiszolgálóhoz, és rendelkezik a *kiszolgálói szerepkörrel.* További információ: SAP Note [1877727] és [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Az SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP Szoftverkiépítési Kezelő (SWPM) megkezdése előtt csatlakoztatnia kell az alkalmazáskiszolgáló virtuális állomásnevének IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet sapacext használatával csatlakoztatja, újraindítás után győződjön meg arról, hogy újracsatlakoztatja az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Használja az *as1-di-0-t* a *PAS-példány nevéhez* az *elsődleges alkalmazáskiszolgáló-példány*párbeszédpanelen.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-and-warnings-during-discover"></a>Hibák és figyelmeztetések a felderítés során

* A SELECT engedély megtagadva
  * [A Microsoft] [ODBC SQL Server illesztőprogram] [SQL Server] A SELECT engedélyt a program megtagadta a "log_shipping_primary_databases", "msdb" adatbázis, "dbo" séma objektumán. [SOAPFaultException]  
  A SELECT engedélyt a program megtagadta a "log_shipping_primary_databases", "msdb" adatbázis, "dbo" séma objektumán.
  * Megoldás  
    Győződjön meg arról, hogy *az NT AUTHORITY\SYSTEM* hozzáfér az SQL Server kiszolgálóhoz. Lásd az SAP [2562184 megjegyzését]


### <a name="errors-and-warnings-for-instance-validation"></a>Hibák és figyelmeztetések a példányérvényesítéshez

* Kivétel merült fel a HDB userstore érvényesítése során  
  * lásd: Naplómegjelenítő  
    com.sap.nw.lm.aci.monitor.api.validation.runtimeValidationException: Kivétel a validatorban a "RuntimeHDBConnectionValidator" azonosítóval (érvényesítés: 'VALIDATION_HDB_USERSTORE'): Nem sikerült beolvasni a hdbuserstore-t  
    A HANA userstore nincs a megfelelő helyen
  * Megoldás  
    Győződjön meg arról, hogy a /usr/sap/AH1/hdbclient/install/installation.ini helyes

### <a name="errors-and-warnings-during-a-system-copy"></a>Hibák és figyelmeztetések a rendszer másolása során

* Hiba történt a rendszerlétesítési lépés érvényesítésekén
  * A következő által okozott: com.sap.nw.lm.aci.motor.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN 1 -f\;50\;-h hn1-db -o szint=0 állapot=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Megoldás  
    A forrás HANA rendszer összes adatbázisának biztonsági mentése

* Az adatbázispéldány Rendszermásoláslépés *kezdete*
  * A '000D3A282BC91EEE8A1D76CF1F92E2944' gazdaügynök-művelet sikertelen (OperationException. FaultCode: '127', Üzenet: 'Parancsvégrehajtás nem sikerült. : [Microsoft][ODBC SQL Server Driver][SQL Server]A felhasználónak nincs engedélye az "AS2" adatbázis módosítására, az adatbázis nem létezik, vagy az adatbázis nem olyan állapotban van, amely lehetővé teszi a hozzáférés ellenőrzését.')
  * Megoldás  
    Győződjön meg arról, hogy *az NT AUTHORITY\SYSTEM* hozzáfér az SQL Server kiszolgálóhoz. Lásd az SAP [2562184 megjegyzését]

### <a name="errors-and-warnings-during-a-system-clone"></a>Hibák és figyelmeztetések a rendszerklónozás során

* Hiba történt, amikor megpróbálta regisztrálni a példányügynököt az alkalmazáskiszolgáló vagy az ASCS *kényszerített regisztrálása és indítási példányügynöke* lépésben
  * Hiba történt a példányügynök regisztrálása közben. (RemoteException: 'Nem sikerült betölteni a\\példányadatokat a profilból ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Nem lehet hozzáférni a profil '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Nincs ilyen fájl vagy könyvtár.')
  * Megoldás  
   Győződjön meg arról, hogy az ASCS/SCS sapmnt megosztása teljes hozzáféréssel rendelkezik a SAP_AS1_GlobalAdmin

* Hiba a lépésben *Indítási védelem engedélyezése klónozáshoz*
  * Nem sikerült megnyitni\\a fájl ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' fájlmegnyitását, mert: Nincs ilyen fájl vagy könyvtár
  * Megoldás  
    Az alkalmazáskiszolgáló számítógépfiókjának írási hozzáféréssel kell rendelkeznie a profilhoz

### <a name="errors-and-warnings-during-create-system-replication"></a>Hibák és figyelmeztetések a rendszerreplikáció létrehozása során

* Kivétel a Rendszerreplikáció létrehozása gombra kattintva
  * A következő által okozott: com.sap.nw.lm.aci.motor.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN 1 -f\;50\;-h hn1-db -o szint=0 állapot=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Megoldás  
    Ellenőrizze, hogy a sapacext>adm-ként `<hanasid` hajtható-e végre

* Hiba, ha a teljes másolás nincs engedélyezve a Tárolási lépésben
  * Hiba történt az IStorageCopyData.storageVolumeCopyList:1 és a targetStorageSystemId mező messageattribútumának jelentésekor
  * Megoldás  
    Figyelmen kívül hagyja a figyelmeztetéseket, és próbálkozzon újra. Ezt a problémát az SAP LaMa új támogatási csomagjában/javításában oldjuk meg.

### <a name="errors-and-warnings-during-relocate"></a>Hibák és figyelmeztetések az áthelyezés során

* Az nfs újrakivitelek esetében a "/usr/sap/AH1" elérési út nem engedélyezett.
  * A részletekért tekintse meg az SAP Note [2628497] megjegyzést.
  * Megoldás  
    AsCS-exportálás hozzáadása az ASCS HostAgent profilhoz. Lásd az SAP Note [2628497 megjegyzést]

* A függvény nincs megvalósítva az ASCS áthelyezésekor
  * Parancskimenet: exportfs: host:/usr/sap/AX1: A függvény nincs megvalósítva
  * Megoldás  
    Győződjön meg arról, hogy az NFS-kiszolgálószolgáltatás engedélyezve van a célvirtuális gép áthelyezése koron

### <a name="errors-and-warnings-during-application-server-installation"></a>Hibák és figyelmeztetések az alkalmazáskiszolgáló telepítése során

* Hiba a SAPinst lépés végrehajtásakor: getProfileDir
  * HIBA: (A lépés által jelentett utolsó hiba: Elkapott ESAPinstException modulhívás: A következő lépés érvényesítője: '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|ind|ind|readProfile|0|getProfileDir' hibát \\jelzett: A Csomópont \as1-ascs\sapmnt\AS1\SYS\profile nem létezik. Indítsa el a SAPinst-ot interaktív módban a probléma megoldásához)
  * Megoldás  
    Győződjön meg arról, hogy az SWPM olyan felhasználóval fut, aki hozzáfér a profilhoz. Ez a felhasználó konfigurálható az Alkalmazáskiszolgáló telepítése varázslóban

* Hiba a SAPinst lépés végrehajtásakor: askUnicode
  * HIBA: (A lépés által jelentett utolsó hiba: Elkapott ESAPinstException modulhívás: A következő lépés érvényesítője: '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' hibaüzenetet jelentett: A SAPinst indítása interaktív módban a probléma megoldásához)
  * Megoldás  
    Ha nemrégiben használt SAP-rendszermagot használ, az SWPM nem tudja megállapítani, hogy a rendszer már unicode rendszer-e az ASCS üzenetkiszolgálójának használatával. További részletek az SAP Note [2445033 megjegyzésében.]  
    Ezt a problémát az SAP LaMa új támogatási csomagjában/javításában oldjuk meg.  
    Állítsa be a profilparaméter OS_UNICODE=uc az SAP-rendszer alapértelmezett profiljában, hogy megkerülje ezt a problémát.

* Hiba a SAPinst lépés végrehajtásakor: dCheckGivenServer
  * Hiba a SAPinst lépés végrehajtásakor: dCheckGivenServer" version="1.0" ERROR: \<(A lépés által jelentett utolsó hiba: p> A telepítést a felhasználó megszakította. \</p>
  * Megoldás  
    Győződjön meg arról, hogy az SWPM olyan felhasználóval fut, aki hozzáfér a profilhoz. Ez a felhasználó konfigurálható az Alkalmazáskiszolgáló telepítése varázslóban

* Hiba a SAPinst lépés végrehajtásakor: checkClient
  * Hiba a SAPinst lépés végrehajtásakor: checkClient" version="1.0" ERROR: \<(A lépés által jelentett utolsó hiba: p> A telepítést a felhasználó megszakította. \</p>)
  * Megoldás  
    Győződjön meg arról, hogy az SQL Server Microsoft ODBC illesztőprogramja telepítve van azon a virtuális gépen, amelyre az alkalmazáskiszolgálót telepíteni szeretné

* Hiba a SAPinst lépés végrehajtásakor: copyScripts
  * A lépés által jelentett utolsó hiba: A rendszerhívás nem sikerült. RÉSZLETEK: Hiba 13 (0x0000000d) (Engedély megtagadva) a rendszer hívás\\"fopenU" végrehajtásában paraméterrel ( \as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), a fájlban lévő sor (\bas/bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), veremnyomkövetés:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& név, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl:open()
  * Megoldás  
    Győződjön meg arról, hogy az SWPM olyan felhasználóval fut, aki hozzáfér a profilhoz. Ez a felhasználó konfigurálható az Alkalmazáskiszolgáló telepítése varázslóban

* Hiba a SAPinst lépés végrehajtásakor: askPasswords
  * A lépés által jelentett utolsó hiba: A rendszerhívás nem sikerült. RÉSZLETEK: 5. hiba (0x00000005) (A hozzáférés megtagadva.) a "NetValidatePasswordPolicy" rendszerhívás végrehajtásakor a fájlban lévő (...), a fájlban lévő (\bas/bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp) paraméterrel), a veremnyomkövetés:  
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
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Megoldás  
    Győződjön meg arról, hogy egy állomásszabályt a *lépésben elkülönítése,* hogy a kommunikáció a virtuális gép a tartományvezérlő

## <a name="next-steps"></a>További lépések
* [SAP HANA az Azure-műveletek útmutatójában][hana-ops-guide]
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
