---
title: Minta - 800-53 előírásain NIST SP R4 tervezet - vezérlő leképezés
description: Az NIST SP leképezését 800-53 előírásain R4 tervezet minta Azure Policy szabályozza.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 251dbc396aea5694c4bdec45c194439c9476238b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226038"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Szabályozhatja a NIST SP leképezését 800-53 előírásain R4 tervezet minta

A következő cikk részletezi, hogyan Azure tervezetek SP-NIST 800-53 előírásain R4 tervezet minta leképezi a NIST SP 800-53 előírásain R4 vezérlők. A vezérlőkkel kapcsolatos további információkért lásd: [NIST SP 800-53 előírásain](https://nvd.nist.gov/800-53).

A következő hozzárendeléseket a rendszer a **NIST SP 800-53-as (fordított 4)** szabályozza. A jobb oldalon a navigációs segítségével közvetlenül egy adott vezérlőt leképezés ugorhat. Számos, a csatlakoztatott vezérlők implementálása egy [Azure Policy](../../../policy/overview.md) kezdeményezésére. Tekintse át a teljes kezdeményezés, nyissa meg a **házirend** az Azure Portalon, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a  **\[előzetes\]: Naplózási NIST SP 800-53 előírásain R4 vezérlők és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése** beépített szabályzat-kezdeményezéshez.

## <a name="ac-2-account-management"></a>AC-2 fiókok kezelése

Ez a megoldás segít tekintse át a fiókokat, előfordulhat, hogy nem felel meg a munkahelyi fiók felügyeleti követelményeket. Ez a megoldás öt Azure szabályzat-meghatározást, amely egy adott előfizetés olvasási, írási és tulajdonosi engedélyekkel rendelkező külső fiókok naplózása és elavult fiókok rendeli hozzá. A fiókok, ezek a szabályzatok által naplózott áttekintésével megfelelő lépéseket a fiók felügyeleti követelmények teljesítése is igénybe vehet.

- \[Előzetes verzió\]: Elavult fiókokat egy előfizetésben naplózása
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- \[Előzetes verzió\]: Egy adott előfizetés olvasási jogosultsággal rendelkező külső fiókok naplózása
- \[Előzetes verzió\]: Előfizetésben írási engedélyekkel rendelkező külső fiókok naplózása

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) felhasználóifiók-kezelés |} Szerepkör-alapú rendszerek

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC), amelyek segítségével kezelheti, ki férhet hozzá erőforrásokat az Azure-ban. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. Ez a megoldás két is rendel [Azure Policy](../../../policy/overview.md) naplózása érdekében használja az Azure Active Directory-hitelesítés az SQL Server-kiszolgálók és a Service Fabric. Az Azure Active Directory authentication lehetővé teszi, engedélykezelés egyszerűsített és központosított Identitáskezelés adatbázis-felhasználók és más Microsoft-szolgáltatások.

- Az Azure Active Directory-rendszergazda, az SQL server üzembe helyezése naplózása
- Az ügyfél-hitelesítéshez, a Service Fabric az Azure Active Directory használatának naplózása

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) felhasználóifiók-kezelés |} Figyelési / a rendellenes használat fiók

– Igény (szerinti JIT) virtuális gép hozzáférés zárolások az Azure virtual machines, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalom le. Hozzáférés a virtuális gépek minden igény szerinti kérése a tevékenységnapló, lehetővé téve, hogy figyelje a szokatlan használati jelentkezett be. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely segít a virtuális gépek figyelése céljából, amelyek igény szerinti elérése is támogatják, de még nem lett konfigurálva.

- \[Előzetes verzió\]: Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információk folyamat kényszerítése

