---
title: Minta - PCI-DSS v3.2.1 tervezet - vezérlő leképezés
description: A fizetési Card Industry Data Security Standard v3.2.1 tervezet minta és az Azure Policy RBAC leképezés vezérlő.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540951"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v3.2.1 tervezet minta vezérlő leképezés

A következő cikk részletezi, hogyan képez le az Azure tervezetek PCI-DSS v3.2.1 tervezet minta a PCI-DSS v3.2.1 vezérlők. A vezérlőkkel kapcsolatos további információkért lásd: [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

A következő hozzárendeléseket a rendszer a **PCI-DSS v3.2.1:2018** szabályozza. A jobb oldalon a navigációs segítségével közvetlenül egy adott vezérlőt leképezés ugorhat. Számos, a csatlakoztatott vezérlők implementálása egy [Azure Policy](../../../policy/overview.md) kezdeményezésére. Tekintse át a teljes kezdeményezés, nyissa meg a **házirend** az Azure Portalon, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a  **[Előzetes verzió] naplózási PCI v3.2.1:2018 vezérli, és a naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése** beépített szabályzat-kezdeményezéshez.

## <a name="132-and-134-boundary-protection"></a>1.3.2 és 1.3.4 határ védelmére

Ez a megoldás segítségével felügyelheti és ellenőrizheti a hálózatok hozzárendelésével [Azure Policy](../../../policy/overview.md) figyeli a megengedő hálózati biztonsági csoportokkal kapcsolatos definíciókat. Előfordulhat, hogy nem kívánt hálózati hozzáférés engedélyezése, amelyek túl megengedő szabályok, és át kell tekinteni. A tervezet hozzárendeli egy védelem nélküli végpontok, alkalmazások és tárfiókok figyelése Azure szabályzatdefiníciók. Végpontok és a egy tűzfal és a korlátlan hozzáférést a storage-fiókok által nem védett alkalmazások lehetővé tehetik a információs rendszerben található információkhoz való nem szándékos hozzáférés.

- A storage-fiókok nem korlátozott hálózati hozzáférés naplózása
- Az Internet felé néző végpont keresztüli hozzáférés korlátozva kell lennie.

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1-, 4.1.g, 4.1.h és 6.5.3 titkosítási védelme

Ez a megoldás segít érvényesíteni a házirendet, cryptograph vezérlők használatával hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek adott cryptograph vezérlők és naplózási gyenge kriptográfiai beállítások használatát. Ismertetése, ahol az Azure-erőforrások rendelkezhetnek titkosítási konfiguráció nem optimális segíthet az információk biztonsági szabályzatának megfelelően konfigurált erőforrások biztosítása érdekében javítási műveleteket. Pontosabban a házirendek, ez a megoldás által hozzárendelt; SQL-adatbázisok transzparens titkosításának kötelezővé tétele a storage-fiókok és az automation-fiók változók titkosítási hiányzó naplózási. Mely címet naplózása nem biztonságos kapcsolatok a storage-fiókok, Függvényalkalmazások, webalkalmazás, az API Apps és Redis Cache és a Service Fabric a titkosítatlan kommunikációnak naplózási házirendek is vannak.

- Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- API-alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Az Azure Security Centerben a titkosítatlan SQL database monitorozása
- Lemeztitkosítás alkalmazni kell a virtuális gépek
- Automation-fiók változókat a rendszer titkosítsa
- A Redis gyorsítótárhoz kizárólag biztonságos kapcsolatot engedélyezni kell
- Engedélyezni kell a biztonságos átvitelt a storage-fiókok
- Service Fabric-fürtök kell rendelkeznie a ClusterProtectionLevel tulajdonság EncryptAndSign beállítása
- Engedélyezni kell az SQL Database-adatbázisok transzparens adattitkosítás
- SQL-Adatbázisok transzparens adattitkosításának üzembe helyezése

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 és 11.2.1 programcsomag rendszerfrissítések és a biztonsági rések keresése

Ez a megoldás segít információt rendszer biztonsági rések kezelése hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek a hiányzó Rendszerfrissítések, operációs rendszer biztonsági rései, az SQL biztonsági réseket és a virtuális gép figyelése az Azure Security Center biztonsági rései. Az Azure Security Center jelentéskészítési lehetőségeket, amelyek lehetővé teszik, hogy üzembe helyezett Azure-erőforrások biztonsági állapotának valós idejű betekintést biztosít.

- Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása
- Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése
- A Fenyegetésészlelés az SQL Server-kiszolgálók üzembe helyezése
- Rendszerfrissítések, telepítenie kell a gépek
- Biztonsági beállítások a gépek biztonsági réseinek kell kijavítható.
- Az SQL-adatbázisok biztonsági rések kell kijavítható.
- Biztonsági rések kell kijavítható biztonságirés-értékelési megoldás által

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2-es és 7.1.3 feladatkörök

Csak egy Azure-előfizetés tulajdonosa kellene nem engedélyezi a felügyeleti redundancia biztosítása érdekében. Ezzel szemben túl sok Azure-előfizetéssel rendelkező kellene növelheti a lehetséges egy biztonsági kockázatot jelentő tulajdonosok fiók keresztül megsértése. Ez a megoldás segít a megfelelő számú Azure-előfizetéssel rendelkező karbantartása hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók, amelyek naplózása az Azure-előfizetések tulajdonosainak száma. Előfizetés tulajdonosi engedélyekkel kezelése segíthetnek a megfelelő feladatkörök megvalósításához.

- Meg kell adni az előfizetéshez hozzárendelt egynél több tulajdonosa
- Legfeljebb 3 tulajdonosok az előfizetéshez kell kijelölni 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2-es, 7.2.1, a kiemelt hozzáférési jogosultságok 8.3.1.a és 8.3.1.b felügyeleti

Ez a megoldás segít korlátozása, valamint szabályozhatja a privilegizált hozzáférési jogosultsága hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciók külső fiókok naplózási tulajdonosával, írási és/vagy tulajdonosa és/vagy írási engedélyek és alkalmazott fiókok olvasása olyan engedélyeket, amelyek nem rendelkeznek a többtényezős hitelesítés engedélyezve van. Azure szerepköralapú hozzáférés-vezérlés (RBAC) való hozzáférés kezeléséhez az Azure-erőforrások valósítja meg. Ismertetése, amelyeknél megvalósítása az egyéni RBAC-szabályok segítségével van szüksége, és megfelelő végrehajtásához, győződjön meg arról, egyéni RBAC-szabályok hibalehetőségeket rejt magában. Ez is tervezetet rendel [Azure Policy](../../../policy/overview.md) SQL-kiszolgálók az Azure Active Directory-hitelesítés használata definíciók naplózását. Az Azure Active Directory hitelesítési egyszerűsíti az engedélyek kezelését és központosítja adatbázis-felhasználók és más Microsoft-identitás kezelése  
Szolgáltatások.
 
- Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből
- MFA engedélyezni kell a fiókok az előfizetésben tulajdonosi engedélyekkel rendelkező
- Az MFA engedélyezett fiókok, az előfizetés írási jogosultsággal rendelkező kell lennie.
- MFA engedélyezni kell a fiókok az előfizetés olvasási jogosultsággal rendelkező
- Az Azure Active Directory-rendszergazda üzembe helyezi az SQL Server-kiszolgálók
- Az egyéni RBAC-szabályok használatának naplózása

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2-es és 8.1.5 legalább jogosultsággal, és tekintse át a felhasználói hozzáférési jogosultságokat

Azure szerepköralapú hozzáférés-vezérlés (RBAC segítségével kezelhetők, ki férhet hozzá az erőforrásokat az Azure-ban) valósítja meg. Az Azure Portalon, megtekintheti, ki férhet hozzá az Azure-erőforrások és a hozzájuk tartozó jogosultságokat. A tervezet rendeli [Azure Policy](../../../policy/overview.md) fiókokat, ellenőrzésre, beleértve a értékcsökkentett és emelt szintű engedélyekkel rendelkező külső fiókok élveznek naplózása érdekében.

- Elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 eltávolítása és a hozzáférési jogosultságok beállítása

Azure szerepköralapú hozzáférés-vezérlés (RBAC), amelyek segítségével kezelheti a kinek van hozzáférése, az Azure-erőforrások valósítja meg. Az Azure Active Directory és az RBAC használatával, frissítheti a szervezeti változásainak felhasználói szerepköröket. Ha szükséges, fiókok bejelentkezés blokkolva (vagy eltávolítása), amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogosultságok. A tervezet rendeli [Azure Policy](../../../policy/overview.md) definíciók naplózása értékcsökkentett fiók eltávolítása kell figyelembe venni.

- Elavult fiókokat el kell távolítani az előfizetésből
- Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b és 8.2.5 jelszóalapú hitelesítés

Ez a megoldás segít a erős jelszavak kényszerítése hozzárendelésével [Azure Policy](../../../policy/overview.md) definíciókat, amelyeket nem minimális erősségét és más jelszókövetelményeinek Windows virtuális gépek naplózása. A jelszó erőssége szabályzat megsértése a virtuális gépek figyelése segítséget nyújt az összes virtuális gép felhasználói fiók jelszavát felelnek meg a házirend biztosítása érdekében javítási műveleteket.

- [Előzetes verzió]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Előzetes verzió]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Előzetes verzió]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Előzetes verzió]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 és 10.5.4 létrehozás naplózása

