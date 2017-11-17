---
title: "Az Azure támogatási feldolgozási tervezetének - tűzfalra vonatkozó követelmények"
description: "1. követelmény PCI DSS"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Tűzfalkövetelmények PCI DSS-kompatibilis környezetben 
## <a name="pci-dss-requirement-1"></a>1. követelmény PCI DSS

**Telepítheti és karbantarthatja a tűzfal beállításait a kártya tulajdonosát adatok védelme**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Tűzfalak olyan eszköz, amely egy entitás hálózatok (belső) között engedélyezett számítógép-forgalmat, és nem megbízható hálózatokon (külső), valamint a forgalom esetében bejövő és kimenő több érzékeny területekre egy entitáson belül belső megbízható hálózatok. A kártya tulajdonosát adatok környezet példája egy entitás megbízható hálózaton belül egy további érzékeny területre.
A tűzfal megvizsgálja az összes hálózati forgalom, és letiltja ezeket az átvitelt, amelyek nem felelnek meg a megadott biztonsági feltételeket.
Minden rendszer védeni kell a jogosulatlan hozzáférést a nem megbízható hálózatokhoz, hogy írja be a rendszer az interneten keresztül e-kereskedelmi, asztali böngészők, alkalmazott e-mail hozzáférés, például dedikált kapcsolatok keresztül alkalmazott Internet-hozzáférés vállalatok kapcsolatok, vezeték nélküli hálózatokon keresztül, vagy más forrásokból keresztül. Gyakran látszólag jelentéktelen elérési utak nem megbízható hálózatokon érkező vagy oda irányuló biztosíthat nem védett útvonallal kulcs rendszerekbe. Tűzfalak egy kulcs védelmi mechanizmust, a számítógép hálózathoz.
Egyéb rendszerösszetevők rendelkezhetnek tűzfal működését, amíg azok megfelelnek a tűzfalak vonatkozó minimális követelmények követelmény az 1. meghatározottak szerint. Ha egyéb rendszerösszetevők tűzfal funkcionalitással használnak a kártya tulajdonosát adatok környezeten belül, ezeknek az eszközöknek a hatókör és követelmény 1 értékelése kell lennie.

## <a name="pci-dss-requirement-11"></a>1.1 a PCI DSS követelmény

**1.1** jöjjön létre megvalósítása tűzfal vagy útválasztó konfigurációs szabványok gyűjteménye, amelyek a következők (lásd a 1.1.1 1.1.7 keresztül).


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló, a CDE firewalling PaaS-elkülönítéssel és biztosít egy App Service Environment-környezet végrehajtása biztosítja, hogy CDE bemenő és kimenő adatok védett legyen.<br /><br />Egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) a Premium szolgáltatás csomag megfelelőségi okokból használni. ASE vezérlők és a konfiguráció további információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>1.1.1 PCI DSS követelmény

**1.1.1** jóváhagyása és a hálózati kapcsolatokon és a módosítások tesztelését szolgáló formális folyamat a tűzfal vagy útválasztó konfigurációra


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló példánya hoz létre olyan CI/CD DevOps modell, győződjön meg arról, hogy minden változást megfelelően kezeli-e. [Az Operations Management Suite (OMS)](/azure/operations-management-suite/) változások kiterjedt naplózás biztosít. Módosítások tekintse át, és pontossága ellenőrizni. Részletesebb útmutatásért lásd: [PCI útmutatást - az Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központosított összes Azure-erőforrások biztonsági állapotát jeleníti meg. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesül, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.|



### <a name="pci-dss-requirement-112"></a>1.1.2 PCI DSS követelmény

**1.1.2** aktuális hálózati diagram, amely azonosítja a kártya tulajdonosát adatok környezet és más hálózatokkal, beleértve a vezeték nélküli hálózatok között, az összes kapcsolat

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Tekintse meg a Contoso webes tároló felépítéséről és kialakításáról referenciadokumentációt a telepítési minta a megoldás részeként.|



### <a name="pci-dss-requirement-113"></a>1.1.3 PCI DSS követelmény

**1.1.3** aktuális bemutató diagram, amelyek minden kártya tulajdonosát adat rendszerek és hálózatok között zajló kommunikációról

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló DFD és a fenyegetések modellezése hivatkozik.|



### <a name="pci-dss-requirement-114"></a>PCI DSS követelmény 1.1.4

**1.1.4** minden internetkapcsolat, és bármely demilitarizált zóna (DMZ) és a belső hálózati zóna között tűzfal követelményei

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure határ védelmi eszközök, például az átjárók, a hálózati hozzáférés-vezérlési listák és a külső és belső határok platform szintjén, a vezérlő kommunikáció tűzfalak funkcióit használja. Az ügyfél ezután konfigurálja azokat a saját specifikációkért és követelményekért. Microsoft Azure szűrők kommunikáció, amikor a platformra hamarosan. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nyújt PaaS-elkülönítéssel DMZ, és az App Service Environment-környezet végrehajtása biztosítja, hogy CDE bemenő és kimenő adatok védett legyen.<br /><br />Egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) a Premium szolgáltatás csomag megfelelőségi okokból használni. ASE vezérlők és a konfiguráció további információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>PCI DSS követelmény 1.1.5 azon esetekben

