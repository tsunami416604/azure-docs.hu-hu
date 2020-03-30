---
title: Biztonságos webalkalmazás a PCI DSS-hez | Microsoft dokumentumok
description: Ez a mintaalkalmazás olyan biztonsági gyakorlati tanácsokat valósít meg, amelyek javítják az alkalmazást és a szervezet biztonsági állapotát az Azure-ban való fejlesztés során.
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
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Biztonságos infrastruktúra kialakítása pci alkalmazáshoz

## <a name="overview"></a>Áttekintés

Ez a biztonságos infrastruktúra a fizetésikártya-ipar (PCI) alkalmazás hoz útmutatást a fizetésikártya-ipari platform szolgáltatásként (PaaS) környezetként történő telepítéséhez, amely alkalmas a kártyatulajdonosi adatok gyűjtésére, tárolására és lekérésére. Ez a megoldás automatizálja az Azure-erőforrások üzembe helyezését és konfigurálását egy közös referenciaarchitektúrához, bemutatva, hogy az ügyfelek milyen módon tudnak megfelelni bizonyos biztonsági és megfelelőségi követelményeknek, és amely az ügyfelek számára a létrehozás és a konfigurálják saját megoldásaikat az Azure-ban. A megoldás a payment card industry data security szabványokhoz (PCI DSS 3.2) hasonló követelmények egy részét valósítja meg.

Ez a minta automatikusan telepíti a PaaS webalkalmazás referenciaarchitektúráját előre konfigurált biztonsági vezérlőkkel, hogy az ügyfelek a PCI DSS 3.2 követelményeihez hasonló megfelelőséget érjenek el. A megoldás az Azure Resource Manager-sablonokból és az erőforrások üzembe helyezését és konfigurálását irányító PowerShell-parancsfájlokból áll.

Az alkalmazás-összetevők megfelelő konfigurálásának biztosításához egymás után kövesse a cikkben ismertetett lépéseket. Az adatbázis, az Azure App Service, az Azure Key Vault-példány és az Azure Application Gateway-példány egymástól függ.

A központi telepítési parancsfájlok beállítják az infrastruktúrát. A központi telepítési parancsfájlok futtatása után az Azure Portalon manuálisan kell konfigurálnia az összetevők és szolgáltatások összekapcsolását.

Ez a minta az Azure tapasztalt fejlesztőinek szól, akik a kiskereskedelmi ágazatban dolgoznak, és biztonságos Azure-infrastruktúrával rendelkező kiskereskedelmi alkalmazást szeretnének építeni.

> [!NOTE]
> Ez az architektúra célja, hogy alapul szolgáljon az ügyfelek számára, hogy alkalmazkodjanak a saját igényeikhez, és nem használható éles környezetben.

Egy alkalmazás módosítás nélküli üzembe helyezése ebben a környezetben nem elegendő a PCI DSS 3.2 követelményeinek teljes teljesítéséhez. Vegye figyelembe a következőket:

- Ez az architektúra alapkonfigurációt biztosít, amely segíti az ügyfeleket az Azure PCI DSS 3.2-kompatibilis módon történő használatához.
- Az ügyfelek felelősek az ezzel az architektúrával készített megoldások megfelelő biztonsági és megfelelőségi értékeléséért, mivel a követelmények az egyes ügyfelek megvalósításának sajátosságaitól függően változhatnak.

Az alkalmazás fejlesztése és üzembe helyezése során megtudhatja, hogyan:

- Hozzon létre egy Azure Key Vault-példányt, és tárolja és olvassa le a titkokat.
- Telepítse az Azure Database for Azure SQL-t, állítson be biztonságos adatokat, és engedélyezze a hozzáférést.
- Telepítse az Azure webapp app szolgáltatás környezet, amely egy dedikált elkülönített előtér-tűzfal aEcess.
- Hozzon létre és konfiguráljon egy Azure Application Gateway-példányt egy [OWASP Top 10 Ruleset](https://coreruleset.org/)rendszert használó tűzfallal.
- Engedélyezze az adatok titkosítását az átvitel és az inaktív szolgáltatások használatával.
- Állítsa be az Azure-szabályzatot és a kék nyomatokat a megfelelőség kiértékeléséhez

Az alkalmazás fejlesztése és üzembe helyezése után a következő minta webalkalmazást, valamint a leírt konfigurációs és biztonsági intézkedéseket fogja beállítani.

## <a name="architecture-diagram-and-components"></a>Építészeti diagram és összetevők
Az alkalmazás egy tipikus n-szintű alkalmazás három réteggel. Az előtér, a háttérrendszer és az integrált figyelési és titkos felügyeleti összetevőket tartalmazó adatbázisréteg itt látható:

![Alkalmazásarchitektúra](./media/secure-pci-web-app/architecture.png)

Az architektúra a következő összetevőkből áll:

- [App Service-környezet v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Az Alkalmazásszolgáltatás-környezetet és az alkalmazásarchitektúrához az App Service Environment és a terheléselosztó biztosít.
- [Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/)alkalmazást. Biztosítja az átjárót és a tűzfalat az alkalmazásarchitektúrához.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatást biztosít több platformon.
- [Az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Tárolja és titkosítja az alkalmazás titkosítatait, és kezeli a hozzáférési szabályzatok létrehozását körülöttük.
- [Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Felhőalapú identitás- és hozzáférés-kezelési szolgáltatást, bejelentkezést és hozzáférési erőforrásokat biztosít.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). A tartomány üzemeltetéséhez a szolgáltatást biztosítja.
- [Az Azure terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Méretezi az alkalmazásokat, és magas rendelkezésre állást biztosít a szolgáltatások számára
- [Az Azure Storage szolgáltatást.](https://docs.microsoft.com/azure/storage/common/storage-introduction/) Megoldást kínál a modern adattárolási forgatókönyvekhez.
- [Az Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  HTTP-alapú szolgáltatást biztosít a webalkalmazások üzemeltetéséhez.
- [Azure Database for AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Biztonságosan tárolja alkalmazásunk adatait.
- [Azure blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview/). Specifikációkat és bizonyos szabványoknak és követelményeknek való megfelelést biztosít.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure-szabályzat](https://docs.microsoft.com/azure/governance/policy/overview). Kiértékeli az erőforrásokat, hogy nem felelnek-e meg a hozzárendelt szabályzatoknak.

## <a name="threat-model"></a>Fenyegetésmodell
A fenyegetésmodellezés az üzleti és alkalmazásra leselkedő potenciális biztonsági fenyegetések azonosításának folyamata, majd annak biztosítása, hogy megfelelő kockázatcsökkentési terv legyen érvényben.

Ez a minta a [Microsoft Threat Modeling Tool segítségével](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) valósította meg a biztonságos mintaalkalmazás fenyegetésmodellezését. Az összetevők és az adatfolyamok diagrammal azonosíthatja a problémákat és fenyegetéseket a fejlesztési folyamat korai szakaszában. Ez időt és pénzt takarít meg később.

Ez a mintaalkalmazás fenyegetésmodellje:

![Fenyegetésmodell](./media/secure-pci-web-app/threat-model.png)

Néhány minta fenyegetések és a potenciális biztonsági rések, amelyek a fenyegetés modellezési eszköz generálja a következő képernyőképen látható. A fenyegetésmodell áttekintést ad a támadási felület elérhetővé, és kéri a fejlesztőket, hogy gondolja át, hogyan lehet enyhíteni a problémákat.

![Fenyegetésmodell kimenete](./media/secure-web-app/threat-model-output.png)

Például az SQL-injektálás az előző fenyegetésmodell kimenetét a felhasználói bemenetek fertőtlenítése és a tárolt függvények használatával az Azure Database for PostgreSQL használatával. Ez a megoldás megakadályozza a lekérdezések tetszőleges végrehajtását az adatok olvasása és írása során.

A fejlesztők a fenyegetésmodell kimenetében lévő fenyegetések csökkentésével javítják a rendszer általános biztonságát.

## <a name="deployment"></a>Környezet
### <a name="prerequisites"></a>Előfeltételek
Az alkalmazás üzembe kapcsolásához telepítenie kell az alábbi eszközöket:

- Kódszerkesztő az alkalmazáskód módosításához és megtekintéséhez. [A Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú lehetőség.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszer. A Git a forráskód helyi klónozására szolgál.
- [jq](https://stedolan.github.io/jq/), egy UNIX eszköz a JSON felhasználóbarát lekérdezésére.

Ez a minta JSON konfigurációs fájlokból és PowerShell-parancsfájlokból áll, amelyeket az Azure Resource Manager API-szolgáltatása kezel az Azure-on belüli erőforrások üzembe helyezéséhez. A részletes telepítési utasítások [itt](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)találhatók.

#### <a name="quickstart"></a>Első lépések

1.  Klónozza vagy töltse le [ezt a](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub-tárházat a helyi munkaállomásra.
2.  Tekintse át 0-Setup-AdministrativeAccountAndPermission.md, és futtassa a megadott parancsokat.
3.  Telepítsen egy tesztmegoldást a Contoso mintaadatokkal, vagy tesztelje a kezdeti éles környezetet.

    Ez a parancsfájl üzembe helyezi az Azure-erőforrásokat egy webáruház bemutatója contosominta adatok használatával.

Ebben a példában futtatja a központi telepítési parancsfájlt, amely telepíti a webalkalmazást az App Service-re, és létrehozza az erőforrásokat.

Az Azure-ban számos módon telepíthet alkalmazásokat, többek között:

- Azure Resource Manager-sablonok
- PowerShell
- Azure CLI
- Azure portál
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Útmutatás és ajánlások

### <a name="network"></a>Network (Hálózat)
Az architektúra egy 10.200.0.0/16 címterülettel rendelkező privát virtuális hálózatot határoz meg.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Hálózati biztonságihttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) csoportok( hozzáférés-vezérlési listákat (ACL-eket) tartalmaznak, amelyek engedélyezik vagy elutasítják a forgalmat egy virtuális hálózaton belül. A hálózati biztonsági csoportok alhálózati vagy egyedi virtuális gép szintű forgalom védelmére használhatók. A következő hálózati biztonsági csoportok léteznek:

- 1 hálózati biztonsági csoport az Application Gateway alkalmazáshoz
- 1 hálózati biztonsági csoport az App Service Environment számára
- 1 hálózati biztonsági csoport az Azure SQL Database-hez
- 1 hálózati biztonsági csoport a megerősített állomáshoz

A hálózati biztonsági csoportok mindegyike rendelkezik megnyitott portokkal és protokollokkal, így a megoldás biztonságosan és helyesen működik. Ezenkívül a következő konfigurációk vannak engedélyezve minden egyes hálózati biztonsági csoporthoz:

- Diagnosztikai naplók éshttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) események( engedélyezettek és tárolódnak egy tárfiókban
- Az Azure Monitor naplói a hálózati biztonsági csoport diagnosztikájához kapcsolódnak(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG konfiguráció
Az NSG config for App Service Environment szolgáltatást az alábbi képen látható módon kell konfigurálni.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Minden alhálózat a megfelelő hálózati biztonsági csoporthoz van társítva.

### <a name="config"></a>Konfigurálás
Az alhálózatok az alábbi képen látható módon vannak konfigurálva.
 ![Konfigurálás](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
A DOMAIN Name System (DNS) felelős a webhely vagy szolgáltatás nevének IP-címre történő fordításáért (vagy feloldásáért). [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) egy olyan DNS-tartományok üzemeltetési szolgáltatása, amely az Azure-infrastruktúra használatával névfeloldást biztosít. A tartományok Azure-beli üzemeltetésével a felhasználók ugyanolyan hitelesítő adatokkal, API-kkal, eszközökkel és számlázással kezelhetik a DNS-rekordokat, mint más Azure-szolgáltatások. Az Azure DNS támogatja a magán DNS-tartományokat is.

### <a name="protect-data"></a>Adatok védelme
A felhőben lévő adatok védelme érdekében figyelembe kell vennie azokat a lehetséges állapotokat, amelyekben az adatok előfordulhatnak, és milyen vezérlők érhetők el az adott állapothoz. Az Azure-adatok biztonsága és titkosítása ajánlott eljárások a következő adatállapotokra vonatkoznak:

- Nyugalmi állapotban: Ez magában foglalja az összes olyan információtároló objektumot, tárolót és típust, amely statikusan létezik a fizikai adathordozón, legyen az mágneses vagy optikai lemez.
- Átvitel közben: Amikor az adatok átvitele összetevők, helyek vagy programok között történik, akkor az átvitel alatt van. Ilyenek például a hálózaton keresztül, egy szolgáltatásbuszon keresztül (a helyszíni felhőbe és fordítva, beleértve a hibrid kapcsolatok, például ExpressRoute), vagy egy bemeneti/kimeneti folyamat során.

### <a name="azure-storage"></a>Azure Storage
To meet encrypted data at rest requirements, all [Azure Storage](https://azure.microsoft.com/services/storage/) uses Azure Key Vault to maintain control of keys that access and encrypt the data. Ez segít megvédeni és megvédeni a kártyabirtokosok adatait a PCI DSS 3.2 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatása érdekében.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Az Azure Disk Encryption a Windows BitLocker szolgáltatását használja az adatlemezek kötettitkosításának biztosításához. A megoldás integrálható az Azure Key Vault segítségével a lemeztitkosítási kulcsok vezérlése és kezelése.

### <a name="azure-sql-database"></a>Azure SQL Database
Az Azure SQL Database-példány a következő adatbázis-biztonsági intézkedéseket használja:

- [Az Active Directory hitelesítése és engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitáskezelését egy központi helyen.
- [AZ SQL-adatbázis naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) nyomon követi az adatbázis-eseményeket, és egy Azure-tárfiók naplójába írja őket.
- Az Azure SQL Database [transzparens adattitkosításhasználatára](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)van konfigurálva, amely valós idejű titkosítást és visszafejtést hajt végre az adatbázis, a kapcsolódó biztonsági mentések és a tranzakciós naplófájlok használatával az inaktív adatok védelme érdekében. Az átlátszó adattitkosítás garantálja, hogy a tárolt adatokhoz nem történt jogosulatlan hozzáférés.
- [A tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálókhoz való hozzáférést, amíg megfelelő engedélyeket nem kapnak. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- [Az SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi a potenciális fenyegetések észlelését és válaszát a gyanús adatbázis-tevékenységek, a potenciális biztonsági rések, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták észlelése és válasza.
- [A titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázisrendszeren belül. Az adattitkosítás engedélyezése után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy alkalmazáskiszolgálók férhetnek hozzá a sima szöveges adatokhoz.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok nak való kitettséget azáltal, hogy elfedi az adatokat a nem kiemelt jogosultságú felhasználók vagy alkalmazások számára. A dinamikus adatmaszkolás automatikusan felderítheti a potenciálisan bizalmas adatokat, és javasolhatja a megfelelő maszkokat. Ez segít az adatokazonosításában és az adatokhoz való hozzáférés csökkentésében, hogy az ne lépjen ki az adatbázisból jogosulatlan hozzáféréssel. Az ügyfelek felelősek a dinamikus adatmaszkolási beállítások módosításáért, hogy azok megfeleljenek az adatbázissémájuknak.

### <a name="identity-management"></a>Identitáskezelés
A következő technológiák lehetővé tetszetést biztosítanak a kártyabirtokosi adatokhoz való hozzáférés kezeléséhez az Azure-környezetben:

- Az Azure Active Directory a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. A megoldás minden felhasználója az Azure Active Directoryban jön létre, beleértve az Azure SQL-adatbázishoz hozzáférő felhasználókat is.
- Az alkalmazás hitelesítése az Azure Active Directory használatával történik. További információt az [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) (Alkalmazások integrációja az Azure Active Directory-val) című témakörben találhat. Emellett az adatbázis oszlop titkosítása az Azure Active Directory segítségével hitelesíti az alkalmazást az Azure SQL Database. További információ: [A bizalmas adatok védelme az Azure SQL Database-ben.](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- Az Azure szerepköralapú hozzáférés-vezérlés lehetővé teszi a rendszergazdák számára, hogy részletes hozzáférési engedélyeket határozzanak meg, hogy csak annyi hozzáférést adjanak a felhasználóknak, amennyia feladataik elvégzéséhez szükséges. Ahelyett, hogy minden felhasználó nak korlátlan engedélyt az Azure-erőforrások, a rendszergazdák engedélyezhetik csak bizonyos műveletek eléréséhez kártyatulajdonos iadatok eléréséhez. Az előfizetési hozzáférés az előfizetés rendszergazdájára korlátozódik.
- Az Azure Active Directory kiemelt identitáskezelés lehetővé teszi az ügyfelek számára, hogy minimálisra csökkentsék a felhasználók száma, akik hozzáférnek bizonyos információkhoz, például a kártyatulajdonos adataihoz. A rendszergazdák az Azure Active Directory kiemelt identitáskezelés segítségével felderíthetik, korlátozhatják és figyelhetik a kiemelt identitásokat és az erőforrásokhoz való hozzáférésüket. Ez a funkció igény szerinti, igény szerinti rendszergazdai hozzáférés kényszerítésére is használható, ha szükséges.
- Az Azure Active Directory Identity Protection észleli a szervezet identitásait érintő potenciális biztonsági réseket, konfigurálja az automatikus válaszokat a szervezet identitásaihoz kapcsolódó gyanús műveletekre, és kivizsgálja a gyanús eseményeket, hogy megtegyék a megfelelő lépéseket azok megoldására.

### <a name="secrets-management"></a>Titkok kezelése
A megoldás az Azure Key Vault a kulcsok és a titkos kulcsok kezelésére. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítenek az ügyfeleknek az ilyen adatok védelmében és elérésében:

- A speciális hozzáférési házirendek igény szerint vannak konfigurálva.
- A Key Vault hozzáférési szabályzatai a kulcsokhoz és titkos kulcsokhoz szükséges minimális anamcsal vannak definiálva.
- A Key Vault minden kulcsa és titkos kulcsa lejárati dátummal rendelkezik.
- A Key Vault ban lévő összes kulcsot speciális hardveres biztonsági modulok védik. A kulcstípusa egy HSM-védelemmel ellátott 2048 bites RSA kulcs.
- A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat, a . PFX-fájlok és jelszavak) hardveres biztonsági modulokkal (HSM) védett billentyűkkel
- Az RBAC használatával engedélyeket rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben.
- A Key Vault segítségével automatikus megújítással kezelheti a TLS-tanúsítványokat.
- A Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal vannak engedélyezve.
- A kulcsok engedélyezett titkosítási műveletei a szükséges kulcsokra korlátozódnak.

### <a name="azure-security-center"></a>Azure Security Center
Az Azure Security Center segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a biztonsági házirendeket a számítási feladatok között, korlátozhatják a fenyegetéseknek való kitettséget, és észlelhetik és reagálhatnak a támadásokra. Emellett az Azure Security Center az Azure-szolgáltatások meglévő konfigurációihoz is hozzáfér, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzet javítása és az adatok védelme érdekében.

Az Azure Security Center számos észlelési lehetőséget használ, hogy figyelmeztesse az ügyfeleket a környezetüket célzó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center egy előre definiált biztonsági riasztások készletét, amelyek akkor aktiválódnak, ha egy fenyegetés, vagy gyanús tevékenység történik. Az Azure Security Center egyéni riasztási szabályai lehetővé teszik az ügyfelek számára, hogy új biztonsági riasztásokat határozzanak meg a környezetükből már gyűjtött adatok alapján.

Az Azure Security Center kiemelt biztonsági riasztásokat és incidenseket biztosít, így az ügyfelek egyszerűbben fedezhetik fel és oldják meg a lehetséges biztonsági problémákat. A fenyegetések felderítéséről szóló jelentés minden észlelt fenyegetés, hogy segítse az incidens-elhárítási csapatok a fenyegetések kivizsgálásában és elhárításában.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Az architektúra csökkenti a biztonsági rések kockázatát egy webalkalmazás-tűzfallal rendelkező Azure Application Gateway használatával, és az OWASP szabálykészlet engedélyezve van. További lehetőségek a következők:

- Végpontok között SSL
- A TLS 1.0-s és 1.1-es és 1.1-es frissítésének letiltása
- TLSv1.2 engedélyezése
- Webalkalmazás tűzfala (megelőzési mód)
- Megelőzési mód oWASP 3.0 szabálykészlettel
- Diagnosztikai naplózás engedélyezése
- Egyéni állapotminta-szondák
- Az Azure Security Center és az Azure Advisor további védelmet és értesítéseket biztosít. Az Azure Security Center is biztosít egy jó hírnév rendszer.

### <a name="logging-and-auditing"></a>Naplózás
Az Azure-szolgáltatások széles körben naplózzák a rendszer- és felhasználói tevékenységeket, valamint a rendszer állapotát:

- [A tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. A tevékenységnaplók segíthetnek meghatározni a művelet kezdeményezőjének, az előfordulás idejét és állapotát.
- [A diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) tartalmazzák az összes erőforrás által kibocsátott összes naplót. Ezek a naplók közé tartoznak a Windows eseményrendszer-naplók, az Azure Storage-naplók, a Key Vault naplózási naplói, valamint az Application Gateway-hozzáférés és a tűzfalnaplók. Minden diagnosztikai naplók írása egy központi és titkosított Azure storage-fiók archiválási. Az adatmegőrzés felhasználó által konfigurálható, akár 730 napig, hogy megfeleljen a szervezet-specifikus megőrzési követelményeknek.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Ezek a naplók az [Azure Monitor naplók](https://azure.microsoft.com/services/log-analytics/) feldolgozása, tárolása és irányítópult-jelentéskészítési konszolidált. Az adatgyűjtést követően az adatok külön táblákba vannak rendezve a Log Analytics-munkaterületeken belüli egyes adattípusokhoz, ami lehetővé teszi az összes adat együttes elemzését, függetlenül az eredeti forrástól. Továbbá az Azure Security Center integrálható az Azure Monitor naplóival, így az ügyfelek kusto-lekérdezések használatával hozzáférhetnek a biztonsági eseményadataikhoz, és kombinálhatják azokat más szolgáltatásokból származó adatokkal.

Az alábbi [Azure-figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) az architektúra részét képezik:

- [Active Directory-felmérés:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)Az Active Directory állapot-ellenőrzési megoldás rendszeres időközönként felméri a kiszolgálói környezetek kockázatát és állapotát, és rangsorolt listát ad a telepített kiszolgálói infrastruktúrára vonatkozó javaslatokról.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL állapot-ellenőrzés megoldás rendszeres időközönként felméri a kiszolgálói környezetek kockázatát és állapotát, és az ügyfelek számára a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját biztosítja.
- [Ügynök állapota:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)Az ügynök állapotmegoldás jelenti, hogy hány ügynökök vannak telepítve, és azok földrajzi eloszlása, valamint hány ügynökök nem válaszol, és az ügynökök száma, amely működési adatokat küld.
- [Tevékenységnapló-elemzés:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)A Tevékenységnapló-elemzési megoldás az Azure-tevékenységnaplók elemzését segíti az összes Azure-előfizetésben egy ügyfél számára.

### <a name="azure-monitor"></a>Azure Monitor
[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segítségével a felhasználók nyomon követhetik a teljesítményt, fenntarthatja a biztonságot, és azonosíthatja a trendeket azáltal, hogy lehetővé teszik a szervezetek számára az adatok naplózását, a riasztások létrehozását és az adatok archiválását, beleértve az API-hívások nyomon követését az Azure-erőforrásokban.

### <a name="application-insights"></a>Application Insights
[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-kezelési szolgáltatás több platformon a webfejlesztők számára. Az Application Insights észleli a teljesítményanomáliákat, és az ügyfelek használhatják az élő webalkalmazás figyelésére. Hatékony elemzési eszközöket tartalmaz, amelyek segítenek az ügyfeleknek diagnosztizálni a problémákat, és megérteni, hogy a felhasználók valójában mit csinálnak az alkalmazásukkal. Úgy tervezték, hogy segítsen az ügyfeleknek folyamatosan javítani a teljesítményt és a használhatóságot.

### <a name="azure-key-vault"></a>Azure Key Vault
Hozzon létre egy tárolót a szervezet számára, amelyben kulcsokat tárolhat, és tartsa fenn az elszámoltathatóságot az alábbihoz hasonló operatív feladatok esetében:

- A Key Vaultban tárolt adatok a következőket tartalmazzák:

   - Alkalmazásélesnek kulcs
   - Adattárolási hozzáférési kulcs
   - Kapcsolati sztring
   - Adattábla neve
   - Felhasználói hitelesítő adatok

- A speciális hozzáférési házirendek igény szerint vannak konfigurálva
- A Key Vault hozzáférési szabályzatai a kulcsokhoz és titkos kulcsokhoz szükséges minimális engedélyekkel vannak definiálva
- A Key Vault összes kulcsa és titkos kulcsa lejárati dátummal rendelkezik
- A Key Vault összes kulcsát hsm védi [Kulcstípus = HSM védett 2048 bites RSA-kulcs]
- Minden felhasználó/identitás minimálisan szükséges engedélyekkel rendelkezik a szerepköralapú hozzáférés-vezérlés (RBAC) használatával
- Az alkalmazások csak akkor osztoznak a Key Vaulton, ha megbíznak egymásban, és futásidőben hozzá kell férniük ugyanazokhoz a titkos kulcsokhoz.
- A Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal vannak engedélyezve.
- A kulcsok engedélyezett kriptográfiai műveletei a szükséges kulcsokra korlátozódnak

### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-bújtatás vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságosan hozzon létre egy kapcsolatot a PaaS webalkalmazás referencia-architektúra részeként üzembe helyezett erőforrásokkal. A VPN vagy expressroute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitel alatt álló adatokszámára.

Egy biztonságos VPN-alagút megvalósítása az Azure-ral, egy virtuális privát kapcsolat egy helyszíni hálózat és egy Azure virtuális hálózat hozható létre. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy biztonságosan "bújtassák" az információkat az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül. A helyek közötti VPN egy biztonságos, kiforrott technológia, amelyet a vállalatok évtizedek óta alkalmaznak bármilyen méretű vállalatnál. Az IPsec-alagút mód ebben a beállításban titkosítási mechanizmusként használatos.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-nel halad át az interneten, a Microsoft egy másik, még biztonságosabb csatlakozási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-tárhelyszolgáltató között. Mivel az ExpressRoute-kapcsolatok nem futnak át az interneten, ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint az interneten keresztül isznak. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adatok nem utaznak az interneten keresztül, és ezért nincsenek kitéve annak.

A helyszíni hálózatot az Azure-ra kiterjesztő biztonságos hibrid hálózat megvalósításának gyakorlati [tanácsai elérhetők.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
Ha még nem rendelkezik Azure-fiókkal, létrehozhat egy ingyeneset. Az [ingyenes fiókoldalra](https://azure.microsoft.com/free/) feladhatja a kezdéshez, megtudhatja, hogy mit tehet egy ingyenes Azure-fiókkal, és megtudhatja, hogy mely termékek 12 hónapig ingyenesek.

Az erőforrások üzembe helyezéséhez a mintaalkalmazásban a biztonsági funkciókkal, meg kell fizetnie néhány prémium funkciók. Mivel az alkalmazás méretei és az Azure által kínált ingyenes szintek és próbaverziók frissítése az alkalmazás követelményeinek teljesítése érdekében, a költségek növekedhetnek. Az Azure [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével megbecsülheti a költségeket.

## <a name="next-steps"></a>További lépések
Az alábbi cikkek segítségével biztonságos alkalmazásokat tervezhet, fejleszthet és helyezhet üzembe.

- [Tervezés](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Telepítés](secure-deploy.md)
