---
title: Biztonságos webalkalmazás a PCI DSShoz | Microsoft Docs
description: Ez a minta alkalmazás olyan biztonsági eljárásokat valósít meg, amelyek javítják az alkalmazást és a szervezete biztonsági helyzetét az Azure-beli fejlesztés során.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992613"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Biztonságos infrastruktúra fejlesztése egy PCI-alkalmazáshoz

## <a name="overview"></a>Áttekintés

A Payment Card Industry (PCI) alkalmazás biztonságos infrastruktúrája útmutatást nyújt a bankkártya-adatok gyűjtéséhez, tárolásához és lekéréséhez alkalmas, a fizetési kártya ipari platformjának szolgáltatásként történő üzembe helyezéséhez. Ez a megoldás automatizálja az Azure-erőforrások üzembe helyezését és konfigurálását egy közös hivatkozási architektúrához, amely megmutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az Azure-ban saját megoldásaikat felépítő és konfiguráló ügyfelek számára. A megoldás a Payment Card Industry-adatbiztonsági szabványokhoz hasonló követelmények egy részhalmazát valósítja meg (PCI DSS 3,2).

Ez a minta automatikusan üzembe helyezi a "Péter webalkalmazás-referenciát" olyan előre konfigurált biztonsági ellenőrzésekkel, amelyek segítségével az ügyfelek a PCI DSS 3,2 követelményeinek megfelelően érhetik el a megfelelőséget. A megoldás Azure Resource Manager sablonokból és PowerShell-parancsfájlokból áll, amelyek az erőforrások üzembe helyezését és konfigurálását ismertetik.

Az alkalmazás-összetevők megfelelő konfigurálásának biztosítása érdekében kövesse a cikkben ismertetett lépéseket. Az adatbázis, Azure App Service, Azure Key Vault példány és az Azure Application Gateway példánya egymástól függ.

Az üzembe helyezési parancsfájlok beállítják az infrastruktúrát. Az üzembe helyezési parancsfájlok futtatása után az összetevők és szolgáltatások összekapcsolásához meg kell adnia néhány manuális konfigurációt a Azure Portalban.

Ez a minta az Azure-ban tapasztalt fejlesztők számára készült, akik a kiskereskedelmi iparágban működnek, és egy biztonságos Azure-infrastruktúrával rendelkező kiskereskedelmi alkalmazást szeretnének létrehozni.

> [!NOTE]
> Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.

Ha módosítás nélkül helyezi üzembe az alkalmazást a környezetbe, az nem elegendő ahhoz, hogy teljes mértékben kielégítse PCI DSS 3,2-es követelményeit. Vegye figyelembe a következőket:

- Ez az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek az Azure-t PCI DSS 3,2-kompatibilis módon használhatják.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi értékelésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

Az alkalmazás fejlesztése és üzembe helyezése során a következőket sajátíthatja el:

- Hozzon létre egy Azure Key Vault példányt, és tárolja és kérje le a titkokat.
- Telepítse az Azure Database-t az Azure SQL-hez, állítsa be a biztonságos adatvédelmet, és engedélyezze a hozzáférést.
- Üzembe helyezheti az Azure-webalkalmazást App Service környezettel, amely dedikált elszigetelt az előtér-tűzfal aEcess.
- Hozzon létre és konfiguráljon egy Azure Application Gateway példányt egy olyan tűzfallal, amely az [OWASP Top 10 szabályrendszert](https://coreruleset.org/)használja.
- Az Azure-szolgáltatások használatával engedélyezheti az átvitelben és a nyugalmában lévő adatok titkosítását.
- Az Azure-szabályzat és a kék nyomatok beállítása a megfelelőség kiértékeléséhez

Az alkalmazás fejlesztése és üzembe helyezése után be kell állítania az alábbi minta-webalkalmazást, valamint a konfigurációs és biztonsági mértékeket.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Az alkalmazás egy tipikus n szintű alkalmazás három réteggel. Itt látható az előtér-, a háttér-és az adatbázis-réteg az integrált figyelési és titkos felügyeleti összetevőkkel:

![Alkalmazásarchitektúra](./media/secure-pci-web-app/architecture.png)

Az architektúra az alábbi összetevőkből áll:

- [App Service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Az alkalmazás architektúrájának App Service Environment és Load balancert biztosít.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Átjárót és tűzfalat biztosít az alkalmazás architektúrája számára.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Egy bővíthető Application Performance Management-(APM-) szolgáltatást biztosít több platformon.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Tárolja és titkosítja az alkalmazás titkait, és felügyeli a rájuk épülő hozzáférési szabályzatok létrehozását.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Felhőalapú identitás-és hozzáférés-kezelési szolgáltatást, bejelentkezést és hozzáférést biztosít az erőforrásokhoz.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Biztosítja a szolgáltatást a tartomány üzemeltetéséhez.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Az alkalmazások méretezése és magas rendelkezésre állás létrehozása a szolgáltatásokhoz
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Megoldást nyújt a modern adattárolási forgatókönyvekhez.
- [Azure-webalkalmazás](https://docs.microsoft.com/azure/app-service/overview/).  HTTP-alapú szolgáltatást biztosít a webalkalmazások üzemeltetéséhez.
- [Azure Database for AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Biztonságosan tárolja az alkalmazás adatfájljait.
- [Azure-tervrajzok](https://docs.microsoft.com/azure/governance/blueprints/overview/). Bizonyos szabványokhoz és követelményekhez nyújt specifikációkat és megfelelőséget.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Kiértékeli az erőforrásokat, hogy nem felel meg a hozzárendelt házirendeknek.

## <a name="threat-model"></a>Veszélyforrások modellje
A veszélyforrások modellezése a potenciális biztonsági fenyegetések azonosításának folyamata a vállalat és az alkalmazás számára, majd a megfelelő kockázatcsökkentő terv biztosítása.

Ez a példa a [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) használta a biztonságos minta alkalmazás veszélyforrások modellezésének megvalósítására. Az összetevők és az adatfolyamatok diagramon való kiépítésével a fejlesztési folyamat elején azonosíthatja a problémákat és a fenyegetéseket. Ez később időt és pénzt takarít meg.

Ez a minta alkalmazáshoz tartozó veszélyforrás-modell:

![Veszélyforrások modellje](./media/secure-pci-web-app/threat-model.png)

A veszélyforrások modellezése eszköz által generált veszélyforrások és potenciális sebezhetőségek a következő képernyőképen jelennek meg. A fenyegetés modell áttekintést nyújt a feltett támadási felületről, és felszólítja a fejlesztőket a problémák enyhítésére.

![A veszélyforrások modell kimenete](./media/secure-web-app/threat-model-output.png)

Az előző veszélyforrás-modell kimenetének SQL-injektálását például a felhasználói bevitelek tisztításával és a Azure Database for PostgreSQL tárolt funkcióinak használatával csökkenthetők. Ez a megoldás megakadályozza a lekérdezések tetszőleges végrehajtását az adatolvasások és írások során.

A fejlesztők javítják a rendszerek általános biztonságát azáltal, hogy elhárítják az egyes fenyegetéseket a veszélyforrások modelljének kimenetében.

## <a name="deployment"></a>Üzembe helyezés
### <a name="prerequisites"></a>Előfeltételek
Az alkalmazás működésének megkezdéséhez telepítenie kell az alábbi eszközöket:

- Az alkalmazás kódjának módosítására és megtekintésére szolgáló Kódszerkesztő. A [Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú megoldás.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszeren. A git a forráskód helyi klónozására szolgál.
- [jQ](https://stedolan.github.io/jq/), egy UNIX-eszköz, amely felhasználóbarát módon kérdezi le a JSON-t.

Ez a példa JSON-konfigurációs fájlokból és PowerShell-parancsfájlokból áll, amelyeket az Azure-beli erőforrások üzembe helyezéséhez Azure Resource Manager API-szolgáltatása kezel. A részletes üzembe helyezési utasítások [itt](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)érhetők el.

#### <a name="quickstart"></a>Első lépések

1.  [A GitHub-](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) tárház klónozása vagy letöltése a helyi munkaállomásra.
2.  Tekintse át a 0-Setup-AdministrativeAccountAndPermission.md, és futtassa a megadott parancsokat.
3.  Tesztelési megoldás üzembe helyezése contoso-mintaadatok vagy kezdeti éles környezet kipróbálása.

    Ez a szkript üzembe helyezi az Azure-erőforrásokat egy webes áruháznak a contoso-mintaadatok használatával történő bemutatásához.

Ebben a példában azt a telepítési parancsfájlt futtatja, amely telepíti a webalkalmazást App Service és létrehozza az erőforrásokat.

Az Azure-ban számos módon telepíthet alkalmazásokat, beleértve a következőket:

- Azure Resource Manager-sablonok
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="network"></a>Network (Hálózat)
Az architektúra a 10.200.0.0/16 címtartomány által definiált privát Virtual Network.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Hálózati biztonsági csoportok (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) olyan Access Control listákat (ACL-eket) tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A hálózati biztonsági csoportok használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő hálózati biztonsági csoportok léteznek:

- 1 hálózati biztonsági csoport Application Gateway
- 1 hálózati biztonsági csoport App Service Environment
- 1 hálózati biztonsági csoport Azure SQL Database
- 1 hálózati biztonsági csoport a megerősített gazdagéphez

A hálózati biztonsági csoportok mindegyike megnyitotta a megadott portokat és protokollokat, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk mindegyik hálózati biztonsági csoport esetében engedélyezve vannak:

- Diagnosztikai naplók és események (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) egy Storage-fiókban engedélyezve és tárolva
- Azure Monitor naplók csatlakoznak a hálózati biztonsági csoport diagnosztika (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG-konfiguráció
Az App Service Environment NSG-konfigurációját az alábbi képen látható módon kell konfigurálni.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Minden alhálózat társítva van a hozzá tartozó hálózati biztonsági csoporttal.

### <a name="config"></a>Konfigurálás
Az alhálózatok konfigurálása az alábbi képen látható módon történik.
 ![Konfigurálás](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
A tartománynévrendszer (DNS) felelős a webhelyek vagy szolgáltatások nevének az IP-címére való fordításához (vagy feloldásához). A [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak az Azure-infrastruktúra használatával. Az Azure-ban a felhasználók a DNS-rekordokat a többi Azure-szolgáltatással azonos hitelesítő adatok, API-k, eszközök és számlázás használatával kezelhetik. A Azure DNS támogatja a magánhálózati DNS-tartományokat is.

### <a name="protect-data"></a>Adatok védelme
A felhőben tárolt adatainak védelme érdekében figyelembe kell vennie azokat a lehetséges állapotokat, amelyekben az adatai megjelenhetnek, és hogy milyen vezérlők érhetők el az adott állapotban. Az Azure-Adatbiztonság és-titkosítás ajánlott eljárásai a következő adatállapotokhoz kapcsolódnak:

- Nyugalmi állapotban: Ez magában foglalja az összes olyan adattárolási objektumot, tárolót és típust, amelyek statikusan léteznek fizikai adathordozón, akár mágneses, akár optikai lemez esetén.
- Tranzitban: Ha az adatátvitelt az összetevők, a helyszínek vagy a programok között végzik, az átvitel zajlik. Ilyenek például a hálózaton keresztüli átvitel (a helyszíniről a felhőbe és fordítva, beleértve a hibrid kapcsolatokat, például a ExpressRoute), vagy egy bemeneti/kimeneti folyamat során.

### <a name="azure-storage"></a>Azure Storage
A titkosított adatok Rest-követelményekkel való teljesítéséhez az összes [Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault használja az adatokhoz hozzáférő és titkosítható kulcsok felügyeletének fenntartásához. Ez segíti a kártyabirtokosok adatainak védelmét és védelmét a PCI DSS 3,2 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

### <a name="azure-sql-database"></a>Azure SQL Database
A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:

- [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
- Az [SQL Database naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
- Azure SQL Database úgy van konfigurálva, hogy [transzparens adattitkosítást](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használjon, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
- A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- Az [SQL-veszélyforrások észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
- A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg az adatbázis-rendszeren belüli egyszerű szövegként. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít az adathozzáférés azonosításában és csökkentésében, hogy ne lépjen ki az adatbázisból jogosulatlan hozzáférés útján. Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák lehetővé teszik a kártyatulajdonos-információhoz való hozzáférés kezelését az Azure-környezetben:

- Azure Active Directory a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információt az [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) (Alkalmazások integrációja az Azure Active Directory-val) című témakörben találhat. Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Az Azure szerepköralapú hozzáférés-vezérlés lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket engedélyezhetik a kártyatulajdonos-adatok eléréséhez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- Azure Active Directory Privileged Identity Management lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz, például a kártyatulajdonos adataihoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- Azure Active Directory Identity Protection észleli a szervezet identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy megtegye a megfelelő lépéseket a megoldásához.

### <a name="secrets-management"></a>Titkok kezelése
A megoldás a kulcsok és titkok kezeléséhez Azure Key Vault használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa HSM-védelemmel ellátott 2048-bites RSA-kulcs.
- A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával
- A RBAC segítségével engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben.
- A TLS-tanúsítványok automatikus megújítással történő kezeléséhez használja a Key Vault.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

### <a name="azure-security-center"></a>Azure Security Center
A Azure Security Center segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center előre meghatározott biztonsági riasztásokkal rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center egyéni riasztási szabályai lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos fenyegetési intelligenciáról szóló jelentés jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Az architektúra csökkenti a biztonsági rések kockázatát egy olyan Azure-Application Gateway használatával, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabályrendszert engedélyezve van. A további funkciók a következők:

- Végpontok közötti SSL
- A TLS 1.0-s és 1.1-es verziójának letiltása
- TLS 1.2 engedélyezése
- Webalkalmazási tűzfal (megelőzési mód)
- Megelőzési mód OWASP 3,0-es szabályrendszert
- Diagnosztikai naplózás engedélyezése
- Egyéni állapot-mintavételek
- A Azure Security Center és Azure Advisor további védelmet és értesítéseket biztosítanak. A Azure Security Center a hírnév rendszerét is biztosítja.

### <a name="logging-and-auditing"></a>Naplózás
Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:

- A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- A [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adatokat Log Analytics munkaterületeken belül minden adattípushoz, ami lehetővé teszi, hogy az összes adatokat együtt elemezze az eredeti forrástól függetlenül. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek:

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára vonatkozó ajánlások rangsorolt listáját biztosítja.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve, valamint azok földrajzi eloszlása, valamint hogy hány ügynök nem válaszol, és milyen ügynököket küld a rendszer a működési adatként.
- [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a Activity log Analytics megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzésében az ügyfelekhez tartozó összes Azure-előfizetésen belül.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

### <a name="application-insights"></a>Application Insights
A [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető Application Performance Management szolgáltatás a webes fejlesztők számára több platformon. Application Insights észleli a teljesítménnyel kapcsolatos rendellenességeket, és az ügyfelek használhatják az élő webalkalmazás figyelését. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével az ügyfelek diagnosztizálják a problémákat, és megtudhatják, hogy a felhasználók hogyan használják ténylegesen az alkalmazást. A megoldás célja, hogy az ügyfelek folyamatosan javítsák a teljesítményt és a használhatóságot.

### <a name="azure-key-vault"></a>Azure Key Vault
Hozzon létre egy tárolót ahhoz a szervezethez, amelyben a kulcsokat tárolni kívánja, valamint az operatív feladatok elszámoltathatóságának fenntartása az alábbihoz hasonló módon:

- A Key Vaultban tárolt adatkészletek a következők:

   - Alkalmazás Insight-kulcsa
   - Adattároló-hozzáférési kulcs
   - Kapcsolati sztring
   - Adattábla neve
   - Felhasználói hitelesítő adatok

- A speciális hozzáférési szabályzatok igény szerint vannak konfigurálva
- Key Vault hozzáférési szabályzatok minimálisan szükséges engedélyekkel rendelkeznek a kulcsok és a titkos kulcsokhoz
- A Key Vault összes kulcsának és titkának lejárati dátuma
- Key Vault összes kulcsát HSM védi [kulcs típusa = HSM által védett 2048-bites RSA-kulcs]
- Minden felhasználó/identitás minimálisan szükséges engedélyeket kap a szerepköralapú Access Control (RBAC) használatával
- Az alkalmazások nem osztják meg Key Vault, hacsak nem bíznak egymással, és a futtatáskor ugyanazokat a titkokat kell elérniük
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek a szükséges értékekre vannak korlátozva

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságosan létre lehessen hozni egy kapcsolatot a jelen Pásti webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az IPsec-alagút mód titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
Ha még nem rendelkezik Azure-fiókkal, akkor létrehozhat egy ingyenes fiókot. A kezdéshez nyissa meg az [ingyenes fiók lapot](https://azure.microsoft.com/free/) , és tekintse meg, hogy mit tehet egy ingyenes Azure-fiókkal, és hogy mely termékek ingyenesek 12 hónapig.

Ha a minta alkalmazásban lévő erőforrásokat a biztonsági funkciókkal szeretné üzembe helyezni, a prémium funkciókért kell fizetnie. Az alkalmazások skálázása, valamint az Azure által kínált ingyenes csomagok és kísérletek frissítése az alkalmazásokra vonatkozó követelmények kielégítése érdekében várható, hogy a költségek növekednek. A költségek becsléséhez használja az Azure [díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/) .

## <a name="next-steps"></a>További lépések
A következő cikkek segíthetnek a biztonságos alkalmazások megtervezésében, fejlesztésében és üzembe helyezésében.

- [Kialakítás](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Telepítés](secure-deploy.md)
