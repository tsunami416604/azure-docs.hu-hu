---
title: Minta – Egyesült Királyság hivatalos tervezet - vezérlő leképezés
description: Az Egyesült Királyság hivatalos tervezet minta vezérlő leképezését.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276976"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>Az Egyesült Királyság hivatalos tervezet minta vezérlő leképezés

A következő cikk részletezi, hogyan képez le az Egyesült Királyság hivatalos tervezet minta az Egyesült Királyság hivatalos vezérlők.
A vezérlőkkel kapcsolatos további információkért lásd: [UK hivatalos](https://www.gov.uk/government/publications/government-security-classifications).

A következő hozzárendeléseket a rendszer a **UK hivatalos** szabályozza. A jobb oldalon a navigációs segítségével közvetlenül egy adott vezérlőt leképezés ugorhat. Számos, a csatlakoztatott vezérlők implementálása egy [Azure Policy](../../../policy/overview.md) kezdeményezésére. Tekintse át a teljes kezdeményezés, nyissa meg a **házirend** az Azure Portalon, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a  **[Előzetes verzió] UK hivatalos naplózási szabályozza és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése** beépített szabályzat-kezdeményezéshez.

## <a name="1-data-in-transit-protection"></a>1 adatok védelmére átvitel közben

A tervezet segítségével győződjön meg, hogy információ átvitele az Azure-szolgáltatásokkal biztonságos két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózását, tárfiókok és a Redis Cache biztonságos kapcsolatok definícióit.

- A Redis gyorsítótárhoz kizárólag biztonságos kapcsolatot engedélyezni kell
- Engedélyezni kell a biztonságos átvitelt a storage-fiókok

## <a name="23-data-at-rest-protection"></a>2.3 rest védelmi adatok

Ez a megoldás segít cryptograph vezérlők használatakor a szabályzat kényszerítéséhez 11 hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyeket adott cryptograph vezérlők és naplózási gyenge kriptográfiai beállítások használatát.
Ismertetése, ahol az Azure-erőforrások rendelkezhetnek titkosítási konfiguráció nem optimális segíthet az információk biztonsági szabályzatának megfelelően konfigurált erőforrások biztosítása érdekében javítási műveleteket. Pontosabban a házirendek, ez a megoldás által hozzárendelt titkosítás megkövetelése a data lake storage-fiókok; Transzparens adattitkosítás; SQL-adatbázis szükséges a storage-fiókok, SQL Database-adatbázisok, virtuális gépek lemezeinek és automation-fiók változók; hiányzó titkosítást naplózása storage-fiókok és a Redis Cache esetében; a nem biztonságos kapcsolatok naplózása gyenge virtuális gép titkosított; naplózása és a Service Fabric a titkosítatlan kommunikációnak.

- Az Azure Security Centerben a titkosítatlan SQL-adatbázisok figyelése
- Lemeztitkosítás alkalmazni kell a virtuális gépek
- Automation-fiók változókat a rendszer titkosítsa
- Engedélyezni kell a biztonságos átvitelt a storage-fiókok
- Engedélyezni kell a biztonságos átvitelt a storage-fiókok
- Service Fabric-fürtök kell rendelkeznie a ClusterProtectionLevel tulajdonság EncryptAndSign beállítása
- Engedélyezni kell az SQL Database-adatbázisok transzparens adattitkosítás
- SQL-Adatbázisok transzparens adattitkosításának üzembe helyezése
- Data Lake Store-fiókok titkosításának kötelezővé tétele
- Engedélyezett helyek (lett rögzített kódolt "Egyesült Királyság déli RÉGIÓJA" és "Egyesült Királyság nyugati RÉGIÓJA")
- Engedélyezett helyek az erőforráscsoportok (lett rögzített kódolt "Egyesült Királyság déli RÉGIÓJA" és "Egyesült Királyság nyugati RÉGIÓJA")

## <a name="52-vulnerability-management"></a>5.2 biztonságirés-kezelő

Ez a megoldás segít információt rendszer biztonsági rések kezelése öt hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek a hiányzó Rendszerfrissítések, az operációs rendszer biztonsági résekről, az SQL hiányzó endpoint protection figyelése biztonsági réseket, és a virtuális gép biztonsági rések. Ezen elemzési valós idejű ismertetik a telepített erőforrások biztonsági állapotát, és segíthet a szervizelési műveletek rangsorolására.

- Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása
- Rendszerfrissítések, telepítenie kell a gépek
- Biztonsági beállítások a gépek biztonsági réseinek kell kijavítható.
- Az SQL-adatbázisok biztonsági rések kell kijavítható.
- Biztonsági rések kell kijavítható biztonságirés-értékelési megoldás által

## <a name="53-protective-monitoring"></a>5.3 védelmi figyelése

Ez a megoldás védheti meg információs rendszerek eszközeit teheti egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a tárfiókok korlátlan. Ez a megoldás is egy Azure szabályzat-definíció, amely figyeli az engedélyezési lista tevékenység rendeli hozzá.

- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása
- Az adaptív Alkalmazásvezérlők a virtuális gépek engedélyezve kell lennie
- Az SQL Server-kiszolgálók üzembe helyezése a Fenyegetésészlelés
- A Windows Server alapértelmezett Microsoft IaaS kártevőirtó bővítmény telepítése

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 biztonságos felhasználókezelés / 10 identitás és hitelesítés

Azure szerepköralapú hozzáférés-vezérlés (RBAC segítségével kezelhetők, ki férhet hozzá az erőforrásokat az Azure-ban) valósítja meg. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. Ez a megoldás segít korlátozása, valamint szabályozhatja a privilegizált hozzáférési jogosultsága a hat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók külső fiókok naplózása a tulajdonosa és/vagy olvasási/írási engedélyek és fiókok tulajdonosával, olvassa el és/vagy írása engedélyek, amelyek nem rendelkeznek a többtényezős hitelesítés engedélyezve van.

- MFA engedélyezni kell a fiókok az előfizetésben tulajdonosi engedélyekkel rendelkező
- Az MFA engedélyezett fiókok, az előfizetés írási jogosultsággal rendelkező kell lennie.
- MFA engedélyezni kell a fiókok az előfizetés olvasási jogosultsággal rendelkező
- Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- Előfizetésben írási engedélyekkel rendelkező külső fiókok naplózása
- Egy adott előfizetés olvasási jogosultsággal rendelkező külső fiókok naplózása

Ez a megoldás az Azure Active Directory-hitelesítés használatát az SQL Server-kiszolgálók és a Service Fabric naplózása két Azure szabályzatdefiníciók rendeli hozzá. Az Azure Active Directory authentication lehetővé teszi, engedélykezelés egyszerűsített és központosított Identitáskezelés adatbázis-felhasználók és más Microsoft-szolgáltatások.

- Az Azure Active Directory-rendszergazda üzembe helyezi az SQL Server-kiszolgálók
- Service Fabric-fürtök az Azure Active Directory csak használja az ügyfél-hitelesítéshez

Ez a megoldás is rendel öt Azure szabályzat-meghatározást, fiókok, ellenőrzésre, beleértve a értékcsökkentett és a külső fiókok élveznek naplózását. Ha szükséges, fiókok bejelentkezés blokkolva (vagy eltávolítása), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogosultságok. Ez a megoldás két Azure szabályzatdefiníciók érdemes megfontolni, eltávolítás értékcsökkentett naplózási-fiókhoz rendeli hozzá.

- Elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a megoldás is rendel egy Azure szabályzat-definíció, amely naplózza a Linux rendszerű virtuális gép jelszó fájlengedélyek riasztást küldjön, ha azok nem megfelelően van-e állítva. Ez a kialakítás lehetővé teszi annak hitelesítők nem sérült a biztonsága érdekében javítási intézkedéseket.

- [Előzetes verzió]: Audit Linux VM /etc/passwd file permissions are set to 0644

Ez a megoldás segít kényszerítése erős jelszónak 10 Azure szabályzat-meghatározást, amelyek Windows virtuális gépek, amelyek nem kényszerítenek minimális erősségét és más jelszókövetelményeinek naplózása hozzárendelésével. A jelszó erőssége szabályzat megsértése a virtuális gépek figyelése segítséget nyújt az összes virtuális gép felhasználói fiók jelszavát felelnek meg a házirend biztosítása érdekében javítási műveleteket.

- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Előzetes verzió]: Audit Windows VMs that do not have the password complexity setting enabled
- [Előzetes verzió]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Előzetes verzió]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Előzetes verzió]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Előzetes verzió]: Audit Windows VMs that allow re-use of the previous 24 passwords

