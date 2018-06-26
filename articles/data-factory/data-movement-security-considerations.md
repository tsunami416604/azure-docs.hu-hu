---
title: Biztonsági szempontok az Azure Data Factory |} Microsoft Docs
description: Ismerteti az alapvető biztonsági infrastruktúra, amelynek segítségével az Azure Data Factory adatátviteli szolgáltatások biztonságos adatait.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 5d9061e12ac9fe0b9d858690897e582acab5169e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754513"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Biztonsági szempontok az Azure Data Factory adatok áttelepítéséről –
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-data-movement-security-considerations.md)
> * [2. verzió – Előzetes verzió](data-movement-security-considerations.md)

Ez a cikk ismerteti, amelynek segítségével az Azure Data Factory adatátviteli szolgáltatások biztonságos alapvető biztonsági infrastruktúra adatait. Adatokat előállító felügyeleti erőforrások az Azure biztonsági infrastruktúra épül, és használja az Azure által kínált minden lehetséges biztonsági intézkedéseket.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használata lásd [adatok mozgása biztonsági szempontok az adat-előállító 1-es verziójú](v1/data-factory-data-movement-security-considerations.md).

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok találhatók a régióban, ahol az adat-előállító létrehozása történt. 

Annak ellenére, hogy a Data Factory néhány régiókban csak érhető el, van-e az adatátviteli szolgáltatás [elérhető globálisan](concepts-integration-runtime.md#integration-runtime-location) adatok megfelelőség biztosítása érdekében a hatékonyságot, és csökkentheti a hálózati kilépési költségeket. 

Az Azure Data Factory nem tárolja az adatokról, kivéve a társított szolgáltatás felhőalapú adattároló, tanúsítványok használata titkosított hitelesítő adatait. Data Factory létrehozása az adatvezérelt munkafolyamatok közötti adatok mozgása vezénylését [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats), és segítségével az adatok feldolgozása [szolgáltatások számítási](compute-linked-services.md) más régiókban vagy a egy a helyszíni környezetben. Figyelése és munkafolyamatok kezelése SDK és Azure figyelő használatával is.

A hitelesített adatátvitelt jelölik a Data Factory használatával:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA CSILLAG](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Ha érdekli az Azure megfelelő, és hogyan Azure biztosítja a saját infrastruktúra, látogasson el a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

Ez a cikk a Microsoft biztonsági szempontok a következő két adatelérési mozgását esetekben tekintse át: 

- **Felhő használata estén**: Ebben a forgatókönyvben a forrás- és a célkiszolgálón nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak például az Azure Storage, Azure SQL Data Warehouse, az Azure SQL Database, az Azure Data Lake Store, Amazon S3, Amazon Redshift szolgáltatott szoftver szolgáltatásokat, például a Salesforce és a webes protokollok-pl.: FTP- és OData felügyelt felhőalapú tárolási szolgáltatások. A támogatott adatforrások teljes listájának [adatokról és formátumok támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hibrid forgatókönyvek**: Ebben a forgatókönyvben a forrás- vagy a cél van egy tűzfal mögött található, vagy egy helyszíni vállalati hálózaton belül. Vagy az adattárban privát vagy virtuális hálózaton (általában a forrás) és nincs nyilvánosan elérhető. Adatbázis-kiszolgálóin futó virtuális gépek is alá ebben a forgatókönyvben.

## <a name="cloud-scenarios"></a>Felhő forgatókönyvek

### <a name="securing-data-store-credentials"></a>Biztonságossá tétele adatok adattárolóhoz használandó hitelesítő adatok

- **Titkosított hitelesítő adatok tárolása az Azure Data Factory felügyelt store**. Adat-előállító segít az adatok adattárolóhoz használandó hitelesítő adatok védelme a Microsoft által felügyelt tanúsítványokkal titkosításával. Ezeknek a tanúsítványoknak (amely tartalmazza a tanúsítvány megújításához és az áttelepítés a hitelesítő adatok) két évente legyenek-e elforgatva. Egy Azure Data Factory szolgáltatások által felügyelt Azure-tárfiók biztonságosan tárolja a titkosított hitelesítő adatokat. Azure Storage biztonsággal kapcsolatos további információkért lásd: [Azure Storage biztonsági áttekintése](../security/security-storage-overview.md).
- **Hitelesítő adatok tárolása az Azure Key Vault**. Az adattár-hitelesítő adatok is tárolhatja [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Adat-előállító tevékenység végrehajtása közben olvassa be a hitelesítő adatokat. További információkért lásd: [tároló-hitelesítő adatok az Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhő adattár támogatja a HTTPS vagy a TLS, adat-előállítóban adatátviteli szolgáltatások közötti minden adatátvitel és felhőalapú adattároló HTTPS- vagy TLS biztonságos csatornán keresztül.

> [!NOTE]
> Azure SQL Database és az Azure SQL Data Warehouse létesített összes kapcsolat (SSL/TLS) titkosításának megkövetelése, amíg adatok az átvitel során, illetve onnan az adatbázis. Ha egy folyamat éppen szerzőként JSON használatával, a titkosítási tulajdonság hozzáadása, és állítsa az értékét **igaz** a kapcsolati karakterláncban. Használhatja az Azure Storage **HTTPS** a kapcsolati karakterláncban.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adatokat inaktív adatok titkosítása támogatási tárolja. Azt javasoljuk, hogy engedélyezi-e az adatok titkosítási mechanizmus ezen adatok tárolóinak. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transzparens Data Encryption (TDE) az Azure SQL Data Warehouse abban a kártékony tevékenység fenyegetés valós idejű titkosítási és visszafejtési az adatok aktívan elvégzésével. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [az SQL Data Warehouse adatbázis védelme](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database átlátható titkosítási (TDE), amely segít valós idejű titkosítási és visszafejtési az adatok anélkül, hogy az alkalmazást módosítani kellene elvégzésével elleni a fenyegetését, hogy a rosszindulatú tevékenységhez is támogatja. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [átlátható adattitkosítást az SQL-adatbázis és adatraktár](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store is biztosít a fiókban tárolt adatok titkosítása. Ha engedélyezve van, a Data Lake Store automatikusan megőrzése előtt titkosítja az adatokat, és visszafejti előtt lekérését, így átlátható az ügyfél, aki hozzáfér az adatokat. További információkért lásd: [az Azure Data Lake Store biztonsági](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Az Azure Blob storage és az Azure Table storage
Az Azure Blob storage és az Azure Table storage támogatja a Storage szolgáltatás titkosítási (SSE), mely automatikusan Storage megőrzése előtt titkosítja az adatokat, és lekérése előtt visszafejti. További információkért lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 inaktív adatok titkosítása ügyfél és kiszolgáló egyaránt támogatja. További információkért lásd: [védelmének használatával adattitkosítás](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift támogatja a fürt titkosítást az inaktív adatok. További információkért lásd: [Amazon Redshift az adatbázis-titkosítás](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce Shield Platform titkosítási, amely lehetővé teszi az összes fájl, a mellékletek és az egyéni mezők titkosítási támogatja. További információkért lásd: [ismertetése a Web Server OAuth hitelesítési Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hibrid forgatókönyvek
Hibrid forgatókönyvekben szükséges önálló üzemeltetett integrációs futásidejű telepíthető egy helyszíni hálózat, a virtuális hálózaton (Azure) vagy virtuális magánfelhőbe (Amazon) belül. Az önálló üzemeltetett integrációs futásidejű való hozzáférést, a helyi képesnek kell lennie. Önálló üzemeltetett integrációs futásidejű kapcsolatos további információkért lásd: [létrehozása és konfigurálása önállóan üzemel integrációs futásidejű](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![önálló üzemeltetett integrációs futásidejű csatornán](media/data-movement-security-considerations/data-management-gateway-channels.png)

A parancs csatorna lehetővé teszi, hogy az adatátviteli szolgáltatások a Data Factory és az önálló üzemeltetett integrációs futásidejű közötti kommunikációhoz. A kommunikációt a tevékenységével kapcsolatos adatokat tartalmazza. Az adatcsatorna szolgál az adatok átviteléhez a helyszíni adattárolókhoz és felhő adattárolók között.    

### <a name="on-premises-data-store-credentials"></a>A helyszíni adatok adattárolóhoz használandó hitelesítő adatok
A hitelesítő adatokat a helyszíni adattárolókhoz mindig titkosítja, és tárolja. Azok vagy az önálló üzemeltetett integrációs futásidejű gépen helyben tárolja, vagy tárolja az Azure Data Factory felügyelt storage (ahogy a felhőalapú adattárolóhoz használandó hitelesítő adatok). 

- **Hitelesítő adatok helyi tárolása**. Ha szeretné titkosítani, és az önálló üzemeltetett integrációs Runtime helyileg tárolja a hitelesítő adatait, kövesse a lépéseket [titkosítsa a hitelesítő adatokat a helyszíni adattárolókhoz az Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Összekötők támogatja ezt a beállítást. Az önálló üzemeltetett integrációs futásidejű használja a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) titkosíthatja a bizalmas adatokat és hitelesítő adatokat. 

   Használja a **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsmag csatolt szolgáltatás hitelesítő adatai és a hivatkozott szolgáltatásban található bizalmas adatainak titkosításához. Vissza JSON segítségével (az a **EncryptedCredential** elem a kapcsolati karakterláncban) a társított szolgáltatás létrehozásához a **Set-AzureRmDataFactoryV2LinkedService** parancsmag.  

- **Az Azure Data Factory felügyelt storage tároló**. Ha közvetlenül a **Set-AzureRmDataFactoryV2LinkedService** parancsmag és a kapcsolati karakterláncok és beágyazottan történjen-e a JSON-felhasználó hitelesítő adatait, a társított szolgáltatás titkosítja és az Azure Data Factory felügyelt storage tárolja. Tanúsítvány még mindig titkosítja a bizalmas adatokat, és a Microsoft felügyeli ezeket a tanúsítványokat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Önálló üzemeltetett integrációs futásidejű a társított szolgáltatás titkosításához használt portok
Alapértelmezés szerint a PowerShell portot 8050 használja a gépen önálló üzemeltetett integrációs futásidejű a biztonságos kommunikáció érdekében. Ha szükséges, a port megváltoztatható.  

![Az átjáró HTTPS-port](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Minden adatátviteli biztonságos csatornán keresztül HTTPS és a TLS-átjárójának Azure-szolgáltatásokhoz való kommunikáció közben megakadályozza a TCP-n keresztül.

Is [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Azure ExpressRoute](../expressroute/expressroute-introduction.md) további védelméhez a helyszíni hálózat és az Azure közötti kommunikációs csatornát.

Azure-beli virtuális hálózat a felhőben a hálózat logikai ábrázolását. Csatlakozhat egy a helyszíni hálózat a virtuális hálózat IPSec VPN-(pont-pont) vagy az ExpressRoute (magánhálózati társviszony-létesítés) beállítást.    

A következő táblázat összefoglalja a hálózati és eltérő kombinációja a forrás- és hibrid adatmozgás helyeit alapján önálló üzemeltetett integrációs futásidejű konfigurációs javaslatait.

| Forrás      | Cél                              | Hálózati konfiguráció                    | Integrációs modul telepítése                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | IPSec VPN (pont – hely vagy pont-pont) | Az önálló üzemeltetett integrációs futásidejű lehet telepítve a helyi vagy egy Azure virtuális gépen egy virtuális hálózatban. |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | Az ExpressRoute (magánhálózati társviszony-létesítés)           | Az önálló üzemeltetett integrációs futásidejű lehet telepítve a helyi vagy egy Azure virtuális gépen egy virtuális hálózatban. |
| Helyszíni követelmények | Azure-alapú szolgáltatások, amelyek egy nyilvános végpontot | Az ExpressRoute (nyilvános társviszony)            | Az önálló üzemeltetett integrációs futásidejű helyszíni telepítve kell lennie. |

A következő ábrákon az önálló üzemeltetett integrációs futásidejű használatát az adatok áthelyezése egy helyi adatbázist és az Azure-szolgáltatások között ExpressRoute- és IPSec VPN (az Azure Virtual Network) használatával láthatók:

**ExpressRoute**

![Használjon ExpressRoute-átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN-átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Tűzfal-konfiguráció, és az engedélyezett IP-címek

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyi/InPrivate-hálózati tűzfal követelményei  
Vállalati a vállalati tűzfal fut, a szervezet a központi útválasztón. A Windows tűzfal démonként fut, a helyi számítógépen, ahol az önálló üzemeltetett integrációs futásidejű telepítése. 

A következő táblázat ismerteti a vállalati tűzfal kimenő port és a tartomány követelményeit:

| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Adat-előállítóban adatátviteli szolgáltatások eléréséhez szükséges önálló üzemeltetett integrációs futásidőben. |
| `*.core.windows.net`          | 443            | Csatlakozás az Azure storage-fiókok használatakor az önálló üzemeltetett integrációs futásidejű segítségével a [másolási előkészített](copy-activity-performance.md#staged-copy) szolgáltatás. |
| `*.frontend.clouddatahub.net` | 443            | A Data Factory szolgáltatásnak való kapcsolódáshoz szükséges önálló üzemeltetett integrációs futásidőben. |
| `*.database.windows.net`      | 1433           | (Választható) Szükséges a vagy az Azure SQL Database vagy az Azure SQL Data Warehouse másolásakor. Funkcióival előkészített adatok másolása az Azure SQL Database vagy az Azure SQL Data Warehouse 1433-as port megnyitása nélkül. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Választható) Szükséges a vagy Azure Data Lake Store másolásakor. |

> [!NOTE] 
> Lehetséges, hogy a portok vagy szinten a vállalati tűzfal szükség szerint a megfelelő adatforrások által engedélyezett tartományok kezelése. Ez a táblázat csak az Azure SQL Database, Azure SQL Data Warehouse és használ az Azure Data Lake Store példaként.   

A következő táblázat a Windows tűzfal bejövő portokra vonatkozó követelmények:

| Bejövő portok | Leírás                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | A PowerShell titkosítási parancsmag által igényelt, a [titkosítsa a hitelesítő adatokat a helyszíni adattárolókhoz az Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), és a hitelesítő adatok manager alkalmazás biztonságosan a helyszíni adattárolókhoz hitelesítő adatok beállítására az önálló üzemeltetett integrációs Runtime. |

![Átjáró portokra vonatkozó követelmények](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfiguráció és adatok áruházakból engedélyezése
Egyes adatokat tárolja, a felhőben is szükség lehet, hogy Ön engedélyezett az IP-cím a gép az áruház elérésének. Győződjön meg arról, hogy az önálló üzemeltetett integrációs futásidejű gép IP-címe szerepel az engedélyezési listán, vagy megfelelően konfigurálva a tűzfalon.

A következő felhőalapú adattároló szükséges, hogy Ön engedélyezett az önálló üzemeltetett integrációs futásidejű gép IP-címét. Alapértelmezés szerint ezek adattárolókhoz némelyike nem igényelhetnek engedélyezése. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Az önálló üzemeltetett integrációs futásidejű meg lehet osztani összes különböző adat-előállítók?**

Ez a funkció még nem támogatott. Folyamatosan dolgozunk a rajta.

**Mik az önálló üzemeltetett integrációs futásidejű működéséhez port követelményei?**

Az önálló üzemeltetett integrációs futásidejű lehetővé teszi a HTTP-alapú kapcsolatok az internet eléréséhez. Az önálló üzemeltetett integrációs futásidejű a kapcsolat létrehozásához a 443-as és a 80-as kimenő portot kell megnyitni. Nyissa meg a bejövő portot 8050 csak a számítógép szintjén (nem a vállalati tűzfal szint) hitelesítő adatok manager alkalmazáshoz. Ha Azure SQL Database vagy az Azure SQL Data Warehouse a forrás vagy a cél szolgál, akkor nyissa meg a 1433-as port is. További információkért lásd: a [tűzfal-konfiguráció, és az engedélyezett IP-címek](#firewall-configurations-and-whitelisting-ip-address-of-gateway) szakasz. 


## <a name="next-steps"></a>További lépések
Azure Data Factory másolási tevékenység teljesítményével kapcsolatos információkért lásd: [másolási tevékenység teljesítmény- és hangolási útmutató](copy-activity-performance.md).

 