Közötti eredetű erőforrások megosztása (CORS) lehetővé tehetik a App Services-erőforrások egy külső tartományból a kötelező. A Microsoft azt javasolja, hogy lehetővé tegye az API-t, a függvény és a webalkalmazások interakcióba csak szükséges tartományok. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíció megfigyelheti a CORS erőforrások hozzáférési korlátozások az Azure Security Centerben.
CORS-megvalósítások ismerete segít a győződjön meg arról, hogy információt folyamatvezérlőket vannak megvalósítva.

- \[Előzetes verzió\]: Naplózási CORS erőforrás-hozzáférési korlátozások webalkalmazás számára

## <a name="ac-5-separation-of-duties"></a>AC-5 feladatkörök

Csak egy Azure-előfizetés tulajdonosa kellene nem engedélyezi a felügyeleti redundancia biztosítása érdekében. Ezzel szemben túl sok Azure-előfizetéssel rendelkező kellene növelheti a lehetséges egy biztonsági kockázatot jelentő tulajdonosok fiók keresztül megsértése. Ez a megoldás segít a megfelelő számú Azure-előfizetéssel rendelkező karbantartása két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózása az Azure-előfizetések tulajdonosainak száma definíciókat. Ez a megoldás is rendel négy Azure szabályzat-meghatározást, amelyek segítségével szabályozhatja a Windows virtuális gépeken a Rendszergazdák csoport tagsága. Előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyek kezelése segíthetnek a megfelelő feladatkörök megvalósításához.

- \[Előzetes verzió\]: Naplózni tulajdonosok egy előfizetéshez tartozó maximális száma
- \[Előzetes verzió\]: Naplózási előfizetés tulajdonosok minimális száma
- A naplózási, hogy a Rendszergazdák csoport a Windows virtuális gépek nem tartalmazza a megadott tagok
- A naplózási, hogy a Rendszergazdák csoport a Windows virtuális gépek tartalmazza-e a megadott tagok
- Naplózását, hogy a Rendszergazdák csoport a Windows virtuális gépek nem tartalmazza a meghatározott tagokat a Virtuálisgép-bővítmény telepítése
- Naplózását, hogy a Rendszergazdák csoport a Windows virtuális gépek tartalmazza a meghatározott tagokat a Virtuálisgép-bővítmény telepítése

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) legalacsonyabb jogosultsági |} Tekintse át a felhasználói jogosultságok

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC), amelyek segítségével kezelheti, ki férhet hozzá erőforrásokat az Azure-ban. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. Ez a megoldás hozzárendeli a hat [Azure Policy](../../../policy/overview.md) definíciók felülvizsgálatra élveznek fiókok naplózását. A fiók mutatók megtekintésével ellenőrizheti, legalacsonyabb jogosultsági vezérlők vannak megvalósítva.

- \[Előzetes verzió\]: Naplózni tulajdonosok egy előfizetéshez tartozó maximális száma
- \[Előzetes verzió\]: Naplózási előfizetés tulajdonosok minimális száma
- A naplózási, hogy a Rendszergazdák csoport a Windows virtuális gépek nem tartalmazza a megadott tagok
- A naplózási, hogy a Rendszergazdák csoport a Windows virtuális gépek tartalmazza-e a megadott tagok
- Naplózását, hogy a Rendszergazdák csoport a Windows virtuális gépek nem tartalmazza a meghatározott tagokat a Virtuálisgép-bővítmény telepítése
- Naplózását, hogy a Rendszergazdák csoport a Windows virtuális gépek tartalmazza a meghatározott tagokat a Virtuálisgép-bővítmény telepítése

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17-es (1) távelérési |} Automatizált Monitoring / szabályozása

Ez a megoldás segítségével monitorozását és ellenőrzését távelérési három hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók képernyőre, amely távoli hibakeresés az Azure App Service-alkalmazás ki van kapcsolva, és két szabályzatdefiníció, amely Linux naplózása virtuális gépek, amelyek lehetővé teszik a távoli kapcsolatok fiókok jelszó nélkül. Ez a megoldás is egy Azure szabályzat-definíció, amelynek segítségével figyelheti a tárfiókok korlátlan hozzáférést rendel. Ezek a mutatók figyelési ellenőrizheti, távelérési módszerek felel meg a biztonsági szabályzatának.

