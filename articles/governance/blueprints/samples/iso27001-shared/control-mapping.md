---
title: ISO 27001 Megosztott szolgáltatások tervezetminta-vezérlők
description: Az ISO 27001 megosztott szolgáltatások tervezetminta vezérlőleképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6c03da7d5d4caada9ef47a828163a79a003bea93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922527"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 megosztott szolgáltatások tervezetminta vezérlőtérképezése

A következő cikk bemutatja, hogy az Azure Blueprints ISO 27001 megosztott szolgáltatások minta minta leképezi az ISO 27001 vezérlők. A vezérlőkről további információt az [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Az alábbi leképezések az **ISO 27001:2013** vezérlőkhöz tartoznak. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[ISO 27001:2013 előzetes naplózási\] vezérlőket, és telepítsen konkrét virtuálisgép-bővítményeket** a beépített házirend-kezdeményezés naplózási követelményeinek támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md)

## <a name="a612-segregation-of-duties"></a>A.6.1.2.

Ha csak egy Azure-előfizetés-tulajdonossal rendelkezik, az nem teszi lehetővé a felügyeleti redundanciát. Ezzel szemben a túl sok Azure-előfizetés-tulajdonos növelheti a feltört tulajdonosi fiókon keresztül a feltört fiók megsértése lehetőségét. Ez a tervezet segít fenntartani a megfelelő számú Azure-előfizetés-tulajdonosok két [Azure-szabályzat](../../../policy/overview.md) definíciók, amelyek naplózják a tulajdonosok száma az Azure-előfizetések. Az előfizetés tulajdonosi engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- \[Előzetes\]verzió : Az előfizetés tulajdonosainak minimális számának ellenőrzése
- \[Előzetes\]verzió : Az előfizetés tulajdonosainak maximális számának ellenőrzése

## <a name="a821-classification-of-information"></a>A.8.2.1.

Az Azure [SQL biztonsági résértékelési szolgáltatása](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) segítséget nyújt az adatbázisokban tárolt bizalmas adatok felderítésében, és javaslatokat tartalmaz az adatok besorolására. Ez a tervezet hozzárendel egy [Azure Policy-definíciót](../../../policy/overview.md) annak naplózásához, hogy az SQL biztonsági résfelmérési vizsgálat során azonosított biztonsági réseket a rendszer kiigazítja.

- \[Előzetes\]verzió : Az SQL biztonsági résértékelésének eredményeinek figyelése az Azure Security Centerben

## <a name="a912-access-to-networks-and-network-services"></a>Hozzáférés a hálózatokhoz és a hálózati szolgáltatásokhoz

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg annak kezeléséhez, hogy ki férhet hozzá az Azure-erőforrásokhoz. Ez a tervezet hét [Azure-szabályzat-definíció](../../../policy/overview.md) hozzárendelésével segít az Azure-erőforrásokhoz való hozzáférés szabályozásához. Ezek a házirendek naplózják az erőforrások típusainak és konfigurációinak használatát, amelyek lehetővé teszik az erőforrásokhoz való megengedhetőbb hozzáférést.
A szabályzatokat megsértő erőforrások ismertetése segíthet a korrekciós műveletek elvégzésében annak biztosítása érdekében, hogy az Azure-erőforrások csak a jogosult felhasználók számára legyenek elérhetők.

- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése jelszavak nélküli Linux virtuálisgép-fiókok naplózásához
- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése a Linux virtuális gép naplózásához, amely lehetővé teszi a távoli kapcsolatokat a jelszavak nélküli fiókokból
- \[Előzetes\]verzió : Linuxos virtuálisgép-fiókok naplózása jelszó nélkül
- \[Előzetes\]verzió : A Linux virtuális gép naplózása, amely lehetővé teszi a jelszavak nélküli fiókok távoli kapcsolatait
- Klasszikus tárfiókok használatának naplózása
- Klasszikus virtuális gépek használatának ellenőrzése
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3.

Ez a tervezet segít korlátozni és szabályozni a kiemelt hozzáférési jogokat azáltal, hogy négy [Azure Policy-definíciót](../../../policy/overview.md) rendel a tulajdonosi és/vagy írási engedélyekkel és olyan tulajdonosi és/vagy írási engedélyekkel rendelkező külső fiókok naplózásához, amelyeknél nincs engedélyezve a többtényezős hitelesítés. Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg annak kezeléséhez, hogy ki férhet hozzá az Azure-erőforrásokhoz. Ez a tervezet is hozzárendel i három Azure Policy-definíciók az Azure Active Directory-hitelesítés sql-kiszolgálók és a Service Fabric használatának naplózásához. Az Azure Active Directory-hitelesítés lehetővé teszi az egyszerűbb engedélykezelést és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezelését. Ez a tervezet is hozzárendel egy Azure Policy-definíciót az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának ismertetése segíthet a szükséges és megfelelő megvalósítás ellenőrzésében, mivel az egyéni RBAC-szabályok hibalehetőségeket rejtenek.

