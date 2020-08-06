---
title: Fenyegetésvédelem az Azure Security Centerben
description: Ez a témakör a Azure Security Center veszélyforrások elleni védelmi funkciói által védett erőforrásokat ismerteti
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e74dac779fc1eafaf33ffbc63bf997cf26b64954
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836802"
---
# <a name="threat-protection-in-azure-security-center"></a>Fenyegetésvédelem az Azure Security Centerben

Ha Security Center fenyegetést észlel a környezet bármely területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt szervizelési lépéseket, valamint bizonyos esetekben a logikai alkalmazások válaszként való aktiválásának lehetőségét.

Azure Security Center fenyegetés elleni védelme átfogó védelmet biztosít a környezet számára:

* **Veszélyforrások elleni védelem Azure számítási erőforrásokhoz**: Windows-gépek, linuxos gépek, Azure app Service és Azure-tárolók

* **Veszélyforrások elleni védelem az Azure-beli adatforrások esetében**: SQL Database és SQL Data Warehouse, Azure Storage és Azure Cosmos db

* **Veszélyforrások elleni védelem az Azure-szolgáltatások rétegeiben**: Azure hálózati réteg, Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió) és Azure Key Vault (előzetes verzió)

Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait. 

> [!NOTE]
> A különböző forrásokból származó riasztások eltérő mennyiségű időt vehetnek igénybe. Előfordulhat például, hogy a hálózati forgalom elemzését igénylő riasztások hosszabb ideig tartanak, mint a virtuális gépeken futó gyanús folyamatokkal kapcsolatos riasztások.

> [!TIP]
> Security Center veszélyforrások elleni védelmi képességeinek engedélyezéséhez alkalmaznia kell a standard szintű díjszabást a megfelelő munkaterheléseket tartalmazó előfizetésre.
>
> Az **Azure Storage-fiókok** veszélyforrások elleni védelmét az előfizetés szintjén vagy az erőforrás szintjén engedélyezheti.
> Engedélyezheti a veszélyforrások elleni védelmet **Azure SQL Database SQL-kiszolgálókon** az előfizetés szintjén vagy az erőforrás szintjén.
> Az **Azure Database for MariaDB/MySQL/PostgreSQL** veszélyforrások elleni védelmét csak az erőforrás szintjén engedélyezheti.



## <a name="threat-protection-for-windows-machines"></a>Veszélyforrások elleni védelem Windows rendszerű gépeken<a name="windows-machines"></a>

A Azure Security Center együttműködik az Azure-szolgáltatásokkal a Windows-alapú gépek monitorozásához és védeleméhez. Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