- \[Előzetes verzió\]: Távoli hibakeresés állapot naplózása egy függvényalkalmazáshoz
- \[Előzetes verzió\]: Távoli hibakeresés állapot naplózása a webalkalmazás számára
- \[Előzetes verzió\]: API-alkalmazások távoli hibakeresési állapot naplózása
- \[Előzetes verzió\]: A naplózási, hogy Linux rendszerű virtuális gépek nem fiókok jelszó nélkül a távoli kapcsolatok engedélyezése
- \[Előzetes verzió\]: Üzembe helyezése Virtuálisgép-bővítmény naplózását, hogy Linux rendszerű virtuális gépek nem fiókok jelszó nélkül a távoli kapcsolatok engedélyezése
- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Az auditálási rekordok AU-3 (2) tartalom |} A tervezett naplózási rekord tartalom központi felügyelet

Az Azure Monitor által gyűjtött naplóadatok engedélyezése központi konfigurálását és felügyeletét a Log Analytics-munkaterület tárolódik. Ez a megoldás segít győződjön meg arról, eseményeket naplóz hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat naplózása és az Azure-beli virtuális gépek a Log Analytics-ügynök telepítésének kényszerítéséhez.

- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés
- \[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése

## <a name="au-5-response-to-audit-processing-failures"></a>Feldolgozási hibák naplózása AU-5-válasz

Ez a megoldás hozzárendeli az öt [Azure Policy](../../../policy/overview.md) naplózási és naplózási konfigurációt esemény figyelése definíciókat. Ezek a konfigurációk figyelési adja meg azt, hogy egy naplózási rendszerhiba vagy a Virtual Network szolgáltatás hibás, és segítséget nyújt a korrekciós műveletek.

- \[Előzetes verzió\]: Az Azure Security Center nem naplózott SQL-kiszolgálók figyelése
- Diagnosztikai beállítás naplózása
- Felügyelt SQL-példányok naplózási adatok biztonságú nélkül
- Az SQL server szintű naplózási beállításainak naplózása
- SQL-kiszolgálók naplózási adatok biztonságú nélkül

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) naplózás áttekintése, elemzés és jelentéskészítés |} Központi áttekintése és elemzése

Az Azure Monitor által összegyűjtött naplózási adatokat tárolja egy Log Analytics-munkaterület engedélyezése központosított jelentéskészítés és elemzés. Ez a megoldás segít győződjön meg arról, eseményeket naplóz hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat naplózása és az Azure-beli virtuális gépek a Log Analytics-ügynök telepítésének kényszerítéséhez.

- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés
- \[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése

## <a name="au-12-audit-generation"></a>AU-12 naplózási generálása

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz 15 hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózási beállításai az Azure-erőforrás naplózása definíciókat. Ezek a szabályzatdefiníciók naplózása és kényszerítése az Azure-beli virtuális gépeken a Log Analytics-ügynök központi telepítése és konfigurálása a naplózási beállításokat minden olyan Azure-erőforrás esetében. Ezek a házirend-definíciók is naplózási nyújt betekintést az Azure-erőforrások belül végrehajtott műveleteket a diagnosztikai naplók konfigurálása. Emellett, naplózás és adatok biztonságú SQL-kiszolgálón vannak konfigurálva.

- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés
- \[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Az Azure Security Center nem naplózott SQL-kiszolgálók figyelése
- Hálózati biztonsági csoportok diagnosztikai beállításainak alkalmazása
- Diagnosztikai beállítás naplózása
- Felügyelt SQL-példányok naplózási adatok biztonságú nélkül
- Az SQL server szintű naplózási beállításainak naplózása
- SQL-kiszolgálók naplózási adatok biztonságú nélkül
- Adatok biztonsági speciális telepítése SQL Server-kiszolgálókon
- Az SQL Server-kiszolgálók üzembe helyezése a naplózás

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) legalább funkció |} Az eszközprogram végrehajtása megakadályozása