- \[Előzetes\]verzió : Olyan tulajdonosi engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Olyan írási engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Külső fiókok naplózása tulajdonosi engedélyekkel egy előfizetésen
- \[Előzetes\]verzió : Írási engedéllyel rendelkező külső fiókok naplózása
- Azure Active Directory-rendszergazda sql-kiszolgálóhoz történő kiépítésének naplózása
- Az Azure Active Directory használatának naplózása az ügyfélhitelesítéshez a Service Fabricben
- Egyéni RBAC-szabályok használatának naplózása

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 A felhasználók titkos hitelesítési adatainak kezelése

Ez a tervezet három [Azure Policy-definíciót](../../../policy/overview.md) rendel olyan naplózási fiókokhoz, amelyekhez nincs engedélyezve a többtényezős hitelesítés. A többtényezős hitelesítés akkor is segít a fiókok biztonságának megőrzésében, ha egy hitelesítési adat biztonsága sérül. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek. Ez a tervezet is hozzárendel két Azure Policy-definíciók, amelyek naplózják a Linux virtuális gép jelszófájl engedélyekriasztást, ha helytelenül vannak beállítva. Ez a beállítás lehetővé teszi, hogy korrekciós intézkedéseket hajtson végre annak érdekében, hogy a hitelesítők ne kerüljenek veszélybe.

- \[Előzetes\]verzió : Olyan tulajdonosi engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Olyan olvasási engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Olyan írási engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése a Linux VM passwd fájlengedélyeinek naplózásához
- \[Előzetes\]verzió : A Linux VM /etc/passwd fájlengedélyek naplózása 0644-re van állítva

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 A felhasználói hozzáférési jogok felülvizsgálata

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet négy [Azure Policy-definíciót](../../../policy/overview.md) rendel a naplózási fiókokhoz, amelyeket felül kell vizsgálni, beleértve az amortizált fiókokat és az emelt szintű engedélyekkel rendelkező külső fiókokat.

