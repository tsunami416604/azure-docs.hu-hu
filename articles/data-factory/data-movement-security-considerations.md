---
title: Biztonsági megfontolások a Azure Data Factoryban | Microsoft Docs
description: Ismerteti az alapszintű biztonsági infrastruktúrát, amelyet az adatátviteli szolgáltatások Azure Data Factory használnak az adatok biztonságossá tételéhez.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: c42e70efc8543e1d255690070ffb51b865e1754f
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608582"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>A Azure Data Factory adatáthelyezésének biztonsági szempontjai
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuális verzió](data-movement-security-considerations.md)

Ez a cikk azt az alapszintű biztonsági infrastruktúrát ismerteti, amelyet az adatátviteli szolgáltatások Azure Data Factory használnak az adatok biztonságossá tételéhez. Data Factory felügyeleti erőforrások az Azure biztonsági infrastruktúrára épülnek, és az Azure által kínált összes lehetséges biztonsági intézkedést felhasználhatják.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok abban a régióban találhatók, ahol az adat-előállítót létrehozták. 

Annak ellenére, hogy Data Factory csak néhány régióban érhető el, az adatátviteli szolgáltatás [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location) az adatmegfelelőség, a hatékonyság és a csökkentett hálózati kimenő költségek biztosításához. 

A Azure Data Factory nem tárol adatokat, kivéve a társított szolgáltatás hitelesítő adatait a felhőalapú adattárakhoz, amelyek tanúsítványokkal vannak titkosítva. A Data Factory segítségével adatvezérelt munkafolyamatokat hozhat létre, amelyek a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)közötti adatáthelyezést és az adatok feldolgozását végzik a más régiókban vagy helyszíni környezetben lévő [számítási szolgáltatások](compute-linked-services.md) használatával. Az SDK-k és a Azure Monitor használatával is nyomon követheti és kezelheti a munkafolyamatokat.

A Data Factory minősítése a következő:

