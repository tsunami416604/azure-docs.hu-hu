---
title: SWIFT CSP-CSCF v2020 mintaellenőrzések
description: A SWIFT CSP-CSCF v2020 tervezetminta ellenőrzési feltérképezése. Minden vezérlő le van képezve egy vagy több Azure-szabályzatok, amelyek segítik az értékelést.
ms.date: 09/24/2019
ms.topic: sample
ms.openlocfilehash: 1826e4820f06fc3a858ce6cd5f8906baaa9808cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546514"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>A SWIFT CSP-CSCF v2020 tervezetminta ellenőrzési feltérképezése

A következő cikk bemutatja, hogy az Azure Blueprints SWIFT CSP-CSCF v2020 tervezet minta minta leképezi a SWIFT CSP-CSCF v2020 vezérlők. Az ellenőrzésekkel kapcsolatos további információkért lásd a [SWIFT CSP-CSCF v2020 című témakört.](https://www.swift.com/myswift/customer-security-programme-csp)

A következő leképezések a **SWIFT CSP-CSCF v2020** vezérlők. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők nagy része egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítva meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[előzetes verziót:\]Ellenőrizze a SWIFT CSP-CSCF v2020-as vezérlőket, és telepítsen konkrét virtuálisgép-bővítményeket** a beépített házirend-kezdeményezés naplózási követelményeinek támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md)

## <a name="12-and-51-account-management"></a>1.2 és 5.1 Számlakezelés

Ez a tervezet segít a szervezet fiókkezelési követelményeinek nem megfelelő fiókok áttekintéséhez. Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek a külső fiókok naplózása olvasási, írási és tulajdonosi engedélyekkel egy előfizetés és elavult fiókok. [Azure Policy](../../../policy/overview.md) A szabályzatok által naplózott fiókok áttekintésével megfelelő lépéseket tehet a fiókkezelési követelmények teljesítéséhez.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1., 6.4. Szerepköralapú rendszerek

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet is hozzárendeli [az Azure Policy-definíciók](../../../policy/overview.md) az Azure Active Directory-hitelesítés SQL-kiszolgálók és a Service Fabric használatának naplózásához. Az Azure Active Directory-hitelesítés lehetővé teszi az egyszerűbb engedélykezelést és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezelését. Emellett ez a tervezet hozzárendel egy Azure Policy-definíciót az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának ismertetése segíthet a szükséges és megfelelő megvalósítás ellenőrzésében, mivel az egyéni RBAC-szabályok hibalehetőségeket rejtenek.

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- Egyéni RBAC-szabályok használatának naplózása
- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Számlakezelés | Fiókfigyelés / atipikus használat

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A virtuális gépek elérésére irányuló jit-kérelmek et a tevékenységnapló naplózza, amely lehetővé teszi az atipikus használat figyelését. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1.

Ha csak egy Azure-előfizetés-tulajdonossal rendelkezik, az nem teszi lehetővé a felügyeleti redundanciát. Ezzel szemben a túl sok Azure-előfizetés-tulajdonos növelheti a feltört tulajdonosi fiókon keresztül a feltört fiók megsértése lehetőségét. Ez a tervezet segít fenntartani a megfelelő számú Azure-előfizetés-tulajdonosok hozzárendelésével Azure Policy-definíciók, amelyek naplózják a tulajdonosok száma az Azure-előfizetések. [Azure Policy](../../../policy/overview.md) Ez a tervezet is hozzárendeli az Azure Policy-definíciók, amelyek segítségével szabályozhatja a rendszergazdák csoport tagságát a Windows virtuális gépeken. Az előfizetés tulajdonosának és a virtuálisgép-rendszergazdai engedélyeknek a kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 és 6.4 Legkevésbé privilégium | Felhasználói jogosultságok áttekintése

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, hogy segítsen kezelni, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. Az Azure Portal használatával megtekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a tervezet hozzárendeli az [Azure Policy-definíciók](../../../policy/overview.md) a naplózási fiókok, amelyeket prioritást kell adni a felülvizsgálathoz. Ezek a fiókmutatók áttekintésével biztosíthatja, hogy a legkevesebb jogosultsági vezérlő tvalósítsa meg.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

## <a name="22-and-27-security-attributes"></a>2.2. és 2.7.

