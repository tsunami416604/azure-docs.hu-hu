---
title: Az Azure Data Factory adatmozgatásának biztonsági szempontjai
description: Ismerje meg az adatok mozgatását az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130841"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – Biztonsági szempontok az adatok mozgatásához

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Data Factory adatmozgással kapcsolatos biztonsági szempontjait.](../data-movement-security-considerations.md)

## <a name="introduction"></a>Bevezetés
Ez a cikk ismerteti az alapvető biztonsági infrastruktúra, amely az adatok áthelyezése szolgáltatások az Azure Data Factory az adatok védelmére. Az Azure Data Factory felügyeleti erőforrásai az Azure biztonsági infrastruktúrájára épülnek, és az Azure által kínált összes lehetséges biztonsági intézkedést használják.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](data-factory-create-pipelines.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok abban a régióban vannak, ahol az adatgyár at létrehozták. 

Annak ellenére, hogy a Data Factory csak **az USA nyugati régiójában**, **az USA keleti régiójában**és **Észak-Európában** érhető el, az adatmozgási szolgáltatás [világszerte számos régióban](data-factory-data-movement-activities.md#global)elérhető . A Data Factory szolgáltatás biztosítja, hogy az adatok nem hagynak el földrajzi területet/régiót, kivéve, ha kifejezetten utasítja a szolgáltatást egy alternatív régió használatára, ha az adatmozgatási szolgáltatás még nincs telepítve az adott régióban. 

Az Azure Data Factory maga nem tárol semmilyen adatot, kivéve a felhőbeli adattárak csatolt szolgáltatáshitelesítő adatait, amelyek tanúsítványok használatával vannak titkosítva. Lehetővé teszi, hogy adatközpontú munkafolyamatokat hozzon létre az adatok [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti mozgásának és az adatok feldolgozásának koordinálására más régiókban vagy helyszíni környezetben [lévő számítási szolgáltatások](data-factory-compute-linked-services.md) használatával. Azt is lehetővé teszi, hogy [a munkafolyamatok programozási és felhasználói felületi mechanizmusokkal történő figyelése](data-factory-monitor-manage-pipelines.md) és kezelése is lehetővé teszi.

Az Azure Data Factory használatával történő adatmozgás a következő **minősítéssel rendelkezik:**
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA CSILLAG](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Ha érdekli az Azure-megfelelőség, és hogy az Azure hogyan biztosítja saját infrastruktúráját, látogasson el a [Microsoft Adatvédelmi központba.](https://microsoft.com/en-us/trustcenter/default.aspx) 

Ebben a cikkben a következő két adatáthelyezési forgatókönyv biztonsági szempontjait tekintjük át: 

- **Felhőbeli forgatókönyv**– Ebben a forgatókönyvben a forrás és a cél is nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak a felügyelt felhőalapú tárolási szolgáltatások, például az Azure Storage, az Azure SQL Data Warehouse, az Azure DATA Database, az Azure Data Lake Store, az Amazon S3, az Amazon Redshift, a SaaS-szolgáltatások, például a Salesforce, valamint az olyan webes protokollok, mint az FTP és az OData. A támogatott adatforrások teljes listáját [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats)találja.
- **Hibrid forgatókönyv**– Ebben a forgatókönyvben a forrás vagy a cél egy tűzfal mögött vagy egy helyszíni vállalati hálózaton belül van, vagy az adattár egy magánhálózatban/ virtuális hálózatban (leggyakrabban a forrásban) található, és nem nyilvánosan elérhető. A virtuális gépeken tárolt adatbázis-kiszolgálók is ebbe a forgatókönyvbe tartoznak.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek
### <a name="securing-data-store-credentials"></a>Adattár hitelesítő adatainak védelme
Az Azure Data Factory a Microsoft **encrypting** **által kezelt tanúsítványok**használatával védi az adattár hitelesítő adatait. Ezeket a tanúsítványokat **kétévente** váltják fel (amely magában foglalja a tanúsítvány megújítását és a hitelesítő adatok áttelepítését). Ezek a titkosított hitelesítő adatok biztonságosan tárolódnak az **Azure Data Factory felügyeleti szolgáltatásai által kezelt Azure Storage-ban.** Az Azure Storage biztonságával kapcsolatos további információkért tekintse meg az [Azure Storage biztonsági áttekintését.](../../security/fundamentals/storage-overview.md)

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhőbeli adattár támogatja a HTTPS vagy TLS, minden adatátvitel között adatmozgatási szolgáltatások Data Factory és a felhőbeli adattár biztonságos csatornán keresztül HTTPS vagy TLS.

> [!NOTE]
> Az **Azure SQL Database** és az Azure SQL Data **Warehouse** minden kapcsolata mindig titkosítást (SSL/TLS) igényel, miközben az adatok az adatbázisba és az adatbázisból vannak átmenők. JSON-szerkesztővel történő folyamat létrehozása közben adja hozzá a **titkosítási** tulajdonságot, és állítsa **igaz** értékre a **kapcsolati karakterláncban.** A Másolás [varázsló](data-factory-azure-copy-wizard.md)használatakor a varázsló alapértelmezés szerint beállítja ezt a tulajdonságot. Az **Azure Storage**használatával **HTTPS** a kapcsolati karakterlánc.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adattárolók támogatják az inaktív adatok titkosítását. Javasoljuk, hogy engedélyezze az adattárolók adattitkosítási mechanizmusát. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Az Azure SQL Data Warehouse transzparens adattitkosítása (TDE) valós idejű titkosításés az adatok inaktív visszafejtése által segít a rosszindulatú tevékenységek fenyegetése elleni védelemben. Ez a viselkedés az ügyfél számára transzparens. További információt az Adatbázis védelme az SQL Data Warehouseban című [témakörben talál.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database is támogatja az átlátható adattitkosítás (TDE), amely segít a rosszindulatú tevékenységek fenyegetése elleni védelemben azáltal, hogy valós idejű titkosítást és visszafejtést hajt végre az adatok anélkül, hogy az alkalmazás módosításai. Ez a viselkedés az ügyfél számára transzparens. További információ: [Transparent Data Encryption with Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Az Azure Data Lake áruház titkosítást is biztosít a fiókban tárolt adatokhoz. Ha engedélyezve van, a Data Lake-tároló automatikusan titkosítja az adatokat, mielőtt megmaradna, és visszafejti a lekérés előtt, így az adatokhoz hozzáférő ügyfél számára transzparens. További információ: [Biztonság az Azure Data Lake Store-ban.](../../data-lake-store/data-lake-store-security-overview.md) 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage és Azure Table Storage
Az Azure Blob Storage és az Azure Table storage támogatja a storage service encryption (SSE), amely automatikusan titkosítja az adatokat, mielőtt megmarad a tárolás és visszafejtés előtt visszafejti. További információ: [Azure Storage Service Encryption for Data at In.](../../storage/common/storage-service-encryption.md)

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 támogatja az adatok ügyfél- és kiszolgálótitkosítását a Rest-ben. További információt az [Adatok védelme titkosítással című témakörben](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)talál. Jelenleg a Data Factory nem támogatja az Amazon S3-ot egy virtuális magánfelhőn (VPC) belül.

#### <a name="amazon-redshift"></a>Amazon Redshift
Az Amazon Redshift támogatja az inaktív adatok fürttitkosítását. További információ: [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Jelenleg a Data Factory nem támogatja az Amazon Redshift-et egy VPC-n belül. 

#### <a name="salesforce"></a>Salesforce
A Salesforce támogatja a Shield Platform Encryption titkosítását, amely lehetővé teszi az összes fájl, melléklet, egyéni mező titkosítását. További információt [a Webkiszolgáló OAuth hitelesítési folyamatának ismertetése című témakörben talál.](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hibrid forgatókönyvek (adatkezelési átjáró használatával)
A hibrid forgatókönyvek használatához az adatkezelési átjárót egy helyszíni hálózatban vagy egy virtuális hálózaton (Azure) vagy egy virtuális magánfelhőn (Amazon) kell telepíteni. Az átjárónak hozzá kell tudnia férni a helyi adattárakhoz. Az átjáróról további információt az [Adatkezelési átjáró című témakörben talál.](data-factory-data-management-gateway.md) 

![Adatkezelési átjáró csatornák](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

A **parancscsatorna** lehetővé teszi a adatátviteli szolgáltatások közötti kommunikációt a Data Factory és az Adatkezelési átjáró között. A közlemény a tevékenységgel kapcsolatos információkat tartalmaz. Az adatcsatorna a helyszíni adattárak és a felhőbeli adattárak közötti adatátvitelre szolgál.    

### <a name="on-premises-data-store-credentials"></a>Helyszíni adattár hitelesítő adatai
A helyszíni adattárak hitelesítő adatait helyileg tárolja (nem a felhőben). Ezek három különböző módon állíthatók be. 

- Egyszerű szöveges (kevésbé biztonságos) **https-en** keresztül az Azure Portal/ Copy wizard használatával. A hitelesítő adatok egyszerű szöveges módon kerülnek átadásra a helyszíni átjárónak.
- **JavaScript-titkosítási függvénytár**használata a Másolás varázslóból .
- Kattintásalapú **hitelesítő adatok kezelője alkalmazás**használata. A kattintás egyszeri alkalmazás végrehajtása a helyszíni gépen, amely hozzáfér az átjáróhoz, és beállítja az adattár hitelesítő adatait. Ez a lehetőség és a következő a legbiztonságosabb lehetőség. A hitelesítő adatok kezelője alkalmazás alapértelmezés szerint a 8050-es portot használja a számítógépen átjáróval a biztonságos kommunikációhoz.  
- A hitelesítő adatok titkosításához használja a [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell-parancsmast. A parancsmag azt a tanúsítványt használja, amelyet az átjáró a hitelesítő adatok titkosítására konfigurált. Használhatja a parancsmag által visszaadott titkosított hitelesítő adatokat, és hozzáadhatja azt a **connectionString** **titkosítotthitelesítő** eleméhez a [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) parancsmaggal vagy a portál Adatgyári szerkesztőjében található JSON-kódrészletben használt JSON-fájlban. Ez a beállítás és a kattintás-egyszer alkalmazás a legbiztonságosabb lehetőségeket. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript-titkosítási függvénytár-alapú titkosítás
Az adattár hitelesítő adatait a [Másolás varázsló](data-factory-copy-wizard.md) [JavaScript-titkosítási könyvtárával](https://www.microsoft.com/download/details.aspx?id=52439) titkosíthatja. Ha ezt a beállítást választja, a Másolás varázsló lekéri az átjáró nyilvános kulcsát, és azt használja az adattár hitelesítő adatainak titkosítására. A hitelesítő adatokat az átjárógép visszafejti és a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)védi.

**Támogatott böngészők:** IE8, IE9, IE10, IE11, Microsoft Edge, és a legújabb Firefox, Chrome, Opera, Safari böngészők. 

#### <a name="click-once-credentials-manager-app"></a>Kattintási hitelesítő adatok kezelője alkalmazás
Elindíthatja az egyszer használható hitelesítő adatok kezelője alkalmazást az Azure Portal/Copy Wizard alkalmazásból a folyamatok létrehozásakor. Ez az alkalmazás biztosítja, hogy a hitelesítő adatok nem kerülnek át egyszerű szöveget a vezetéken keresztül. Alapértelmezés szerint a **8050-es** portot használja a számítógépen átjáróval a biztonságos kommunikációhoz. Szükség esetén ez a port módosítható.  
  
![HTTPS-port az átjáróhoz](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Az Adatkezelési átjáró jelenleg egyetlen **tanúsítványt**használ. Ez a tanúsítvány az átjáró telepítése során jön létre (a 2016 novembere után létrehozott adatkezelési átjáróra és a 2.4.xxxx.x vagy újabb verzióra vonatkozik). Ezt a tanúsítványt lecserélheti saját SSL/TLS tanúsítványára. Ezt a tanúsítványt a kattintásos hitelesítő adatok kezelője használja az adattároló hitelesítő adatainak biztonságos csatlakoztatásához az átjárógéphez. Az adattár hitelesítő adatait biztonságosan tárolja a helyszínen a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) használatával a számítógépen az átjáróval. 

> [!NOTE]
> A 2016 novembere előtt telepített vagy a 2.3.xxxx.x verziójú régebbi átjárók továbbra is a felhőben titkosított és tárolt hitelesítő adatokat használják. Még akkor is, ha frissíti az átjárót a legújabb verzióra, a hitelesítő adatok nem kerülnek áttelepítésre egy helyszíni gépre    
  
| Átjáró verziója (létrehozás közben) | Tárolt hitelesítő adatok | Hitelesítő adatok titkosítása/biztonsága | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Felhőben | Titkosított tanúsítvány használatával (eltér a Hitelesítő adatkezelő alkalmazás által használttól) | 
| > = 2.4.xxxx.x | A helyszínen | DPAPI-n keresztül biztosítva | 
  

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Minden adatátvitel biztonságos csatorna **HTTPS** és **TLS TCP-n keresztül,** hogy megakadályozzák a man-in-the-middle támadások kommunikáció során az Azure-szolgáltatásokkal.
 
[Az IPSec VPN-t](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [az Express Route-ot](../../expressroute/expressroute-introduction.md) is használhatja a helyszíni hálózat és az Azure közötti kommunikációs csatorna további védelméhez.

A virtuális hálózat a hálózat logikai ábrázolása a felhőben. Az IPSec VPN (helyek közötti) vagy az Express Route (privát társviszony-létesítés) beállításával helyszíni hálózatot csatlakoztathat az Azure virtuális hálózatához (VNet)     

Az alábbi táblázat a hálózati és az átjáró konfigurációs javaslatait foglalja össze a hibrid adatmozgatás forrás- és célhelyeinek különböző kombinációi alapján.

| Forrás | Cél | Hálózati konfiguráció | Átjáró beállítása |
| ------ | ----------- | --------------------- | ------------- | 
| Helyszíni követelmények | Virtuális hálózatokban telepített virtuális gépek és felhőszolgáltatások | IPSec VPN (pont-hely vagy hely közötti) | Az átjáró telepíthető a helyszínen vagy egy Azure virtuális gépen (VM) a virtuális hálózatban | 
| Helyszíni követelmények | Virtuális hálózatokban telepített virtuális gépek és felhőszolgáltatások | ExpressRoute (privát társviszony-létesítés) | Az átjáró telepíthető a helyszínen vagy egy Azure virtuális gépre a virtuális hálózatban | 
| Helyszíni követelmények | Nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (nyilvános társviszony-létesítés) | Az átjárót a helyszínen kell telepíteni | 

Az alábbi képek az adatkezelési átjáró használatát mutatják a helyszíni adatbázis és az Express route és IPSec VPN (virtuális hálózattal rendelkező) IPSec VPN használatával az Azure-szolgáltatások közötti adatáthelyezéshez:

**Expressz útvonal:**
 
![Expressz útvonal használata átjáróval](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN átjáróval](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Tűzfal-konfigurációk és az átjáró engedélyezési IP-címe

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyszíni/magánhálózat tűzfalkövetelményei  
Egy vállalatnál **a vállalati tűzfal** a szervezet központi útválasztóján fut. A **Windows tűzfal** démonként fut azon a helyi számítógépen, amelyen az átjáró telepítve van. 

Az alábbi táblázat a **vállalati tűzfal** **kimenő port-** és tartománykövetelményeit tartalmazza.

| Tartománynevek | Kimenő portok | Leírás |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Az átjáró által a Data Factory adatmozgatási szolgáltatásaihoz való csatlakozáshoz szükséges |
| `*.core.windows.net` | 443 | Az átjáró az Azure Storage-fiókhoz való csatlakozáshoz használható a [szakaszos másolási](data-factory-copy-activity-performance.md#staged-copy) funkció használatakor. | 
| `*.frontend.clouddatahub.net` | 443 | Az átjáró az Azure Data Factory szolgáltatáshoz való csatlakozáshoz szükséges. | 
| `*.database.windows.net` | 1433   | (OPTIONAL) szükséges, ha a cél az Azure SQL Database/ Azure SQL Data Warehouse. A szakaszos másolási funkcióval adatokat másolhat az Azure SQL Database/Azure SQL Data Warehouse szolgáltatásba az 1433-as port megnyitása nélkül. | 
| `*.azuredatalakestore.net` | 443 | (OPTIONAL) szükséges, ha az úti cél az Azure Data Lake áruház | 

> [!NOTE] 
> Előfordulhat, hogy a portokat/engedélyezési tartományokat a megfelelő adatforrások által megkövetelt vállalati tűzfalszinten kell kezelnie. Ez a tábla csak az Azure SQL Database, az Azure SQL Data Warehouse, az Azure Data Lake Store példákat használja.   

Az alábbi táblázat a Windows **tűzfal** **bejövő portkövetelményeit** tartalmazza.

| Bejövő portok | Leírás | 
| ------------- | ----------- | 
| 8050 (TCP) | A hitelesítő adatok kezelője által igényelt, az átjárón lévő helyszíni adattárolók hitelesítő adatainak biztonságos beállításához szükséges. | 

![Átjáróport követelményei](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurációk/ engedélyezési lista az adattárban
Egyes adattárak a felhőben is szükség engedélyezési IP-cím a gép hozzáférését. Győződjön meg arról, hogy az átjárógép IP-címe megfelelően van konfigurálva/ konfigurálva a tűzfalon.

A következő felhőalapú adattárak az átjárógép IP-címének engedélyezési listáját igénylik. Előfordulhat, hogy ezek közül az adattárak közül néhány nem igényli az IP-cím engedélyezési listáját. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL adattárház](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés:** Megosztható-e az átjáró a különböző adatgyárak között?
**Válasz:** Még nem támogatjuk ezt a funkciót. Már dolgozunk rajta.

**Kérdés:** Milyen portkövetelmények vonatkoznak az átjáró működésére?
**Válasz:** Az átjáró HTTP-alapú kapcsolatokat létesít a nyílt internethez. A **443-as és 80-as kimenő portokat** meg kell nyitni az átjáró számára a kapcsolat létrejöttéhez. Nyissa meg **a 8050-es bejövő portot** csak a számítógép szintjén (nem vállalati tűzfal szinten) a Credential Manager alkalmazáshoz. Ha az Azure SQL Database vagy az Azure SQL Data Warehouse forrásként/célként használatos, akkor az **1433-as** portot is meg kell nyitnia. További információt a [Tűzfal konfigurációk és az IP-címek engedélyezési listázása](#firewall-configurations-and-whitelisting-ip-address-of gateway) című szakaszban talál. 

**Kérdés:** Mik a gateway tanúsítványkövetelményei?
**Válasz:** A jelenlegi átjáróhoz olyan tanúsítványszükséges, amelyet a hitelesítőadatok-kezelő alkalmazás használ az adattár hitelesítő adatainak biztonságos beállításához. Ez a tanúsítvány az átjáró telepítője által létrehozott és konfigurált önaláírt tanúsítvány. Használhatja helyette saját TLS/SSL tanúsítványát. További információt az [egyszeri hitelesítő adatok kezelője alkalmazásszakaszban](#click-once-credentials-manager-app) talál. 

## <a name="next-steps"></a>További lépések
A másolási tevékenység teljesítményéről a [Tevékenység teljesítményének másolása és finomhangolása című témakörben talál további információt.](data-factory-copy-activity-performance.md)

 