Adaptív Alkalmazásvezérlés az Azure Security Centerben egy intelligens, automatizált teljes körű engedélyezési megoldás, amely letiltása vagy meghatározott megakadályozza a virtuális gépeken futó is. Alkalmazás-vezérlő egy kényszerítési mód, amely nem engedélyezi a nem jóváhagyott alkalmazások futtatását is futtatható. Ez a megoldás egy Azure szabályzat-definíciót, amely segít a virtuális gépek figyelése céljából, az alkalmazás-engedélyezési listát ajánlott, de még konfigurációja nem rendeli hozzá.

- \[Előzetes verzió\]: A figyelő alkalmazások lehetséges engedélyezési listáinak az Azure Security Centerben

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) legalább funkció |} Engedélyezett szoftverek / engedélyezés

Adaptív Alkalmazásvezérlés az Azure Security Centerben egy intelligens, automatizált teljes körű engedélyezési megoldás, amely letiltása vagy meghatározott megakadályozza a virtuális gépeken futó is. Alkalmazásvezérlés segítségével hozhat létre a virtuális gépek jóváhagyott alkalmazások listája. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely segít a virtuális gépek figyelése céljából, az alkalmazás-engedélyezési listát ajánlott, de még nincs konfigurálva.

- \[Előzetes verzió\]: A figyelő alkalmazások lehetséges engedélyezési listáinak az Azure Security Centerben

## <a name="cm-11-user-installed-software"></a>CM-11 által telepített szoftverek

Adaptív Alkalmazásvezérlés az Azure Security Centerben egy intelligens, automatizált teljes körű engedélyezési megoldás, amely letiltása vagy meghatározott megakadályozza a virtuális gépeken futó is. Alkalmazás-vezérlő segítségével kényszerítése és a szoftverkorlátozó házirendek megfelelőségének figyeléséhez. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely segít a virtuális gépek figyelése céljából, az alkalmazás-engedélyezési listát ajánlott, de még nincs konfigurálva.

- \[Előzetes verzió\]: A figyelő alkalmazások lehetséges engedélyezési listáinak az Azure Security Centerben

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív Felderítésfeldolgozó hely

Az Azure Site Recovery replikálja az elsődleges helyről egy másodlagos helyre virtuális gépeken futó számítási feladatokat. Egy kimaradás során az elsődleges helyen, a számítási feladat átadja a feladatokat a másodlagos helyen. A tervezet hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely a virtuális gépeket naplózza a konfigurált katasztrófa utáni helyreállítás nélkül. Ez a kijelző figyelési ellenőrizheti, szükséges a készenléti beállítások vannak érvényben.

- Nem vész-helyreállítási konfigurált virtuális gépek naplózása

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) azonosítás és hitelesítés (szervezeti felhasználó) |} Kiemelt jogosultságú fiókok való hálózati hozzáférés

Ez a megoldás segítségével korlátozása, és irányítást emelt szintű hozzáférések hozzárendelése két [Azure Policy](../../../policy/overview.md) definíciók fiókok naplózása a tulajdonosa és/vagy írási engedélyekkel, amelyek nem rendelkeznek a többtényezős hitelesítés engedélyezve van. A multi-factor authentication teszi lehetővé, fiókok biztonságos akkor is, ha sérül a hitelesítési adatok egy részét. Figyelési fiók engedélyezve van a multi-factor authentication nélkül, a fiókok, amelyek nagyobb a valószínűsége, hogy sérülhet azonosíthatja.

- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) azonosítás és hitelesítés (szervezeti felhasználó) |} Hálózati hozzáférés nem kiemelt jogosultságú fiókok

