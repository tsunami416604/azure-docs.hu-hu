---
title: Minta - ISO 27001, a megosztott szolgáltatások tervezet - vezérlő leképezés
description: Az ISO 27001, a megosztott szolgáltatások tervezet minta és az Azure Policy RBAC vezérlő leképezését.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 5c225d76e1822d42500713578a7159eed0699a66
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225966"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001, a megosztott szolgáltatások tervezet minta vezérlő leképezés

A következő cikk részletezi, hogyan az Azure tervezetek ISO 27001 megosztott szolgáltatások tervezetet a minta az ISO 27001 vezérlők vannak leképezve. A vezérlőkkel kapcsolatos további információkért lásd: [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

A következő hozzárendeléseket a rendszer a **ISO 27001:2013** szabályozza. A jobb oldalon a navigációs segítségével közvetlenül egy adott vezérlőt leképezés ugorhat. Számos, a csatlakoztatott vezérlők implementálása egy [Azure Policy](../../../policy/overview.md) kezdeményezésére. Tekintse át a teljes kezdeményezés, nyissa meg a **házirend** az Azure Portalon, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a  **\[előzetes\] naplózási ISO 27001:2013 vezérli, és a naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése** beépített szabályzat-kezdeményezéshez.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 szétválasztására

Csak egy Azure-előfizetés tulajdonosa kellene nem engedélyezi a felügyeleti redundancia biztosítása érdekében. Ezzel szemben túl sok Azure-előfizetéssel rendelkező kellene növelheti a lehetséges egy biztonsági kockázatot jelentő tulajdonosok fiók keresztül megsértése. Ez a megoldás segít a megfelelő számú Azure-előfizetéssel rendelkező karbantartása két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózása az Azure-előfizetések tulajdonosainak száma definíciókat. Előfizetés tulajdonosi engedélyekkel kezelése segíthetnek a megfelelő feladatkörök megvalósításához.

- \[Előzetes verzió\]: Naplózni tulajdonosok egy előfizetéshez tartozó minimális száma
- \[Előzetes verzió\]: Naplózni tulajdonosok egy előfizetéshez tartozó maximális száma

## <a name="a821-classification-of-information"></a>Információk A.8.2.1 besorolása

Az Azure [SQL-sebezhetőségi felmérés szolgáltatás](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) segíthet az adatbázisokban tárolt bizalmas adatok felderítéséhez és az adatok besorolására javaslatokat tartalmaz. A tervezet hozzárendel egy [Azure Policy](../../../policy/overview.md) , hogy megtörténjen-e az SQL biztonságirés-értékelési vizsgálat során azonosított biztonsági rések naplózása definíciót.

- \[Előzetes verzió\]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben

## <a name="a912-access-to-networks-and-network-services"></a>A hálózatok és hálózati szolgáltatások A.9.1.2 hozzáférés

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC) az Azure-erőforrásokhoz való hozzáférés kezeléséhez. Ez a megoldás segít az Azure-erőforrásokhoz való hozzáférés szabályozása hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat. Ezek a szabályzatok, erőforrás-típus használatának naplózása és konfigurációkat lehet megengedőbb elérhető erőforrások.
A feljogosított felhasználók ezek házirendeket is meg javítási műveleteket az Azure-erőforrásokhoz való hozzáférés biztosítása súgó korlátozódik, nem megfelelő erőforrások ismertetése.

- \[Előzetes verzió\]: Linux rendszerű virtuális gép fiókok jelszavakat nem rendelkező naplózási Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Linux rendszerű virtuális gép nem jelszóval rendelkező fiókokat a távoli kapcsolatok engedélyezése a naplózandó Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Linux rendszerű virtuális gép fiókok nem jelszóval rendelkező naplózása
- \[Előzetes verzió\]: Naplózási Linux rendszerű virtuális gép nem jelszóval rendelkező fiókokat a távoli kapcsolatok engedélyezése
- A klasszikus tárfiókok használatának naplózása
- Klasszikus virtuális gépek naplózása
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="a923-management-of-privileged-access-rights"></a>Az emelt szintű A.9.2.3 felügyeleti hozzáférési jogosultságok

