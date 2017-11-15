---
title: "Biztonsági szempontok az Azure Data Factory |} Microsoft Docs"
description: "Ismerteti az alapvető biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.openlocfilehash: bba2781d43aff9e462246cfe21961695e48196d8
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Az Azure Data Factory - adatátvitelt jelölik a kapcsolódó biztonsági szempontok
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-data-movement-security-considerations.md)
> * [2. verzió – Előzetes verzió](data-movement-security-considerations.md)

Ez a cikk ismerteti az alapvető biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme. Az Azure Data Factory-kezelési források az Azure biztonsági infrastruktúra épül, és használja az Azure által kínált minden lehetséges biztonsági intézkedéseket.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [adatok mozgása biztonsági szempontok az adat-előállító 1-es verziójú](v1/data-factory-data-movement-security-considerations.md).

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok találhatók a régióban, ahol az adat-előállító létrehozása történt. 

Annak ellenére, hogy a Data Factory érhető el csak **USA keleti régiója**, **USA keleti régiója 2**, és **Nyugat-Európa** régiók (előzetes verzió 2-es verzió), az adatátviteli szolgáltatás elérhető [globálisan több régióban](concepts-integration-runtime.md#azure-ir). Az adatátviteli szolgáltatás még nincs telepítve a régióba, ha a Data Factory szolgáltatásnak biztosítja az adatok nem hagynak egy földrajzi terület vagy régió kivéve, ha explicit módon utasíthatja a szolgáltatás egy másik régióban. 

Az Azure Data Factory maga nem tárol kivéve a társított szolgáltatás hitelesítő adatait felhőalapú adattároló, tanúsítványok használata titkosított adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi a SDK és Azure figyelő használata munkafolyamatok figyelése és kezelése.

Azure Data Factory használatával adatmozgás lett **hitelesített** esetében:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA CSILLAG](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Ha Ön Azure megfelelőségét, és hogyan Azure biztosítja a saját infrastruktúra iránt érdeklődik, keresse fel a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx). 

Ez a cikk a Microsoft biztonsági szempontok a következő két adatelérési mozgását esetekben tekintse át: 

