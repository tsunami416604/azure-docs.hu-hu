---
title: NIST SP 800-53 R4 mintamintavezérlők
description: A NIST SP 800-53 R4 tervminta vezérlőleképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: 104224ec96a5989a103f4d00310cf08b684b4d67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546627"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>A NIST SP 800-53 R4 minta vezérlőtérképezése

Az alábbi cikk bemutatja, hogy az Azure Blueprints NIST SP 800-53 R4 mintamintképezése hogyan leképezi a NIST SP 800-53 R4 vezérlőket. A vezérlőkről további információt a [NIST SP 800-53](https://nvd.nist.gov/800-53)című témakörben talál.

Az alábbi leképezések a **NIST SP 800-53 (Rev. 4)** vezérlőkhöz tartoznak. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[előzetes verziót:\]Ellenőrizze a NIST SP 800-53 R4 vezérlőket, és telepítsen konkrét Virtuálisgép-bővítményeket** a beépített házirend-kezdeményezés naplózási követelményeinek támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/nist-sp-800-53-rev4/control-mapping.md)

## <a name="ac-2-account-management"></a>AC-2 számlakezelés

Ez a tervezet segít a szervezet fiókkezelési követelményeinek nem megfelelő fiókok áttekintéséhez. Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek a külső fiókok naplózása olvasási, írási és tulajdonosi engedélyekkel egy előfizetés és elavult fiókok. [Azure Policy](../../../policy/overview.md) A szabályzatok által naplózott fiókok áttekintésével megfelelő lépéseket tehet a fiókkezelési követelmények teljesítéséhez.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Számlakezelés | Szerepköralapú rendszerek

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet is hozzárendeli [az Azure Policy-definíciók](../../../policy/overview.md) az Azure Active Directory-hitelesítés SQL-kiszolgálók és a Service Fabric használatának naplózásához. Az Azure Active Directory-hitelesítés lehetővé teszi az egyszerűbb engedélykezelést és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezelését. Emellett ez a tervezet hozzárendel egy Azure Policy-definíciót az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának ismertetése segíthet a szükséges és megfelelő megvalósítás ellenőrzésében, mivel az egyéni RBAC-szabályok hibalehetőségek.

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- Egyéni RBAC-szabályok használatának naplózása
- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Számlakezelés | Fiókfigyelés / atipikus használat

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A virtuális gépek elérésére irányuló jit-kérelmek et a tevékenységnapló naplózza, amely lehetővé teszi az atipikus használat figyelését. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információáramlás érvényesítése

A kereszteredetű erőforrások megosztása (CORS) lehetővé teheti, hogy az App Services-erőforrásokat külső tartományból kell kérni. A Microsoft azt javasolja, hogy csak a szükséges tartományok számára engedélyezze az API-, funkció- és webalkalmazás-interakciót. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a CORS-erőforrások hozzáférési korlátozásainak figyelésében az Azure Security Centerben. [Azure Policy](../../../policy/overview.md)
A CORS-implementációk ismertetése segíthet az információáramlás-vezérlők megvalósításának ellenőrzésében.

- A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a webalkalmazáshoz

## <a name="ac-5-separation-of-duties"></a>AC-5 A feladatok elkülönítése

