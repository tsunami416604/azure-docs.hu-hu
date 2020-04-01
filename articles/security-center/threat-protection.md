---
title: Veszélyforrások elleni védelem az Azure Security Centerben
description: Ez a témakör az Azure Security Center fenyegetésvédelmi funkciói által védett erőforrásokat ismerteti.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 395f4b5481fcf2028d6bfe736e58c3174a0c80b2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435348"
---
# <a name="threat-protection-in-azure-security-center"></a>Veszélyforrások elleni védelem az Azure Security Centerben

Ha a Security Center fenyegetést észlel a környezet bármely területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, javasolt javítási lépéseket, és bizonyos esetekben egy lehetőséget, hogy egy logikai alkalmazás válaszként.

Az Azure Security Center fenyegetésvédelmi szolgáltatása átfogó védelmet nyújt a környezet számára:

* **Fenyegetésvédelem az Azure számítási erőforrásaihoz**: Windows-gépek, Linux-gépek, Azure App Service és Azure-tárolók

* **Fenyegetéselleni védelem az Azure-adaterőforrásokhoz:** SQL Database és SQL Data Warehouse, Azure Storage és Azure Cosmos DB

* **Fenyegetésvédelem az Azure szolgáltatási rétegeiszámára:** Azure hálózati réteg, Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió) és Azure Key Vault (előzetes verzió)

Függetlenül attól, hogy a Biztonsági központ által generált riasztást, vagy a Security Center egy másik biztonsági terméktől kapta, exportálhatja azt. Ha a riasztásokat az Azure Sentinel (vagy egy külső SIEM) vagy bármely más külső eszközbe szeretné exportálni, kövesse a [riasztások exportálása siem-be](continuous-export.md)című útmutatóutasításait. 




## <a name="threat-protection-for-windows-machines"></a>Veszélyforrások elleni védelem Windows-gépekhez<a name="windows-machines"></a>

Az Azure Security Center integrálható az Azure-szolgáltatásokkal a Windows-alapú gépek figyeléséhez és védelméhez. A Security Center könnyen használható formátumban mutatja be az összes szolgáltatás riasztási és javítási javaslatait.

* **A Microsoft Defender ATP** <a name="windows-atp"></a> – Security Center a Microsoft Defender komplex veszélyforrások elleni védelemmel (ATP) való integrálásával bővíti felhőalapú számítási feladatok elleni védelmi platformjait. Együttesen átfogó végpontészlelési és válaszkezelési (EDR) képességeket biztosítanak.

    > [!IMPORTANT]
    > A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Security Centert használó Windows-kiszolgálókon.

    Amikor a Microsoft Defender ATP fenyegetést észlel, riasztást vált ki. A riasztás a Biztonsági központ irányítópultján jelenik meg. Az irányítópultról a Microsoft Defender ATP-konzolra léphet, és részletes vizsgálatot végezhet a támadás hatókörének feltárása érdekében. A Microsoft Defender ATP szolgáltatásról további információt [a Microsoft Defender ATP szolgáltatás fedélzeti kiszolgálói című](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)témakörben talál.