* **Microsoft Defender komplex veszélyforrások elleni védelem (ATP)** <a name="windows-atp"></a> A-Security Center a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) integrálásával kiterjeszti a felhőalapú munkaterhelés-védelmi platformokat. Együttesen átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosítanak.

    > [!IMPORTANT]
    > A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Security Center-t használó Windows-kiszolgálókon.

    Ha a Microsoft Defender ATP fenyegetést észlel, riasztást indít el. A riasztás a Security Center irányítópulton jelenik meg. Az irányítópulton megtekintheti a Microsoft Defender ATP-konzolt, és részletes vizsgálatot végezhet a támadás hatókörének felderítése érdekében. További információ a Microsoft Defender ATP szolgáltatással kapcsolatban: kiszolgálók beléptetése [a Microsoft DEFENDER ATP szolgáltatásba](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Fájlok közötti támadás észlelése** <a name="windows-fileless"></a> – A fájl nélküli támadások rosszindulatú hasznos adatokat szúrnak be a memóriába, így elkerülhetők a lemezes ellenőrzési módszerek. A támadó adattartalma a feltört folyamatok memóriájában marad, és számos kártékony tevékenységet hajt végre.

    A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeres időközönként ellenőrzi a gépet, és kinyeri az eredményeket közvetlenül a folyamatok memóriájában. A Linux-specifikus megállapítások a következők azonosítását tartalmazzák: 

    - Jól ismert eszközkészletek és kriptográfiai adatbányászati szoftverek 
    - A héjkód, amely egy kis kódrészlet, amely általában a szoftveres biztonsági rések kiaknázása során hasznos adattartalomként használatos.
    - Rosszindulatú végrehajtható fájl beinjektálása a folyamat memóriájába

    A fájlok közötti támadás észlelése olyan részletes biztonsági riasztásokat hoz létre, amelyek a további folyamat metaadatainak, például a hálózati tevékenység leírását tartalmazzák. Ez felgyorsítja a riasztások osztályozását, korrelációját és az alsóbb rétegbeli válaszadási időt. Ez a megközelítés kiegészíti az Event-alapú EDR megoldásokat, és nagyobb észlelési lefedettséget biztosít.

    A fájlokba nem kerülő támadás észlelésével kapcsolatos riasztások részleteiért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

> [!TIP]
> A Windows-riasztásokat a [biztonsági riasztások Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)forgatókönyvek letöltésével szimulálhatja.






## <a name="threat-protection-for-linux-machines"></a>Veszélyforrások elleni védelem Linux rendszerű gépeken<a name="linux-machines"></a>

A Security Center a Linux rendszerű gépekről naplózza a naplózási rekordokat **, az**egyik leggyakrabban használt Linux-naplózási keretrendszerben. a naplózott élet a fővonali kernelben. 

* **Linux auditált riasztások és log Analytics ügynök integrációja** <a name="linux-auditd"></a> – A naplózott rendszer egy kernel szintű alrendszerből áll, amely a rendszerhívások figyelésének felelőse. Egy megadott szabálykészlet alapján szűri őket, és üzeneteket küld nekik egy szoftvercsatorna számára. A Security Center a Log Analytics-ügynökön belül az auditált csomag funkcióit integrálja. Ez az integráció lehetővé teszi az auditált események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.

    a naplózott rekordok gyűjtése, bővítése és összesítése az eseményekre a Linux-ügynök Log Analytics ügynökének használatával történik. Security Center folyamatosan bővíti a Linux-jeleket használó új elemzéseket a Felhőbeli és a helyszíni linuxos gépek kártékony viselkedésének észlelése érdekében. A Windows-képességekhez hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is kiterjedhetnek. Ezek a tevékenységek jelezhetik, hogy a gép támadás alatt áll, vagy megsértették.  

    A Linux-riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-linux)tartalmazza.

> [!TIP]
> A Linux-riasztásokat a Linux- [észlelések Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)forgatókönyv letöltésével szimulálhatja.





## <a name="threat-protection-for-azure-app-service"></a>Veszélyforrások elleni védelem Azure App Service<a name="app-services"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

Security Center a felhő méretezésével azonosítja a App Serviceon futó alkalmazásokat célzó támadásokat. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek egy kis részhalmazát mutatja be, és a hasonló végpontokra való bejárási mintákat jeleníti meg több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.

Ha Windows-alapú App Service csomagot futtat, Security Center a mögöttes munkaterületeket és virtuális gépeket is elérheti. A fent említett naplózási adatokkal együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.

A Azure App Service riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureappserv)tekintheti meg.