Ha csak egy Azure-előfizetés-tulajdonossal rendelkezik, az nem teszi lehetővé a felügyeleti redundanciát. Ezzel szemben a túl sok Azure-előfizetés-tulajdonos növelheti a feltört tulajdonosi fiókon keresztül a feltört fiók megsértése lehetőségét. Ez a tervezet segít fenntartani a megfelelő számú Azure-előfizetés-tulajdonosok hozzárendelésével Azure Policy-definíciók, amelyek naplózják a tulajdonosok száma az Azure-előfizetések. [Azure Policy](../../../policy/overview.md) Ez a tervezet is hozzárendeli az Azure Policy-definíciók, amelyek segítségével szabályozhatja a rendszergazdák csoport tagságát a Windows virtuális gépeken. Az előfizetés tulajdonosának és a virtuálisgép-rendszergazdai engedélyeknek a kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- A Rendszergazdák csoport által megadott tagok bármelyikét tartalmazó Windows-virtuális gépek naplózása
- Olyan Windows virtuális gépek naplózása, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- A Rendszergazdák csoport által a megadott tagok bármelyikét tartalmazó Windows-virtuális gépek naplózására vonatkozó követelmények központi telepítése
- A Windows virtuális gépek naplózására vonatkozó követelmények központi telepítése, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Legkisebb Jogosultság | Felhasználói jogosultságok áttekintése

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet hozzárendeli az [Azure Policy-definíciók](../../../policy/overview.md) a naplózási fiókok, amelyeket prioritást kell adni a felülvizsgálathoz. Ezek a fiókmutatók áttekintésével biztosíthatja, hogy a legkevesebb jogosultsági vezérlő tvalósítsa meg.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- A Rendszergazdák csoport által megadott tagok bármelyikét tartalmazó Windows-virtuális gépek naplózása
- Olyan Windows virtuális gépek naplózása, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- A Rendszergazdák csoport által a megadott tagok bármelyikét tartalmazó Windows-virtuális gépek naplózására vonatkozó követelmények központi telepítése
- A Windows virtuális gépek naplózására vonatkozó követelmények központi telepítése, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

## <a name="ac-16-security-attributes"></a>AC-16 biztonsági attribútumok

Az Azure SQL Database speciális adatbiztonságának adatfelderítési és besorolási képessége lehetővé teszi az adatbázisokban lévő bizalmas adatok felderítését, osztályozását, címkézését és védelmét. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül. A speciális adatbiztonság segítségével biztosíthatja a szervezet megfelelő biztonsági attribútumaival társított információkat. Ez a tervezet [hozzárendeli az Azure Policy-definíciók](../../../policy/overview.md) a speciális adatbiztonság használatának figyeléséhez és érvényesítéséhez az SQL-kiszolgálón. 

- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) távelérés | Automatizált monitoring / ellenőrzés