* **Összeomlási memóriakép-elemzés** <a name="windows-dump"></a> – Amikor a szoftver összeomlik, az összeomlási memóriakép rögzíti a memória egy részét az összeomlás idején.

    Az összeomlást rosszindulatú programok okozhatták, vagy rosszindulatú programokat tartalmazhatnak. A biztonsági termékek általi észlelés elkerülése érdekében a rosszindulatú programok különböző formái fájlnélküli támadást használnak, amely megakadályozza a lemezre írást vagy a lemezre írt szoftverösszetevők titkosítását. Az ilyen típusú támadásokat nehéz észlelni a hagyományos lemezalapú megközelítések használatával.

    A memóriaelemzés segítségével azonban észlelheti az ilyen típusú támadásokat. Az összeomlási memóriakép memóriájának elemzésével a Security Center észleli a támadás által használt technikákat. Előfordulhat például, hogy a támadás megpróbálja kihasználni a szoftver biztonsági réseit, bizalmas adatokhoz fér hozzá, és titokban megmarad egy feltört gépen belül. A Security Center ezt a munkát úgy végzi el, hogy a gazdagépek nek minimális hatással lesz a teljesítményre.

    Az összeomlási memóriakép elemzési riasztásainak részleteit a [Riasztások hivatkozási táblázatában találja.](alerts-reference.md#alerts-windows)

* **Fájlnélküli támadásészlelés** <a name="windows-fileless"></a> – A végpontokat célzó fájl nélküli támadások gyakoriak. Az észlelés elkerülése érdekében a fájl nélküli támadások rosszindulatú adatokat juttatnak a memóriába. A támadó imitátrától megmaradnak a feltört folyamatok memóriájában, és számos rosszindulatú tevékenységet hajtanak végre.

    A fájl nélküli támadásészleléssel az automatizált memóriakriminalisztikai technikák azonosítják a fájl nélküli támadási eszközkészleteket, technikákat és viselkedésmódokat. Ez a megoldás futásidőben rendszeresen megvizsgálja a gépet, és közvetlenül a biztonság szempontjából kritikus folyamatok memóriájából nyeri ki az elemzéseket.

    Bizonyítékot talál a kizsákmányolásra, a kódbefecskendezésre és a rosszindulatú rakományok végrehajtására. A fájl nélküli támadásészlelés részletes biztonsági riasztásokat hoz létre a riasztási osztályozás, a korreláció és az alsóbb rétegbeli válaszidő felgyorsítása érdekében. Ez a megközelítés kiegészíti az eseményalapú EDR-megoldásokat, nagyobb észlelési lefedettséget biztosítva.

    A fájl nélküli támadásészlelési riasztásokról a [Riasztások hivatkozási táblázatában talál.](alerts-reference.md#alerts-windows)

> [!TIP]
> A Windows-riasztások szimulálhatók az [Azure Security Center forgatókönyvének letöltésével: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Fenyegetésvédelem Linux rendszerű gépekhez<a name="linux-machines"></a>

A Security Center naplóztatja a naplózási rekordokat a Linux-gépekről a **naplózási**rendszeregyik leggyakoribb Linux-naplózási keretrendszer használatával. auditált él a mainline kernel. 

* **Linux auditált riasztások és log analytics ügynök integráció** <a name="linux-auditd"></a> – A naplózta rendszer áll egy kernel-szintű alrendszer, amely felelős a rendszer hívások figyelése. Egy megadott szabálykészlettel szűri őket, és üzeneteket ír számukra egy szoftvercsatornába. A Security Center integrálja a naplóztatott csomag funkcióit a Log Analytics-ügynökbe. Ez az integráció lehetővé teszi a naplóztatott események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.

    a naplózott rekordokat a Rendszer a Log Analytics-ügynök Linux-ügynökhöz használatával gyűjti, gazdagítja és összesíti eseményekké. A Security Center folyamatosan új elemzéseket ad hozzá, amelyek Linux-jeleket használnak a rosszindulatú viselkedések észlelésére a felhőbeli és a helyszíni Linux-gépeken. A Windows képességeihez hasonlóan ezek az elemzések a gyanús folyamatokra, a kétes bejelentkezési kísérletekre, a kernelmodul betöltésére és egyéb tevékenységekre is kiterjednek. Ezek a tevékenységek azt jelezhetik, hogy egy gép támadás alatt áll, vagy megsértették.  

    A Linux-riasztások listáját a [Riasztások hivatkozási táblázatában láthatja.](alerts-reference.md#alerts-linux)

> [!TIP]
> A Linux-riasztások szimulálhatók az [Azure Security Center forgatókönyvének letöltésével: Linux észlelések](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Fenyegetésvédelem az Azure App Service-hez<a name="app-services"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure kormányzati és a szuverén felhőrégiókban.

A Security Center a felhő skáláját használja az App Service-en futó alkalmazásokat célzó támadások azonosítására. A támadók a webes alkalmazásokat megvizsgálják a gyengeségek felkeresésére és kihasználására. Mielőtt adott környezetekbe irányítanák, az Azure-ban futó alkalmazásokhoz érkező kérelmek több átjárón keresztül haladnak, ahol a rendszer ellenőrzi és naplózza őket. Ezeket az adatokat ezután a rendszer a biztonsági rések és a támadók azonosítására, valamint a később használt új minták megismerésére használja.

Az Azure felhőszolgáltatóként való láthatóságának használatával a Security Center elemzi az App Service belső naplóit, hogy több célponton azonosíthassa a támadási módszertant. A módszertan például széles körű szkennelést és elosztott támadásokat foglal magában. Az ilyen típusú támadások általában az IP-k egy kis részhalmazából származnak, és több állomás hasonló végpontjaihoz való bejárási mintákat jelenít meg. A támadások sebezhető oldalt vagy bővítményt keresnek, és nem azonosíthatók egyetlen gazdagép szempontjából.

Ha Windows-alapú App Service-csomagot futtat, a Security Center is hozzáfér az alapul szolgáló sandboxokhoz és virtuális gépekhez. A fent említett naplóadatokkal együtt az infrastruktúra meg tudja mondani a történetet, a vadon ban keringő új támadástól az ügyfélgépekben lévő kompromisszumokig. Ezért még akkor is, ha a Security Center egy webalkalmazás kihasználása után van telepítve, észlelheti a folyamatban lévő támadásokat.

Az Azure App Service-riasztások listáját a [Riasztások hivatkozási táblázatában található.](alerts-reference.md#alerts-azureappserv)

Az App Service-csomagokról az [App Service-csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/)című témakörben talál további információt.





## <a name="threat-protection-for-azure-containers"></a>Az Azure-tárolók fenyegetésvédelmi szolgáltatása<a name="azure-containers"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure kormányzati és a szuverén felhőrégiókban.

A Security Center valós idejű veszélyforrások elleni védelmet biztosít a tárolóba helyezett környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A Security Center különböző szinteken nyújt veszélyforrások elleni védelmet: 

* **Host szint** – A Security Center ügynöke (elérhető a standard szinten, lásd: [díjszabás](security-center-pricing.md) a részletekért) figyeli a Linux gyanús tevékenységeket. Az ügynök riasztásokat indít a csomópontról vagy a rajta futó tárolóból származó gyanús tevékenységekről. Ilyen tevékenységek közé tartozik a webes rendszerhéj észlelése és az ismert gyanús IP-címekkel való kapcsolat.

    A tárolóba helyezett környezet biztonságának mélyebb megismerése érdekében az ügynök figyeli a tárolóspecifikus elemzéseket. Riasztásokat indít el olyan eseményekhez, mint például a kiemelt tárolólétrehozása, az API-kiszolgálókhoz való gyanús hozzáférés és a Docker-tárolóban futó Secure Shell (SSH) kiszolgálók.

    >[!IMPORTANT]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, akkor csak a fenyegetésvédelmi előnyök és a biztonsági riasztások egy részét kapja meg. Továbbra is kap a hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat.

    Az állomásszintű riasztások listáját a [Riasztások hivatkozási táblázatában található.](alerts-reference.md#alerts-containerhost)


* Az **AKS-fürt szintjén**a veszélyforrások elleni védelem a Kubernetes naplóinak elemzésén alapul. Az **ügynök nélküli** figyelés engedélyezéséhez adja hozzá a Kubernetes-beállítást az előfizetéshez a **Díjszabási & beállítások** lapon (lásd: [díjszabás).](security-center-pricing.md) Ezen a szinten riasztások létrehozásához a Security Center az AKS által felügyelt szolgáltatásokat figyeli az AKS által beolvasott naplók használatával. Ilyen szintű események példák közé tartozik a kubernetes-irányítópultok létrehozása, magas jogosultságú szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása.

    >[!NOTE]
    > Security Center biztonsági riasztásokat hoz létre az Azure Kubernetes Service műveletek és üzembe helyezések után előforduló Kubernetes beállítás engedélyezve van az előfizetési beállításokat. 

    Az AKS-fürtszintű riasztások listáját a [Riasztások hivatkozási táblázatában található.](alerts-reference.md#alerts-akscluster)

Továbbá, a globális biztonsági kutatócsoport folyamatosan figyelemmel kíséri a fenyegetés táj. Tárolóspecifikus riasztásokat és biztonsági réseket adnak hozzá, amint felderítik őket.

> [!TIP]
> A tárolóriasztásokat a [blogbejegyzésben](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)található utasításokat követve szimulálhatja.








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Veszélyforrások elleni védelem az SQL Database és az SQL Data Warehouse számára<a name="data-sql"></a>

Az Azure SQL Database komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

Riasztások jelennek meg, ha gyanús adatbázis-tevékenységek, potenciális biztonsági rések vagy SQL-injektálási támadások, valamint rendellenes adatbázis-hozzáférési és lekérdezési minták jelennek meg.

Az Azure SQL Database és SQL speciális veszélyforrások elleni védelem része a fejlett SQL-biztonsági képességekhez szükséges, egységes ített [data security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) csomagnak, amely az Azure SQL-adatbázisokat, az Azure SQL Database által felügyelt példányokat, az Azure SQL Data Warehouse-adatbázisokat és az Azure virtuális gépeken lévő SQL-kiszolgálókat fedi le.

További információkért lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése az Azure SQL Database-hez](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [A komplex veszélyforrások elleni védelem engedélyezése AZ SQL-kiszolgálók számára az Azure virtuális gépeken](security-center-iaas-advanced-data.md)
* [Az SQL Database és az SQL Data Warehouse veszélyforrások elleni riasztásainak listája](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Fenyegetésvédelem az Azure Storage-hoz<a name="azure-storage"></a>

> [!NOTE]
> Ez a szolgáltatás az Egyesült Államok kormányzati felhőiben érhető el, de más szuverén vagy Azure kormányzati felhőrégiókban nem.

Komplex veszélyforrások elleni védelem a storage (jelenleg csak a Blob storage) észleli a szokatlan és potenciálisan káros kísérletek elérésére vagy a tárfiókok kihasználására. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, és segít a biztonsági figyelőrendszerek kezelésében.

További információkért lásd:

* [A komplex veszélyforrások elleni védelem engedélyezése az Azure Storage-hoz](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Az Azure Storage veszélyforrások elleni riasztásainak listája](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Az Azure Storage-riasztások szimulálása a [blogbejegyzésben](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)található utasításokat követve szimulálhatja.




## <a name="threat-protection-for-azure-cosmos-db"></a>Fenyegetésvédelem az Azure Cosmos DB-hez<a name="cosmos-db"></a>

Az Azure Cosmos DB riasztások szokatlan és potenciálisan káros kísérletek et hoznak létre az Azure Cosmos DB-fiókok elérésére vagy kihasználására.

További információkért lásd:

* [Komplex veszélyforrások elleni védelem az Azure Cosmos DB számára (előzetes verzió)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Az Azure Cosmos DB fenyegetésvédelmi riasztásainak listája (előzetes verzió)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Fenyegetésvédelem az Azure hálózati rétegéhez<a name="network-layer"></a>

A Security Center hálózati rétegelemzései minta [IPFIX-adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure alapvető útválasztói által gyűjtött csomagfejlécek. Ezen adatcsatorna alapján a Security Center gépi tanulási modelleket használ a rosszindulatú forgalmi tevékenységek azonosítására és megjelölésére. A Security Center a Microsoft Threat Intelligence adatbázist is használja az IP-címek bővítésére.

Egyes hálózati konfigurációk korlátozhatják a Security Centert abban, hogy riasztásokat generáljon a gyanús hálózati tevékenységre vonatkozóan. Ha a Security Center hálózati riasztásokat szeretne létrehozni, győződjön meg arról, hogy:

- A virtuális gép rendelkezik egy nyilvános IP-címet (vagy egy nyilvános IP-címmel rendelkező terheléselosztón van).

- A virtuális gép hálózati kimenő forgalom nem blokkolja egy külső IDS-megoldás.

- A virtuális gép ugyanazt az IP-címet kapta a teljes óra, amely alatt a gyanús kommunikáció történt. Ez a felügyelt szolgáltatás részeként létrehozott virtuális gépekre is vonatkozik (például AKS, Databricks).

Az Azure hálózati rétegriasztásainak listáját a [Riasztások hivatkozási táblázatában található.](alerts-reference.md#alerts-azurenetlayer)

A Security Center fenyegetésvédelem hálózati jelekkel való használatáról a [Biztonsági központ Heurisztikus DNS-észlelések című témakörében talál részleteket.](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Fenyegetésvédelem az Azure felügyeleti rétegéhez (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

A Security Center Azure Resource Manager alapú védelmi rétege jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget kínál az Azure Resource Manager-események használatával, amely az Azure vezérlősíkjának minősül. Az Azure Resource Manager-rekordok elemzésével a Security Center szokatlan vagy potenciálisan káros műveleteket észlel az Azure-előfizetési környezetben.

Az Azure Resource Manager (Előzetes verzió) riasztásainak listáját a [Riasztások hivatkozási táblázatában tekintheti meg.](alerts-reference.md#alerts-azureresourceman)



>[!NOTE]
> Az előző elemzések közül többre a Microsoft Cloud App Security szolgáltatás ad ki. Ahhoz, hogy ezeket az elemzéseket élvezhesse, aktiválnia kell egy Cloud App Security licencet. Ha rendelkezik egy Cloud App Security licenccel, akkor ezek a riasztások alapértelmezés szerint engedélyezve vannak. A riasztások letiltása:
>
> 1. A **Biztonsági központ** panelen válassza a **Biztonsági házirend**lehetőséget. A módosítani kívánt előfizetéshez válassza a **Beállítások szerkesztése**lehetőséget.
> 2. Válassza **a Fenyegetésészlelés lehetőséget.**
> 3. Az **Integrációk engedélyezése**csoportban törölje **a jelet a Microsoft Cloud App Security hozzáférésének engedélyezése az adataimhoz**jelölőnégyzetből, és válassza a **Mentés lehetőséget.**

>[!NOTE]
>A Security Center a biztonsággal kapcsolatos ügyféladatokat az erőforrásával azonos földrajzi helyen tárolja. Ha a Microsoft még nem telepítette a Security Centert az erőforrás földrajzi területén, akkor az adatokat az Egyesült Államokban tárolja. Ha a Cloud App Security engedélyezve van, ezeket az adatokat a Cloud App Security földrajzi helymeghatározási szabályainak megfelelően tároljuk. További információ: [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Fenyegetéselleni védelem az Azure Key Vaulthoz (előzetes verzió)<a name="azure-keyvault"></a>

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure kormányzati és a szuverén felhőrégiókban.

Az Azure Key Vault egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat és a titkos kulcsokat, például a tanúsítványokat, a kapcsolati karakterláncokat és a jelszavakat. 

Az Azure Security Center azure-natív, fejlett veszélyforrások elleni védelmet biztosít az Azure Key Vault számára, amely a biztonsági intelligencia további rétegét biztosítja. A Security Center észleli a Key Vault-fiókok elérésére és kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértő lenne, és külső biztonsági figyelőrendszereket kellene kezelnie.  

Rendellenes tevékenységek esetén a Security Center riasztásokat jelenít meg, és opcionálisan e-mailben elküldi azokat az előfizetés rendszergazdáinak. Ezek a riasztások tartalmazzák a gyanús tevékenység részleteit, valamint a fenyegetések kivizsgálására és elhárítására vonatkozó ajánlásokat. 

Az Azure Key Vault-riasztások listáját a [Riasztások hivatkozási táblájában láthatja.](alerts-reference.md#alerts-azurekv)





## <a name="threat-protection-for-other-microsoft-services"></a>Veszélyforrások elleni védelem más Microsoft-szolgáltatásokszámára<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Fenyegetéselleni védelem az Azure WAF-hez<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazásokat egyre inkább rosszindulatú támadások célozzák meg, amelyek kihasználják az általánosan ismert biztonsági réseket. Az Application Gateway WAF az Open Web Application Security Project 3.0-s vagy 2.2.9-es alapszabálykészletén alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében. 

Ha rendelkezik az Azure WAF licenccel, a WAF-riasztások streamelése a Security Centerbe további konfiguráció nélkül. A WAF által létrehozott riasztásokról további információt a [Webalkalmazás tűzfal CRS szabálycsoportjai és szabályai](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)című témakörben talál.


### <a name="threat-protection-for-azure-ddos-protection"></a>Fenyegetésvédelem az Azure DDoS-védelemhez<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagy biztonsági problémát jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadás megpróbálja kimeríteni az alkalmazás erőforrásait, így az alkalmazás nem érhető el a jogos felhasználók számára. A DDoS-támadások bármely végpontot megcélozhatnak, amely az interneten keresztül érhető el.

A DDoS-támadások elleni védelem érdekében vásároljon licencet az Azure DDoS Protection szolgáltatáshoz, és győződjön meg arról, hogy az alkalmazástervezési gyakorlati tanácsokat követi. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Az Azure DDoS Protection-riasztások listáját a [Riasztások referenciatáblázata](alerts-reference.md#alerts-azureddos)című témakörben található.


## <a name="next-steps"></a>További lépések
A veszélyforrások elleni védelem ezen szolgáltatásaiból származó biztonsági riasztásokról az alábbi cikkekben olvashat bővebben:

* [Referenciatábla az Azure Security Center összes riasztásához](alerts-reference.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md)