**1.1.5 azon esetekben** csoportok, szerepkörök és felelősségek hálózati összetevők kezelését leírása

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló használ [átruházásához hozzáférés-vezérlés (RBAC)](/azure/active-directory/role-based-access-control-configure) felhasználói szerepkörök elkülönítése. Szerepalapú lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Specifikus konfigurációk létezik előfizetés hozzáférést, és az Azure Key Vault hozzáférhetnek.|



### <a name="pci-dss-requirement-116"></a>PCI DSS követelmény 1.1.6

**1.1.6** üzleti indoklásának és a szolgáltatások, protokollok és portok engedélyezett, köztük dokumentációhoz ezekhez nem biztonságosnak tekintett tartozó végrehajtott biztonsági funkciók használata jóváhagyás dokumentációjában talál.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló csak a szükséges portok és protokollok a Regisztrációszolgáltató tervezési nyílik meg. Adatfolyam adatait a DFD és a fenyegetések modell látható.|



### <a name="pci-dss-requirement-117"></a>PCI DSS követelmény 1.1.7

**1.1.7** vonatkozó követelmény tekintse át a tűzfal vagy útválasztó szabály beállítja legalább hat havonta

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló, a tűzfal szabálykészletek javasoljuk, hogy ellenőrizze, hogy nincsenek a szükségtelen vagy a nem használt szabályok tartalmazza. Úgy lett kialakítva a bemutató a legalacsonyabb jogosultsági szint, legkisebb elérési helyigénnyel üzembe helyezéséhez.|



## <a name="pci-dss-requirement-12"></a>1.2 a PCI DSS követelmény

**1.2** tűzfal vagy útválasztó-konfigurációkat, amelyek korlátozzák a nem megbízható hálózatokhoz és a kártya tulajdonosát adatok környezetben rendszer összetevők közötti kapcsolatok létrehozása. 

> [!NOTE]
> "Nem megbízható hálózaton" az egyetlen hálózathoz sem, amely a hálózatokhoz tartozó az entitást a felülvizsgálati külső, és/vagy amely szabályozza, vagy kezelje az entitás lehessen kívül esik.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló CDE van definiálva az RA és a telepítési dokumentációban. Nem megbízható hálózatokon van Tiltás alatt úgy lett kialakítva.|



### <a name="pci-dss-requirement-121"></a>PCI DSS követelmény 1.2.1-es

**1.2.1-es** korlátozzák a bejövő és kimenő forgalmat a kártya tulajdonosát adatok környezet szükséges, és kifejezetten megtagadja az összes többi forgalom.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló CDE van definiálva az RA és a telepítési dokumentációban. Nem megbízható hálózatokon van Tiltás alatt úgy lett kialakítva. A Contoso webes tároló bemutató konfigurálja a Microsoft Azure tűzfal engedélyezi az IP-címek Microsoft Azure-szolgáltatások eléréséhez csak a megadott tartományokon. A Contoso webes tároló minden biztosít a Megtagadás – minden tűzfal, CDE határokat. Az összes konfiguráció a központi telepítés kezdeti beállítás során történik.