- \[Előzetes\]verzió : Elavult fiókok naplózása előfizetésen
- \[Előzetes\]verzió : Elavult fiókok naplózása tulajdonosi engedélyekkel egy előfizetésen
- \[Előzetes\]verzió : Külső fiókok naplózása tulajdonosi engedélyekkel egy előfizetésen
- \[Előzetes\]verzió : Írási engedéllyel rendelkező külső fiókok naplózása

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6.

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) és az RBAC használatával frissítheti a felhasználói szerepköröket, hogy tükrözze a szervezeti változásokat. Szükség esetén a fiókok letilthatók a bejelentkezésben (vagy az eltávolításban), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a tervezet két [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá az elavult fiók naplózásához, amelyet meg kell fontolni az eltávolításhoz.

- \[Előzetes\]verzió : Elavult fiókok naplózása előfizetésen
- \[Előzetes\]verzió : Elavult fiókok naplózása tulajdonosi engedélyekkel egy előfizetésen

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Biztonságos bejelentkezési eljárások

Ez a tervezet három Azure Policy-definíciót rendel olyan naplózási fiókokhoz, amelyekhez nincs engedélyezve a többtényezős hitelesítés. Az Azure többtényezős hitelesítés további biztonságot nyújt a hitelesítés második formájának megkövetelésével, és erős hitelesítést biztosít. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek.

- \[Előzetes\]verzió : Olyan tulajdonosi engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Olyan olvasási engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben
- \[Előzetes\]verzió : Olyan írási engedélyekkel rendelkező fiókok naplózása, amelyek nem engedélyezve vannak az előfizetésben

## <a name="a943-password-management-system"></a>A.9.4.3 Jelszókezelő rendszer

Ez a tervezet segít az erős jelszavak kényszerítésében 10 [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózják a Windows virtuális gépek, amelyek nem kényszerítik a minimális erősséget és egyéb jelszókövetelmények. A jelszóerősségre vonatkozó szabályzatot megsértő virtuális gépek ismertségét figyelembe véve korrekciós műveleteket is végrehajthatsz annak érdekében, hogy az összes virtuálisgép-felhasználói fiók jelszavai megfeleljenek a szabályzatnak.

- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése a Windows virtuális gép naplózásához a jelszó összetettségére vonatkozó követelményeket kényszeríti ki
- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése a Windows vm maximális jelszavának 70 napos naplózásához
- \[Előzetes\]verzió : Virtuálisgép-bővítmény telepítése a Windows virtuális gép minimális jelszóidejének naplózásához 1 nap
- \[Előzetes\]verzió : A Virtuálisgép-bővítmény telepítése a Windows virtuálisgép-jelszavak naplózásához legalább 14 karakterből kell állhatatosnak lennie
- \[Előzetes\]verzió : A Virtuálisgép-bővítmény telepítése a Windows virtuális gép naplózásához nem engedélyezi a korábbi 24 jelszót
- \[Előzetes\]verzió : A Windows virtuális gép naplózása jelszó-összetettségi követelményeket kényszerít ki
- \[Előzetes\]verzió : A Windows virtuális gép maximális jelszavának ellenőrzése 70 napos
- \[Előzetes\]verzió : A Windows virtuális gép minimális jelszóidejének naplózása 1 napos
- \[Előzetes\]verzió : A Windows virtuális gép jelszavainak legalább 14 karakterből kell lenniük
- \[Előzetes\]verzió : A Windows virtuális gép naplózása nem engedélyezi a korábbi 24 jelszót

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 A kriptográfiai vezérlők használatára vonatkozó irányelv

Ez a tervezet segít a kriptográfiai vezérlők használatára vonatkozó szabályzat kényszerítésében azáltal, hogy 13 [Azure-szabályzatdefiníciót](../../../policy/overview.md) rendel hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket és a gyenge kriptográfiai beállítások naplózását.
Ha tisztában van azzal, hogy az Azure-erőforrások hol rendelkezhetnek nem optimális kriptográfiai konfigurációkkal, akkor korrekciós műveleteket végezhet annak érdekében, hogy az erőforrások az információbiztonsági szabályzatnak megfelelően legyenek konfigurálva. Pontosabban a tervezet által hozzárendelt házirendek titkosítást igényelnek a blob storage-fiókok és a Data Lake storage-fiókok számára; transzparens adattitkosítást igényel nek az SQL-adatbázisokon; a hiányzó titkosítás naplózása a tárfiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az automatizálási fiókváltozókon; nem biztonságos kapcsolatok naplózása tárfiókokhoz, függvényalkalmazásokhoz, webalkalmazáshoz, API-alkalmazásokhoz és Redis-gyorsítótárhoz; a virtuális gép gyenge jelszótitkosításának naplózása; és a nem titkosított Service Fabric kommunikáció naplózása.

- \[Előzetes\]verzió: A HTTPS-hozzáférés naplózása csak egy függvényalkalmazáshoz
- \[Előzetes\]verzió : Csak webalkalmazás HTTPS-hozzáférésének naplózása
- \[Előzetes\]verzió: Csak az API-alkalmazások HTTPS-hozzáférésének naplózása
- \[Előzetes\]verzió : A tárolófiókok hiányzó blobtitkosításának naplózása
- \[Előzetes\]verzió : A Virtuálisgép-bővítmény telepítése a Windows virtuális gép naplózásához nem tárolhat jelszavakat megfordítható titkosítással
- \[Előzetes\]verzió : A Windows virtuális gép naplózása nem tárolhat jelszavakat visszafordítható titkosítással
- \[Előzetes\]verzió : Titkosítatlan virtuálisgép-lemezek figyelése az Azure Security Centerben
- Automation-fiókváltozók titkosításának engedélyezése
- Csak a Redis-gyorsítótárral létesített biztonságos kapcsolatok naplózása
- Tárfiókokba való biztonságos átvitel naplózása
- A ClusterProtectionLevel tulajdonság beállításának naplózása encryptAndSign szolgáltatásra a Service Fabric-ben
- Transzparens adattitkosítás állapotának naplózása
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell

## <a name="a1241-event-logging"></a>A.12.4.1 Eseménynaplózás

Ez a tervezet segít biztosítani a rendszeresemények naplózása azáltal, hogy hét [Azure Policy-definíciók,](../../../policy/overview.md) amelyek naplóbeállítások at Azure-erőforrások naplózási beállításait.
A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekbe.

- \[Előzetes\]verzió : A függőségi ügynök telepítésének naplózása – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : A függőségi ügynök központi telepítése a VMSS-ben – a virtuális gép lemezképe (OS) nem szerepel a tőzsdén.
- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3.

Ez a tervezet segít biztosítani a rendszeresemények naplózása azáltal, hogy hét Azure Policy-definíciók, amelyek naplóbeállítások at Azure-erőforrások naplózási beállításait. A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekbe.

- \[Előzetes\]verzió : A függőségi ügynök telepítésének naplózása – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : A függőségi ügynök központi telepítése a VMSS-ben – a virtuális gép lemezképe (OS) nem szerepel a tőzsdén.
- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Óraszinkronizálás

Ez a tervezet segít biztosítani a rendszeresemények naplózása azáltal, hogy hét Azure Policy-definíciók, amelyek naplóbeállítások at Azure-erőforrások naplózási beállításait. Az Azure-naplók a szinkronizált belső órák segítségével időbeli korrelált rekord az erőforrások közötti események létrehozása.

- \[Előzetes\]verzió : A függőségi ügynök telepítésének naplózása – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : A függőségi ügynök központi telepítése a VMSS-ben – a virtuális gép lemezképe (OS) nem szerepel a tőzsdén.
- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Szoftver telepítése operatív rendszereken

Az adaptív alkalmazásvezérlés az Azure Security Center megoldása, amely segít annak szabályozásában, hogy mely alkalmazások futtathatók az Azure-ban található virtuális gépeken. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely figyeli az engedélyezett alkalmazások készletének módosításait. Ez a funkció segít a szoftverek és alkalmazások azure-beli virtuális gépeken történő telepítésének vezérlésében.

- \[Előzetes\]verzió : A lehetséges alkalmazásengedélyezési lista figyelése az Azure Security Centerben

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 A technikai rések kezelése

Ez a tervezet segít az információs rendszer biztonsági réseinek kezelésében azáltal, hogy öt [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek biztonsági réseit az Azure Security Centerben. Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába.

- \[Előzetes\]verzió : Hiányzó végpontvédelem figyelése az Azure Security Centerben
- \[Előzetes\]verzió : Hiányzó rendszerfrissítések figyelése az Azure Security Centerben
- \[Előzetes\]verzió : Az operációs rendszer biztonsági réseifigyelése az Azure Security Centerben
- \[Előzetes\]verzió : Az SQL biztonsági résértékelésének eredményeinek figyelése az Azure Security Centerben
- \[Előzetes\]verzió : A virtuális gépek biztonsági réseifigyelése az Azure Security Centerben

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 A szoftvertelepítés korlátozása

Az adaptív alkalmazásvezérlés az Azure Security Center megoldása, amely segít annak szabályozásában, hogy mely alkalmazások futtathatók az Azure-ban található virtuális gépeken. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely figyeli az engedélyezett alkalmazások készletének módosításait. A szoftvertelepítésre vonatkozó korlátozások segítségével csökkentheti a szoftverbiztonsági rések bevezetésének valószínűségét.

- \[Előzetes\]verzió : A lehetséges alkalmazásengedélyezési lista figyelése az Azure Security Centerben

## <a name="a1311-network-controls"></a>A.13.1.1.

Ez a tervezet segít a hálózatok kezeléséhez és vezérléséhez egy [Azure Policy-definíció](../../../policy/overview.md) hozzárendelésével, amely megengedő szabályokkal figyeli a hálózati biztonsági csoportokat. A túl megengedő szabályok nem kívánt hálózati hozzáférést engedélyezhetnek, ezért azát felül kell vizsgálni. Ez a tervezet is hozzárendel i három Azure Policy-definíciók, amelyek a nem védett végpontok, alkalmazások és tárfiókok figyelése. Végpontok és alkalmazások, amelyek nem védi a tűzfal, és a tárfiókok korlátlan hozzáféréssel lehetővé teheti a nem kívánt hozzáférést az információs rendszer ben található információkhoz.

- \[Előzetes\]verzió : Az engedékeny hálózati hozzáférés figyelése az Azure Security Centerben
- \[Előzetes\]verzió : Nem védett hálózati végpontok figyelése az Azure Security Centerben
- \[Előzetes\]verzió : Nem védett webalkalmazás figyelése az Azure Security Centerben
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Információátadási irányelvek és eljárások

A tervezet segítségével biztosíthatja az adatok átvitelét az Azure-szolgáltatások biztonságos hozzárendelésével két [Azure-szabályzat](../../../policy/overview.md) definíciók a tárfiókok és a Redis cache nem biztonságos kapcsolatok naplózásához.

- Csak a Redis-gyorsítótárral létesített biztonságos kapcsolatok naplózása
- Tárfiókokba való biztonságos átvitel naplózása

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 Megosztott szolgáltatások tervének vezérlőleképezését, az alábbi cikkekben megtudhatja az architektúrát és a minta üzembe helyezését:

> [!div class="nextstepaction"]
> [ISO 27001 Megosztott szolgáltatások tervezete – Áttekintés az](./index.md)
> [ISO 27001 Megosztott szolgáltatások tervrajzáról – Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)