Az Azure SQL Database speciális adatbiztonságának adatfelderítési és besorolási képessége lehetővé teszi az adatbázisokban lévő bizalmas adatok felderítését, osztályozását, címkézését és védelmét. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül. A speciális adatbiztonság segítségével biztosíthatja a szervezet megfelelő biztonsági attribútumaival társított információkat. Ez a tervezet [hozzárendeli az Azure Policy-definíciók](../../../policy/overview.md) a speciális adatbiztonság használatának figyeléséhez és érvényesítéséhez az SQL-kiszolgálón. 

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7., 4.1. Automatizált monitoring / ellenőrzés

Ez a tervezet segít a távelérés figyelésében és vezérlésében azáltal, hogy az Azure Policy-definíciókat hozzárendeli az Azure App Service-alkalmazás távoli hibakeresésének kikapcsolását, és a linuxos virtuális gépeket naplózó szabályzatdefiníciók, amelyek jelszavak nélküli fiókokból távoli kapcsolatokat engedélyeznek. [Azure Policy](../../../policy/overview.md) Ez a tervezet is hozzárendel egy Azure Policy-definíciót, amely segít a tárfiókokhoz való korlátlan hozzáférés figyelésében. Ezek a mutatók figyelése segíthet biztosítani, hogy a távelérési módszerek megfeleljenek a biztonsági házirendnek.

- \[Előzetes\]verzió : A linuxos virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a jelszavak nélküli fiókok távoli kapcsolatait
- \[Előzetes\]verzió : Előfeltételek telepítése a Linux-alapú virtuális gépek naplózásához, amelyek lehetővé teszik a jelszavak nélküli fiókokból származó távoli kapcsolatokat
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni a Függvényalkalmazásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 és 6.4 Az ellenőrzési nyilvántartások tartalma | Tervezett naplórekord-tartalom központosított kezelése

Az Azure Monitor által gyűjtött naplóadatok at egy Log Analytics-munkaterület tárolja, amely lehetővé teszi a központi konfigurációt és felügyeletet. Ez a tervezet segít biztosítani az események naplózását az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 és 6.4 Válasz a naplózási feldolgozási hibákra

Ez a tervezet hozzárendeli az [Azure Policy-definíciók,](../../../policy/overview.md) amelyek figyelik a naplózási és eseménynaplózási konfigurációk. Ezeknek a konfigurációknak a figyelése a naplózási rendszer hibájának vagy helytelen konfigurációjának jelzője lehet, és segíthet a korrekciós intézkedések elvégzésében.

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Diagnosztikai beállítás naplózása
- Naplózás telepítése AZ SQL-kiszolgálón

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3. és 6.4. Központi felülvizsgálat és elemzés

Az Azure Monitor által gyűjtött naplóadatokat egy Log Analytics-munkaterület tárolja, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a tervezet segít biztosítani az események naplózását az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2,2,2,7,6.4 és 6.5A Audit Generation

Ez a tervezet segít biztosítani a rendszeresemények naplózása az [Azure-szabályzat](../../../policy/overview.md) definícióinak hozzárendelésével, amelyek naplóbeállításokat naplóznak az Azure-erőforrásokon. Ezek a szabályzatdefiníciók naplózzák és kényszerítik a Log Analytics-ügynök üzembe helyezését az Azure virtuális gépeken, valamint a más Azure-erőforrástípusok naplózási beállításainak konfigurálását. Ezek a szabályzatdefiníciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekbe. Ezenkívül a naplózás és a speciális adatbiztonság az SQL-kiszolgálókon van konfigurálva.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez
- Diagnosztikai beállítás naplózása
- SQL-kiszolgálószintű naplózási beállítások naplózása
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Naplózás telepítése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Legkevésbé funkcionalitás | A program végrehajtásának megakadályozása

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlő olyan kényszerítési módban futhat, amely megtiltja a nem jóváhagyott alkalmazás futtatását. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Legkevésbé funkcionalitás | Engedélyezett szoftver / Engedélyezési lista

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlés segítségével jóváhagyott alkalmazáslistákat hozhat létre a virtuális gépekhez. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva. [Azure Policy](../../../policy/overview.md)

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="11-user-installed-software"></a>1.1 Felhasználó által telepített szoftver

Az Azure Security Center adaptív alkalmazásvezérlése egy intelligens, teljes körű alkalmazásengedélyezési megoldás, amely blokkolhatja vagy megakadályozhatja, hogy bizonyos szoftverek fussanak a virtuális gépeken. Az alkalmazásvezérlés segítségével kényszerítheti és figyelheti a szoftverkorlátozó házirendeknek való megfelelést. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, ahol egy alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva. [Azure Policy](../../../policy/overview.md)

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés kiemelt jogosultságú fiókokhoz