> [!NOTE]
> App Service-környezet (ASE) szolgál ebben a megoldásban elkülöníteni a CDE azonban fontos, hogy a minősített biztonsági végző (QSA) kiértékeli az ebben a megoldásban, ASE megvalósítja a DMZ elkülönítési, amely lehetővé teszi a kimenő kapcsolatok a ASE által történő. PCI DSS megköveteli, hogy az összes bejövő és kimenő kapcsolatokat, amelyek nem kötelezőek le kell tiltani. A ASE megfelelően működjön, ASE főkiszolgálójával kimenő kapcsolatok szükséges a ["App Service-környezet hálózati szempontjai"](/azure/app-service/app-service-environment/network-info). Az ügyfelek ki kell értékelnie, hogy a kimenő kapcsolatok a QSA előtt a megoldás telepítése éles környezetbe történő győződjön meg arról, hogy az megfeleljen a követelményeknek. |



### <a name="pci-dss-requirement-122"></a>PCI DSS követelmény 1.2.2

**1.2.2** biztonságos, és szinkronizálja a útválasztó konfigurációs fájlok.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló konfigurációk esetén a Microsoft Azure natív hálózati vezérlők biztosít.|



### <a name="pci-dss-requirement-123"></a>PCI DSS követelmény 1.2.3

**1.2.3** peremhálózati tűzfalakon közötti összes vezeték nélküli hálózatokat és a kártya tulajdonosát adatok környezet telepítése és konfigurálása a tűzfalat, hogy az engedélyezése vagy letiltása, ha a forgalom üzleti okokból szükséges csak a vezeték nélküli közötti forgalom engedélyezett környezet és a kártya tulajdonosát adatok környezet.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nem rendelkezik a vezeték nélküli megoldások vagy engedélyezett képességek.|



## <a name="pci-dss-requirement-13"></a>1.3 a PCI DSS követelmény

**1.3** az internethez és a kártya tulajdonosát adatok környezetben rendszerösszetevők között közvetlen nyilvános hozzáférés letiltása.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure alkalmazta a hálózat- és állomásalapú határ védelmi eszközök, például a tűzfalak, terheléselosztók, és hozzáférés-vezérlési listák. Ezek az eszközök használja például a VLAN elkülönítése, NAT és internetes forgalmát különálló ügyfél és felügyeleti forgalom szűrés csomag funkcióját. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít, a központi telepítés, idején a konfigurációkat, az Azure-alkalmazásokban tűzfal engedélyezése csak az IP-címek és hozzáférhetnek a webhelyhez, a megerősített Azure virtuális gépeket szerepeltetni a CDE megadott tartományokon.|



### <a name="pci-dss-requirement-131"></a>1.3.1 PCI DSS követelmény

**1.3.1** csak a jogosult nyilvánosan elérhető szolgáltatások, protokollok és portok-összetevőkkel bejövő forgalom korlátozása DMZ megvalósításához.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A DMZ Contoso webes tároló végrehajtása biztosítja, hogy csak a jogosult szolgáltatások csatlakozhatnak a CDE.|



### <a name="pci-dss-requirement-132"></a>1.3.2 PCI DSS követelmény

**1.3.2** korlát bejövő internetes forgalmat a Szegélyhálózaton belüli IP-címek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A DMZ Contoso webes tároló végrehajtása biztosítja, hogy csak a jogosult szolgáltatások csatlakozhatnak a CDE.|



### <a name="pci-dss-requirement-133"></a>1.3.3 a PCI DSS követelmény

**1.3.3** megvalósítása hamisításszűrés intézkedések észleli és blokkolja a forrás IP-címek a hálózati belépésének hamis. (Például egy belső forráscímmel az internetről származó forgalmat blokkolja.)

**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure valósítja meg a hálózati szűrés hamisított forgalom és megbízható webplatform-összetevők bejövő és kimenő forgalom korlátozása érdekében. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-134"></a>PCI DSS követelmény 1.3.4