Ez a tervezetet is Azure-erőforrásokhoz való hozzáférés hét Azure szabályzatdefiníciók hozzárendelésével szabályozásában nyújt segítséget. Ezek a szabályzatok, erőforrás-típus használatának naplózása és konfigurációkat lehet megengedőbb elérhető erőforrások. A feljogosított felhasználók ezek házirendeket is meg javítási műveleteket az Azure-erőforrásokhoz való hozzáférés biztosítása súgó korlátozódik, nem megfelelő erőforrások ismertetése.

- [Előzetes verzió]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Előzetes verzió]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Előzetes verzió]: Audit Linux VMs that have accounts without passwords
- [Előzetes verzió]: Audit Linux VMs that allow remote connections from accounts without passwords
- Storage-fiókokat kell áttelepíteni az új Azure Resource Manager-erőforrások
- Virtuális gépek kell áttelepíteni az új Azure Resource Manager-erőforrások
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="11-external-interface-protection"></a>11 külső felület védelme

Nem több mint 25 házirendeket használ a megfelelő biztonságos felhasználói felügyeleti, ez a megoldás segít szolgáltatás jogosulatlan hozzáférés elleni védelmének hozzárendelésével egy [Azure Policy](../../../policy/overview.md) definíciót, amely a figyelők unrestricted Storage-fiókok. Korlátlan hozzáférést a Storage-fiókok a információs rendszerben található információk jogosulatlan hozzáférést biztosíthat. Ez a megoldás is rendel egy szabályzatot, amely lehetővé teszi az adaptív Alkalmazásvezérlési virtuális gépeken.

- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása
- Az adaptív Alkalmazásvezérlők a virtuális gépek engedélyezve kell lennie

## <a name="12-secure-service-administration"></a>12 biztonságos Service felügyelete

Azure szerepköralapú hozzáférés-vezérlés (RBAC segítségével kezelhetők, ki férhet hozzá az erőforrásokat az Azure-ban) valósítja meg. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. Ez a megoldás segít korlátozása, valamint szabályozhatja a privilegizált hozzáférési jogosultsága az öt hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók külső fiókok naplózás a tulajdonos és/vagy írási engedélyek és a fiókok a tulajdonosa és/vagy írási engedélyekkel nem rendelkezik, amely a multi-factor authentication szolgáltatás engedélyezve van.

. Egy felhőalapú szolgáltatás felügyeletére szolgáló rendszereket fogja rendelkezik magas szintű jogosultságokkal rendelkeznek a szolgáltatáshoz való hozzáférést. A biztonsági sérülése jelentős hatást, beleértve a biztonsági vezérlőket megkerülése és nagy mennyiségű adatot módosítására vagy ellopják, hogy rendelkezik. A kezelése, a üzemeltetési szolgáltatás-rendszergazdák a szolgáltató által használt módszerek úgy kell megtervezni, amelyek veszélyeztethetik a szolgáltatás biztonságának kihasználása kockázatának csökkentése érdekében. Ha ezt az elvet nincs implementálva, a támadó megkerülése biztonsági vezérlők és nagy mennyiségű adatot módosítására vagy ellopják, hogy előfordulhat, hogy rendelkezik.

- MFA engedélyezni kell a fiókok az előfizetésben tulajdonosi engedélyekkel rendelkező
- Az MFA engedélyezett fiókok, az előfizetés írási jogosultsággal rendelkező kell lennie.
- Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a megoldás az Azure Active Directory-hitelesítés használatát az SQL Server-kiszolgálók és a Service Fabric naplózása két Azure szabályzatdefiníciók rendeli hozzá. Az Azure Active Directory authentication lehetővé teszi, engedélykezelés egyszerűsített és központosított Identitáskezelés adatbázis-felhasználók és más Microsoft-szolgáltatások.

- Az Azure Active Directory-rendszergazda üzembe helyezi az SQL Server-kiszolgálók
- Service Fabric-fürtök az Azure Active Directory csak használja az ügyfél-hitelesítéshez

Ez a megoldás is rendel négy Azure szabályzat-meghatározást, fiókok, ellenőrzésre, beleértve a értékcsökkentett és emelt szintű engedélyekkel rendelkező külső fiókok élveznek naplózását. Ha szükséges, fiókok bejelentkezés blokkolva (vagy eltávolítása), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogosultságok. Ez a megoldás két Azure szabályzatdefiníciók érdemes megfontolni, eltávolítás értékcsökkentett naplózási-fiókhoz rendeli hozzá.

- Elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a megoldás is rendel egy Azure szabályzat-definíció, amely naplózza a Linux rendszerű virtuális gép jelszó fájlengedélyek riasztást küldjön, ha azok nem megfelelően van-e állítva. Ez a kialakítás lehetővé teszi annak hitelesítők nem sérült a biztonsága érdekében javítási intézkedéseket.

- [Előzetes verzió]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>13 naplózási adatait a felhasználók számára

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz 6 hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózási beállításai az Azure-erőforrás naplózása definíciókat. Szabályzat akkor is naplózza, ha a virtuális gépek nem naplókat küld a megadott log analytics-munkaterület.

- Az Azure Security Center nem naplózott SQL-kiszolgálók figyelése
- Diagnosztikai beállítás naplózása
- Az SQL server szintű naplózási beállításainak naplózása
- [Előzetes verzió]: Deploy Log Analytics Agent for Linux VMs
- [Előzetes verzió]: Deploy Log Analytics Agent for Windows VMs
- Network watcher üzembe helyezése virtuális hálózatok létrehozásakor

## <a name="next-steps"></a>További lépések

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