Ez a tervezet segít korlátozni és szabályozni a kiemelt hozzáférés hozzárendelésével [Azure Policy-definíciók](../../../policy/overview.md) a könyvvizsgálói fiókok tulajdonosi és/vagy írási engedélyek, amelyek nem rendelkeznek többtényezős hitelesítés engedélyezve van. A többtényezős hitelesítés akkor is segít a fiókok biztonságának megőrzésében, ha egy hitelesítési adat biztonsága sérül. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek.

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-t engedélyezni kell az előfizetéshez írási engedéllyel rendelkező fiókokon

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés nem kiemelt jogosultságú fiókokhoz

Ez a tervezet segít korlátozni és szabályozni a hozzáférést azáltal, hogy egy [Azure Policy-definíciót](../../../policy/overview.md) rendel a többtényezős hitelesítést nem engedélyező olvasási engedélyekkel rendelkező naplózási fiókokhoz. A többtényezős hitelesítés akkor is segít a fiókok biztonságának megőrzésében, ha egy hitelesítési adat biztonsága sérül. Ha a többtényezős hitelesítést nem engedélyező fiókokat figyeli, azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel sérülhetnek.

- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon

## <a name="23-and-41-authenticator-management"></a>2.3. és 4.1.

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek naplózják a Linux virtuális gépek, amelyek lehetővé teszik a távoli kapcsolatok fiókok jelszavak nélkül, és/vagy helytelen engedélyekkel a passwd fájlt. [Azure Policy](../../../policy/overview.md) Ez a tervezet is hozzárendeli a házirend-definíciók, amelyek naplózják a windowsos virtuális gépek jelszótitkosítási típusának konfigurációját. Ezek a mutatók figyelése segít biztosítani, hogy a rendszer hitelesítők megfelelnek a szervezet azonosítási és hitelesítési házirend.

- \[Előzetes\]verzió : A 0644-es passwd fájlengedélyekkel nem rendelkező Linux-virtuális gépek naplózási eredményeinek megjelenítése
- \[Előzetes\]verzió: A 0644-es jelszóval nem rendelkező passwd fájlengedélyekkel nem rendelkező Linux-virtuális gépek naplózására vonatkozó követelmények üzembe helyezése
- \[Előzetes\]verzió : A jelszó nélküli fiókkal rendelkező Linux-virtuális gépek naplózási eredményeinek megjelenítése
- \[Előzetes\]verzió: A jelszavak nélküli fiókkal rendelkező Linux-virtuális gépek naplózására vonatkozó követelmények üzembe helyezése
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem tárolnak jelszavakat visszafordítható titkosítással
- \[Előzetes\]verzió : Követelmények telepítése olyan Windows virtuális gépek naplózására, amelyek nem tárolnak jelszavakat visszafordítható titkosítással

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3. és 4.1. Jelszóalapú hitelesítés

Ez a tervezet segít az erős jelszavak kényszerítésében az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek olyan Windows virtuális gépeket naplóznak, amelyek nem kényszerítik ki a minimális erősséget és az egyéb jelszókövetelményeket. A jelszóerősségre vonatkozó szabályzatot megsértő virtuális gépek ismertségét figyelembe véve korrekciós műveleteket tesz lehet annak biztosítására, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezet jelszóházirendjének.

- \[Előzetes\]verzió : A Windows virtuális gépek ből származó olyan naplózási eredmények megjelenítése, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek maximális jelszószáma nem 70 nap
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek minimális jelszóéletkora nem 1 nap
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyeknél nincs engedélyezve a jelszó-összetettségi beállítás
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem tárolnak jelszavakat visszafordítható titkosítással
- \[Előzetes\]verzió : Előfeltételek telepítése a Windows virtuális gépek naplózásához, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát
- \[Előzetes\]verzió : Előfeltételek telepítése a 70 napos maximális jelszóval nem rendelkező Windows virtuális gépek naplózásához
- \[Előzetes\]verzió : Előfeltételek telepítése az 1 napos minimális jelszóval nem rendelkező Windows-virtuális gépek naplózásához
- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyeknél nincs engedélyezve a jelszó-összetettségi beállítás
- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes\]verzió : Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyek nem tárolnak jelszavakat visszafordítható titkosítással

## <a name="22-and-27-vulnerability-scanning"></a>2.2 és 2.7 Sebezhetőség vizsgálata