- **Felhő használata estén**-ebben a forgatókönyvben a forrás- és is nyilvánosan elérhető az interneten keresztül. Ezek közé tartozik a felügyelt tárolási felhőszolgáltatások pl. Azure Storage Azure SQL Data Warehouse, az Azure SQL Database, az Azure Data Lake Store, Amazon S3, Amazon Redshift, például a Salesforce szolgáltatott szoftver szolgáltatásokat, és a webes protokollok-pl.: FTP- és OData. A támogatott adatforrások teljes listáját megtalálhatja [Itt](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hibrid forgatókönyvek**– ebben a forgatókönyvben a cél- és a tűzfal mögött, vagy egy helyszíni vállalati hálózaton vagy az adatok belül tároló egy magánhálózaton / virtuális hálózatot (általában a forrás), és nincs nyilvánosan elérhető. Adatbázis-kiszolgálóin futó virtuális gépek is alá ebben a forgatókönyvben.

## <a name="cloud-scenarios"></a>Felhő forgatókönyvek
###<a name="securing-data-store-credentials"></a>Biztonságossá tétele adatok adattárolóhoz használandó hitelesítő adatok
- Azure Data Factory felügyelt store titkosított hitelesítő adatok tárolása.

   Az Azure Data Factory védi az adatokat adattárolóhoz használandó hitelesítő adatok szerint **titkosított** őket a **tanúsítványokat a Microsoft által felügyelt**. Ezek a tanúsítványok legyenek-e elforgatva minden **kétéves** (amely tartalmazza a tanúsítvány megújítása és a hitelesítő adatok áttelepítését). A titkosított hitelesítő adatokat biztonságosan vannak tárolva egy **Azure Storage Azure Data Factory szolgáltatások által kezelt**. Azure Storage biztonsággal kapcsolatos további információkért tekintse meg a [Azure Storage biztonsági áttekintése](../security/security-storage-overview.md).
- Hitelesítő adatok tárolása az Azure Key Vaultban 

   A hitelesítő adatokat tároló adatok tárolásához választhat [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), hagyja meg az Azure Data Factory ennek lekéréséhez tevékenység végrehajtása közben. További információkért lásd: [tároló-hitelesítő adatok az Azure Key Vault](store-credentials-in-key-vault.md).

   > [!NOTE]
   > Jelenleg csak [Dynamics összekötő](connector-dynamics-crm-office-365.md) támogatja ezt a szolgáltatást. 

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhő adattár támogatja a HTTPS vagy a TLS, adat-előállítóban adatátviteli szolgáltatások közötti minden adatátvitel és felhőalapú adattároló HTTPS- vagy TLS biztonságos csatornán keresztül.

> [!NOTE]
> Az összes kapcsolat **Azure SQL Database** és **Azure SQL Data Warehouse** mindig titkosítás (SSL/TLS) közben az átvitel során, illetve onnan az adatbázis. Adatcsatorna JSON használatával készítése, során adja hozzá a **titkosítási** tulajdonság, majd állítsa be **igaz** a a **kapcsolati karakterlánc**. A **Azure Storage**, használhat **HTTPS** a kapcsolati karakterláncban.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adatokat inaktív adatok titkosítása támogatási tárolja. Javasoljuk, hogy engedélyezze ezeket adattárolókhoz adatok titkosítási mechanizmus. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transzparens Data Encryption (TDE) az Azure SQL Data Warehouse elősegíti a valós idejű titkosítási és visszafejtési az adatok aktívan elvégzésével kártevő szándékú tevékenységek fenyegetés elleni védelem. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [az SQL Data Warehouse adatbázis védelme](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL adatbázis is támogatja a transzparens adattitkosítás (TDE), amely segít a valós idejű titkosítási és visszafejtési az adatok anélkül, hogy az alkalmazást módosítani kellene elvégzésével kártevő szándékú tevékenységek fenyegetés elleni védelem. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [átlátható adattitkosítást az Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store is biztosít a fiókban tárolt adatok titkosítása. Ha engedélyezve van, a Data Lake store automatikusan megőrzése előtt titkosítja az adatokat, és lekérése, így átlátható az ügyfélnek, az adatok elérése előtt visszafejti. További információkért lásd: [az Azure Data Lake Store biztonsági](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Az Azure Blob Storage és az Azure Table Storage
Az Azure Blob Storage és az Azure Table storage támogatja a Storage szolgáltatás titkosítási (SSE), mely automatikusan Storage megőrzése előtt titkosítja az adatokat, és lekérése előtt visszafejti. További információkért lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 inaktív adatok titkosítása ügyfél és kiszolgáló egyaránt támogatja. További információkért lásd: [védelmének használatával adattitkosítás](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Jelenleg adat-előállító nem támogatja a Amazon S3 virtuális magánfelhőbe (VPC) belül.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift támogatja a fürt titkosítást az inaktív adatok. További információkért lásd: [Amazon Redshift az adatbázis-titkosítás](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Adat-előállító jelenleg nem támogatja a Amazon Redshift egy VPC belül. 

#### <a name="salesforce"></a>Salesforce
Salesforce Shield Platform titkosítási, amely lehetővé teszi az összes olyan fájlok, mellékleteket, egyéni mezők titkosítási támogatja. További információkért lásd: [ismertetése a Web Server OAuth hitelesítési Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>Hibrid környezetekben (önálló üzemeltetett integrációs futásidejű használatával)
Hibrid forgatókönyvekben szükséges önálló üzemeltetett integrációs futásidejű telepíteni kell egy helyi hálózaton vagy egy virtuális hálózatot (Azure) vagy virtuális magánfelhőbe (Amazon) belül. Az önálló üzemeltetett integrációs futásidejű való hozzáférést, a helyi képesnek kell lennie. Az önálló üzemeltetett integrációs futásidejű kapcsolatos további információkért lásd: [önállóan üzemel integrációs futásidejű](create-self-hosted-integration-runtime.md). 

![önálló üzemeltetett integrációs futásidejű csatornán](media/data-movement-security-considerations/data-management-gateway-channels.png)

A **parancscsatornát** lehetővé teszi, hogy a Data Factory adatátviteli szolgáltatások és önálló üzemeltetett integrációs futásidejű közötti kommunikáció. A kommunikációt a tevékenységével kapcsolatos adatokat tartalmazza. Az adatcsatorna szolgál az adatok átviteléhez a helyszíni adattárolókhoz és felhő adattárolók között.    

### <a name="on-premises-data-store-credentials"></a>A helyszíni adatok adattárolóhoz használandó hitelesítő adatok
A hitelesítő adatokat a helyszíni adattárolókhoz mindig titkosítja, és tárolja. Vagy tárolhatja helyben a önálló üzemeltetett integrációs futásidejű gépen, vagy az Azure Data Factory felügyelt tárolási (ahogy a felhőalapú adattárolóhoz használandó hitelesítő adatok). 

1. Dönthet úgy, hogy **hitelesítő adatok helyi tárolása**. Ha szeretné titkosítani, és az önálló üzemeltetett integrációs Runtime helyileg tárolja a hitelesítő adatait, kövesse a lépéseket [önálló üzemeltetett integrációs futásidejű hitelesítő adatok titkosításához](encrypt-credentials-self-hosted-integration-runtime.md). Összekötők támogatja ezt a beállítást. Az önálló üzemeltetett integrációs futásidejű használja a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) titkosíthatja a bizalmas adatokat / hitelesítőadat-információkat. 

   Használjon **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsmag társított szolgáltatás hitelesítő adatok titkosításához / titkosítsa a bizalmas részletek hivatkozott szolgáltatásban található. Adott vissza JSON segítségével (az **EncryptedCredential** eleme a **connectionString**) által hivatkozott szolgáltatás létrehozása **Set-AzureRmDataFactoryV2LinkedSevrice**parancsmag.  

2. Ha nem használja a **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsmag, a fenti lépésben leírt eljárást, és inkább közvetlenül **Set-AzureRmDataFactoryV2LinkedSevrice** parancsmagot a kapcsolati karakterláncok / beágyazottan történjen-e a JSON-felhasználó hitelesítő adatait, akkor a társított szolgáltatás lesz **titkosított és tárolja a rendszer Azure Data Factory felügyelt tárolási**. A bizalmas adatok továbbra is a tanúsítvánnyal titkosított, és ezeket a tanúsítványokat a Microsoft által felügyelt.



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Önálló üzemeltetett integrációs futásidejű a társított szolgáltatás titkosítása során használt portok
Alapértelmezés szerint PowerShell használja a port **8050** a gépen önálló üzemeltetett integrációs futásidejű a biztonságos kommunikáció érdekében. Ha szükséges, a port megváltoztatható.  

![Az átjáró HTTPS-port](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Biztonságos csatornán keresztül van minden adatátviteli **HTTPS** és **TCP-n keresztül TLS** -átjárójának érdekében az Azure-szolgáltatásokkal folytatott kommunikáció során.

Is [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Express Route](../expressroute/expressroute-introduction.md) további védelméhez a helyszíni hálózat és az Azure közötti kommunikációs csatornát.

Virtuális hálózat a logikai reprezentációjává a hálózaton, a felhőben. Kapcsolódás egy a helyszíni hálózat az Azure virtuális hálózathoz (VNet) IPSec VPN-(pont-pont) vagy Express Route (magánhálózati társviszony-létesítés) beállítása     

A következő táblázat összefoglalja a hálózati és eltérő kombinációja a forrás- és hibrid adatmozgás helyeit alapján önálló üzemeltetett integrációs futásidejű konfigurációs javaslatait.

| Forrás      | Cél                              | Hálózati konfiguráció                    | Integrációs futásidejű beállítása                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | IPSec VPN (pont – hely vagy pont-pont) | Az önálló üzemeltetett integrációs futásidejű lehet telepítve a helyi vagy egy Azure virtuális gép (VM) virtuális hálózat |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | Az ExpressRoute (magánhálózati társviszony-létesítés)           | Az önálló üzemeltetett integrációs futásidejű lehet telepítve a helyi vagy egy Azure virtuális gépen a virtuális hálózat |
| Helyszíni követelmények | Azure-alapú szolgáltatások, amelyek egy nyilvános végpontot | Az ExpressRoute (nyilvános Társviszony)            | Az önálló üzemeltetett integrációs futásidejű telepített helyszíni kell lennie. |

A következő ábrákon az használatát, az adatok áthelyezése egy helyi adatbázist és az Azure-szolgáltatások expressroute- és IPSec VPN-(a virtuális hálózattal) használatával önálló üzemeltetett integrációs futásidejű láthatók:

**Express Route:**

![Használjon Expressroute-átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN-átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>Tűzfalbeállítások és engedélyezett IP-cím (önálló üzemeltetett integrációs futásidejű)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyi/InPrivate-hálózati tűzfal követelményei  
Vállalati a **vállalati tűzfal** futtat a szervezet a központi útválasztón. Emellett a **Windows tűzfal** démonként fut, a helyi számítógépen, amelyen telepítve van az önálló üzemeltetett integrációs futásidejű. 

A következő táblázat **kimenő port** és a tartomány vonatkozó követelményeket a **vállalati tűzfal**.

| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443, 80        | Az önálló üzemeltetett integrációs futtatókörnyezet adatátviteli szolgáltatások adat-előállítóban való kapcsolódáshoz szükséges |
| `*.core.windows.net`          | 443            | Csatlakozhat a önálló üzemeltetett integrációs futtatókörnyezet Azure Storage-fiók használatakor a [másolási előkészített](copy-activity-performance.md#staged-copy) szolgáltatás. |
| `*.frontend.clouddatahub.net` | 443            | Az Azure Data Factory szolgáltatásnak való kapcsolódáshoz szükséges önálló üzemeltetett integrációs futásidőben. |
| `*.database.windows.net`      | 1433           | (Nem kötelező) szükséges, ha a cél az Azure SQL Database az Azure SQL Data Warehouse- /. Funkcióival előkészített adatok másolása az Azure SQL Database vagy az Azure SQL Data Warehouse az 1433-as port megnyitása nélkül. |
| `*.azuredatalakestore.net`    | 443            | (Nem kötelező) szükséges, ha a célként megadott Azure Data Lake store |

> [!NOTE] 
> Lehet, hogy a portok kezelése / megfelelő adatforrások szükség szerint szinten engedélyezett tartomány a vállalati tűzfalon. Ez a táblázat csak akkor használja az Azure SQL Database, az Azure SQL Data Warehouse, az Azure Data Lake Store példaként.   

A következő táblázat **port bejövő** követelményei a **Windows tűzfal**.

| Bejövő portok | Leírás                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | A szükséges titkosítási PowerShell parancsmag [önálló üzemeltetett integrációs futásidejű hitelesítő adatok titkosításához](encrypt-credentials-self-hosted-integration-runtime.md)/ hitelesítőadat-kezelő alkalmazás biztonságosan meg hitelesítő adatokat a helyszíni adattárolókhoz önálló üzemeltetett integrációs futásidejű. |

![Átjáró portokra vonatkozó követelmények](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>IP konfigurációk és az engedélyezett adattár
Egyes adatokat tárolja, a felhőben is szükség lehet az engedélyezett IP-cím a gép hozzájuk férni. Győződjön meg arról, hogy az önálló üzemeltetett integrációs futásidejű gép IP-címe szerepel az engedélyezési listán / megfelelően konfigurálva a tűzfalon.

A következő felhőalapú adattároló önálló üzemeltetett integrációs futásidejű gép IP-cím engedélyezése szükséges. Alapértelmezés szerint ezek adattárolókhoz némelyike nem követelheti meg az IP-címet az engedélyezett. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés:** lehet önálló központi integrációs futásidejű oszthatók meg különböző adat-előállítók?
**Válasz:** még nem támogatott ez a szolgáltatás. Folyamatosan dolgozunk a rajta.

**Kérdés:** Mik az önálló üzemeltetett integrációs futásidejű működéséhez port követelményei?
**Válasz:** az önálló üzemeltetett integrációs futásidejű létesít kapcsolatot HTTP-alapú internet megnyitásához. A **443-as és a 80-as kimenő portok** önálló üzemeltetett integrációs futási időben, a kapcsolat létrehozásához kell megnyitni. Nyissa meg **bejövő Port 8050** csak a számítógép szintjén (nem a vállalati tűzfal szintjén) hitelesítőadat-kezelő alkalmazáshoz. Azure SQL Database vagy az Azure SQL Data Warehouse használata, mivel a(z) forrás / cél, akkor meg kell nyitnia **1433** portot is. További információkért lásd: [tűzfal-konfiguráció, és az engedélyezett IP-címek](#firewall-configurations-and-whitelisting-ip-address-of gateway) szakasz. 


## <a name="next-steps"></a>Következő lépések
A másolási tevékenység teljesítményével kapcsolatos információkért lásd: [másolása tevékenység teljesítmény- és hangolási útmutató](copy-activity-performance.md).

 