Ez a megoldás segít korlátozása, valamint szabályozhatja a privilegizált hozzáférési jogosultsága négy hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók külső fiókok naplózás a tulajdonos és/vagy írási engedélyek és a fiókok a tulajdonosa és/vagy írási engedélyekkel többtényezős hitelesítés engedélyezve van, amely nem rendelkezik. Azure szerepköralapú hozzáférés-vezérlés (RBAC) való hozzáférés kezeléséhez az Azure-erőforrások valósítja meg. Ez a megoldás is rendel naplózása az Azure Active Directory-hitelesítés az SQL Server-kiszolgálók és a Service Fabric három Azure szabályzat-definíciókat. Az Azure Active Directory authentication lehetővé teszi, engedélykezelés egyszerűsített és központosított Identitáskezelés adatbázis-felhasználók és más Microsoft-szolgáltatások. Ez a megoldás is rendel egy Azure szabályzat-definíció egyéni RBAC-szabályok naplózni kell. Ismertetése, amelyeknél megvalósítása az egyéni RBAC-szabályok segítségével van szüksége, és megfelelő végrehajtásához, győződjön meg arról, egyéni RBAC-szabályok hibalehetőségeket rejt magában.

- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- \[Előzetes verzió\]: Előfizetésben írási engedélyekkel rendelkező külső fiókok naplózása
- Az Azure Active Directory-rendszergazda, az SQL server üzembe helyezése naplózása
- Az ügyfél-hitelesítéshez, a Service Fabric az Azure Active Directory használatának naplózása
- Az egyéni RBAC-szabályok használatának naplózása

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A felhasználók titkos hitelesítési adatok A.9.2.4 kezelése

Ez a megoldás három rendeli [Azure Policy](../../../policy/overview.md) definíciók naplózása, amelyek nem rendelkeznek a többtényezős hitelesítést engedélyező fiókokat. A multi-factor authentication teszi lehetővé, fiókok biztonságos akkor is, ha sérül a hitelesítési adatok egy részét. Figyelési fiók engedélyezve van a multi-factor authentication nélkül, a fiókok, amelyek nagyobb a valószínűsége, hogy sérülhet azonosíthatja. Ez a megoldás is rendel két Azure szabályzatdefiníció, amely Linux rendszerű virtuális gépek naplózása jelszó fájlengedélyek riasztást küldjön, ha azok nem megfelelően van-e állítva. Ez a beállítás lehetővé teszi annak hitelesítők nem sérült a biztonsága érdekében javítási intézkedéseket.

- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés olvasási jogosultsággal rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok
- \[Előzetes verzió\]: Linux rendszerű virtuális gép passwd fájlengedélyek naplózása Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Naplózási Linux rendszerű virtuális gép passwd fájlengedélyek 0644 beállítása

## <a name="a925-review-of-user-access-rights"></a>Felhasználói hozzáférési jogosultságokat A.9.2.5 áttekintése

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC), amelyek segítségével kezelheti, ki férhet hozzá erőforrásokat az Azure-ban. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. A tervezet rendeli hozzá, négy [Azure Policy](../../../policy/overview.md) fiókokat, ellenőrzésre, beleértve a értékcsökkentett és emelt szintű engedélyekkel rendelkező külső fiókok élveznek naplózása érdekében.

- \[Előzetes verzió\]: Elavult fiókokat egy előfizetésben naplózása
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező külső fiókok naplózása
- \[Előzetes verzió\]: Előfizetésben írási engedélyekkel rendelkező külső fiókok naplózása

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 eltávolítása és a hozzáférési jogosultságok beállítása