Ez a tervezet segít a távelérés figyelésében és vezérlésében azáltal, hogy [Az Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével figyeli, hogy az Azure App Service-alkalmazás távoli hibakeresés ki van kapcsolva. A tervezet is hozzárendeli a szabályzatdefiníciók, amelyek a Linux virtuális gépek, amelyek lehetővé teszik a távoli kapcsolatok fiókok jelszavak nélkül. Emellett a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a tárfiókokhoz való korlátlan hozzáférés figyelésében. Ezek a mutatók figyelése segíthet biztosítani, hogy a távelérési módszerek megfeleljenek a biztonsági házirendnek.

- \[Előzetes\]verzió : A jelszavak nélküli fiókokból távoli kapcsolatokat engedélyező Linux-virtuális gépek naplózása
- \[Előzetes\]verzió : A jelszavak nélküli fiókokból távoli kapcsolatokat engedélyező Linux-virtuális gépek naplózására vonatkozó követelmények telepítése
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni a Függvényalkalmazásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Az ellenőrzési nyilvántartások tartalma | Tervezett naplórekord-tartalom központosított kezelése

Az Azure Monitor által gyűjtött naplóadatok at egy Log Analytics-munkaterület tárolja, amely lehetővé teszi a központi konfigurációt és felügyeletet. Ez a tervezet segít biztosítani az események naplózását az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- \[Előzetes\]verzió : Naplózási elemzési munkaterület a virtuális géphez – Jelentéseltérés
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Válasz a naplózási feldolgozási hibákra

Ez a tervezet hozzárendeli az [Azure Policy-definíciók,](../../../policy/overview.md) amelyek figyelik a naplózási és eseménynaplózási konfigurációk. Ezeknek a konfigurációknak a figyelése a naplózási rendszer hibájának vagy helytelen konfigurációjának jelzője lehet, és segíthet a korrekciós intézkedések elvégzésében.

- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Ellenőrzés felülvizsgálata, elemzése és jelentése | Központi felülvizsgálat és elemzés

Az Azure Monitor által gyűjtött naplóadatokat egy Log Analytics-munkaterület tárolja, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a tervezet segít biztosítani az események naplózását az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- \[Előzetes\]verzió : Naplózási elemzési munkaterület a virtuális géphez – Jelentéseltérés
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez

## <a name="au-12-audit-generation"></a>AU-12 audit generáció

Ez a tervezet segít biztosítani a rendszeresemények naplózása az [Azure-szabályzat](../../../policy/overview.md) definícióinak hozzárendelésével, amelyek naplóbeállításokat naplóznak az Azure-erőforrásokon. Ezek a szabályzatdefiníciók naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken, valamint a más Azure-erőforrástípusok naplózási beállításainak konfigurálását. Ezek a szabályzatdefiníciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekbe. Ezenkívül a naplózás és a speciális adatbiztonság az SQL-kiszolgálókon van konfigurálva.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- \[Előzetes\]verzió : Naplózási elemzési munkaterület a virtuális géphez – Jelentéseltérés
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Naplózás telepítése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Legkevésbé funkcionalitás | A program végrehajtásának megakadályozása

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlő olyan kényszerítési módban futhat, amely megtiltja a nem jóváhagyott alkalmazás futtatását. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Legkevésbé funkcionalitás | Engedélyezett szoftver / Engedélyezési lista

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlés segítségével jóváhagyott alkalmazáslistákat hozhat létre a virtuális gépekhez. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva. [Azure Policy](../../../policy/overview.md)

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="cm-11-user-installed-software"></a>CM-11 felhasználó által telepített szoftver

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlés segítségével kényszerítheti és figyelheti a szoftverkorlátozó házirendeknek való megfelelést. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva. [Azure Policy](../../../policy/overview.md)

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív feldolgozási hely

Az Azure Site Recovery replikálja a virtuális gépeken futó számítási feladatokat egy elsődleges helyről egy másodlagos helyre. Ha kimaradás történik az elsődleges helyen, a számítási feladatok nem felelnek meg a másodlagos helyen. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely a vész-helyreállítási konfigurálás nélküli virtuális gépeket naplózza. [Azure Policy](../../../policy/overview.md) A mutató figyelése segíthet biztosítani a szükséges vészhelyzeti ellenőrzéseket.

- Virtuális gépek naplózása a vészhelyreállítás konfigurálása nélkül

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés kiemelt jogosultságú fiókokhoz

Ez a tervezet segít korlátozni és szabályozni a kiemelt hozzáférés hozzárendelésével [Azure Policy-definíciók](../../../policy/overview.md) a könyvvizsgálói fiókok tulajdonosi és/vagy írási engedélyek, amelyek nem rendelkeznek többtényezős hitelesítés engedélyezve van. A többtényezős hitelesítés akkor is segít a fiókok biztonságának megőrzésében, ha egy hitelesítési adat biztonsága sérül. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek.

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-t engedélyezni kell az előfizetéshez írási engedéllyel rendelkező fiókokon

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés nem kiemelt jogosultságú fiókokhoz

Ez a tervezet segít korlátozni és szabályozni a hozzáférést azáltal, hogy egy [Azure Policy-definíciót](../../../policy/overview.md) rendel a többtényezős hitelesítést nem engedélyező olvasási engedélyekkel rendelkező naplózási fiókokhoz. A többtényezős hitelesítés akkor is segít a fiókok biztonságának megőrzésében, ha egy hitelesítési adat biztonsága sérül. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek.

- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon

## <a name="ia-5-authenticator-management"></a>IA-5 hitelesítő kezelése

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek naplózják a Linux virtuális gépek, amelyek lehetővé teszik a távoli kapcsolatok fiókok jelszavak nélkül, és/vagy helytelen engedélyekkel a passwd fájlt. [Azure Policy](../../../policy/overview.md) Ez a tervezet is hozzárendeli a házirend-definíciók, amelyek naplózják a windowsos virtuális gépek jelszótitkosítási típusának konfigurációját. Ezek a mutatók figyelése segít biztosítani, hogy a rendszer hitelesítők megfelelnek a szervezet azonosítási és hitelesítési házirend.

- \[Előzetes\]verzió : A 0644-es passwd fájlengedélyekkel nem rendelkező Linux-virtuális gépek naplózása
- \[Előzetes\]verzió : A jelszó nélküli fiókkal rendelkező Linux-virtuális gépek naplózása
- \[Előzetes\]verzió : A jelszavakat nem tároló Windows-virtuális gépek naplózása visszafordítható titkosítással
- \[Előzetes\]verzió: A 0644-es jelszóval nem rendelkező passwd fájlengedélyekkel nem rendelkező Linux-virtuális gépek naplózására vonatkozó követelmények üzembe helyezése
- \[Előzetes\]verzió: A jelszavak nélküli fiókkal rendelkező Linux-virtuális gépek naplózására vonatkozó követelmények üzembe helyezése
- \[Előzetes\]verzió : Követelmények telepítése olyan Windows virtuális gépek naplózására, amelyek nem tárolnak jelszavakat visszafordítható titkosítással

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Hitelesítő menedzsment | Jelszóalapú hitelesítés

Ez a tervezet segít az erős jelszavak kényszerítésében az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek olyan Windows virtuális gépeket naplóznak, amelyek nem kényszerítik ki a minimális erősséget és az egyéb jelszókövetelményeket. A jelszóerősségre vonatkozó szabályzatot megsértő virtuális gépek ismertségét figyelembe véve korrekciós műveleteket tesz lehet annak biztosítására, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezet jelszóházirendjének.

- \[Előzetes\]verzió : A korábbi 24 jelszó újbóli használatát lehetővé tévő Windows-virtuális gépek naplózása
- \[Előzetes\]verzió : A 70 napos maximális jelszóval nem rendelkező Windows-virtuális gépek naplózása
- \[Előzetes\]verzió : A Windows virtuális gépek naplózása, amelyek minimális jelszókoruk nem 1 nap
- \[Előzetes\]verzió : A jelszó-összetettségi beállítással nem rendelkező Windows-virtuális gépek naplózása
- \[Előzetes\]verzió : A jelszó minimális hosszát nem tartalmazó Windows-virtuális gépek naplózása 14 karakterre
- \[Előzetes\]verzió : A jelszavakat nem tároló Windows-virtuális gépek naplózása visszafordítható titkosítással
- \[Előzetes\]verzió : A korábbi 24 jelszó újbóli használatát lehetővé tévő Windows virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió: A 70 napos maximális jelszóval nem rendelkező Windows-virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió: A windowsos virtuális gépek naplózására vonatkozó követelmények telepítése, amelyek minimális jelszóéletkora nem 1 nap
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózására vonatkozó követelmények telepítése, amelyeknél engedélyezve van a jelszó-összetettségi beállítás
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózására vonatkozó követelmények telepítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : Követelmények telepítése olyan Windows virtuális gépek naplózására, amelyek nem tárolnak jelszavakat visszafordítható titkosítással

## <a name="ra-5-vulnerability-scanning"></a>RA-5 biztonsági rés vizsgálata

Ez a tervezet segít az információs rendszer biztonsági rései kezelésében azáltal, hogy olyan Azure Policy-definíciókat rendel hozzá, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuálisgépek biztonsági réseit az Azure Security Centerben. [Azure Policy](../../../policy/overview.md) Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába. Ez a tervezet olyan házirend-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot naplózják és kényszerítik. A speciális adatbiztonság magában foglalta a biztonsági rések felmérését és a speciális veszélyforrások elleni védelmi képességeket, amelyek segítenek megérteni a telepített erőforrások biztonsági réseit.

- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- A virtuális gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani
- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás elleni védelem

Az Azure elosztott szolgáltatásmegtagadási (DDoS) standard rétege további funkciókat és kockázatcsökkentési lehetőségeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók közé tartozik az Azure Monitor integrációja és a támadás utáni kockázatcsökkentési jelentések áttekintése. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely naplózza, ha a DDoS standard réteg engedélyezve van. [Azure Policy](../../../policy/overview.md) A szolgáltatásszintek közötti képességbeli különbség megértése segíthet kiválasztani a legjobb megoldást az Azure-környezet szolgáltatásmegtagadási védelmének kezelésére.

- A DDoS Protection Standard-ot engedélyezni kell

## <a name="sc-7-boundary-protection"></a>SC-7 határvédelem

Ez a tervezet segít a rendszerhatár kezeléséhez és vezérléséhez egy [Azure Policy-definíció](../../../policy/overview.md) hozzárendelésével, amely figyeli a hálózati biztonsági csoport edzési javaslatokat az Azure Security Centerben. Az Azure Security Center elemzi az internetes virtuális gépek forgalmi mintáit, és a potenciális támadási felület csökkentésére vonatkozó hálózati biztonsági csoportszabályokat biztosít.
Emellett ez a tervezet is hozzárendeli a nem védett végpontok, alkalmazások és tárfiókok figyelő házirend-definíciók. Végpontok és alkalmazások, amelyek nem védi a tűzfal, és a tárfiókok korlátlan hozzáféréssel lehetővé teheti a nem kívánt hozzáférést az információs rendszer ben található információkhoz.

- A virtuális gépek internetre néző hálózati biztonsági csoportszabályait meg kell erősíteni
- Az internetfelé néző végponton keresztüli hozzáférést korlátozni kell
- Az IaaS-alapú webes alkalmazásokra vonatkozó NSG-szabályokat
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Határvédelem | Hozzáférési pontok

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A JIT virtuálisgépek hez való hozzáférés segítségével korlátozhatja az Azure-beli erőforrásokhoz való külső kapcsolatok számát. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Határvédelem | Külső távközlési szolgáltatások

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A JIT virtuálisgépek hez való hozzáférés a hozzáférési kérelem- és jóváhagyási folyamatok megkönnyítése révén segít a forgalomkezelési szabályzat alóli kivételek kezelésében. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Átviteli titkosság és integritás | Kriptográfiai vagy alternatív fizikai védelem

Ez a tervezet segít megvédeni a bizalmas és integritását továbbított információk hozzárendelésével Azure Policy-definíciók, amelyek segítségével figyelheti a kommunikációs protokollok megvalósított kriptográfiai mechanizmus. [Azure Policy](../../../policy/overview.md) A kommunikáció megfelelő titkosításának biztosítása segíthet a szervezet követelményeinek teljesítésében, illetve az adatok jogosulatlan nyilvánosságra hozatallal és módosításokkal szembeni védelmében.

- Az API-alkalmazás csak HTTPS-en keresztül érhető el
- Biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózása
- A nem biztonságos kommunikációs protokollokat használó Windows webkiszolgálók naplózására vonatkozó követelmények központi telepítése
- A Függvényalkalmazás csak HTTPS-en keresztül érhető el
- Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A webalkalmazás csak HTTPS-en keresztül érhető el

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) A nyugalmi információk védelme | Kriptográfiai védelem