Ez a megoldás segít korlátozása, valamint szabályozhatja a hozzáférést rendel egy [Azure Policy](../../../policy/overview.md) rendelkező fiókok naplózása definíciót olvasási engedélyeket, amelyek nem rendelkeznek a többtényezős hitelesítés engedélyezve van. A multi-factor authentication teszi lehetővé, fiókok biztonságos akkor is, ha sérül a hitelesítési adatok egy részét. Figyelési fiók engedélyezve van a multi-factor authentication nélkül, a fiókok, amelyek nagyobb a valószínűsége, hogy sérülhet azonosíthatja.

- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés olvasási jogosultsággal rendelkező fiókok

## <a name="ia-5-authenticator-management"></a>IA-5 Authenticator kezelése

Ez a megoldás hozzárendeli az öt [Azure Policy](../../../policy/overview.md) passwd fájlon beállítása Linux rendszerű virtuális gépek, amelyek fiókok jelszó nélküli távoli kapcsolatok engedélyezése és/vagy nem megfelelő engedélyekkel rendelkezik naplózási definíciókat. Ez a megoldás is rendel egy olyan szabályzatdefiníció, amely a Windows virtuális gépek titkosítási jelszó típusú konjugáció naplózza. A mutatók segítségével, győződjön meg arról, hogy a rendszer hitelesítők megfeleljenek a szervezeti azonosítási és hitelesítési házirend figyelése.

- \[Előzetes verzió\]: A naplózási, hogy a Linux rendszerű virtuális gépek nem rendelkeznek fiókok jelszó nélkül
- \[Előzetes verzió\]: Naplózását, hogy Linux rendszerű virtuális gépek jelszó nélküli fiókkal nem rendelkező Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Hogy Linux rendszerű virtuális gépek a passwd fájl engedélyekkel rendelkező 0644 naplózása
- \[Előzetes verzió\]: Naplózási, hogy a Windows virtuális gépek tárolása a jelszavak visszafejthető titkosítással
- \[Előzetes verzió\]: Naplózását, hogy Linux rendszerű virtuális gépek a passwd fájl engedélyekkel rendelkező 0644 Virtuálisgép-bővítmény telepítése

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) hitelesítő felügyeleti |} Jelszóalapú hitelesítés

Ez a megoldás segít kényszerítése az erős jelszónak 12 hozzárendelésével [Azure Policy](../../../policy/overview.md) minimális erősségét és egyéb jelszó-követelmények nem kényszerítenek Windows virtuális gépek naplózása definíciókat. A jelszó erőssége szabályzat megsértése a virtuális gépek figyelése segít annak biztosítására, az összes virtuális gép felhasználói fiók jelszavát a szervezet jelszóházirend javítási műveleteket.

- \[Előzetes verzió\]: Windows virtuális gépek újból nem használható a korábbi 24 jelszavak naplózása
- \[Előzetes verzió\]: Windows virtuális gép rendelkezik-e a jelszó maximális kora 70 nap naplózása
- \[Előzetes verzió\]: Naplózási, hogy Windows virtuális gépek rendelkeznek-e a jelszó minimális kora 1 nap
- \[Előzetes verzió\]: Naplózási, hogy Windows virtuális gépek rendelkeznek-e a jelszó összetettségi beállítás engedélyezve van
- \[Előzetes verzió\]: Windows virtuális gépek, hogy a jelszó minimális hossza 14 karakter korlátozása naplózása
- \[Előzetes verzió\]: Naplózási, hogy a Windows virtuális gépek tárolása a jelszavak visszafejthető titkosítással
- \[Előzetes verzió\]: Windows virtuális gépek újból nem használható a korábbi 24 jelszavak naplózását Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Virtuálisgép-bővítmény naplózását, rendelkezik-e a jelszó maximális kora 70 nap Windows virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Virtuálisgép-bővítmény naplózását, rendelkezik-e a jelszó minimális kora 1 nap Windows virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Naplózását, hogy Windows virtuális gépek rendelkeznek-e a jelszó összetettségi beállítás engedélyezve van a Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Windows virtuális gépek, hogy a jelszó minimális hossza 14 karakter korlátozása naplózása Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Naplózását, hogy a Windows virtuális gépek tárolása a jelszavak visszafejthető titkosítással Virtuálisgép-bővítmény telepítése