| **[CSA-csillag minősítése](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Ha érdekli az Azure megfelelősége, és hogyan védi az Azure a saját infrastruktúráját, látogasson el a [Microsoft adatvédelmi](https://microsoft.com/en-us/trustcenter/default.aspx)központba. Az összes Azure- https://aka.ms/AzureCompliance megfelelőségi ajánlat legújabb listájáért látogasson el a következőre:.

Ebben a cikkben a következő két adatáthelyezési forgatókönyvben tekintjük át a biztonsági szempontokat: 

- **Felhőbeli forgatókönyv**: Ebben az esetben a forrás és a cél is nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak a felügyelt felhőalapú tárolási szolgáltatások, például az Azure Storage, a Azure SQL Data Warehouse, a Azure SQL Database, a Azure Data Lake Store, az Amazon S3, az Amazon vöröseltolódás, az SaaS-szolgáltatások, például a Salesforce és a webes protokollok, például az FTP és a OData. A támogatott adattárakban [és](copy-activity-overview.md#supported-data-stores-and-formats)-formátumokban található támogatott adatforrások teljes listája.
- **Hibrid forgatókönyv**: Ebben az esetben a forrás vagy a cél egy tűzfal mögött vagy egy helyszíni vállalati hálózaton belül van. Az adattár pedig magánhálózat vagy virtuális hálózat (leggyakrabban a forrás), és nem nyilvánosan elérhető. A virtuális gépeken üzemeltetett adatbázis-kiszolgálók is ebbe a forgatókönyvbe tartoznak.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek

### <a name="securing-data-store-credentials"></a>Adattároló hitelesítő adatainak biztonságossá tétele

- **Titkosított hitelesítő adatok tárolása egy Azure Data Factory felügyelt tárolóban**. A Data Factory az adattár hitelesítő adatainak védelme érdekében titkosítja őket a Microsoft által kezelt tanúsítványokkal. Ezeket a tanúsítványokat kétévente elforgatják (ami magában foglalja a tanúsítvány megújítását és a hitelesítő adatok áttelepítését). A titkosított hitelesítő adatokat a rendszer a Azure Data Factory felügyeleti szolgáltatások által kezelt Azure Storage-fiókban tárolja biztonságosan. Az Azure Storage biztonságával kapcsolatos további információkért lásd: az [Azure Storage biztonsági áttekintése](../security/fundamentals/storage-overview.md).
- **Hitelesítő adatok tárolása Azure Key Vaultban**. Az adattár hitelesítő adatait [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)is tárolhatja. Data Factory lekéri a hitelesítő adatot egy tevékenység végrehajtása során. További információ: [a hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás az átvitel során
Ha a felhőalapú adattár támogatja a HTTPS-t vagy a TLS-t, az adatátviteli Data Factory szolgáltatások és a felhőalapú adattárolók közötti adatforgalom a biztonságos csatorna HTTPS vagy TLS protokollon keresztül történik.

> [!NOTE]
> Azure SQL Database és Azure SQL Data Warehouse összes kapcsolata titkosítást igényel (SSL/TLS), miközben az adatok átvitele folyamatban van az adatbázisba és onnan. Amikor JSON használatával készít folyamatokat, adja hozzá a titkosítási tulajdonságot, és állítsa **igaz** értékre a kapcsolódási karakterláncban. Az Azure Storage esetében a kapcsolati sztringben **HTTPS protokollt** használhat.

> [!NOTE]
> Ha engedélyezni szeretné a titkosítást az Oracle-adatok áthelyezése közben, kövesse az alábbi lehetőségek egyikét:
> 1. Az Oracle-kiszolgálón nyissa meg az Oracle Advanced Security (OAS) parancsot, és konfigurálja a titkosítási beállításokat, amelyek támogatják a Triple DES encryption (3DES) és a Advanced Encryption Standard (AES) használatát, további részletekért [tekintse](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) meg a következő témakört:. Az ADF automatikusan egyezteti a titkosítási módszert, hogy az Oracle-hez való kapcsolódáskor a OAS-ben konfigurált konfigurációt használja.
> 2. Az ADF-ben a EncryptionMethod = 1 a kapcsolati sztringben (a társított szolgáltatásban) adható hozzá. Ez titkosítási módszerként az SSL/TLS protokollt fogja használni. Ennek használatához le kell tiltania a nem SSL titkosítási beállításokat a OAS az Oracle-kiszolgáló oldalán a titkosítási ütközés elkerülése érdekében.

> [!NOTE]
> A használt TLS-verzió 1,2.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adattárak támogatják a nyugalmi állapotban lévő adatok titkosítását. Javasoljuk, hogy engedélyezze az adattitkosítási mechanizmust ezekhez az adattárakhoz. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
A Azure SQL Data Warehouse transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy valós idejű titkosítást és visszafejtést végez az inaktív adatokon. Ez a viselkedés átlátható az ügyfél számára. További információ: [adatbázis biztonságossá tétele SQL Data Warehouseban](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
A Azure SQL Database támogatja az transzparens adattitkosítást (TDE), amely segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy az adatok valós idejű titkosítását és visszafejtését végzi, anélkül, hogy az alkalmazás módosítására lenne szükség. Ez a viselkedés átlátható az ügyfél számára. További információ: [transzparens adattitkosítás a SQL Database és](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)az adattárházban.

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Azure Data Lake Store a fiókban tárolt adatkezeléshez is biztosít titkosítást. Ha engedélyezve van, a Data Lake Store automatikusan titkosítja az adatokkal, mielőtt megőrzi és visszafejti azokat a beolvasás előtt, így transzparensvé válik az adatokhoz hozzáférő ügyfél számára. További információ: [Security in Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage és Azure Table Storage
Az Azure Blob Storage és az Azure Table Storage támogatási Storage Service Encryption (SSE), amely automatikusan titkosítja az adatait, mielőtt megőrzi a tárterületet, és visszafejti a beolvasás előtt. További információ: [Azure Storage Service encryption for](../storage/common/storage-service-encryption.md)inaktív adatok.

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 az inaktív adatok ügyfél-és kiszolgálói titkosítását is támogatja. További információkért lásd: [adatok védelme titkosítás használatával](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Az Amazon vöröseltolódás támogatja a fürtök titkosítását a nyugalmi állapotban lévő adatokhoz. További információ: [Amazon vöröseltolódás Database encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
A Salesforce támogatja a védelmi platform titkosítását, amely lehetővé teszi az összes fájl, melléklet és egyéni mező titkosítását. További információ: [a webkiszolgáló OAuth-hitelesítési folyamatának ismertetése](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hibrid forgatókönyvek
Hibrid forgatókönyvek esetén a saját üzemeltetésű integrációs modult egy helyszíni hálózaton, egy virtuális hálózaton (Azure) belül vagy egy virtuális magánhálózati felhőben (Amazon) belül kell telepíteni. A saját üzemeltetésű integrációs modulnak képesnek kell lennie a helyi adattárak elérésére. További információ a saját üzemeltetésű integrációs modulról: saját üzemeltetésű integrációs modul [létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![saját üzemeltetésű Integration Runtime-csatornák](media/data-movement-security-considerations/data-management-gateway-channels.png)

A parancssori csatorna lehetővé teszi az adatátviteli szolgáltatások közötti kommunikációt Data Factory és saját üzemeltetésű integrációs modulban. A kommunikáció a tevékenységgel kapcsolatos információkat tartalmaz. Az adatcsatorna a helyszíni adattárak és a Felhőbeli adattárak közötti adatátvitelhez használatos.    

### <a name="on-premises-data-store-credentials"></a>Helyszíni adattároló hitelesítő adatai
A helyszíni adattárakhoz tartozó hitelesítő adatokat a rendszer mindig titkosítja és tárolja. Ezeket helyileg tárolhatja a saját üzemeltetésű Integration Runtime gépen, vagy Azure Data Factory felügyelt tárolóban (akárcsak a Cloud Store hitelesítő adataival). 

- **Hitelesítő adatok helyi tárolása**. Ha helyileg kívánja titkosítani és tárolni a hitelesítő adatokat a saját üzemeltetésű integrációs modulban, kövesse a [hitelesítő](encrypt-credentials-self-hosted-integration-runtime.md)adatok titkosítása a helyszíni adattárakhoz Azure Data Factoryban című témakör lépéseit. Az összes összekötő támogatja ezt a beállítást. A saját üzemeltetésű integrációs modul a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) használatával titkosítja a bizalmas adatokat és a hitelesítő adatokat. 

   A **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag használatával Titkosítsa a társított szolgáltatás hitelesítő adatait és a bizalmas adatokat a társított szolgáltatásban. Ezután a visszaadott JSON-t (a kapcsolati karakterlánc **EncryptedCredential** eleme) használva hozzon létre egy társított szolgáltatást a **set-AzDataFactoryV2LinkedService** parancsmag használatával.  

- **Azure Data Factory felügyelt tárolóban tárolhatja**. Ha közvetlenül a **set-AzDataFactoryV2LinkedService** parancsmagot használja a kapcsolati karakterláncokkal és a JSON-ban beágyazott hitelesítő adatokkal, akkor a társított szolgáltatás titkosítva van, és Azure Data Factory felügyelt tárolóban tárolódik. A bizalmas adatokat a tanúsítvány továbbra is titkosítja, és a Microsoft kezeli ezeket a tanúsítványokat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>A társított szolgáltatás saját üzemeltetésű integrációs modulban való titkosításához használt portok
Alapértelmezés szerint a PowerShell az 8060-as portot használja a gépen a saját üzemeltetésű integrációs modul használatával a biztonságos kommunikáció érdekében. Ha szükséges, ez a port módosítható.  

![HTTPS-port az átjáróhoz](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Titkosítás átvitel közben
Az adatátvitel a biztonságos csatorna HTTPS protokollon és a TLS protokollon keresztül történik az Azure-szolgáltatásokkal folytatott kommunikáció során.

Az [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) -t vagy az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) -t is használhatja a kommunikációs csatorna további biztonságossá tételéhez a helyszíni hálózat és az Azure között.

Az Azure Virtual Network a hálózat logikai ábrázolása a felhőben. A helyszíni hálózatot a virtuális hálózathoz az IPSec VPN (helyek közötti) vagy a ExpressRoute (privát társ) beállításával lehet összekapcsolni.    

A következő táblázat összefoglalja a hálózat és a saját üzemeltetésű integrációs modul konfigurációs javaslatait a hibrid adatáthelyezéshez használt forrás-és célhelyek különböző kombinációi alapján.

| Source      | Cél                              | Hálózati konfiguráció                    | Integrációs modul telepítése                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | IPSec VPN (pont – hely vagy hely – hely) | A saját üzemeltetésű integrációs modult egy Azure-beli virtuális gépre kell telepíteni a virtuális hálózaton.  |
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | ExpressRoute (privát társ)           | A saját üzemeltetésű integrációs modult egy Azure-beli virtuális gépre kell telepíteni a virtuális hálózaton.  |
| Helyszíni | Nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (Microsoft-társ)            | A saját üzemeltetésű integrációs modult a helyszínen vagy egy Azure-beli virtuális gépen lehet telepíteni. |

Az alábbi képek a saját üzemeltetésű integrációs modul használatát mutatják be a helyszíni adatbázis és az Azure-szolgáltatások közötti adatáthelyezéshez a ExpressRoute és az IPSec VPN használatával (Azure Virtual Network):

**ExpressRoute**

![ExpressRoute használata az átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Tűzfal-konfigurációk és engedélyezési IP-címek

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A tűzfalra vonatkozó követelmények helyszíni/magánhálózati hálózatokhoz  
A vállalatokban a vállalati tűzfal a szervezet központi útválasztóján fut. A Windows tűzfal démonként fut azon a helyi gépen, amelyben a saját üzemeltetésű integrációs modul telepítve van. 

A következő táblázat a vállalati tűzfalak kimenő portokra és tartományokra vonatkozó követelményeit tartalmazza:

| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory adatátviteli szolgáltatásaihoz való csatlakozáshoz. |
| `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, kihagyhatja ezt. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](copy-activity-performance.md#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Választható Azure SQL Database vagy Azure SQL Data Warehouse másolásakor szükséges. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhatja az Adatmásolást Azure SQL Databaseba vagy Azure SQL Data Warehouseba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Választható A vagy a rendszerből Azure Data Lake Store történő másoláskor szükséges. |

> [!NOTE] 
> Előfordulhat, hogy a megfelelő adatforrások által megkövetelt portokat vagy engedélyezési tartományokat a vállalati tűzfal szintjén kell kezelnie. Ez a táblázat csak Azure SQL Database, Azure SQL Data Warehouse és Azure Data Lake Storeeket használ példaként.   

A következő táblázat a Windows tűzfal bejövő portokra vonatkozó követelményeit tartalmazza:

| Bejövő portok | Leírás                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | A PowerShell-titkosítási parancsmag szükséges a [helyi adattárakhoz tartozó hitelesítő](encrypt-credentials-self-hosted-integration-runtime.md)adatok titkosítása Azure Data Factoryban, valamint a Hitelesítőadat-kezelő alkalmazás által a helyi adattárakhoz tartozó hitelesítő adatok biztonságos beállítása a saját üzemeltetésű rendszeren integrációs modul. |

![Az átjáró portjának követelményei](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfigurációk és engedélyezési listák az adattárakban
A felhőben lévő egyes adattárakhoz a tárolóhoz hozzáférő számítógép IP-címét is meg kell adni. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul számítógépének IP-címe engedélyezett vagy a tűzfalon megfelelően van konfigurálva.

A következő felhőalapú adattárakhoz a saját üzemeltetésű Integration Runtime-gép IP-címének engedélyezési helye szükséges. Az adattárak némelyike alapértelmezés szerint nem igényel engedélyezési listát. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Megoszthatók a saját üzemeltetésű integrációs modul a különböző adatüzemek között?**

Igen. További részleteket [itt](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/) talál.

**Milyen portokra vonatkozó követelmények vonatkoznak a saját üzemeltetésű Integration Runtime működésére?**

A saját üzemeltetésű integrációs modul lehetővé teszi, hogy a HTTP-alapú kapcsolatok hozzáférjenek az internethez. A kapcsolódáshoz a saját üzemeltetésű integrációs modul 443 kimenő portjait kell megnyitni. Nyissa meg a 8060-es bejövő portot csak a számítógép szintjén (nem a vállalati tűzfal szintjén) a Hitelesítőadat-kezelő alkalmazáshoz. Ha Azure SQL Database vagy Azure SQL Data Warehouse a forrásként vagy a célhelyként van használatban, akkor a 1433-es portot is meg kell nyitnia. További információ: a [tűzfal beállításai és az IP-címek engedélyezési](#firewall-configurations-and-whitelisting-ip-address-of-gateway) listája szakasz. 


## <a name="next-steps"></a>További lépések
A másolási tevékenység teljesítményének Azure Data Factory a [másolási tevékenység teljesítményének és hangolásának útmutatója](copy-activity-performance.md)című témakörben talál további információt.

 