Ez a tervezet segít a szabályzat kényszerítése a kriptográfiai vezérlők használatával az inkriptográfiai információk védelmére az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek kikényszerítik az adott titkosítási vezérlőket és a gyenge titkosítási beállítások naplózását. Ha tisztában van azzal, hogy az Azure-erőforrások hol rendelkezhetnek nem optimális kriptográfiai konfigurációkkal, akkor korrekciós műveleteket végezhet annak érdekében, hogy az erőforrások az információbiztonsági szabályzatnak megfelelően legyenek konfigurálva. Pontosabban a tervezet által hozzárendelt házirend-definíciók titkosítást igényelnek az adattó-tárfiókokhoz; transzparens adattitkosítást igényel nek az SQL-adatbázisokon; és a hiányzó titkosítás naplózása az SQL-adatbázisokon, a virtuálisgép-lemezeken és az automatizálási fiókváltozókon.

- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának telepítése
- A lemeztitkosítást virtuális gépeken kell alkalmazni
- Titkosítás megkövetelése data lake store-fiókokon
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell

## <a name="si-2-flaw-remediation"></a>SI-2 hibaelhárítás

Ez a tervezet segít az információs rendszer hibáinak kezelésében azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek biztonsági réseit az Azure Security Centerben. Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába. Ez a tervezet is hozzárendel egy házirend-definíciót, amely biztosítja az operációs rendszer javítását a virtuálisgép-méretezési készletekhez.

