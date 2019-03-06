---
title: Adatok áthelyezése az Azure Data Factoryban történő futtatásának biztonsági szempontjai |} A Microsoft Docs
description: További információ biztonságossá tétele az adatok Azure Data Factoryval történő áthelyezését.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0d601df7914b7280de4b3c16c8b00c96cb5427e4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435984"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Az Azure Data Factory - adatáthelyezés biztonsági szempontjai

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [az adatáthelyezés biztonsági szempontjai a Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Bevezetés
Ez a cikk ismerteti az alapvető biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme. Azure Data Factory-felügyeleti erőforrások az Azure biztonsági infrastruktúra épül, és használja az Azure-ban elérhető összes lehetséges biztonsági intézkedések.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](data-factory-create-pipelines.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok a régió, ahol az adat-előállító létrejött-e lennie. 

Bár a Data Factory szolgáltatás elérhető csak **USA nyugati RÉGIÓJA**, **USA keleti RÉGIÓJA**, és **Észak-Európa** régióban, az adatátviteli szolgáltatás érhető el [a globálisan számos régióban](data-factory-data-movement-activities.md#global). A Data Factory szolgáltatás biztosítja, hogy nem kerülnek egy földrajzi területen vagy régióban, kivéve, ha explicit módon utasíthatja a szolgáltatás egy másik régiót használni, ha az adatátviteli szolgáltatás van még nem telepítették az adott régióban. 

Az Azure Data Factory magát nem tárol adatokat a felhőalapú adattárolók, amelyek tanúsítványok segítségével titkosítja a társított szolgáltatás hitelesítő adatainak kivételével. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Adatok áthelyezése az Azure Data Factory használatával lett **certified** számára:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Ha érdekli az Azure megfelelőségi, és hogyan védi az Azure a saját infrastruktúráját, keresse fel a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Ez a cikk a következő két adattovábbítási esetekben biztonsági szempontok vizsgáljuk meg: 

- **Alkalmazási helyzetben**– ebben a forgatókönyvben a forrás- és program interneten keresztül nyilvánosan elérhető. Ezek közé tartozik például az Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, az Amazon S3, Amazon Redshift, SaaS-szolgáltatásokhoz, például a Salesforce és a webes protokollok, mint például az FTP- és OData felügyelt felhőalapú tárolási szolgáltatásokról. Támogatott adatforrások teljes listáját megtalálhatja [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hibrid forgatókönyv**– ebben a forgatókönyvben a cél- és van egy tűzfal mögött található, vagy egy helyszíni vállalati hálózat vagy az adatokat tároló van egy magánhálózaton lévő / virtuális hálózat (általában a forrás), és nem nyilvánosan elérhető-e. Ebben a forgatókönyvben is tartozik a virtuális gépeken futtatott adatbázis-kiszolgálók.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek
### <a name="securing-data-store-credentials"></a>Biztonságossá tétele adattár hitelesítő adatait
Az Azure Data Factory védi a tároló hitelesítő adatainak által **titkosítása** azokat használatával **tanúsítványokat a Microsoft által felügyelt**. Ezek a tanúsítványok vannak-e forgatni minden **két évig** (amely tartalmazza a tanúsítvány megújítása és a hitelesítő adatok migrálása). Ezek a titkosított hitelesítő adatok biztonságosan tárolódnak az **Azure Storage szolgáltatások az Azure Data Factory által felügyelt**. Azure Storage-biztonsággal kapcsolatos további információkért tekintse meg [Azure Storage biztonsági áttekintése](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás átvitel közben
Ha a felhőbeli adattárban támogatja a HTTPS vagy a TLS, adat-előállítóban adatátviteli szolgáltatások közötti minden adatátvitel és egy felhőalapú adattár HTTPS vagy a TLS biztonságos csatornán keresztül.

> [!NOTE]
> Az összes kapcsolat **Azure SQL Database** és **Azure SQL Data Warehouse** mindig szükség van a titkosítás (SSL/TLS) adatokat átvitel közben, és az adatbázisból. Meggondolni egy folyamat egy JSON-szerkesztő használatával, adja hozzá a **titkosítási** tulajdonság, beállíthatja azt a **igaz** a a **kapcsolati karakterlánc**. Ha a [másolása varázsló](data-factory-azure-copy-wizard.md), a varázsló alapértelmezés szerint állítja ezt a tulajdonságot. A **Azure Storage**, használhat **HTTPS** a kapcsolati karakterláncban.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Néhány adatot tárol az inaktív adatok titkosításának támogatása. Javasoljuk, hogy engedélyezze ezeket adattárak adatok titkosítási mechanizmus. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transzparens adattitkosítás (TDE) az Azure SQL Data Warehouse segítségével valós időben titkosítja és az inaktív adatok visszafejtése a kártevő szándékú tevékenységek fenyegetés elleni védelme. Ez a viselkedés az átlátható az ügyfélnek. További információkért lásd: [biztonságossá egy adatbázis az SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database támogatja a transzparens adattitkosítás (TDE), ami segít a kártevő szándékú tevékenységek a fenyegetések ellen védelmet biztosító anélkül, hogy az alkalmazás módosításai az adatok valós idejű titkosítását és visszafejtését végrehajtásával is. Ez a viselkedés az átlátható az ügyfélnek. További információkért lásd: [az Azure SQL Database transzparens adattitkosítási](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Az Azure Data Lake store is biztosít a fiókban tárolt adatok titkosítását. Ha engedélyezve van, a Data Lake store automatikusan megőrzése előtt titkosítja az adatokat, és visszafejti őket a lekérés, így átlátható az ügyfélnek, az adatok elérése előtt. További információkért lásd: [biztonság az Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Az Azure Blob Storage és Azure Table Storage
Az Azure Blob Storage és Azure Table storage támogatja a Storage Service Encryption (SSE), amely automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 támogatja az ügyfél- és az inaktív adatok titkosítását. További információkért lásd: [védelme használatával adattitkosítás](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Jelenleg a Data Factory nem támogatja az Amazon S3 virtuális magánfelhő (VPC) belül.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift fürt titkosítási támogatja az inaktív adatok. További információkért lásd: [Amazon Redshift az adatbázis-titkosítás](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). A Data Factory jelenleg nem támogatja az Amazon Redshift-VPC belül. 

#### <a name="salesforce"></a>Salesforce
Salesforce Shield Platform titkosítás, amely lehetővé teszi, hogy a titkosítás az összes olyan fájlok, a mellékleteket, egyéni mezők támogatja. További információkért lásd: [ismertetése a Web Server OAuth hitelesítési folyamatát](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>A hibrid forgatókönyvek (adatkezelési átjáró segítségével)
Hibrid forgatókönyvek igényelnek, az adatkezelési átjáró egy helyszíni hálózat vagy egy virtuális hálózatot (Azure) vagy virtuális magánfelhőbe (Amazon) belül kell telepíteni. Az átjáró a helyi adattárak eléréséhez képesnek kell lennie. Az átjáró kapcsolatos további információkért lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md). 

![Data Management Gateway csatornák](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

A **parancscsatornát** adatátviteli szolgáltatások a Data Factory és az adatkezelési átjáró közötti kommunikáció lehetővé teszi. A kommunikáció a tevékenységével kapcsolatos adatokat tartalmaz. Az adatcsatorna szolgál az adatok átviteléhez a helyszíni és felhőalapú adattárak között.    

### <a name="on-premises-data-store-credentials"></a>A helyszíni adattár hitelesítő adatait
A rendszer helyben tárolja a hitelesítő adatokat a helyszíni adattárakban (nem a felhőben). Három különböző módon állítható. 

- Használatával **egyszerű szöveges** (kevésbé biztonságos) Azure Portal használatával HTTPS-kapcsolaton keresztül / másolás varázslójával. A helyszíni átjáró az egyszerű szöveges rendszer átadja a hitelesítő adatokat.
- Használatával **JavaScript titkosítás könyvtár a másolás varázsló**.
- Használatával **kattintson-alapú hitelesítő adatokat kezelő alkalmazás után**. Kattintson a – Miután az alkalmazás végrehajtja a helyszíni gépen, amely rendelkezik hozzáféréssel az átjáróhoz, és az adattár hitelesítő adatait. Ez a beállítás és a következő módon a legbiztonságosabb. A credential manager alkalmazást, alapértelmezés szerint a portot használja 8050 a gépen az átjárót a biztonságos kommunikáció érdekében.  
- Használat [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) hitelesítő adatainak titkosítása PowerShell-parancsmagot. A parancsmag a tanúsítványt használja, hogy az átjáró a hitelesítő adatok titkosításához használatára van konfigurálva. Ez a parancsmag által visszaadott titkosított hitelesítő adatokat, majd adja hozzá a **EncryptedCredential** eleme a **connectionString** a JSON-fájlban, amelyet a [ Új AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) parancsmag vagy a JSON-kódrészletben a Data Factory szerkesztőjében a portálon. Ezt a beállítást, és kattintson a – Ha az alkalmazás a legbiztonságosabb lehetőség. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript-titkosítás library-alapú titkosítás
Tár hitelesítő adatainak használatával titkosíthatók [titkosítás JavaScript-kódtár](https://www.microsoft.com/download/details.aspx?id=52439) származó a [másolása varázsló](data-factory-copy-wizard.md). Ha ezt a lehetőséget választja, a másolás varázsló kérdezi le a nyilvános kulcsot az átjáró, és a segítségével a tároló hitelesítő adatainak titkosítása. A hitelesítő adatok vannak visszafejteni az átjárót tartalmazó számítógépen, és a Windows által védett [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Támogatott böngészők:** IE8, IE9, IE10, IE11, a Microsoft Edge és a Firefox legutóbbi, Chrome, Opera, a Safari böngésző. 

#### <a name="click-once-credentials-manager-app"></a>Kattintson a – egyszer a hitelesítő adatok manager alkalmazásra
Kattintson a elindíthatja-után a credential manager alkalmazást, az Azure portal/másolás varázsló alapú folyamatok készítésekor. Ez az alkalmazás biztosítja, hogy hitelesítő adatok nem kerülnek szövegként a hálózaton keresztül. Alapértelmezés szerint azt a portot használja **8050** a gépen az átjárót a biztonságos kommunikáció érdekében. Ha szükséges, a port megváltoztatható.  
  
![Az átjáró HTTPS-portja](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Jelenleg az adatkezelési átjárót használja egyetlen **tanúsítvány**. Ez a tanúsítvány jön létre az átjáró telepítése során (adatkezelési átjáró 2016. November után létrehozott és 2.4.xxxx.x verzió érvényes vagy újabb). Ezt a tanúsítványt lecserélheti saját SSL/TLS-tanúsítványt. Ezt a tanúsítványt használják a kattintson – egyszer hitelesítőadat-kezelő alkalmazás biztonságosan csatlakozhat az átjárót tartalmazó számítógépen állítja az adattár hitelesítő adatait. Tárolja az adatokat tároló hitelesítő adatait biztonságos helyszíni használatával a Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) az átjárót a gépen. 

> [!NOTE]
> Régebbi verzió 2.3.xxxx.x vagy 2016. November előtti telepített átjárók továbbra is titkosítva, és a felhőben tárolt hitelesítő adatokat. Akkor is, ha az átjárót a legújabb verzióra frissít, a hitelesítő adatok nem települnek át egy helyszíni gépre    
  
| Átjáró verziója (létrehozásakor) | Tárolt hitelesítő adatok | Hitelesítőadat-titkosítási / biztonság | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | A felhőben | Tanúsítvány (eltérő hitelesítő adatok manager alkalmazás által használt) használatával titkosított | 
| > = 2.4.xxxx.x | A helyszínen | DPAPI védelméről | 
  

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Biztonságos csatornán keresztül zajló teljes adatforgalom **HTTPS** és **TLS feletti TCP** man-in-the-middle támadások megelőzése Azure-szolgáltatásokhoz való kommunikáció során.
 
Is [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Express Route](../../expressroute/expressroute-introduction.md) további védelméhez a kommunikációs csatornát a helyszíni hálózat és az Azure között.

Virtuális hálózat, a felhőbeli hálózatának logikai megfelelője. Csatlakozhat egy helyszíni hálózat az Azure virtuális hálózat (VNet) beállításával (site-to-site) IPSec VPN vagy Express Route (magánhálózati társviszony-létesítés)     

Az alábbi táblázat a forrás- és helyek az adatok hibrid áthelyezése másik kombinációja alapján a hálózat és átjáró konfigurációs javaslatokat foglalja össze.

| Forrás | Cél | Hálózati konfiguráció | Átjáró beállítása |
| ------ | ----------- | --------------------- | ------------- | 
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | IPSec VPN (pont – hely vagy site-to-site) | Átjáró lehet helyi telepítve, vagy egy Azure-beli virtuális gép (VM) a virtuális hálózaton | 
| Helyszíni követelmények | Virtuális gépek és felhőszolgáltatások üzembe helyezett virtuális hálózatok | ExpressRoute (privát társviszony-létesítés) | Átjáró lehet helyi telepítve, vagy egy Azure virtuális gépen a virtuális hálózaton | 
| Helyszíni követelmények | Egy nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (nyilvános társviszony-létesítés) | Átjáró telepített helyszíni kell lennie. | 

Az alábbi képeken az adatkezelési átjáró használata az adatok áthelyezését egy helyi adatbázist és az Express route és IPSec VPN (a Virtual Network) Azure-szolgáltatások között:

**Az expressz útvonal:**
 
![Használja az Express Route-átjáróval](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN-átjáróval](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Tűzfal-konfigurációk és átjáróhoz whitelisting IP-címét

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A helyi/InPrivate-hálózat tűzfalkövetelményei  
Vállalati egy **vállalati tűzfal** fut a szervezet a központi útválasztón. És **Windows tűzfal** démonként fut, a helyi számítógépen, amelyen az átjáró telepítve van. 

Az alábbi táblázatban **kimenő port** és a tartomány vonatkozó követelményeket a **vállalati tűzfal**.

| Tartománynevek | Kimenő portok | Leírás |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Az átjáró által adatátviteli szolgáltatások a Data Factoryban való csatlakozáshoz szükséges |
| `*.core.windows.net` | 443 | Az átjáró által használt szeretne csatlakozni az Azure Storage-fiók használata esetén a [szakaszos Másolás](data-factory-copy-activity-performance.md#staged-copy) funkció. | 
| `*.frontend.clouddatahub.net` | 443 | Az Azure Data Factory szolgáltatás eléréséhez szükséges az átjáró. | 
| `*.database.windows.net` | 1433   | (Nem kötelező) szükséges, ha a cél Azure SQL Database az Azure SQL Data Warehouse /. A szakaszos másolás funkció segítségével az adatok másolása az Azure SQL Database vagy az Azure SQL Data Warehouse az 1433-as port megnyitása nélkül. | 
| `*.azuredatalakestore.net` | 443 | (Nem kötelező) szükséges, ha a cél az Azure Data Lake store | 

> [!NOTE] 
> Előfordulhat, hogy felügyelni portokat kell / engedélyezési tartomány a vállalati tűzfalon által érintett adatforrások. szintű szükség szerint. Ez a tábla használja Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store csak példaként.   

Az alábbi táblázatban **port bejövő** követelményei a **a windows tűzfal**.

| Bejövő portok | Leírás | 
| ------------- | ----------- | 
| 8050 (TCP) | Szükséges a helyszíni hitelesítő adatok biztonságos beállítása az átjárón a hitelesítőadat-kezelő alkalmazást. | 

![Átjáró-port követelményei](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfiguráció / engedélyezési adatok tárolása
Néhány adattár a felhőben is szükséges engedélyezési IP-cím a gép hozzájuk férni. Győződjön meg arról, hogy az átjárót tartalmazó számítógép IP-címe szerepel az engedélyezési listán / megfelelően konfigurált tűzfal.

A következő felhőalapú adattárak alkalmazásátjáróra IP-címét az átjárót tartalmazó számítógépre van szükség. Alapértelmezés szerint ezek adattárak némelyike nem követelheti meg az IP-címek engedélyezési. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés:** Különböző adat-előállítók között megosztható az átjáró?
**Válasz:** Ez a funkció még nem támogatott. Aktívan dolgozunk rajta.

**Kérdés:** Milyen követelmények vonatkoznak a portokra működik az átjáró?
**Válasz:** Átjáró lehetővé teszi a kapcsolat HTTP-alapú internet megnyitásához. A **443-as és a 80-as kimenő portot** kell megnyitni az átjáró a kapcsolat létrehozásához. Nyissa meg **bejövő Port 8050** csak a gépek szintjén (nem a vállalati tűzfalon szintjén) a hitelesítőadat-kezelő alkalmazás. Azure SQL Database vagy Azure SQL Data Warehouse használata esetén, a forrás / cél, akkor meg kell nyitnia **1433-as** portot is. További információkért lásd: [tűzfal-konfigurációk és IP-címek engedélyezési](#firewall-configurations-and-whitelisting-ip-address-of gateway) szakaszban. 

**Kérdés:** Mik azok az átjáró esetében a tanúsítványkövetelmények?
**Válasz:** Jelenlegi átjáróval biztonságosan a tároló hitelesítő adatainak beállításához a hitelesítőadat-kezelő alkalmazást által használt tanúsítvány szükséges. Ez a tanúsítvány egy önaláírt tanúsítvány létrehozása és konfigurálása az átjáró telepítője által. Használhatja a saját TLS / SSL-tanúsítvány helyett. További információkért lásd: [kattintson – egyszer hitelesítőadat-kezelő alkalmazás](#click-once-credentials-manager-app) szakaszban. 

## <a name="next-steps"></a>További lépések
További információ a másolási tevékenység teljesítménye: [másolási tevékenységek teljesítményéhez és finomhangolási útmutató](data-factory-copy-activity-performance.md).

 
