---
title: Biztonsági szempontok
description: Az azure Data Factory adatmozgatási szolgáltatásai nak az adatok védelmére használt alapvető biztonsági infrastruktúrát ismertetik.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416473"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Az Azure Data Factory adatmozgatásának biztonsági szempontjai
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuális verzió](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti az alapvető biztonsági infrastruktúra, amely az adatok áthelyezése szolgáltatások az Azure Data Factory segítségével az adatok védelme érdekében. A Data Factory felügyeleti erőforrásai az Azure biztonsági infrastruktúrájára épülnek, és az Azure által kínált összes lehetséges biztonsági intézkedést használják.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok abban a régióban vannak, ahol az adatgyár at létrehozták. 

Annak ellenére, hogy a Data Factory csak néhány régióban érhető el, az adatmozgatási szolgáltatás [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location) az adatok megfelelőségének, hatékonyságának és a hálózati kimenő forgalom költségeinek csökkentése érdekében. 

Az Azure Data Factory nem tárol semmilyen adatot, kivéve a felhőbeli adattárak csatolt szolgáltatáshitelesítő adatait, amelyek tanúsítványok használatával vannak titkosítva. A Data Factory segítségével adatvezérelt munkafolyamatokat hozhat létre az adatok [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)közötti mozgásának és az adatok feldolgozásának koordinálására más régiókban vagy helyszíni környezetben [lévő számítási szolgáltatások](compute-linked-services.md) használatával. A munkafolyamatokat sdk-k és az Azure Monitor használatával is figyelheti és kezelheti.

A Data Factory a következőkre rendelkezik:

| **[CSA STAR tanúsítvány](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Ha érdekli az Azure-megfelelőség, és hogy az Azure hogyan biztosítja saját infrastruktúráját, látogasson el a [Microsoft Adatvédelmi központba.](https://microsoft.com/en-us/trustcenter/default.aspx) Az Azure Compliance-ajánlatok legfrissebb listájáért https://aka.ms/AzureComplianceellenőrizze a - .

Ebben a cikkben a következő két adatáthelyezési forgatókönyv biztonsági szempontjait tekintjük át: 

- **Felhőbeli forgatókönyv:** Ebben a forgatókönyvben mind a forrás, mind a cél nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak a felügyelt felhőalapú tárolási szolgáltatások, például az Azure Storage, az Azure SQL Data Warehouse, az Azure SQL Database, az Azure Data Lake Store, az Amazon S3, az Amazon Redshift, a SaaS-szolgáltatások, például a Salesforce, valamint a webes protokollok, például az FTP és az OData. A támogatott adatforrások teljes listájának megkeresése [támogatott adattárakban és formátumokban.](copy-activity-overview.md#supported-data-stores-and-formats)
- **Hibrid forgatókönyv:** Ebben az esetben a forrás vagy a cél tűzfal mögött vagy egy helyszíni vállalati hálózaton belül van. Vagy az adattár magánhálózatban vagy virtuális hálózatban (leggyakrabban a forrásban) található, és nem nyilvánosan hozzáférhető. A virtuális gépeken tárolt adatbázis-kiszolgálók is ebbe a forgatókönyvbe tartoznak.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek

### <a name="securing-data-store-credentials"></a>Adattár hitelesítő adatainak védelme

- **Titkosított hitelesítő adatoktárolása egy Azure Data Factory által kezelt tárolóban.** A Data Factory a Microsoft által kezelt tanúsítványokkal való titkosítással segít megvédeni az adattár hitelesítő adatait. Ezeket a tanúsítványokat kétévente váltják fel (beleértve a tanúsítvány megújítását és a hitelesítő adatok áttelepítését). Az Azure Storage biztonságáról az Azure Storage biztonsági áttekintése című témakörben olvashat [bővebben.](../security/fundamentals/storage-overview.md)
- **Hitelesítő adatok at tárolhatja az Azure Key Vaultban.** Az adattár hitelesítő adatait az [Azure Key Vaultban](https://azure.microsoft.com/services/key-vault/)is tárolhatja. A Data Factory lekéri a hitelesítő adatokat egy tevékenység végrehajtása során. További információ: [Store credential in Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhőbeli adattár támogatja a HTTPS-t vagy a TLS-t, a Data Factory és a felhőalapú adattár adatmozgatási szolgáltatásai közötti összes adatátvitel biztonságos https vagy TLS csatornán keresztül történik.

> [!NOTE]
> Az Azure SQL Database és az Azure SQL Data Warehouse összes kapcsolata titkosítást (SSL/TLS) igényel, miközben az adatok az adatbázisba és az adatbázisból vannak átmenők. Amikor egy folyamatot a JSON használatával hoz létre, adja hozzá a titkosítási tulajdonságot, és állítsa **igaz** értékre a kapcsolati karakterláncban. Az Azure Storage számára **https-t** használhat a kapcsolati karakterláncban.

> [!NOTE]
> Ha engedélyezni szeretné a titkosítást az átvitel során, miközben adatokat helyez át az Oracle-ből, kövesse az alábbi lehetőségek egyikét:
> 1. Az Oracle kiszolgálón keresse fel az Oracle Advanced Security (OAS) webhelyet, és konfigurálja a titkosítási beállításokat, amelyek támogatják a Triple-DES Encryption (3DES) és az Advanced Encryption Standard (AES) titkosítást, olvassa el [a](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) részleteket. Az ADF automatikusan egyezteti a titkosítási módszert, hogy az Oracle-hez való csatlakozás során az OAS-ban konfigurált titkosítási módszert használja.
> 2. Az ADF-ben hozzáadhat encryptionmethod=1-et a kapcsolati karakterlánchoz (a csatolt szolgáltatásban). Ez az SSL/TLS titkosítási módszert fogja használni. Ennek használatához le kell tiltania a nem SSL titkosítási beállításokat az Oracle kiszolgálói oldalán lévő OAS-ban a titkosítási ütközések elkerülése érdekében.

> [!NOTE]
> TLS használt verzió 1.2.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adattárolók támogatják az inaktív adatok titkosítását. Azt javasoljuk, hogy engedélyezze az adattárolók adattitkosítási mechanizmusát. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Az Azure SQL Data Warehouse transzparens adattitkosítása (TDE) valós idejű titkosítással és az adatok inaktív visszafejtésével segít a rosszindulatú tevékenységek fenyegetése elleni védekezésben. Ez a viselkedés az ügyfél számára transzparens. További információt az Adatbázis védelme az SQL Data Warehouseban című [témakörben talál.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database is támogatja az átlátható adattitkosítás (TDE), amely segít a rosszindulatú tevékenységek fenyegetése elleni védelem ben az adatok valós idejű titkosítása és visszafejtése, az alkalmazás módosítása nélkül. Ez a viselkedés az ügyfél számára transzparens. További információ: [Transparent data encryption for SQL Database and Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Az Azure Data Lake Store titkosítást is biztosít a fiókban tárolt adatokhoz. Ha engedélyezve van, a Data Lake Store automatikusan titkosítja az adatokat, mielőtt megmaradna, és visszafejti a lekérés előtt, így az adatokhoz hozzáférő ügyfél számára transzparens. További információ: [Biztonság az Azure Data Lake Store-ban.](../data-lake-store/data-lake-store-security-overview.md) 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob-tároló és Azure Table-tároló
Az Azure Blob storage és az Azure Table Storage támogatja a storage service encryption (SSE) szolgáltatást, amely automatikusan titkosítja az adatokat, mielőtt megmaradna a tároláshoz, és visszafejti a lekérés előtt. További információ: [Azure Storage Service Encryption for Data at In.](../storage/common/storage-service-encryption.md)

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 támogatja az inaktív adatok ügyfél- és kiszolgálótitkosítását. További információt az [Adatok védelme titkosítással című témakörben](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)talál.

#### <a name="amazon-redshift"></a>Amazon Redshift
Az Amazon Redshift támogatja az inaktív adatok fürttitkosítását. További információ: [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
A Salesforce támogatja a Shield Platform Encryption titkosítását, amely lehetővé teszi az összes fájl, melléklet és egyéni mező titkosítását. További információt [a Webkiszolgáló OAuth hitelesítési folyamatának ismertetése című témakörben talál.](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)  

## <a name="hybrid-scenarios"></a>Hibrid forgatókönyvek
A hibrid forgatókönyvek használatához saját üzemeltetésű integrációs futásidejű kell telepíteni egy helyszíni hálózat, egy virtuális hálózat (Azure) vagy egy virtuális magánfelhő (Amazon) belül. A saját üzemeltetésű integrációs futásidejűnek képesnek kell lennie a helyi adattárak elérésére. Az önkiszolgáló integrációs futásidejűről a [Saját üzemeltetésű integrációs futásidő létrehozása és konfigurálása című](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)témakörben talál további információt. 

![saját üzemeltetésű integrációs futásidejű csatornák](media/data-movement-security-considerations/data-management-gateway-channels.png)

A parancscsatorna lehetővé teszi a data-mozgalom szolgáltatásai közötti kommunikációt a Data Factory-ban és az önkiszolgáló integrációs futásidejű között. A közlemény a tevékenységgel kapcsolatos információkat tartalmaz. Az adatcsatorna a helyszíni adattárak és a felhőbeli adattárak közötti adatátvitelre szolgál.    

### <a name="on-premises-data-store-credentials"></a>Helyszíni adattár hitelesítő adatai
A hitelesítő adatok tárolhatók az adat-előállító, vagy [az adatok gyári](store-credentials-in-key-vault.md) az Azure Key Vault futásidejű. Ha a hitelesítő adatok tárolására adat-előállító, mindig tárolja titkosított a saját üzemeltetésű integrációs futásidejű. 
 
- **Tárolja a hitelesítő adatokat helyileg.** Ha közvetlenül használja a **Set-AzDataFactoryV2LinkedService** parancsmag a kapcsolati karakterláncok és hitelesítő adatok inline a JSON, a csatolt szolgáltatás titkosítva van, és a saját üzemeltetésű integrációs futásidejű.  Ebben az esetben a hitelesítő adatok az azure-háttérszolgáltatáson keresztül, amely rendkívül biztonságos, a saját üzemeltetésű integrációs gép, ahol végül titkosítva és tárolva. A saját üzemeltetésű integrációs futásidő a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) segítségével titkosítja a bizalmas adatokat és a hitelesítő adatokat.

- **Hitelesítő adatok at tárolhatja az Azure Key Vaultban.** Az adattár hitelesítő adatait az [Azure Key Vaultban](https://azure.microsoft.com/services/key-vault/)is tárolhatja. A Data Factory lekéri a hitelesítő adatokat egy tevékenység végrehajtása során. További információ: [Store credential in Azure Key Vault](store-credentials-in-key-vault.md).

- **A hitelesítő adatokat helyileg tárolhatja anélkül, hogy a hitelesítő adatokat az Azure-háttérrendszeren keresztül az önkiszolgáló integrációs futásórába irányítana.** Ha helyileg szeretné titkosítani és tárolni a hitelesítő adatokat a saját üzemeltetésű integrációs futásidőben anélkül, hogy a hitelesítő adatokat az adat-előszolgáltatáson keresztül kellene továbbítania, kövesse a Hitelesítő adatok titkosítása az Azure Data Factory ban a [helyszíni adattárak titkosítása](encrypt-credentials-self-hosted-integration-runtime.md)című lépéseit. Minden csatlakozó támogatja ezt a lehetőséget. A saját üzemeltetésű integrációs futásidő a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) segítségével titkosítja a bizalmas adatokat és a hitelesítő adatokat. 

   A **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag használatával titkosíthatja a csatolt szolgáltatás hitelesítő adatait és a bizalmas részleteket a csatolt szolgáltatásban. Ezután a visszaadott JSON segítségével (a kapcsolati karakterlánc **titkosítotthitelesítő eleme)** a **Set-AzDataFactoryV2LinkedService** parancsmag használatával hozhat létre csatolt szolgáltatást.  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>A csatolt szolgáltatás saját üzemeltetésű integrációs futásidejű titkosításakor használt portok
Alapértelmezés szerint a PowerShell a 8060-as portot használja a számítógépen, saját üzemeltetésű integrációs futásidejű használatával a biztonságos kommunikációhoz. Szükség esetén ez a port módosítható.  

![HTTPS-port az átjáróhoz](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Minden adatátvitel biztonságos csatorna HTTPS és TLS TCP-n keresztül, hogy megakadályozzák a man-in-the-middle támadások kommunikáció során az Azure-szolgáltatásokkal.

[Az IPSec VPN-t](../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [az Azure ExpressRoute-ot](../expressroute/expressroute-introduction.md) is használhatja a helyszíni hálózat és az Azure közötti kommunikációs csatorna további védelméhez.

Az Azure Virtual Network a hálózat logikai ábrázolása a felhőben. Az IPSec VPN (helyek közötti) vagy expressroute (privát társviszony-létesítés) beállításával helyszíni hálózatot csatlakoztathat a virtuális hálózathoz.    

Az alábbi táblázat összefoglalja a hálózat és a saját üzemeltetésű integrációs futásidejű konfigurációs javaslatokat a hibrid adatáthelyezés forrás- és célhelyeinek különböző kombinációi alapján.

| Forrás      | Cél                              | Hálózati konfiguráció                    | Integrációs modul telepítése                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni követelmények | Virtuális hálózatokban telepített virtuális gépek és felhőszolgáltatások | IPSec VPN (pont-hely vagy hely közötti) | A saját üzemeltetésű integrációs futásidejű kell telepíteni egy Azure virtuális gépen a virtuális hálózatban.  |
| Helyszíni követelmények | Virtuális hálózatokban telepített virtuális gépek és felhőszolgáltatások | ExpressRoute (privát társviszony-létesítés)           | A saját üzemeltetésű integrációs futásidejű kell telepíteni egy Azure virtuális gépen a virtuális hálózatban.  |
| Helyszíni követelmények | Nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (Microsoft társviszony-létesítés)            | A saját üzemeltetésű integrációs futásidejű lehet telepíteni a helyszínen vagy egy Azure virtuális gépen. |

Az alábbi képek a saját üzemeltetésű integrációs futásidejű ek használatát mutatják be a helyszíni adatbázis és az Azure-szolgáltatások közötti adatáthelyezéshez az ExpressRoute és az IPSec VPN használatával (az Azure virtuális hálózattal):

**ExpressRoute**

![Az ExpressRoute használata átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Tűzfal-konfigurációk és az IP-címek listájának engedélyezése

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyszíni/magánhálózat tűzfalkövetelményei    
Egy vállalatnál a vállalati tűzfal a szervezet központi útválasztóján fut. A Windows tűzfal démonként fut azon a helyi számítógépen, amelyen az önkiszolgáló integrációs futásidejű telepítve van. 

Az alábbi táblázat a vállalati tűzfalak kimenő port- és tartománykövetelményeit tartalmazza:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Előfordulhat, hogy a megfelelő adatforrások által megkövetelt feltételeknek megfelelően a vállalati tűzfal szintjén kell kezelnie a portokat, vagy engedélyezési listát kell beállítania a tartományokhoz. Ez a tábla csak az Azure SQL Database, az Azure SQL Data Warehouse és az Azure Data Lake Store példákat használja.   

Az alábbi táblázat a Windows tűzfal bejövő portkövetelményeit tartalmazza:

| Bejövő portok | Leírás                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | A PowerShell titkosítási parancsmagja az [Azure Data Factory helyszíni adattárak hitelesítő adatainak titkosítása,](encrypt-credentials-self-hosted-integration-runtime.md)valamint a hitelesítő adatok kezelője alkalmazás által a helyszíni adattárak hitelesítő adatainak biztonságos beállításához szükséges a saját üzemeltetésű integrációs futásidőben. |

![Átjáróport követelményei](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP-konfigurációk és listabeállítás engedélyezése az adattárakban
Egyes adattárak a felhőben is megkövetelik, hogy engedélyezze az IP-címét a gép az áruház eléréséhez. Győződjön meg arról, hogy a saját üzemeltetésű integrációs futásidejű számítógép IP-címe megfelelően engedélyezett vagy konfigurálva van a tűzfalon.

A következő felhőalapú adattárak megkövetelik, hogy engedélyezze a saját üzemeltetésű integrációs futásidejű gép IP-címét. Előfordulhat, hogy az adattárak némelyike alapértelmezés szerint nem igényel engedélyezési listát. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL adattárház](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Az önkiszolgáló integrációs futásidejű megosztható a különböző adatgyárak között?**

Igen. További részleteket [itt](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/) talál.

**Milyen portkövetelmények az önkiszolgáló integrációs futásidejű működéséhez?**

A saját üzemeltetésű integrációs futásidejű teszi HTTP-alapú kapcsolatok az internet eléréséhez. A 443-as kimenő portokat meg kell nyitni az önkiszolgáló integrációs futásidejűhez a kapcsolat létrejöttéhez. Nyissa meg a 8060-as bejövő portot csak a számítógép szintjén (nem a vállalati tűzfal szintjén) a hitelesítő adatok kezelőjéhez. Ha az Azure SQL Database vagy az Azure SQL Data Warehouse a forrás vagy a cél, meg kell nyitnia port 1433 is. További információt a [Tűzfal konfigurációja és az IP-címek listájának engedélyezése](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) című részben talál. 


## <a name="next-steps"></a>További lépések
Az Azure Data Factory Copy Activity teljesítményéről a [Tevékenység teljesítményének másolása és finomhangolása című témakörben talál további információt.](copy-activity-performance.md)

 