- Automatikus operációsrendszer-lemezkép-javítás megkövetelése a virtuálisgép-méretezési készleteken
- A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- A virtuális gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani
- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni

## <a name="si-3-malicious-code-protection"></a>SI-3 rosszindulatú kódvédelem

Ez a tervezet segít a végpontvédelem kezelésében, beleértve a rosszindulatú kódvédelmet is, azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó végpontvédelmet az Azure Security Center virtuális gépein, és kényszerítik a Microsoft kártevőirtó megoldást a Windows virtuális gépeken.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez
- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Rosszindulatú kódvédelem | Központi irányítás

Ez a tervezet segít a végpontvédelem kezeléséhez, beleértve a rosszindulatú kódvédelmet is, az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek figyelik a hiányzó végpontvédelmet az Azure Security Center virtuális gépeken. Az Azure Security Center központosított felügyeleti és jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába.

- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="si-4-information-system-monitoring"></a>SI-4 információs rendszer ellenőrzése

Ez a tervezet segít a rendszer figyelése naplózása és az adatok biztonságának érvényesítése az Azure-erőforrások között. Pontosabban a naplózáshoz rendelt naplózási és kényszerítési üzembe helyezés a Log Analytics-ügynök, és a továbbfejlesztett biztonsági beállításokat az SQL-adatbázisok, tárfiókok és hálózati erőforrások. Ezek a képességek segíthetnek a rendellenes viselkedés és a támadások jelzőinek észlelésében, így megteheti a megfelelő lépéseket.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Naplózási ügynök központi telepítése vMSs - virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén
- \[Előzetes\]verzió : Naplózási elemzési munkaterület a virtuális géphez – Jelentéseltérés
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez
- A felügyelt példányokon engedélyezni kell a speciális adatbiztonságot
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Speciális veszélyforrások elleni védelem telepítése a tárfiókokon
- Naplózás telepítése SQL-kiszolgálókon
- Hálózati figyelő telepítése virtuális hálózatok létrehozásakor
- Fenyegetésészlelés telepítése SQL-kiszolgálókon

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Információs Rendszer monitoring | Forgalom / Rejtett kiszivárgás elemzése

Az Azure Storage komplex veszélyforrások elleni védelme szokatlan és potenciálisan káros kísérleteket észlel a tárfiókok elérésére vagy kihasználására. A védelmi riasztások közé tartozik a rendellenes hozzáférési minták, rendellenes kivonatok/feltöltések és a gyanús tárolási tevékenység. Ezek a mutatók segíthetnek az információk rejtett kiszivárgásának észlelésében.

- Speciális veszélyforrások elleni védelem telepítése a tárfiókokon

> [!NOTE]
> Bizonyos Azure-szabályzat-definíciók elérhetősége az Azure Government és más nemzeti felhők ben eltérő lehet.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a NIST SP 800-53 R4 tervrajz vezérlőleképezését, az alábbi cikkekben megtudhatja a tervrajzot és a minta üzembe helyezését:

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 tervezet - Áttekintés](./index.md)
> [NIST SP 800-53 R4 terv - Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