Az Azure valósítja meg [szerepköralapú hozzáférés-vezérlés](../../../../role-based-access-control/overview.md) (RBAC), amelyek segítségével kezelheti, ki férhet hozzá erőforrásokat az Azure-ban. Használatával [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) és RBAC, felhasználói szerepkörök szervezeti változásainak frissítheti. Ha szükséges, fiókok bejelentkezés blokkolva (vagy eltávolítása), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogosultságok. Ez a megoldás két rendeli [Azure Policy](../../../policy/overview.md) definíciók naplózása értékcsökkentett fiók eltávolítása kell figyelembe venni.

- \[Előzetes verzió\]: Elavult fiókokat egy előfizetésben naplózása
- \[Előzetes verzió\]: Egy adott előfizetés tulajdonosi engedélyekkel rendelkező elavult fiókok naplózása

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 biztonságos bejelentkezési eljárások

Ez a megoldás a naplózása, amelyek nem rendelkeznek a többtényezős hitelesítést engedélyező fiókokat három Azure szabályzatdefiníciók rendeli hozzá. Az Azure multi-factor Authentication azzal, hogy egy második hitelesítési mód további biztonságot nyújt, és a szolgáltatás szigorú hitelesítést végez. Figyelési fiók engedélyezve van a multi-factor authentication nélkül, a fiókok, amelyek nagyobb a valószínűsége, hogy sérülhet azonosíthatja.

- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés tulajdonosi engedélyekkel rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés olvasási jogosultsággal rendelkező fiókok
- \[Előzetes verzió\]: Naplózási, akik nem többtényezős hitelesítés engedélyezve van egy adott előfizetés írási jogosultsággal rendelkező fiókok

## <a name="a943-password-management-system"></a>A.9.4.3 Ügyféljelszó-kezelő rendszert

Ez a megoldás segít kényszerítése az erős jelszónak 10 hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat, amelyeket nem minimális erősségét és más jelszókövetelményeinek Windows virtuális gépek naplózása. A jelszó erőssége szabályzat megsértése a virtuális gépek figyelése segítséget nyújt az összes virtuális gép felhasználói fiók jelszavát felelnek meg a házirend biztosítása érdekében javítási műveleteket.

- \[Előzetes verzió\]: Naplózási Virtuálisgép-bővítmény telepítése Windows virtuális gép előírja a jelszó-összetettségi követelményeknek
- \[Előzetes verzió\]: Windows virtuális gép jelszó maximális kora 70 nap naplózási Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Windows virtuális Gépet a jelszó minimális kora 1 nap naplózási Virtuálisgép-bővítmény telepítése
- \[Előzetes verzió\]: Virtuális gép üzembe helyezése Windows virtuális gép jelszavak naplózását bővítmény legalább 14 karakterből kell állnia.
- \[Előzetes verzió\]: Naplózási Virtuálisgép-bővítmény telepítése Windows virtuális gép nem engedélyezheti a korábbi 24 jelszavak
- \[Előzetes verzió\]: Naplózási Windows virtuális gép előírja a jelszó-összetettségi követelményeknek
- \[Előzetes verzió\]: Windows virtuális gép jelszó maximális kora 70 nap naplózása
- \[Előzetes verzió\]: Windows virtuális Gépet a jelszó minimális kora 1 nap naplózása
- \[Előzetes verzió\]: Naplózási Windows virtuális gép jelszavak legalább 14 karakterből kell állnia.
- \[Előzetes verzió\]: Naplózási Windows virtuális gép nem engedélyezheti a korábbi 24 jelszavak

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Titkosítási funkciók használatakor A.10.1.1 házirend