Ez a tervezet segít az információs rendszer biztonsági rései kezelésében azáltal, hogy olyan Azure Policy-definíciókat rendel hozzá, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuálisgépek biztonsági réseit az Azure Security Centerben. [Azure Policy](../../../policy/overview.md) Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába. Ez a tervezet olyan házirend-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot naplózják és kényszerítik. A speciális adatbiztonság magában foglalta a biztonsági rések felmérését és a speciális veszélyforrások elleni védelmi képességeket, amelyek segítenek megérteni a telepített erőforrások biztonsági réseit.

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani 
- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani

## <a name="13-denial-of-service-protection"></a>1.3 Szolgáltatásmegtagadás elleni védelem

Az Azure elosztott szolgáltatásmegtagadási (DDoS) standard rétege további funkciókat és kockázatcsökkentési lehetőségeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók közé tartozik az Azure Monitor integrációja és a támadás utáni kockázatcsökkentési jelentések áttekintése. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely naplózza, ha a DDoS standard réteg engedélyezve van. [Azure Policy](../../../policy/overview.md) A szolgáltatásszintek közötti képességbeli különbség megértése segíthet kiválasztani a legjobb megoldást az Azure-környezet szolgáltatásmegtagadási védelmének kezelésére.

- A DDoS Protection Standard-ot engedélyezni kell

## <a name="11-and-61-boundary-protection"></a>1.1 és 6.1 Határvédelem

Ez a tervezet segít a rendszerhatár kezeléséhez és vezérléséhez egy [Azure Policy-definíció](../../../policy/overview.md) hozzárendelésével, amely figyeli a hálózati biztonsági csoport edzési javaslatokat az Azure Security Centerben. Az Azure Security Center elemzi az internetes virtuális gépek forgalmi mintáit, és a potenciális támadási felület csökkentésére vonatkozó hálózati biztonsági csoportszabályokat biztosít.
Emellett ez a tervezet is hozzárendeli a nem védett végpontok, alkalmazások és tárfiókok figyelő házirend-definíciók. Végpontok és alkalmazások, amelyek nem védi a tűzfal, és a tárfiókok korlátlan hozzáféréssel lehetővé teheti a nem kívánt hozzáférést az információs rendszer ben található információkhoz.

- A virtuális gépek internetre néző hálózati biztonsági csoportszabályait meg kell erősíteni
- Az internetfelé néző végponton keresztüli hozzáférést korlátozni kell
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

## <a name="29a-boundary-protection--access-points"></a>2.9A Határvédelem | Hozzáférési pontok

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A JIT virtuálisgépek hez való hozzáférés segítségével korlátozhatja az Azure-beli erőforrásokhoz való külső kapcsolatok számát. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Határvédelem | Külső távközlési szolgáltatások

A just-in-time (JIT) virtuális gépi hozzáférés zárolja az Azure virtuális gépekre irányuló bejövő forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. A JIT virtuálisgépek hez való hozzáférés a hozzáférési kérelem- és jóváhagyási folyamatok megkönnyítése révén segít a forgalomkezelési szabályzat alóli kivételek kezelésében. Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépek figyelésében, amelyek támogatják a just-in-time hozzáférést, de még nincsenek konfigurálva. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4., 2.4A., 2.5A. Kriptográfiai vagy alternatív fizikai védelem

Ez a tervezet segít megvédeni a bizalmas és integritását továbbított információk hozzárendelésével Azure Policy-definíciók, amelyek segítségével figyelheti a kommunikációs protokollok megvalósított kriptográfiai mechanizmus. [Azure Policy](../../../policy/overview.md) A kommunikáció megfelelő titkosításának biztosítása segíthet a szervezet követelményeinek teljesítésében, illetve az adatok jogosulatlan nyilvánosságra hozatallal és módosításokkal szembeni védelmében.

- Az API-alkalmazás csak HTTPS-en keresztül érhető el
- Biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- Előfeltételek telepítése a nem biztonságos kommunikációs protokollokat használó Windows webkiszolgálók naplózásához
- A Függvényalkalmazás csak HTTPS-en keresztül érhető el
- Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A webalkalmazás csak HTTPS-en keresztül érhető el

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3., 2.5., 4.1. Kriptográfiai védelem

