---
title: Minta-CIS Microsoft Azure alapítványok teljesítményteszt – javaslat-hozzárendelés
description: Javaslat a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének leképezése a Azure Policyra.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 118ff46ca3b640c6ec24ab85fd598f213229417a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014194"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Javaslat a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének hozzárendelésére

A következő cikk azt ismerteti, hogy az Azure-tervrajzok CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének mintáját a CIS-Microsoft Azure a Founds-referenciaértékekre vonatkozó ajánlások alapján. A javaslatokkal kapcsolatos további információkért lásd: a [CIS Microsoft Azure alapítványok alapjai](https://www.cisecurity.org/benchmark/azure/).

A következő leképezések a **CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0** javaslataira vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy konkrét javaslat-hozzárendelésre ugorhat.
A leképezett javaslatok többsége [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósul meg. A teljes kezdeményezés áttekintéséhez nyissa meg a szabályzatot a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki  **\[az\] előzetes verziójú audit CIS Microsoft Azure a founds benchmark v 1.1.0 javaslatokat, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!NOTE]
> A terv teljes mintája hamarosan elérhető lesz. A társított Azure Policy kezdeményezés most már elérhető.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Ellenőrizze, hogy a többtényezős hitelesítés engedélyezve van-e minden Kiemelt felhasználó számára

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek illeszkednek ehhez a CIS-javaslathoz.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Ellenőrizze, hogy a többtényezős hitelesítés engedélyezve van-e az összes nem Kiemelt felhasználó számára

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Ellenőrizze, hogy nincsenek-e vendég felhasználók

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek illeszkednek ehhez a CIS-javaslathoz.

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Az ASC alapértelmezett házirend-beállításának biztosítása a "rendszerfrissítések figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A számítógépekre telepíteni kell a rendszerfrissítéseket

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 gondoskodjon az ASC alapértelmezett házirend-beállításáról: "az operációs rendszer sebezhetőségének figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Az ASC alapértelmezett házirend-beállításának biztosítása "figyelő Endpoint Protection" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 annak biztosítása, hogy az ASC alapértelmezett házirendje "a lemez titkosításának figyelése" beállítás ne legyen "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Az ASC alapértelmezett házirend-beállításának biztosítása a "webalkalmazási tűzfal figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 az ASC alapértelmezett házirend-beállításának biztosítása "a sebezhetőségi felmérés figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Az ASC alapértelmezett házirend-beállításának biztosítása a "JIT hálózati hozzáférés figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 gondoskodjon az ASC alapértelmezett házirend-beállításáról: "az SQL-titkosítás figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Győződjön meg arról, hogy a "biztonságos átvitel szükséges" beállítás értéke "enabled" (engedélyezve)

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 ellenőrizze, hogy a Storage-fiókok alapértelmezett hálózati hozzáférési szabálya megtagadás értékű-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Győződjön meg arról, hogy a "naplózás" beállítás "on" értékre van beállítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Ellenőrizze, hogy a "AuditActionGroups" az SQL Server rendszerhez tartozó naplózási szabályzatban megfelelően van-e beállítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL naplózási beállításainak a kritikus tevékenységek rögzítéséhez konfigurált műveleti csoportokkal kell rendelkezniük

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Győződjön meg róla, hogy a "naplózás" megőrzés "nagyobb, mint 90 nap"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Győződjön meg róla, hogy az SQL Server "Advanced Security" beállítása "on" értékre van állítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 győződjön meg arról, hogy a "veszélyforrások észlelésének típusai" értéke "all".

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek illeszkednek ehhez a CIS-javaslathoz.

- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 ellenőrizze, hogy be van-e állítva a "riasztások küldése ide"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 gondoskodjon arról, hogy az "e-mail-szolgáltatás és a társ-rendszergazdák" engedélyezve legyen

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 ellenőrizze, hogy Azure Active Directory rendszergazda konfigurálva van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 gondoskodjon arról, hogy az "adattitkosítás" beállítása "on" értékre legyen SQL Database

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 gondoskodjon arról, hogy az SQL Server TDE Protector titkosítva legyen a BYOK (saját kulcs használata)

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek illeszkednek ehhez a CIS-javaslathoz.

- Az SQL-kiszolgáló TDE-védelmet saját kulcs használatával ajánlott titkosítani
- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>Győződjön meg arról, hogy az Azure kulcstartó naplózása engedélyezve van a 5.1.7

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A Key Vault lévő diagnosztikai naplókat engedélyezni kell

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Ellenőrizze, hogy az operációs rendszer lemeze titkosítva van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Ellenőrizze, hogy az adatlemezek titkosítva vannak-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 ellenőrizze, hogy az összes Virtual Machines legújabb operációsrendszer-javításait alkalmazza-e a rendszer

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- A számítógépekre telepíteni kell a rendszerfrissítéseket

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 ellenőrizze, hogy telepítve van-e az összes Virtual Machines Endpoint Protection

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure Kubernetes-szolgáltatásokon belül

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- \[Előzetes\]verzió: Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Ellenőrizze, hogy a webalkalmazás átirányítja-e az összes HTTP-forgalmat a HTTPS-re Azure App Service

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely az ehhez a CIS-javaslathoz igazodik.

- Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a CIS Microsoft Azure Founds teljesítményteszt-tervezetének vezérlési leképezését, a következő cikkben tájékozódhat a tervről, vagy felkeresheti a Azure Policy a Azure Portal a kezdeményezés hozzárendeléséhez:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure alapítványok teljesítményteszt-tervezete – áttekintés](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.