Ez a megoldás segít cryptograph vezérlők használatakor a szabályzat kényszerítéséhez 13 hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyeket adott cryptograph vezérlők és naplózási gyenge kriptográfiai beállítások használatát.
Ismertetése, ahol az Azure-erőforrások rendelkezhetnek titkosítási konfiguráció nem optimális segíthet az információk biztonsági szabályzatának megfelelően konfigurált erőforrások biztosítása érdekében javítási műveleteket. Pontosabban a házirendek, ez a megoldás által hozzárendelt titkosítás használata a blob storage-fiókok és a data lake storage-fiókok; Transzparens adattitkosítás; SQL-adatbázis szükséges a storage-fiókok, SQL Database-adatbázisok, virtuális gépek lemezeinek és automation-fiók változók; hiányzó titkosítást naplózása a storage-fiókok, Function Apps, Web App, az API Apps és Redis Cache; nem biztonságos kapcsolatok naplózása gyenge virtuális gép titkosított; naplózása és a Service Fabric a titkosítatlan kommunikációnak.

- \[Előzetes verzió\]: Csak HTTPS-hozzáférés naplózása egy függvényalkalmazáshoz
- \[Előzetes verzió\]: Csak HTTPS-hozzáférés naplózása a webalkalmazás számára
- \[Előzetes verzió\]: Csak HTTPS-hozzáférés naplózása egy API-alkalmazás
- \[Előzetes verzió\]: Tárfiókok hiányzó blobtitkosításának naplózása
- \[Előzetes verzió\]: Naplózási Virtuálisgép-bővítmény telepítése Windows virtuális gép nem tárolja a jelszavak visszafejthető titkosítással
- \[Előzetes verzió\]: Naplózási Windows virtuális gép nem tárolja a jelszavak visszafejthető titkosítással
- \[Előzetes verzió\]: Az Azure Security Centerben a titkosítatlan SQL database monitorozása
- \[Előzetes verzió\]: Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása
- Automation-fiók változók titkosítási naplózás engedélyezése
- Naplózás engedélyezése csak a Redis Cache biztonságos kapcsolatok
- Biztonságos átvitelt a tárfiókok naplózása
- Naplózási ClusterProtectionLevel tulajdonság EncryptAndSign és a Service Fabricben
- Transzparens adattitkosítás állapotának naplózása

## <a name="a1241-event-logging"></a>A.12.4.1 események naplózása

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz hétrészes sorozat hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózási beállításai az Azure-erőforrás naplózása definíciókat.
Diagnosztikai naplók adja meg az Azure-erőforrások belül végrehajtott műveletekkel kapcsolatos információk.

- \[Előzetes verzió\]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Függőségi ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Az Azure Security Center nem naplózott SQL database monitorozása
- Diagnosztikai beállítás naplózása
- Az SQL server szintű naplózási beállításainak naplózása

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 rendszergazda és az operátorral naplók

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz, amely az Azure-erőforrások naplóbeállítások naplózása hét Azure szabályzat-meghatározást hozzárendelésével. Diagnosztikai naplók adja meg az Azure-erőforrások belül végrehajtott műveletekkel kapcsolatos információk.

- \[Előzetes verzió\]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Függőségi ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Az Azure Security Center nem naplózott SQL database monitorozása
- Diagnosztikai beállítás naplózása
- Az SQL server szintű naplózási beállításainak naplózása

## <a name="a1244-clock-synchronization"></a>A.12.4.4 számítógépóra-szinkronizálás

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz, amely az Azure-erőforrások naplóbeállítások naplózása hét Azure szabályzat-meghatározást hozzárendelésével. Az Azure-naplók az események idő korrelált rekord létrehozása az erőforrások között szinkronizált belső órát támaszkodnak.

- \[Előzetes verzió\]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Függőségi ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Log Analytics-ügynök telepítése a VMSS - virtuális gép rendszerkép (OS) listán nem szereplő naplózása
- \[Előzetes verzió\]: Az Azure Security Center nem naplózott SQL database monitorozása
- Diagnosztikai beállítás naplózása
- Az SQL server szintű naplózási beállításainak naplózása

## <a name="a1251-installation-of-software-on-operational-systems"></a>A működési rendszerek szoftverek A.12.5.1 telepítése