App Service csomagokkal kapcsolatos további információkért lásd: [app Service csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-containers"></a>Veszélyforrások elleni védelem tárolók esetén<a name="azure-containers"></a>

### <a name="availability"></a>Rendelkezésre állás

- Kiadási állapot: **általánosan elérhető**
- Szükséges szerepkörök: a **biztonsági rendszergazda** figyelmen kívül hagyhatja a riasztásokat. A **biztonsági olvasó** megtekintheti az eredményeket.
- Felhők<br>
    ✔ Kereskedelmi felhők<br>
    ✘ US Gov<br>
    ✘ China gov, egyéb gov

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Veszélyforrások elleni védelem SQL Database és SQL Data Warehouse<a name="data-sql"></a>

A Azure SQL Database komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A riasztások akkor jelennek meg, ha gyanús adatbázis-tevékenységek, potenciális sebezhetőségek vagy SQL-injektálási támadások, valamint rendellenes adatbázis-hozzáférés és lekérdezési minták vannak.

A Azure SQL Database és az SQL komplex veszélyforrások elleni védelme a speciális SQL biztonsági képességek [(ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) egységes csomag részét képezi, amely a Azure SQL Database, az Azure SQL felügyelt példányaira, a Azure SQL Data Warehouse adatbázisokra és az Azure Virtual Machines SQL-kiszolgálóira terjed ki.

További információ:

* [A komplex veszélyforrások elleni védelem engedélyezése a Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Az Azure-beli SQL serverek komplex veszélyforrások elleni védelmének engedélyezése Virtual Machines](security-center-iaas-advanced-data.md)
* [A veszélyforrások elleni védelmi riasztások listája SQL Database és SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Veszélyforrások elleni védelem az Azure Storage-ban<a name="azure-storage"></a>

### <a name="availability"></a>Rendelkezésre állás

- Kiadás állapota:
    - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (általánosan elérhető)
    - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (előzetes verzió)
    - [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (előzetes verzió)
- Felhők<br>
    ✔ Kereskedelmi felhők<br>
    ✔ US Gov<br>
    ✘ China gov, egyéb gov

### <a name="whats-protected"></a>Mi a védett?

Az Azure Storage veszélyforrások elleni védelme észleli az Azure Storage-fiókok potenciálisan káros tevékenységeit. Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva.

Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését *anélkül* , hogy biztonsági szakértőnek kellene lennie, és segít a biztonsági monitorozási rendszerek kezelésében.

A Storage-fiókok védettek 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Milyen típusú riasztásokat biztosít a fenyegetések elleni védelem az Azure Storage-ban?

A biztonsági riasztások akkor aktiválódnak, ha:

- **Gyanús tevékenység** – például a Storage-fiók sikeresen elérhető a Tor aktív kilépési csomópontjának nevezett IP-címről.
- **Rendellenes viselkedés** – például a hozzáférési minta módosítása egy Storage-fiókra
- **Lehetséges kártevők feltöltése** – a kivonatoló hírnevének elemzése azt jelzi, hogy egy feltöltött fájl kártevőket tartalmaz

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Mi a kivonatoló hírnevének elemzése kártevők számára?

Annak megállapításához, hogy a feltöltött fájl gyanús-e, a fenyegetések elleni védelem az Azure Storage-ban a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)által támogatott kivonat-felismerési elemzést használ. A veszélyforrások elleni védelem eszközei nem ellenőrzik a feltöltött fájlokat, hanem megvizsgálják a tárolási naplókat, és összehasonlítják az újonnan feltöltött fájlok kivonatait az ismert vírusok, trójaiak, kémprogramok és ransomware esetében. 

Ha egy fájl gyanúja szerint kártevőt tartalmaz, Security Center riasztást jelenít meg, és opcionálisan e-mailben is elküldheti a tároló tulajdonosának jóváhagyását a gyanús fájl törléséhez. Ha be szeretné állítani a fájlok automatikus eltávolítását, amely alapján a kivonatoló hírnevének elemzése a kártevőket tartalmazza, a Munkafolyamat-automatizálás üzembe helyezésével [aktiválhatja azokat a riasztásokat, amelyek "a Storage-fiókba feltöltött lehetséges kártevőket" tartalmaznak](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>További lépések 

A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót is, tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

További információ:

* [A komplex veszélyforrások elleni védelem engedélyezése az Azure Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Az Azure Storage veszélyforrások elleni védelmi értesítéseinek listája](alerts-reference.md#alerts-azurestorage)
* [A Microsoft fenyegetés-felderítési képességei](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> A tárolási riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)utasításait követve végezhető el.







## <a name="threat-protection-for-azure-cosmos-db"></a>Veszélyforrások elleni védelem Azure Cosmos DB<a name="cosmos-db"></a>

A Azure Cosmos DB riasztások szokatlan és potenciálisan ártalmas kísérletekkel jönnek létre Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához.

További információ:

* [A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB (előzetes verzió)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Veszélyforrások elleni védelem az Azure hálózati rétegben<a name="network-layer"></a>

Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján a Security Center gépi tanulási modelleket használ a kártékony forgalmi tevékenységek azonosítására és megjelölésére. A Security Center a Microsoft Threat Intelligence-adatbázist is használja az IP-címek dúsítására.

Bizonyos hálózati konfigurációk korlátozhatják Security Center a gyanús hálózati tevékenységekre vonatkozó riasztások generálását. A hálózati riasztások létrehozásához Security Center a következőket:

- A virtuális gép nyilvános IP-címmel rendelkezik (vagy egy nyilvános IP-címmel rendelkező terheléselosztó).

- A virtuális gép hálózati kimenő forgalmát nem blokkolja külső azonosító megoldás.

- A virtuális gép ugyanazzal az IP-címmel lett hozzárendelve, mint az a teljes óra, amelyben a gyanús kommunikáció történt. Ez a felügyelt szolgáltatás részeként létrehozott virtuális gépekre is vonatkozik (például: AK, Databricks).

Az Azure hálózati réteggel kapcsolatos riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-azurenetlayer)tartalmazza.

További információ arról, hogy a Security Center hogyan használhatók a hálózattal kapcsolatos jelek a veszélyforrások elleni védelem alkalmazásához: [a heurisztikus DNS-észlelések a Security Centerban](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Veszélyforrások elleni védelem az Azure felügyeleti rétegben (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események használatával, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

A Azure Resource Manager (előzetes verzió) riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureresourceman)tekintheti meg.



>[!NOTE]
> Az előző elemzések közül több Microsoft Cloud App Security van. Ezen elemzések kihasználása érdekében aktiválni kell egy Cloud App Security licencet. Ha Cloud App Security licenccel rendelkezik, ezek a riasztások alapértelmezés szerint engedélyezve vannak. A riasztások letiltása:
>
> 1. A Security Center menüjében válassza a **díjszabás & beállítások**lehetőséget.
> 1. Válassza ki a módosítani kívánt előfizetést.
> 1. Válassza a **veszélyforrások észlelése**lehetőséget.
> 1. Törölje a **Microsoft Cloud app Security az adataim elérésének engedélyezése**jelölőnégyzet jelölését, majd válassza a **Mentés**lehetőséget.

>[!NOTE]
>Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nem telepített Security Center az erőforrás geo-ban, akkor az a Egyesült Államok tárolja azokat. Ha Cloud App Security engedélyezve van, a rendszer ezeket az adatokat a Cloud App Security földrajzi hely szabályainak megfelelően tárolja. További információkért lásd: [adattároló a nem regionális szolgáltatásokhoz](https://azuredatacentermap.azurewebsites.net/).

1. Állítsa be azt a munkaterületet, amelyre telepíteni kívánja az ügynököt. Győződjön meg arról, hogy a munkaterület ugyanahhoz az előfizetéshez tartozik, amelyet Security Center használ, és hogy rendelkezik írási/olvasási engedéllyel a munkaterületen.

1. Állítsa be a standard díjszabási szintet, majd kattintson a **Mentés**gombra.






## <a name="threat-protection-for-azure-key-vault-preview"></a>Veszélyforrások elleni védelem Azure Key Vault (előzetes verzió)<a name="azure-keyvault"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Azure Security Center magában foglalja az Azure-natív, komplex veszélyforrások elleni védelmet Azure Key Vault, amely egy további biztonsági intelligenciát biztosít. Security Center szokatlan és potenciálisan ártalmas kísérleteket észlel Key Vault fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő nélkül kezeljék a fenyegetéseket, és nincs szükség a harmadik féltől származó biztonsági figyelő rendszerek felügyeletére.  

Ha rendellenes tevékenységek történnek, Security Center riasztásokat jelenít meg, és opcionálisan e-mailben küldi el őket az előfizetés-rendszergazdáknak. Ezek a riasztások tartalmazzák a gyanús tevékenység részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. 

A Azure Key Vault riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azurekv)tekintheti meg.





## <a name="threat-protection-for-other-microsoft-services"></a>Veszélyforrások elleni védelem más Microsoft-szolgáltatások esetében<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Veszélyforrások elleni védelem az Azure WAF<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF a 3,0-es alapszintű szabálykészlet alapján, vagy az Open Web Application biztonsági projekt 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében. 

Ha rendelkezik Azure WAF-licenccel, a WAF-riasztások továbbítása a Security Center, és nincs szükség további konfigurálásra. A WAF által generált riasztásokkal kapcsolatos további információkért lásd: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Veszélyforrások elleni védelem Azure DDoS Protection<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagyszerű biztonsági szempontot jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

A DDoS-támadások elleni védelemhez vásároljon Azure DDoS Protection-licencet, és győződjön meg róla, hogy az alkalmazás kialakításának ajánlott eljárásait követi. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

A Azure DDoS Protection riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureddos)tekintheti meg.


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a veszélyforrások elleni védelmi funkciókkal kapcsolatos biztonsági riasztásokról, tekintse meg a következő cikkeket:

* [Az összes Azure Security Center-riasztás hivatkozási táblázata](alerts-reference.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md)