Ez a tervezet segít a szabályzat kényszerítése a kriptográfiai vezérlők használatával az inkriptográfiai információk védelmére az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek kikényszerítik az adott titkosítási vezérlőket és a gyenge titkosítási beállítások naplózását. Ha tisztában van azzal, hogy az Azure-erőforrások hol rendelkezhetnek nem optimális kriptográfiai konfigurációkkal, akkor korrekciós műveleteket végezhet annak érdekében, hogy az erőforrások az információbiztonsági szabályzatnak megfelelően legyenek konfigurálva. Pontosabban a tervezet által hozzárendelt házirend-definíciók titkosítást igényelnek az adattó-tárfiókokhoz; transzparens adattitkosítást igényel nek az SQL-adatbázisokon; és a hiányzó titkosítás naplózása az SQL-adatbázisokon, a virtuálisgép-lemezeken és az automatizálási fiókváltozókon.

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának telepítése
- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 és 2.7 Hibaelhárítás

Ez a tervezet segít az információs rendszer hibáinak kezelésében azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek biztonsági réseit az Azure Security Centerben. Az Azure Security Center olyan jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába. Ez a tervezet is hozzárendel egy házirend-definíciót, amely biztosítja az operációs rendszer javítását a virtuálisgép-méretezési készletekhez.

- Automatikus operációsrendszer-lemezkép-javítás megkövetelése a virtuálisgép-méretezési készleteken
- A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- A virtuális gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani

## <a name="61-malicious-code-protection"></a>6.1 Rosszindulatú kódvédelem

Ez a tervezet segít a végpontvédelem kezelésében, beleértve a rosszindulatú kódvédelmet is, azáltal, hogy [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek figyelik a hiányzó végpontvédelmet az Azure Security Center virtuális gépein, és kényszerítik a Microsoft kártevőirtó megoldást a Windows virtuális gépeken.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Server rendszerhez
- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="61-malicious-code-protection--central-management"></a>6.1 Rosszindulatú kódvédelem | Központi irányítás

Ez a tervezet segít a végpontvédelem kezeléséhez, beleértve a rosszindulatú kódvédelmet is, az [Azure Policy-definíciók](../../../policy/overview.md) hozzárendelésével, amelyek figyelik a hiányzó végpontvédelmet az Azure Security Center virtuális gépeken. Az Azure Security Center központosított felügyeleti és jelentéskészítési lehetőségeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést nyerjen az üzembe helyezett Azure-erőforrások biztonsági állapotába.

- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3,2,2,2., 2.7., 2.8.

Ez a tervezet segít a rendszer figyelése naplózása és az adatok biztonságának érvényesítése az Azure-erőforrások között. Pontosabban a naplózáshoz rendelt naplózási és kényszerítési üzembe helyezés a Log Analytics-ügynök, és a továbbfejlesztett biztonsági beállításokat az SQL-adatbázisok, tárfiókok és hálózati erőforrások. Ezek a képességek segíthetnek a rendellenes viselkedés és a támadások jelzőinek észlelésében, így megteheti a megfelelő lépéseket.

- \[Előzetes\]verzió : Naplózási ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)
- \[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Az SQL-kiszolgáló speciális adatbiztonsági beállításainak tartalmazniuk kell egy e-mail címet a biztonsági riasztások fogadásához
- Az Azure Stream Analytics diagnosztikai naplóit engedélyezni kell
- Speciális adatbiztonság telepítése SQL-kiszolgálókon
- Naplózás telepítése SQL-kiszolgálókon
- Hálózati figyelő telepítése virtuális hálózatok létrehozásakor
- Fenyegetésészlelés telepítése SQL-kiszolgálókon

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2. és 2.8. Forgalom / Rejtett kiszivárgás elemzése

Az Azure Storage komplex veszélyforrások elleni védelme szokatlan és potenciálisan káros kísérleteket észlel a tárfiókok elérésére vagy kihasználására. A védelmi riasztások közé tartozik a rendellenes hozzáférési minták, rendellenes kivonatok/feltöltések és a gyanús tárolási tevékenység. Ezek a mutatók segíthetnek az információk rejtett kiszivárgásának észlelésében.

- Fenyegetésészlelés telepítése SQL-kiszolgálókon

> [!NOTE]
> Bizonyos Azure-szabályzat-definíciók elérhetősége az Azure Government és más nemzeti felhők ben eltérő lehet.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a SWIFT CSP-CSCF v2020 tervrajz vezérlőtérképezését, az alábbi cikkekben megtudhatja a tervezetet és a minta üzembe helyezését:

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF v2020 tervezet - Áttekintés](./index.md)
> [SWIFT CSP-CSCF v2020 tervezet - Lépések telepítése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