## <a name="ra-5-vulnerability-scanning"></a>RA-5 biztonsági rés vizsgálata

Ez a megoldás segít információt rendszer biztonsági rések kezelése négy hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók figyelő operációs rendszer biztonsági réseivel, a SQL biztonsági réseket és a virtuális gép biztonsági résekről, az Azure-ban A Security Center. Az Azure Security Center jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy üzembe helyezett Azure-erőforrások biztonsági állapotának valós idejű betekintést biztosít. Ez a megoldás három szabályzatdefiníciókat, naplózási és speciális adatok biztonság kényszerítése SQL Server-kiszolgálókon is rendel. Speciális biztonsági tartalmazza a biztonságirés-értékelési és a komplex veszélyforrások elleni védelmi képességek segítségével megismerheti a telepített erőforrások biztonsági réseinek.

- Felügyelt SQL-példányok naplózási adatok biztonságú nélkül
- SQL-kiszolgálók naplózási adatok biztonságú nélkül
- Adatok biztonsági speciális telepítése SQL Server-kiszolgálókon
- \[Előzetes verzió\]: Naplózási operációs rendszerek sebezhetőségeinek a virtuálisgép-méretezési csoportok az Azure Security Centerben
- \[Előzetes verzió\]: Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése
- \[Előzetes verzió\]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben
- \[Előzetes verzió\]: A figyelő virtuális gépek biztonsági réseinek az Azure Security Centerben

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás elleni

Az Azure elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás standard csomag további szolgáltatásai és funkciói az alapszintű szolgáltatásszinten készít. Ezek további szolgáltatásai többek között az Azure Monitor integrációja, és tekintse át a támadás utáni kockázatcsökkentési jelentések lehetővé teszi. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely naplózza, ha a standard szintű DDoS-csomag engedélyezve van. Képesség a szolgáltatási szintek közti különbség segítségével válassza ki a cím szolgáltatásmegtagadási szolgáltatás erősebb védelmét az Azure-környezet a legjobb megoldás.

- \[Előzetes verzió\]: Naplózási standard szintű DDoS elleni védelem engedélyezve van a virtuális hálózat

## <a name="sc-7-boundary-protection"></a>SC-7 határ védelmére

Ez a megoldás segítségével kezelheti és szabályozására, a rendszer határ hozzárendelése egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a megengedő hálózati biztonsági csoportok. Előfordulhat, hogy nem kívánt hálózati hozzáférés engedélyezése, amelyek túl megengedő szabályok, és át kell tekinteni. Ez a megoldás is rendel egy olyan szabályzatdefiníció, amely figyeli a hálózati biztonsági csoport korlátozási javaslatok az Azure Security Centerben. Az Azure Security Center elemzi az internetkapcsolattal rendelkező virtuális gépek adatforgalmi mintái, és nyújt a hálózati biztonsági csoport a szabály a potenciális támadási felület csökkentése.
Ez a megoldás emellett is figyelheti a védelem nélküli végpontok, alkalmazások és storage-fiókok három szabályzatdefiníciók rendeli hozzá. Végpontok és a egy tűzfal és a korlátlan hozzáférést a storage-fiókok által nem védett alkalmazások lehetővé tehetik a információs rendszerben található információkhoz való nem szándékos hozzáférés.

