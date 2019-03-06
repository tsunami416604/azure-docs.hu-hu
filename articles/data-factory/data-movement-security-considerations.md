---
title: Biztonsági szempontok az Azure Data Factoryban |} A Microsoft Docs
description: Ismerteti az alapvető biztonsági infrastruktúra, amellyel az Azure Data Factoryban adatátviteli szolgáltatások védelme érdekében az adatok.
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
ms.openlocfilehash: a996703f3719c2be90851241c1fe23c89f24e606
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447948"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factoryban történő futtatásának biztonsági szempontjai
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [1-es verzió](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuális verzió](data-movement-security-considerations.md)

Ez a cikk ismerteti, amelyek adatátviteli szolgáltatások az Azure Data Factory segítségével biztonságos alapvető biztonsági infrastruktúra adatait. Data Factory felügyeleti erőforrás az Azure biztonsági infrastruktúra épül, és használja az Azure-ban elérhető összes lehetséges biztonsági intézkedések.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok a régió, ahol az adat-előállító létrejött-e lennie. 

Annak ellenére, hogy a Data Factory csak régiókban érhető el néhány, az adatátviteli szolgáltatás van [elérhető globálisan](concepts-integration-runtime.md#integration-runtime-location) adatok megfelelőség biztosítása érdekében hatékonyságát, és csökkentheti a hálózati kimenő forgalom költségeit. 

Az Azure Data Factory nem tárol semmilyen adatot, kivéve a felhőalapú adattárolók, amelyek tanúsítványok segítségével lettek titkosítva társított szolgáltatás hitelesítő adatait. A Data Factory létrehozása adatvezérelt munkafolyamatok, amelyekkel előkészíthető a közötti adatmozgás [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats), és az adatok feldolgozása [számítási szolgáltatások](compute-linked-services.md) más régiókban, illetve a egy a helyszíni környezetben. Is figyelése és kezelése a munkafolyamatok SDK-k és az Azure Monitor használatával.

A Data Factory a hitelesített:
| **[CSA STAR-minősítés](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Ha érdeklik az Azure megfelelőségi, és hogyan védi az Azure a saját infrastruktúráját, látogasson el a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). A teljes lista az összes Azure-megfelelőségi ajánlatok ellenőrzése – http://aka.ms/AzureCompliance.

Ez a cikk a következő két adattovábbítási esetekben biztonsági szempontok vizsgáljuk meg: 

- **Alkalmazási helyzetben**: Ebben a forgatókönyvben a forrás- és a célkiszolgálón is az interneten keresztül nyilvánosan elérhető. Ezek közé tartozik például az Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, az Amazon S3, Amazon Redshift, SaaS-szolgáltatásokhoz, például a Salesforce és a webes protokollok, mint például az FTP- és OData felügyelt felhőalapú tárolási szolgáltatásokról. Keresse meg a támogatott adatforrások teljes listája [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hibrid forgatókönyv**: Ebben a forgatókönyvben a forrás- vagy a cél van egy tűzfal mögött található, vagy egy helyszíni vállalati hálózaton belül. Vagy az adattár egy saját hálózaton vagy virtuális hálózaton (általában a forrás) és nem nyilvánosan elérhető-e. Ebben a forgatókönyvben is tartozik a virtuális gépeken futtatott adatbázis-kiszolgálók.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek

### <a name="securing-data-store-credentials"></a>Biztonságossá tétele adattár hitelesítő adatait

- **Store titkosított hitelesítő adatokat egy Azure Data Factory felügyelt áruházban**. A Data Factory segítségével a tároló hitelesítő adatainak védelme a Microsoft által kezelt tanúsítványokkal titkosításával. Ezeket a tanúsítványokat (amely tartalmazza a tanúsítvány megújításához, és a hitelesítő adatok áttelepítését) két évente vannak elforgatva. Azure Data Factory szolgáltatás által felügyelt Azure storage-fiókkal biztonságosan tárolja a titkosított hitelesítő adatokat. Azure Storage-biztonsággal kapcsolatos további információkért lásd: [Azure Storage biztonsági áttekintése](../security/security-storage-overview.md).
- **Hitelesítő adatok Store az Azure Key Vaultban**. Az adattár hitelesítő adat is tárolhat [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). A Data Factory a hitelesítő adatokat a tevékenységek végrehajtása során kérdezi le. További információkért lásd: [Store hitelesítő adatok az Azure Key Vaultban](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhőbeli adattárban támogatja a HTTPS vagy a TLS, adat-előállítóban adatátviteli szolgáltatások közötti minden adatátvitel és egy felhőalapú adattár HTTPS vagy a TLS biztonságos csatornán keresztül.

> [!NOTE]
> Azure SQL Database és az Azure SQL Data warehouse-ba irányuló összes kapcsolatot igényelnek a titkosítás (SSL/TLS), miközben az átvitel során, illetve onnan az adatbázis az adatokat. Ha egy folyamat JSON használatával Ön szerzői, a titkosítási tulajdonság hozzáadása, illetve beállíthatja **igaz** a kapcsolati karakterláncban. Az Azure Storage esetében használható **HTTPS** a kapcsolati karakterláncban.

> [!NOTE]
> Engedélyezése közben, amely adatokat helyez át az Oracle titkosítás az átvitel során kövesse az az alábbi beállításokat:
> 1. Az Oracle-kiszolgálón nyissa meg az Oracle speciális biztonsági (OAS), és adja meg a titkosítási beállításokat, mely támogatja a háromszoros DES-titkosítás (3DES) és Advanced Encryption Standard (AES), tekintse meg [Itt](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) részleteiről. Az ADF automatikusan egyezteti OAS Oracle-kapcsolat létesítéséhez konfigurálja egy használandó titkosítási módszer.
> 2. Az ADF, adhat hozzá, EncryptionMethod = 1 (a társított szolgáltatás), a kapcsolati karakterláncban. Ez a beállítás használja az SSL/TLS titkosítási módszert. Ennek kell az SSL titkosítási beállításainak letiltása OAS Oracle kiszolgálóoldali titkosítás ütközés elkerülése érdekében.

> [!NOTE]
> Használt verzió a TLS 1.2-es.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Néhány adatot tárol az inaktív adatok titkosításának támogatása. Azt javasoljuk, hogy engedélyezze az adatok titkosítási mechanizmus ezeket olyan adattárakban. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transzparens adattitkosítás (TDE) az Azure SQL Data Warehouse segítségével elleni védelemhez kártevő szándékú tevékenységek által valós időben titkosítja és visszafejtése az inaktív adatokat. Ez a viselkedés az átlátható az ügyfélnek. További információkért lásd: [biztonságossá egy adatbázis az SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database támogatja a transzparens adattitkosítás (TDE) köszönhetően a amely védelmet nyújt a kártevő szándékú tevékenységek fenyegetés elleni valós idejű titkosítási és visszafejtési az adatok nem igényel változtatásokat a kérelem végrehajtásával is. Ez a viselkedés az átlátható az ügyfélnek. További információkért lásd: [transzparens adattitkosítás az SQL Database és az adatraktár](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Az Azure Data Lake Store is biztosít a fiókban tárolt adatok titkosítását. Ha engedélyezve van, a Data Lake Store automatikusan megőrzése előtt titkosítja az adatokat, és visszafejti előtt lekéréséhez, így átlátható az ügyfélnek, amely hozzáfér az adatok. További információkért lásd: [biztonság az Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Az Azure Blob storage és Azure Table storage
Az Azure Blob storage és Azure Table storage támogatja a Storage Service Encryption (SSE), amely automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 támogatja az ügyfél- és az inaktív adatok titkosítását. További információkért lásd: [védelme használatával adattitkosítás](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift fürt titkosítási támogatja az inaktív adatok. További információkért lásd: [Amazon Redshift az adatbázis-titkosítás](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce Shield Platform titkosítás, amely lehetővé teszi az összes fájlt, a mellékletek és egyéni mezők titkosítási támogatja. További információkért lásd: [ismertetése a Web Server OAuth hitelesítési folyamatát](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>A hibrid forgatókönyvek
Hibrid forgatókönyvek igényelnek, saját üzemeltetésű integrációs modult egy helyi hálózaton (Azure) a virtuális hálózatokon belüli, vagy virtuális magánfelhőbe (Amazon) belül kell telepíteni. A saját üzemeltetésű integrációs modult a helyi adattárak eléréséhez képesnek kell lennie. Saját üzemeltetésű integrációs modul kapcsolatos további információkért lásd: [létrehozása és konfigurálása saját üzemeltetésű integrációs modul](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![saját üzemeltetésű integrációs modul csatornák](media/data-movement-security-considerations/data-management-gateway-channels.png)

A parancscsatornát adatátviteli szolgáltatások a Data Factory és a saját üzemeltetésű integrációs modul közötti kommunikáció lehetővé teszi. A kommunikáció a tevékenységével kapcsolatos adatokat tartalmaz. Az adatcsatorna szolgál az adatok átviteléhez a helyszíni és felhőalapú adattárak között.    

### <a name="on-premises-data-store-credentials"></a>A helyszíni adattár hitelesítő adatait
A hitelesítő adatokat a helyszíni adattárakban mindig titkosítva, és tárolja. Azok, akár a saját üzemeltetésű integrációs modult tartalmazó számítógépen helyben tárolt vagy az Azure Data Factory által felügyelt storage (ugyanúgy, mint a felhőbeli adattár hitelesítő adatait) tárolja. 

- **Hitelesítő adatok helyben Store**. Ha azt szeretné, titkosítására és a saját üzemeltetésű integrációs modulban helyileg tárolja a hitelesítő adatait, hajtsa végre a lépéseit [adattárak az Azure Data Factory a helyszíni hitelesítő adatok titkosításához](encrypt-credentials-self-hosted-integration-runtime.md). Összekötők támogatják ezt a beállítást. A saját üzemeltetésű integrációs modult használja a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) titkosíthatja a bizalmas adatokat és hitelesítő adatokat. 

   Használja a **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag társított szolgáltatás hitelesítő adatai és a hivatkozott szolgáltatásban található bizalmas adatainak titkosításához. Ezután használhatja a visszaadott JSON-(az a **EncryptedCredential** elemet a kapcsolati karakterlánc) használatával egy társított szolgáltatás létrehozásához a **Set-AzDataFactoryV2LinkedService** parancsmagot.  

- **Az Azure Data Factory által felügyelt tárolási Store**. Ha közvetlenül a **Set-AzDataFactoryV2LinkedService** parancsmag és a kapcsolati sztringeket, és hitelesítő adatokat beágyazva a JSON-fájlban, a társított szolgáltatás titkosított, az Azure Data Factory által felügyelt storage tárolja. A bizalmas adatok továbbra is titkosítja a tanúsítványt, és a Microsoft felügyeli ezeket a tanúsítványokat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modult a társított szolgáltatás titkosításához használt portok
Alapértelmezés szerint a PowerShell 8050 portot használja a gépen a saját üzemeltetésű integrációs modul a biztonságos kommunikáció érdekében. Ha szükséges, a port megváltoztatható.  

![Az átjáró HTTPS-portja](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Zajló teljes adatforgalom biztonságos csatornán keresztül HTTPS és a TLS man-in-the-middle támadások megelőzése érdekében az Azure-szolgáltatásokkal folytatott kommunikáció során a TCP-n keresztül.

Is [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Azure ExpressRoute](../expressroute/expressroute-introduction.md) további védelméhez a kommunikációs csatornát a helyszíni hálózat és az Azure között.

Az Azure virtuális hálózat a felhőbeli hálózatának logikai megfelelője. A virtuális hálózat egy helyszíni hálózat csatlakozhat IPSec VPN (site-to-site) vagy az ExpressRoute (privát társviszony-létesítés) beállításával.    

A következő táblázat összefoglalja a hálózaton, és eltérő kombinációja a forrás- és helyek az adatok hibrid áthelyezése alapján saját üzemeltetésű integrációs modul konfigurációs javaslatokat.

| Forrás      | Cél                              | Hálózati konfiguráció                    | Integrációs modul telepítése                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | IPSec VPN (pont – hely vagy site-to-site) | A saját üzemeltetésű integrációs modult is lehet a helyszínen telepített vagy a virtuális hálózat egy Azure virtuális gépen. |
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | ExpressRoute (privát társviszony-létesítés)           | A saját üzemeltetésű integrációs modult is lehet a helyszínen telepített vagy a virtuális hálózat egy Azure virtuális gépen. |
| Helyszíni követelmények | Egy nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (nyilvános társviszony-létesítés)            | A saját üzemeltetésű integrációs modult helyszíni telepítve kell lennie. |

Az alábbi képeken az adatok áthelyezése egy helyszíni adatbázisból és az Azure-szolgáltatások között ExpressRoute- és IPSec VPN (az Azure Virtual Network) a saját üzemeltetésű integrációs modul használata:

**ExpressRoute**

![Használja az ExpressRoute-átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN-átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Tűzfal-konfigurációk és engedélyezési IP-címek

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyi/InPrivate-hálózat tűzfalkövetelményei  
Vállalati vállalati tűzfal fut, a szervezet a központi útválasztón. Windows tűzfal démonként fut, a helyi gépen, amely a saját üzemeltetésű integrációs modul telepítve van. 

A következő táblázat tartalmazza a kimenő port és tartományokra vonatkozó követelmények a vállalati tűzfal:

| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul adatátviteli szolgáltatások a Data Factoryban való kapcsolódáshoz szükséges. |
| `*.frontend.clouddatahub.net` | 443            | A Data Factory szolgáltatás kapcsolódni a saját üzemeltetésű integrációs modul szükséges. |
| `download.microsoft.com`    | 443            | A saját üzemeltetésű integrációs modult a frissítések letöltéséhez szükséges. Ha letiltotta az automatikus frissítés majd, kihagyhatja ezt. |
| `*.core.windows.net`          | 443            | Csatlakozhat a saját üzemeltetésű integrációs modul által az Azure storage-fiók használata esetén a [szakaszos Másolás](copy-activity-performance.md#staged-copy) funkció. |
| `*.database.windows.net`      | 1433           | (Nem kötelező) Szükséges, vagy az Azure SQL Database vagy Azure SQL Data Warehouse másolásakor. A szakaszos másolás funkció segítségével az adatok másolása az Azure SQL Database vagy Azure SQL Data Warehouse az 1433-as port megnyitása nélkül. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Nem kötelező) Szükséges, vagy az Azure Data Lake Store másolásakor. |

> [!NOTE] 
> Előfordulhat, hogy portok vagy engedélyezési tartományok szinten vállalati tűzfalon, szükség szerint a megfelelő adatforrások kezelhetők. Ez a tábla használja Azure SQL Database, Azure SQL Data Warehouse és az Azure Data Lake Store csak példaként.   

A következő táblázat tartalmazza a Windows tűzfal bejövő port követelményei:

| Bejövő portok | Leírás                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | A PowerShell-parancsmag titkosítás szükséges leírtak szerint [adattárak az Azure Data Factory a helyszíni hitelesítő adatok titkosításához](encrypt-credentials-self-hosted-integration-runtime.md), és a hitelesítőadat-kezelő alkalmazást biztonságosan a helyszíni adattárak hitelesítő adatok beállítása a saját üzemeltetésű integrációs modulban. |

![Átjáró-port követelményei](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfigurációk és engedélyezés az adattárak
Néhány adattár a felhőben is megkövetelheti, hogy Ön engedélyezett a tároló eléréséhez a gép IP-címét. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modult tartalmazó számítógépen IP-címe szerepel az engedélyezési listán, vagy megfelelően konfigurálva a tűzfal.

A következő felhőalapú adattárak van szükség, hogy Ön engedélyezett IP-címét a saját üzemeltetésű integrációs modult tartalmazó számítógépen. Alapértelmezés szerint ezek adattárak némelyike nem igénylik az engedélyezés. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Különböző adat-előállítók között megosztható a saját üzemeltetésű integrációs modul?**

Ez a funkció még nem támogatott. Aktívan dolgozunk rajta.

**Mik azok a saját üzemeltetésű integrációs modul működjön a port követelményei?**

A saját üzemeltetésű integrációs modul lehetővé teszi a HTTP-alapú kapcsolatokat az internet eléréséhez. A saját üzemeltetésű integrációs modult, hogy ezt a kapcsolatot a 443-as kimenő portokat kell megnyitni. Nyissa meg a bejövő portot 8050 csak a gépek szintjén (nem a vállalati tűzfalon szint) a hitelesítőadat-kezelő alkalmazást. Ha az Azure SQL Database vagy Azure SQL Data Warehouse a forrás vagy cél használják, meg kell nyitnia az 1433-as portot is. További információkért lásd: a [tűzfal-konfigurációk és IP-címek engedélyezési](#firewall-configurations-and-whitelisting-ip-address-of-gateway) szakaszban. 


## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység teljesítményével kapcsolatos információkért lásd: [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](copy-activity-performance.md).

 