Adaptív Alkalmazásvezérlés a megoldás az Azure Security Centerben, amelyek segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépek. Ez a megoldás egy Azure szabályzat-definíció, amely figyeli a módosításokat az engedélyezett alkalmazások a csoporthoz rendeli hozzá. Ez a funkció segítségével szabályozhatja a szoftverek és Azure virtuális gépeken az alkalmazások telepítését.

- \[Előzetes verzió\]: Alkalmazások az Azure Security Center engedélyezésének figyelése

## <a name="a1261-management-of-technical-vulnerabilities"></a>Műszaki biztonsági rések A.12.6.1 kezelése

Ez a megoldás segít információt rendszer biztonsági rések kezelése öt hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek a hiányzó Rendszerfrissítések, operációs rendszer biztonsági rései, az SQL biztonsági réseket és a virtuális gép figyelése az Azure Security Center biztonsági rései. Az Azure Security Center jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy üzembe helyezett Azure-erőforrások biztonsági állapotának valós idejű betekintést biztosít.

- \[Előzetes verzió\]: Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása
- \[Előzetes verzió\]: Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése
- \[Előzetes verzió\]: Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése
- \[Előzetes verzió\]: A figyelő SQL-sebezhetőségi felmérés eredménye az Azure Security Centerben
- \[Előzetes verzió\]: A figyelő virtuális gépek biztonsági réseinek az Azure Security Centerben

## <a name="a1262-restrictions-on-software-installation"></a>Szoftvertelepítés A.12.6.2 korlátozásai

Adaptív Alkalmazásvezérlés a megoldás az Azure Security Centerben, amelyek segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépek. Ez a megoldás egy Azure szabályzat-definíció, amely figyeli a módosításokat az engedélyezett alkalmazások a csoporthoz rendeli hozzá. Korlátozások a Szoftvertelepítés segítségével csökkentheti annak lehetőségét, hogy a szoftver biztonsági rések bemutatása.

- \[Előzetes verzió\]: Alkalmazások az Azure Security Center engedélyezésének figyelése

## <a name="a1311-network-controls"></a>A.13.1.1 hálózati vezérlők

Ez a megoldás segítségével kezelheti és vezérelheti hálózatok hozzárendelése egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a megengedő hálózati biztonsági csoportok. Előfordulhat, hogy nem kívánt hálózati hozzáférés engedélyezése, amelyek túl megengedő szabályok, és át kell tekinteni. Ez a megoldás három Azure szabályzat-meghatározást, amelyek figyelése nem védett végpontokat, alkalmazások és storage-fiókok is rendel. Végpontok és a egy tűzfal és a korlátlan hozzáférést a storage-fiókok által nem védett alkalmazások lehetővé tehetik a információs rendszerben található információkhoz való nem szándékos hozzáférés.

- \[Előzetes verzió\]: Az Azure Security Center megengedő hálózati hozzáférés figyelése
- \[Előzetes verzió\]: Az Azure Security Center nem védett hálózati végpontok figyelése
- \[Előzetes verzió\]: Az Azure Security Center nem védett webalkalmazás figyelése
- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 információk átviteli szabályok és eljárások

A tervezet segítségével győződjön meg, hogy információ átvitele az Azure-szolgáltatásokkal biztonságos két hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózását, tárfiókok és a Redis Cache biztonságos kapcsolatok definícióit.

- Naplózás engedélyezése csak a Redis Cache biztonságos kapcsolatok
- Biztonságos átvitelt a tárfiókok naplózása

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001, a megosztott szolgáltatások tervezet vezérlő leképezése, látogasson el a további információ az architektúra és üzembe helyezése a minta az alábbi cikkeket:

> [!div class="nextstepaction"]
> [ISO 27001, a megosztott szolgáltatások tervezet - áttekintés](./index.md)
> [ISO 27001, a megosztott szolgáltatások tervezetet – a telepítés lépéseit](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