Ez a megoldás segítségével győződjön meg, hogy a rendszer eseményeket naplóz hozzárendelésével [Azure Policy](../../../policy/overview.md) naplózási beállításai az Azure-erőforrás naplózása definíciókat.
Diagnosztikai naplók adja meg az Azure-erőforrások belül végrehajtott műveletekkel kapcsolatos információk. Az Azure-naplók az események idő korrelált rekord létrehozása az erőforrások között szinkronizált belső órát támaszkodnak.

- Az Azure Security Center nem naplózott SQL-kiszolgálók figyelése
- Diagnosztikai beállítás naplózása
- Az SQL server szintű naplózási beállításainak naplózása
- Az SQL Server-kiszolgálók üzembe helyezése a naplózás
- Storage-fiókokat kell áttelepíteni az új Azure Resource Manager-erőforrások
- Virtuális gépek kell áttelepíteni az új Azure Resource Manager-erőforrások

## <a name="1236-and-1237-information-security"></a>12.3.6 és 12.3.7 információ-biztonság

Ez a megoldás segítségével kezelheti és irányítást a hálózat hozzárendelése [Azure Policy](../../../policy/overview.md) elfogadható hálózati helyek és a jóváhagyott vállalati termékek definíciókat engedélyezett a környezethez. Ezek a szabályzat paraméterei ezekkel a szabályzatokkal belül minden egyes vállalat testre szabható.

- Engedélyezett helyek
- Engedélyezett helyek az erőforráscsoportok

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a PCI-DSS v3.2.1 tervezet vezérlő leképezése, látogasson el a további információ az áttekintést, és ez a minta üzembe helyezése a következő cikkeket:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 tervezet - áttekintés](./index.md)
> [PCI-DSS v3.2.1 tervezetet – a telepítés lépéseit](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
