---
title: Biztonsági szempontok az Azure Data Factory adatok áttelepítéséről – |} Microsoft Docs
description: További információk a biztonságossá tétele az Azure Data Factory adatátvitelt jelölik.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 222558a6596c676034e52812d3b2dd0c77e1466b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046901"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Az Azure Data Factory - adatátvitelt jelölik a kapcsolódó biztonsági szempontok

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [adatok mozgása biztonsági szempontok az adat-előállító](../data-movement-security-considerations.md).

## <a name="introduction"></a>Bevezetés
Ez a cikk ismerteti az alapvető biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme. Az Azure Data Factory-kezelési források az Azure biztonsági infrastruktúra épül, és használja az Azure által kínált minden lehetséges biztonsági intézkedéseket.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](data-factory-create-pipelines.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok találhatók a régióban, ahol az adat-előállító létrehozása történt. 

Annak ellenére, hogy a Data Factory érhető el csak **USA nyugati régiója**, **USA keleti régiója**, és **Észak-Európa** régiókban, az adatátviteli szolgáltatás érhető el [globálisan a több régiók](data-factory-data-movement-activities.md#global). Data Factory szolgáltatásnak biztosítja, hogy adatokat nem hagynak egy földrajzi terület vagy régió kivéve, ha explicit módon utasíthatja a szolgáltatás egy másik régióban használandó, ha az adatátviteli szolgáltatás még nincs telepítve a régióba. 

Az Azure Data Factory maga nem tárol kivéve a társított szolgáltatás hitelesítő adatait felhőalapú adattároló, tanúsítványok használata titkosított adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Azure Data Factory használatával adatmozgás lett **hitelesített** esetében:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA CSILLAG](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Ha Ön Azure megfelelőségét, és hogyan Azure biztosítja a saját infrastruktúra iránt érdeklődik, keresse fel a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Ez a cikk a Microsoft biztonsági szempontok a következő két adatelérési mozgását esetekben tekintse át: 

- **Felhő használata estén**-ebben a forgatókönyvben a forrás- és is nyilvánosan elérhető az interneten keresztül. Ezek közé tartozik a felügyelt tárolási felhőszolgáltatások pl. Azure Storage Azure SQL Data Warehouse, az Azure SQL Database, az Azure Data Lake Store, Amazon S3, Amazon Redshift, például a Salesforce szolgáltatott szoftver szolgáltatásokat, és a webes protokollok-pl.: FTP- és OData. A támogatott adatforrások teljes listáját megtalálhatja [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hibrid forgatókönyvek**– ebben a forgatókönyvben a cél- és a tűzfal mögött, vagy egy helyszíni vállalati hálózaton vagy az adatok belül tároló egy magánhálózaton / virtuális hálózatot (általában a forrás), és nincs nyilvánosan elérhető. Adatbázis-kiszolgálóin futó virtuális gépek is alá ebben a forgatókönyvben.

## <a name="cloud-scenarios"></a>Felhő forgatókönyvek
### <a name="securing-data-store-credentials"></a>Biztonságossá tétele adatok adattárolóhoz használandó hitelesítő adatok
Az Azure Data Factory védi az adatokat adattárolóhoz használandó hitelesítő adatok szerint **titkosított** őket a **tanúsítványokat a Microsoft által felügyelt**. Ezek a tanúsítványok legyenek-e elforgatva minden **kétéves** (amely tartalmazza a tanúsítvány megújítása és a hitelesítő adatok áttelepítését). A titkosított hitelesítő adatokat biztonságosan vannak tárolva egy **Azure Storage Azure Data Factory szolgáltatások által kezelt**. Azure Storage biztonsággal kapcsolatos további információkért tekintse meg a [Azure Storage biztonsági áttekintése](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhő adattár támogatja a HTTPS vagy a TLS, adat-előállítóban adatátviteli szolgáltatások közötti minden adatátvitel és felhőalapú adattároló HTTPS- vagy TLS biztonságos csatornán keresztül.

> [!NOTE]
> Az összes kapcsolat **Azure SQL Database** és **Azure SQL Data Warehouse** mindig titkosítás (SSL/TLS) közben az átvitel során, illetve onnan az adatbázis. Egy folyamatot, egy JSON-szerkesztővel készítése, során adja hozzá a **titkosítási** tulajdonság, majd állítsa be **igaz** a a **kapcsolati karakterlánc**. Ha a [másolása varázsló](data-factory-azure-copy-wizard.md), a varázsló alapértelmezés szerint állítja ezt a tulajdonságot. A **Azure Storage**, használhat **HTTPS** a kapcsolati karakterláncban.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adatokat inaktív adatok titkosítása támogatási tárolja. Javasoljuk, hogy engedélyezze ezeket adattárolókhoz adatok titkosítási mechanizmus. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transzparens Data Encryption (TDE) az Azure SQL Data Warehouse elősegíti a valós idejű titkosítási és visszafejtési az adatok aktívan elvégzésével kártevő szándékú tevékenységek fenyegetés elleni védelem. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [az SQL Data Warehouse adatbázis védelme](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL adatbázis is támogatja a transzparens adattitkosítás (TDE), amely segít a valós idejű titkosítási és visszafejtési az adatok anélkül, hogy az alkalmazást módosítani kellene elvégzésével kártevő szándékú tevékenységek fenyegetés elleni védelem. Ez a viselkedés az transzparens az ügyfélnek. További információkért lásd: [átlátható adattitkosítást az Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store is biztosít a fiókban tárolt adatok titkosítása. Ha engedélyezve van, a Data Lake store automatikusan megőrzése előtt titkosítja az adatokat, és lekérése, így átlátható az ügyfélnek, az adatok elérése előtt visszafejti. További információkért lásd: [az Azure Data Lake Store biztonsági](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Az Azure Blob Storage és az Azure Table Storage
Az Azure Blob Storage és az Azure Table storage támogatja a Storage szolgáltatás titkosítási (SSE), mely automatikusan Storage megőrzése előtt titkosítja az adatokat, és lekérése előtt visszafejti. További információkért lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 inaktív adatok titkosítása ügyfél és kiszolgáló egyaránt támogatja. További információkért lásd: [védelmének használatával adattitkosítás](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Jelenleg adat-előállító nem támogatja a Amazon S3 virtuális magánfelhőbe (VPC) belül.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift támogatja a fürt titkosítást az inaktív adatok. További információkért lásd: [Amazon Redshift az adatbázis-titkosítás](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Adat-előállító jelenleg nem támogatja a Amazon Redshift egy VPC belül. 

#### <a name="salesforce"></a>Salesforce
Salesforce Shield Platform titkosítási, amely lehetővé teszi az összes olyan fájlok, mellékleteket, egyéni mezők titkosítási támogatja. További információkért lásd: [ismertetése a Web Server OAuth hitelesítési Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hibrid környezetekben (az adatkezelési átjáró használatával)
Hibrid forgatókönyvekben az adatkezelési átjáró telepíthető egy helyszíni hálózat vagy egy virtuális hálózatot (Azure) vagy virtuális magánfelhőt (Amazon) szükséges. Az átjáró való hozzáférést, a helyi képesnek kell lennie. Az átjáró kapcsolatos további információkért lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md). 

![Adatkezelési átjáró adatcsatorna](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

A **parancscsatornát** adatátviteli szolgáltatások a Data Factory és az adatkezelési átjáró közötti kommunikáció lehetővé teszi. A kommunikációt a tevékenységével kapcsolatos adatokat tartalmazza. Az adatcsatorna szolgál az adatok átviteléhez a helyszíni adattárolókhoz és felhő adattárolók között.    

### <a name="on-premises-data-store-credentials"></a>A helyszíni adatok adattárolóhoz használandó hitelesítő adatok
A hitelesítő adatokat a helyszíni adattárolókhoz helyileg tárolja (nem a felhőben). Három különböző módon állítható. 

- Használatával **egyszerű szöveges** (kevésbé biztonságos) HTTPS Azure-portálon keresztül / másolása varázsló. A rendszer az átadott hitelesítő adatokat egyszerű szöveges a helyszíni átjáró.
- Használatával **másolása varázsló JavaScript titkosítás kódtárat**.
- Használatával **kattintson-alapú hitelesítő adatokat kezelő alkalmazások után**. Kattintson a-alkalmazás végrehajtja a helyszíni gépen, amely hozzáfér az átjáró és az adattár hitelesítő adatok beállítása után. Ez a beállítás és a következő a legbiztonságosabb lehetőség áll. A hitelesítő alkalmazással, alapértelmezés szerint a portot használja 8050 a gépen átjáró a biztonságos kommunikáció érdekében.  
- Használjon [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) PowerShell-parancsmag hitelesítő adatok titkosításához. A parancsmag a tanúsítványt használja, hogy az átjáró a hitelesítő adatok titkosításához használatára van konfigurálva. Ez a parancsmag által visszaadott titkosított hitelesítő adatokat használja, és hozzá kell adnia **EncryptedCredential** eleme a **connectionString** a JSON-fájl, amelyet a [ Új AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) parancsmag vagy a JSON-részlet a Data Factory Editor a portálon. Ezt a lehetőséget, majd kattintson az – Ha az alkalmazás a legbiztonságosabb beállításokat. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript titkosítás library-alapú titkosítás
Adatok adattárolóhoz használandó hitelesítő adatok használatával titkosíthatja [JavaScript titkosítás könyvtár](https://www.microsoft.com/download/details.aspx?id=52439) a a [másolása varázsló](data-factory-copy-wizard.md). Ha ezt a lehetőséget választja, a varázsló lekéri az átjáró nyilvános kulcsát, és segítségével titkosítja az adatokat adattárolóhoz használandó hitelesítő adatok. A hitelesítő adatokat az átjáró számítógépe visszafejteni, és a Windows által védett [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Támogatott böngészők:** IE8, az IE9, IE10, IE11, Microsoft Edge és a legújabb Firefox, Chrome, Opera, Safari böngésző. 

#### <a name="click-once-credentials-manager-app"></a>Kattintson a-egyszer hitelesítő alkalmazással
Is elindíthatja, kattintson a-credential manager alkalmazást, az Azure portál vagy másolási varázsló alapú folyamatok készítésekor után. Ez az alkalmazás biztosítja, hogy hitelesítő adatok nem kerülnek egyszerű szövegként a hálózaton keresztül. Alapértelmezés szerint azt a portot használja **8050** a gépen átjáró a biztonságos kommunikáció érdekében. Ha szükséges, a port megváltoztatható.  
  
![Az átjáró HTTPS-port](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Jelenleg az adatkezelési átjáró használja egyetlen **tanúsítvány**. Ez a tanúsítvány jön létre az átjáró telepítése során (az adatkezelési átjáró 2016. November után létrehozott és a verzió 2.4.xxxx.x vagy újabb). Ezt a tanúsítványt lecserélheti a saját SSL/TLS-tanúsítványt. Ezt a tanúsítványt használják a kattintson-egyszer hitelesítőadat-kezelő alkalmazáshoz való biztonságos kapcsolódás az átjáró számítógépe adatok adattárolóhoz használandó hitelesítő adatok beállításához. Tárolja az adatokat adattárolóhoz használandó hitelesítő adatok biztonságos helyszíni a Windows segítségével [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) átjáró a gépen. 

> [!NOTE]
> Régebbi November 2016 előtt vagy a verziójával 2.3.xxxx.x telepített átjárók továbbra is titkosítja, és a felhőben tárolt hitelesítő adatokat. Akkor is, ha az átjárót a legújabb verzióra frissít, a hitelesítő adatok nem települnek át egy a helyi számítógépen    
  
| Az átjáró verziójának (létrehozásakor) | Tárolt hitelesítő adatok | Hitelesítőadat-titkosítási / biztonsági | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | A felhőben | Titkosított (eltérő hitelesítő adatok manager alkalmazás által használt) tanúsítvánnyal | 
| > = 2.4.xxxx.x | A helyszíni | DPAPI védi | 
  

### <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Biztonságos csatornán keresztül van minden adatátviteli **HTTPS** és **TCP-n keresztül TLS** -átjárójának érdekében az Azure-szolgáltatásokkal folytatott kommunikáció során.
 
Is [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Express Route](../../expressroute/expressroute-introduction.md) további védelméhez a helyszíni hálózat és az Azure közötti kommunikációs csatornát.

Virtuális hálózat a logikai reprezentációjává a hálózaton, a felhőben. Kapcsolódás egy a helyszíni hálózat az Azure virtuális hálózathoz (VNet) IPSec VPN-(pont-pont) vagy Express Route (magánhálózati társviszony-létesítés) beállítása     

A következő táblázat összefoglalja a hálózati és az átjáró javaslatok alapján különböző kombinációival hibrid adatátvitelt jelölik a forrás és cél helyét.

| Forrás | Cél | Hálózati konfiguráció | Átjáró beállítása |
| ------ | ----------- | --------------------- | ------------- | 
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | IPSec VPN (pont – hely vagy pont-pont) | Átjáró lehet telepítve a helyi vagy egy Azure virtuális gép (VM) virtuális hálózat | 
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | Az ExpressRoute (magánhálózati társviszony-létesítés) | Átjáró lehet telepítve a helyi vagy egy Azure virtuális gépen a virtuális hálózat | 
| Helyszíni követelmények | Azure-alapú szolgáltatások, amelyek egy nyilvános végpontot | Az ExpressRoute (nyilvános Társviszony) | Átjáró helyszíni telepítve kell lennie. | 

A következő ábrákon az adatkezelési átjáró használati adatok egy helyi adatbázist és az Azure-szolgáltatások expressroute- és IPSec VPN-(a virtuális hálózattal) használatával közötti áthelyezésére láthatók:

**Express Route:**
 
![Használjon Expressroute-átjáróval](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN-átjáróval](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Tűzfalbeállítások és átjáró engedélyezett IP-címe

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyi/InPrivate-hálózati tűzfal követelményei  
Vállalati a **vállalati tűzfal** futtat a szervezet a központi útválasztón. Emellett a **Windows tűzfal** démonként fut, amelyen az átjáró telepítve van a helyi számítógépen. 

A következő táblázat **kimenő port** és a tartomány vonatkozó követelményeket a **vállalati tűzfal**.

| Tartománynevek | Kimenő portok | Leírás |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Az átjáró adatátviteli szolgáltatások adat-előállítóban való kapcsolódáshoz szükséges |
| `*.core.windows.net` | 443 | Az átjáró által használt Azure-Tárfiók csatlakozni, ha használja a [másolási előkészített](data-factory-copy-activity-performance.md#staged-copy) szolgáltatás. | 
| `*.frontend.clouddatahub.net` | 443 | Az Azure Data Factory szolgáltatásnak való kapcsolódáshoz szükséges a hálózati címfordítást. | 
| `*.database.windows.net` | 1433   | (Nem kötelező) szükséges, ha a cél az Azure SQL Database az Azure SQL Data Warehouse- /. Funkcióival előkészített adatok másolása az Azure SQL Database vagy az Azure SQL Data Warehouse az 1433-as port megnyitása nélkül. | 
| `*.azuredatalakestore.net` | 443 | (Nem kötelező) szükséges, ha a célként megadott Azure Data Lake store | 

> [!NOTE] 
> Lehet, hogy a portok kezelése / megfelelő adatforrások szükség szerint szinten engedélyezett tartomány a vállalati tűzfalon. Ez a táblázat csak akkor használja az Azure SQL Database, az Azure SQL Data Warehouse, az Azure Data Lake Store példaként.   

A következő táblázat **port bejövő** követelményei a **windows tűzfal**.

| Bejövő portok | Leírás | 
| ------------- | ----------- | 
| 8050 (TCP) | Biztonságosan beállítása a helyszíni adattárolókhoz hitelesítő adatait az átjárón a hitelesítő adatok manager alkalmazáshoz szükséges. | 

![Átjáró portokra vonatkozó követelmények](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurációk / engedélyezett az adatok tárolásához
Egyes adatokat tárolja, a felhőben is szükség lehet az engedélyezett IP-cím a gép hozzájuk férni. Győződjön meg arról, hogy az átjáró számítógépe IP-címe szerepel az engedélyezési listán / megfelelően konfigurálva a tűzfalon.

A következő felhőalapú adattároló megkövetelése az engedélyezett IP-címet az átjárót működtető gépen. Alapértelmezés szerint ezek adattárolókhoz némelyike nem követelheti meg az IP-címet az engedélyezett. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés:** az átjáró meg lehet osztani összes különböző adat-előállítók?
**Válasz:** még nem támogatott ez a szolgáltatás. Folyamatosan dolgozunk a rajta.

**Kérdés:** Mik az átjáró működéséhez port követelményei?
**Válasz:** átjáró hoz meg kapcsolatok HTTP-alapú internet megnyitásához. A **443-as és a 80-as kimenő portok** átjáró a kapcsolat létrehozásához kell megnyitni. Nyissa meg **bejövő Port 8050** csak a számítógép szintjén (nem a vállalati tűzfal szintjén) hitelesítőadat-kezelő alkalmazáshoz. Azure SQL Database vagy az Azure SQL Data Warehouse használata, mivel a(z) forrás / cél, akkor meg kell nyitnia **1433** portot is. További információkért lásd: [tűzfal-konfiguráció, és az engedélyezett IP-címek](#firewall-configurations-and-whitelisting-ip-address-of gateway) szakasz. 

**Kérdés:** Mik átjáró tanúsítványokkal kapcsolatos követelményei?
**Válasz:** aktuális átjáró biztonságosan az adatokat tároló hitelesítő adatok beállítása a hitelesítő adatok manager alkalmazás által használt tanúsítványt igényel. Ezt a tanúsítványt az önaláírt tanúsítvány létrehozása és konfigurálása az átjáró a telepítővel. Használhatja a saját TLS / SSL-tanúsítványt helyette. További információkért lásd: [kattintson-egyszer hitelesítőadat-kezelő alkalmazás](#click-once-credentials-manager-app) szakasz. 

## <a name="next-steps"></a>További lépések
A másolási tevékenység teljesítményével kapcsolatos információkért lásd: [másolása tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md).

 