**1.3.4** nem teszik lehetővé a jogosulatlan kimenő forgalmat a kártya tulajdonosát adatok környezetből az internethez.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló-architektúra megakadályozza a jogosulatlan kimenő forgalmat a hatókör környezetből az internethez. Mindez által engedélyezett portok és protokollok a kimenő forgalom hozzáférés-vezérlési listák beállítása a Microsoft Azure-ban. Ezek az intézkedések foglalja a hozzáférést a CDE az SQL Server-adatbázisban. <br /><br />A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>PCI DSS követelmény 1.3.5

**1.3.5** engedély csak "létrehozott" azokat a hálózati kapcsolatok.


**Feladatok:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure valósítja meg a hálózati szűrés hamisított forgalom és megbízható webplatform-összetevők bejövő és kimenő forgalom korlátozása érdekében. A Microsoft Azure-hálózathoz van elkülönített felhasználói forgalom elkülönítéséhez felügyeleti forgalmat. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Nem alkalmazható.|



### <a name="pci-dss-requirement-136"></a>PCI DSS követelmény 1.3.6

**1.3.6** kártya tulajdonosát adatokat (például egy adatbázis) tárolja egy belső hálózati zóna hely rendszerösszetevő elkülönített a Szegélyhálózaton és más nem megbízható hálózatokon.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure a hálózati elkülönítést és a NAT külön felhasználói forgalom a felügyeleti forgalom használ. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló-architektúra megakadályozza a jogosulatlan kimenő forgalmat a hatókör környezetből az internethez. Mindez által engedélyezett portok és protokollok a kimenő forgalom hozzáférés-vezérlési listák beállítása a Microsoft Azure-ban. Ezek az intézkedések foglalja a hozzáférést a CDE az SQL Server-adatbázisban. <br /><br />A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>PCI DSS követelmény 1.3.7

**1.3.7** nem fedjük fel a magánhálózati IP-címek és a nem hitelesített felek útvonal-információkat. 

> [!NOTE]
> Módszerek nem rejthetik el IP-címzést tartalmazhatják, azonban nem csak:
> - Hálózati címfordítás (NAT).
> - Proxy kiszolgálók/tűzfal mögött elhelyezkedő kártya tulajdonosát adatokat tartalmazó kiszolgálók elhelyezéséhez.
> - Eltávolítás vagy magánhálózatok alkalmazó szoftverbiztonsági útvonal-hirdetéseinek szűrését regisztrált címzést.
> - Belső használatra RFC1918 címtartomány regisztrált címek helyett.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure hálózati címfordítás (NAT) és a hálózati elkülönítés használatával külön felhasználói forgalom a felügyeleti forgalom. Az Azure eszközök egyedileg azonosítja az UUID és hitelesítése Kerberos használatával. Azure által felügyelt hálózati eszközök azonosítják az RFC 1918 IP-címmel. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kártya tulajdonosát adatokat proxy kiszolgálók/tűzfal mögött elhelyezkedő helyezi, és RFC1918 címterület belső használja.|



## <a name="pci-dss-requirement-14"></a>1.4 PCI DSS követelmény

**1.4** személyes tűzfal szoftver vagy ezzel egyenértékű csoport funkció telepítését a hordozható eszközök (többek között a vállalati vagy alkalmazotti), amely csatlakozni az internethez, amikor a hálózaton kívül, (például a alkalmazottak által használt laptopok), és is használt a CDE eléréséhez. Tűzfal (vagy egyenértékű) konfigurációk a következők:-konfigurációs beállítások határozzák meg.
- Van aktívan futó személyes tűzfal (vagy ezzel egyenértékű funkciók).
- Nincs a hordozható eszközök felhasználói általi tárolóeszközökön személyes tűzfal (vagy ezzel egyenértékű funkciók).

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nem nyújt védelmet a végfelhasználói eszközöket. [A Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) a dolgozók számára a vállalati adatok elérésére használt mobil eszközök kezelésére is használható.|



## <a name="pci-dss-requirement-15"></a>1.5-ös PCI DSS követelmény

**1.5-ös** győződjön meg arról, hogy a biztonsági házirendek és működési eljárások tűzfalakra kezelésére szolgáló dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít, a központi telepítés, idején a konfigurációkat, az Azure-alkalmazásokban tűzfal engedélyezése csak az IP-címek és hozzáférhetnek a webhelyhez, a megerősített Azure virtuális gépeket szerepeltetni a CDE megadott tartományokon.|