- \[Előzetes verzió\]: Internetkapcsolattal rendelkező virtuális gépek figyelése céljából a hálózati biztonsági csoport forgalom korlátozására vonatkozó javaslatok
- \[Előzetes verzió\]: Az Azure Security Center megengedő hálózati hozzáférés figyelése
- \[Előzetes verzió\]: Az Azure Security Center nem védett hálózati végpontok figyelése
- \[Előzetes verzió\]: Az Azure Security Center nem védett webalkalmazás figyelése
- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) határ Protection |} Hozzáférési pontok

– Igény (szerinti JIT) virtuális gép hozzáférés zárolások az Azure virtual machines, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalom le. Igény szerinti hozzáférés a virtuális gépek segítségével az erőforrások az Azure-ban történő külső kapcsolódásokat számának korlátozásához. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely segít a virtuális gépek figyelése céljából, amelyek igény szerinti elérése is támogatják, de még nem lett konfigurálva.

- \[Előzetes verzió\]: Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) határ Protection |} Külső távközlési szolgáltatások

– Igény (szerinti JIT) virtuális gép hozzáférés zárolások az Azure virtual machines, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalom le. Igény szerinti hozzáférés a virtuális gépek segítségével kezelhetők a forgalom folyamat szabályzatában kivételeket hozhat a hozzáférési kérelem és jóváhagyási folyamatok megkönnyítésével. Ez a megoldás hozzárendeli egy [Azure Policy](../../../policy/overview.md) definíciót, amely segít a virtuális gépek figyelése céljából, amelyek igény szerinti elérése is támogatják, de még nem lett konfigurálva.

- \[Előzetes verzió\]: Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>Adatok inaktív védelme SC-28 (1) |} Titkosítási védelme

Ez a megoldás segít az adatok inaktív védelme 9 hozzárendelésével cryptograph vezérlők használatára vonatkozó szabályzat kényszerítése [Azure Policy](../../../policy/overview.md) adott cryptograph vezérlők és naplózási definíciókat a gyenge titkosítási felhasználása beállítások. Ismertetése, ahol az Azure-erőforrások rendelkezhetnek titkosítási konfiguráció nem optimális segíthet az információk biztonsági szabályzatának megfelelően konfigurált erőforrások biztosítása érdekében javítási műveleteket. Pontosabban a szabályzatdefiníciók, ez a megoldás által hozzárendelt titkosítás megkövetelése a data lake storage-fiókok; Transzparens adattitkosítás; SQL-adatbázis szükséges és az SQL-adatbázisok, virtuális gépek lemezeinek és automation-fiók változók hiányzó titkosítást.

- \[Előzetes verzió\]: Az Azure Security Centerben a titkosítatlan SQL-adatbázisok figyelése
- \[Előzetes verzió\]: Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása
- Biztonságos átvitelt a tárfiókok naplózása
- Felügyelt SQL-példányok naplózási adatok biztonságú nélkül
- SQL-kiszolgálók naplózási adatok biztonságú nélkül
- Transzparens adattitkosítás állapotának naplózása
- Adatok biztonsági speciális telepítése SQL Server-kiszolgálókon
- SQL-Adatbázisok transzparens adattitkosításának üzembe helyezése
- Data Lake Store-fiókok titkosításának kényszerítése

## <a name="si-2-flaw-remediation"></a>Szervizelési SI-2 hibája

Ez a megoldás segít az adatokat a rendszer hiba kezelésében hat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek a hiányzó Rendszerfrissítések, operációs rendszer biztonsági rései, az SQL biztonsági réseket és a virtuális gép figyelése az Azure Security Center biztonsági rései. Az Azure Security Center jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy üzembe helyezett Azure-erőforrások biztonsági állapotának valós idejű betekintést biztosít. Ez a megoldás is rendel egy olyan szabályzatdefiníció, amely biztosítja a virtuálisgép-méretezési csoportokhoz az operációs rendszer automatikus verziófrissítése.

