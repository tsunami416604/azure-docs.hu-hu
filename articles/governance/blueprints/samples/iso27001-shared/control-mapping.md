---
title: ISO 27001 megosztott szolgáltatások tervrajz mintájának vezérlői
description: Az ISO 27001 megosztott szolgáltatások tervrajzi mintájának vezérlése. Mindegyik vezérlő egy vagy több olyan Azure-szabályzatra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: b484bdc2fdaa346e5137b31e3225dc913f43ed0c
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546726"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 megosztott szolgáltatások tervrajzi mintájának vezérlése

A következő cikk azt ismerteti, hogyan jelennek meg az Azure-tervezetek ISO 27001 megosztott szolgáltatások tervrajza az ISO 27001-vezérlőkhöz. További információ a vezérlőelemekről: [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

A következő leképezések az **ISO 27001:2013** -vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a **\[előzetes verzióját\] az ISO 27001:2013 vezérlőket, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 feladatai elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a terv segít a megfelelő számú Azure-előfizetési tulajdonos fenntartásában azáltal, hogy két [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. Az előfizetés tulajdonosi engedélyeinek kezelése segítséget nyújt a feladatok megfelelő elkülönítésének megvalósításában.

- \[előzetes verzió\]: az előfizetéshez tartozó tulajdonosok minimális számának naplózása
- \[előzetes verzió\]: az előfizetéshez tartozó tulajdonosok maximális számának naplózása

## <a name="a821-classification-of-information"></a>A. 8.2.1 besorolása

Az Azure [SQL sebezhetőség-értékelő szolgáltatása](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) segít felderíteni az adatbázisaiban tárolt bizalmas adatokat, és javaslatokat is tartalmaz az adatok besorolására. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá az SQL sebezhetőség-értékelési vizsgálat során azonosított sebezhetőségek ellenőrzéséhez.

- \[előzetes verzió\]: figyelje az SQL sebezhetőségi felmérés eredményeit Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2-hozzáférés a hálózatokhoz és a hálózati szolgáltatásokhoz

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz. Ez a terv az Azure-erőforrásokhoz való hozzáférés szabályozását teszi lehetővé a hét [Azure Policy](../../../policy/overview.md) definíció kiosztásával. Ezek a házirendek olyan erőforrástípusok és konfigurációk használatát naplózzák, amelyek lehetővé tehetik az erőforrásokhoz való hozzáférést.
A szabályzatok megsértése miatti erőforrások megismerése segíthet az Azure-erőforrások elérését engedélyező, a jogosult felhasználókra korlátozódó kijavítási műveletek elvégzésében.

- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése a Linux rendszerű virtuális gépek fiókjainak a jelszavak nélküli naplózásához
- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése a Linux rendszerű virtuális gép naplózása, amely lehetővé teszi a távoli kapcsolatokat jelszavak nélküli fiókokkal
- \[előzetes verzió\]: Linux rendszerű virtuális gépek naplózása jelszavak nélkül
- \[előzetes verzió\]: Linux rendszerű virtuális gép naplózása, amely lehetővé teszi a távoli kapcsolatokat jelszavak nélküli fiókokkal
- Klasszikus Storage-fiókok használatának naplózása
- Klasszikus virtuális gépek használatának naplózása
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3-kezelés az emelt szintű hozzáférési jogosultságokkal

Ez a terv segít a privilegizált hozzáférési jogosultságok korlátozásában és szabályozásában azáltal, hogy négy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a tulajdonossal és/vagy írási engedélyekkel rendelkező külső fiókokat, valamint olyan fiókokat, amelyek tulajdonosi és/vagy írási engedélyekkel rendelkeznek, amelyek nem rendelkeznek a többtényezős Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz. A terv emellett három Azure Policy definíciót is hozzárendel az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. Ez a terv egy Azure Policy-definíciót is hozzárendel az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni RBAC-szabályok hibásak.

- \[előzetes verzió\]: fiókok naplózása olyan tulajdonosi engedélyekkel, akik nem MFA engedélyezve vannak az előfizetéseken
- \[előzetes verzió\]: fiókok naplózása olyan írási engedélyekkel, akik nem engedélyezett az MFA-előfizetés
- \[előzetes verzió\]: az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- \[előzetes verzió\]: az előfizetésre vonatkozó írási engedélyekkel rendelkező külső fiókok naplózása
- Az SQL Server Azure Active Directory-rendszergazdájának kiépítés naplózása
- Azure Active Directory használatának naplózása az ügyfél-hitelesítéshez Service Fabric
- Egyéni RBAC-szabályok használatának naplózása

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. a felhasználók titkos hitelesítési adatainak 9.2.4 kezelése

Ez a terv három [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a többtényezős hitelesítéssel nem rendelkező fiókok naplózásához. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik. A terv két Azure Policy-definíciót is hozzárendel, amelyek a Linux rendszerű virtuális gép jelszavas fájljának engedélyeit naplózzák, ha helytelenül vannak beállítva. Ez a beállítás lehetővé teszi, hogy a hitelesítő adatok biztonságának megelőzése érdekében javítsa a megfelelő lépéseket.

- \[előzetes verzió\]: fiókok naplózása olyan tulajdonosi engedélyekkel, akik nem MFA engedélyezve vannak az előfizetéseken
- \[előzetes verzió\]: a fiókok naplózása olyan olvasási engedélyekkel, akik nem engedélyezték az MFA-t az előfizetésen
- \[előzetes verzió\]: fiókok naplózása olyan írási engedélyekkel, akik nem engedélyezett az MFA-előfizetés
- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése Linux rendszerű virtuális gép passwd fájljának engedélyeinek naplózásához
- \[előzetes verzió\]: a Linux rendszerű virtuális gép/etc/passwd-engedélyeinek naplózása a 0644 értékre van állítva.

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5-áttekintés a felhasználói hozzáférési jogosultságokról

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv négy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a emelt szintű engedélyekkel rendelkező külső fiókokat.

- \[előzetes verzió\]: elavult fiókok naplózása egy előfizetésen
- \[előzetes verzió\]: a tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása egy előfizetésen
- \[előzetes verzió\]: az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- \[előzetes verzió\]: az előfizetésre vonatkozó írási engedélyekkel rendelkező külső fiókok naplózása

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 eltávolítása vagy a hozzáférési jogosultságok módosítása

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) és a RBAC használatával frissítheti a felhasználói szerepköröket a szervezeti változások tükrözése érdekében. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- \[előzetes verzió\]: elavult fiókok naplózása egy előfizetésen
- \[előzetes verzió\]: a tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása egy előfizetésen

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 biztonságos bejelentkezés eljárásai

Ez a terv három Azure Policy definíciót rendel hozzá a többtényezős hitelesítéssel nem rendelkező fiókok naplózásához. Az Azure Multi-Factor Authentication további biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést biztosít. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- \[előzetes verzió\]: fiókok naplózása olyan tulajdonosi engedélyekkel, akik nem MFA engedélyezve vannak az előfizetéseken
- \[előzetes verzió\]: a fiókok naplózása olyan olvasási engedélyekkel, akik nem engedélyezték az MFA-t az előfizetésen
- \[előzetes verzió\]: fiókok naplózása olyan írási engedélyekkel, akik nem engedélyezett az MFA-előfizetés

## <a name="a943-password-management-system"></a>A. 9.4.3 jelszavas felügyeleti rendszer

Ez a terv segít kikényszeríteni az erős jelszavakat olyan 10 [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek olyan Windows rendszerű virtuális gépeket naplóznak, amelyek nem érvényesítik a minimális szilárdság A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- \[előzetes verzió\]: virtuálisgép-bővítmény központi telepítése a Windows rendszerű virtuális gép naplózása a jelszó-összetettségi követelmények betartatása
- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése a Windows rendszerű virtuális gép maximális jelszavának 70 nap alatt történő naplózásához
- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése a Windows rendszerű virtuális gép minimális jelszava 1 nap alatt történő naplózásához
- \[előnézet\]: virtuálisgép-bővítmény központi telepítése a Windows rendszerű virtuális gépek jelszavainak naplózásához legalább 14 karakternek kell lennie
- \[előzetes verzió\]: virtuálisgép-bővítmény üzembe helyezése a Windows rendszerű virtuális gép naplózása nem engedélyezheti az előző 24 jelszavakat
- \[előzetes verzió\]: a Windows rendszerű virtuális gép naplózása a jelszó bonyolultságára vonatkozó követelményeket kényszeríti
- \[előzetes verzió\]: a Windows rendszerű virtuális gép maximális jelszavának naplózása 70 nap
- \[előzetes verzió\]: a Windows rendszerű virtuális gép minimális jelszavának naplózása 1 nap
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek jelszavainak naplózása legalább 14 karakterből állhat.
- \[előzetes verzió\]: a Windows rendszerű virtuális gép naplózása nem engedélyezheti az előző 24 jelszavakat

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A. 10.1.1 szabályzat a titkosítási vezérlők használatára

Ez a terv segít kikényszeríteni a szabályzatot a kriptográfiai vezérlők használatára úgy, hogy 13 [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek bizonyos titkosítási vezérlőket kényszerítenek, és a gyenge titkosítási beállítások naplózását használják.
Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzatok titkosítást igényelnek a blob Storage-fiókokhoz és a adatlake Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; a Storage-fiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az Automation-fiók változójában a hiányzó titkosítás naplózása; nem biztonságos kapcsolatok naplózása a Storage-fiókok, a Function apps, a webalkalmazás, a API Apps és a Redis Cache használatával; gyenge virtuális gép jelszavas titkosításának naplózása; és a titkosítatlan Service Fabric kommunikáció naplózása.

- \[előzetes verzió\]: csak a HTTPS-hozzáférés naplózása függvényalkalmazás
- \[előzetes verzió\]: csak a HTTPS-hozzáférés naplózása webalkalmazásokhoz
- \[előzetes verzió\]: csak a HTTPS-alkalmazásokhoz való hozzáférés naplózása
- \[előzetes verzió\]: a Storage-fiókok hiányzó blob-titkosításának naplózása
- \[előzetes verzió\]: virtuálisgép-bővítmény központi telepítése a Windows rendszerű virtuális gép naplózásához ne tárolja a jelszavakat visszafejthető titkosítással
- \[előzetes verzió\]: a Windows rendszerű virtuális gép naplózása ne tárolja a jelszavakat visszafejthető titkosítással
- \[előzetes verzió\]: titkosítatlan virtuálisgép-lemezek figyelése a Azure Security Center
- Az Automation-fiókok változóinak titkosításának naplózása
- Csak biztonságos kapcsolatok engedélyezése a Redis Cache
- Biztonságos átvitel naplózása a Storage-fiókokba
- A ClusterProtectionLevel tulajdonság beállításának naplózása a Service Fabric EncryptAndSign
- Transzparens adattitkosítás állapotának naplózása
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="a1241-event-logging"></a>A. 12.4.1 eseménynaplózása

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait.
A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre.

- \[előnézet\]: naplózási Dependency Agent üzembe helyezése – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva
- \[előzetes verzió\]: Dependency Agent üzembe helyezésének naplózása a VMSS-ben – virtuális gép rendszerképe (operációs rendszer) nem listázva
- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3-rendszergazdai és-kezelői naplók

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét Azure Policy definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait. A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre.

- \[előnézet\]: naplózási Dependency Agent üzembe helyezése – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva
- \[előzetes verzió\]: Dependency Agent üzembe helyezésének naplózása a VMSS-ben – virtuális gép rendszerképe (operációs rendszer) nem listázva
- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="a1244-clock-synchronization"></a>A. 12.4.4 óra szinkronizálása

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét Azure Policy definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait. Az Azure-naplók a szinkronizált belső órákat használják az események időbeli korrelációs rekordjának létrehozásához az erőforrások között.

- \[előnézet\]: naplózási Dependency Agent üzembe helyezése – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva
- \[előzetes verzió\]: Dependency Agent üzembe helyezésének naplózása a VMSS-ben – virtuális gép rendszerképe (operációs rendszer) nem listázva
- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 szoftver telepítése az operatív rendszereken

Az adaptív alkalmazások vezérlése Azure Security Center megoldás, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely figyeli az engedélyezett alkalmazások készletének változásait. Ez a funkció segítséget nyújt a szoftverek és alkalmazások Azure-beli virtuális gépeken való telepítésének szabályozásában.

- \[előzetes verzió\]: a lehetséges alkalmazások engedélyezési listájának figyelése Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. a technikai sebezhetőségek 12.6.1 kezelése

Ebből a tervből megtudhatja, hogyan kezelheti az információs rendszer biztonsági réseit úgy, hogy öt [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, Azure Security Center az SQL biztonsági réseket és a virtuális gép A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára.

- \[előzetes verzió\]: a hiányzó Endpoint Protection figyelése Azure Security Center
- \[előzetes verzió\]: a hiányzó rendszerfrissítések figyelése a Azure Security Center
- \[előzetes verzió\]: az operációs rendszer biztonsági réseinak figyelése Azure Security Center
- \[előzetes verzió\]: figyelje az SQL sebezhetőségi felmérés eredményeit Azure Security Center
- \[előzetes verzió\]: a virtuális gépek biztonsági Réseinak figyelése Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A szoftver telepítésének. 12.6.2 korlátozása

Az adaptív alkalmazások vezérlése Azure Security Center megoldás, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely figyeli az engedélyezett alkalmazások készletének változásait. A Szoftvertelepítés korlátozásai segíthetnek csökkenteni a szoftveres biztonsági rések bevezetésének valószínűségét.

- \[előzetes verzió\]: a lehetséges alkalmazások engedélyezési listájának figyelése Azure Security Center

## <a name="a1311-network-controls"></a>A. 13.1.1 hálózati vezérlők

Ez a terv segít a hálózatok kezelésében és szabályozásában egy olyan [Azure Policy](../../../policy/overview.md) -definíció hozzárendelésével, amely a hálózati biztonsági csoportokat a megengedő szabályokkal figyeli. A túl megengedhető szabályok lehetővé tehetik a nem kívánt hálózati hozzáférést, és azt felül kell vizsgálni. Ez a terv a nem védett végpontok, alkalmazások és Storage-fiókok figyelésére szolgáló három Azure Policy definíciót is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- \[előzetes verzió\]: a megengedő hálózati hozzáférés figyelése Azure Security Center
- \[előzetes verzió\]: a nem védett hálózati végpontok figyelése a Azure Security Center
- \[előzetes verzió\]: a nem védett webalkalmazások figyelése a Azure Security Center
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1-adatátviteli szabályzatok és eljárások

A terv segítségével gondoskodhat arról, hogy az Azure-szolgáltatásokkal való adatátvitel biztonságos legyen, ha két [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a nem biztonságos kapcsolatok naplózásához a Storage-fiókokhoz és a Redis cache.

- Csak biztonságos kapcsolatok engedélyezése a Redis Cache
- Biztonságos átvitel naplózása a Storage-fiókokba

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 megosztott szolgáltatások tervének vezérlési leképezését, az alábbi cikkekben megismerheti az architektúrát és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [Iso 27001 megosztott szolgáltatások tervrajza – áttekintés](./index.md)
> [ISO 27001 megosztott szolgáltatások tervezete – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.