- \[Előzetes verzió\]: A virtual machine scale sets az Azure Security Center összes hiányzó rendszerfrissítések naplózása
- \[Előzetes verzió\]: Naplózási operációs rendszerek sebezhetőségeinek a virtuálisgép-méretezési csoportok az Azure Security Centerben
- \[Előzetes verzió\]: Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése
- \[Előzetes verzió\]: Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése
- \[Előzetes verzió\]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben
- \[Előzetes verzió\]: A figyelő virtuális gépek biztonsági réseinek az Azure Security Centerben
- Operációs rendszer automatikus verziófrissítése, az ellenőrzés a VMSS kényszerítése

## <a name="si-3-malicious-code-protection"></a>Védelem SI-3 kártevő kódja

Ez a megoldás segítségével kezelheti az endpoint protection kártevő kódja védelmet, beleértve három hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat a hiányzó virtuális gépeken az Azure Security Centerben az endpoint protection figyelése és a Microsoft kártevőirtó megoldást a Windows virtuális gépek kényszerítése.

- \[Előzetes verzió\]: A virtual machine scale sets az Azure Security Centerben a végpontvédelmi megoldás naplózása
- \[Előzetes verzió\]: Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása
- Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) kártevő kódja Protection |} Központi kezelés

Ez a megoldás segítségével kezelheti az endpoint protection kártevő kódja védelmet, beleértve két hozzárendelésével [Azure Policy](../../../policy/overview.md) a hiányzó virtuális gépeken az Azure Security Centerben az endpoint protection figyelése definíciókat. Az Azure Security Center Itt a központi felügyeletet és jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy a valós idejű betekintést üzembe helyezett Azure-erőforrások biztonsági állapotát.

- \[Előzetes verzió\]: A virtual machine scale sets az Azure Security Centerben a végpontvédelmi megoldás naplózása
- \[Előzetes verzió\]: Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása

## <a name="si-4-information-system-monitoring"></a>SI-4 információ rendszerek figyelése

Ez a megoldás segít naplózása és végrehajtása során a naplózás és adatbiztonság között az Azure-erőforrások monitorozása a rendszer. Pontosabban a hozzárendelt szabályzatokat naplózása, és a Log Analytics-ügynök telepítését, és az SQL adatbázisok, tárfiókok és hálózati erőforrások fokozott biztonsági beállítások. Ezek a képességek segítségével észlelheti a rendellenes viselkedés és támadások mutatók, így a megfelelő műveleteket végezheti el.

- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés
- \[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése
- \[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése
- Felügyelt SQL-példányok naplózási adatok biztonságú nélkül
- SQL-kiszolgálók naplózási adatok biztonságú nélkül
- Adatok biztonsági speciális telepítése SQL Server-kiszolgálókon
- Komplex veszélyforrások elleni védelem a Storage-fiókok üzembe helyezése
- Az SQL Server-kiszolgálók üzembe helyezése a naplózás
- Network watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Az SQL Server-kiszolgálók üzembe helyezése a Fenyegetésészlelés

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) információ rendszerek figyelése |} Forgalom elemzése / fedett kiszűrése

Komplex veszélyforrások elleni védelem az Azure Storage észleli a szokatlan és vélhetően kárt okozó kísérleteket elérni vagy kiaknázni a storage-fiókok. Protection-riasztások tartalmazzák a rendellenes hozzáférési mintákat, a rendellenes kivonatok/feltöltéseket és a gyanús tárolási tevékenység. Ezek a mutatók segítségével fedett kiszűrése információk észlelésére.

- Komplex veszélyforrások elleni védelem a Storage-fiókok üzembe helyezése

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a NIST SP 800-53 előírásain R4 tervezet vezérlő leképezése, látogasson el a további információt talál a tervezet-Ez a minta üzembe helyezése a következő cikkeket:

> [!div class="nextstepaction"]
> [800-53 előírásain NIST SP R4 tervezet - áttekintés](./index.md)
> [NIST SP 800-53 előírásain R4 tervezetet – a telepítés lépéseit](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
