---
title: CIS Microsoft Azure alapítványok teljesítményteszt-mintájának vezérlői
description: Javaslat a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének leképezése a Azure Policyra.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707425"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Javaslat a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének hozzárendelésére

A következő cikk azt ismerteti, hogy az Azure-tervrajzok CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének mintáját a CIS-Microsoft Azure a Founds-referenciaértékekre vonatkozó ajánlások alapján. A javaslatokkal kapcsolatos további információkért lásd: a [CIS Microsoft Azure alapítványok alapjai](https://www.cisecurity.org/benchmark/azure/).

A következő leképezések a **CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0** javaslataira vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy konkrét javaslat-hozzárendelésre ugorhat.
A leképezett javaslatok többsége [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósul meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a **\[előzetes verzióját\] a következőt: audit CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 javaslatok és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Ellenőrizze, hogy a többtényezős hitelesítés engedélyezve van-e minden Kiemelt felhasználó számára

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítségével figyelheti, ha a többtényezős hitelesítés nincs engedélyezve a privilegizált Azure Active Directory-fiókokban.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Ellenőrizze, hogy a többtényezős hitelesítés engedélyezve van-e az összes nem Kiemelt felhasználó számára

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít megfigyelni, hogy a többtényezős hitelesítés nincs-e engedélyezve a nem privilegizált Azure Active Directory fiókokon.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Ellenőrizze, hogy nincsenek-e vendég felhasználók

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek segítenek az esetlegesen eltávolítható vendég fiókok figyelésében.

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1,23 gondoskodjon arról, hogy ne hozzon létre egyéni előfizetési tulajdonosi szerepkört

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítségével figyelheti az egyéni előfizetés tulajdonosi szerepköreit, amelyeket el kell távolítani.

- Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 Ellenőrizze, hogy a standard árképzési szint van-e kiválasztva

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a hálózatok és a virtuális gépek figyelésében, ahol a standard szintű Security Center nincs engedélyezve.

 - Security Center Standard díjszabási szintet kell kiválasztani

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 Győződjön meg arról, hogy a figyelési ügynök automatikus kiépítés beállítása "on" értékre van állítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az log Analytics-ügynök automatikus kiosztása engedélyezve legyen.

- Az előfizetéshez engedélyezni kell a Log Analytics figyelő ügynök automatikus üzembe helyezését

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Az ASC alapértelmezett házirend-beállításának biztosítása a "rendszerfrissítések figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a rendszerfrissítések telepítve legyenek a virtuális gépeken.

- A számítógépekre telepíteni kell a rendszerfrissítéseket

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 gondoskodjon az ASC alapértelmezett házirend-beállításáról: "az operációs rendszer sebezhetőségének figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít figyelni a unremediated virtuális gépek biztonsági réseit.

- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Az ASC alapértelmezett házirend-beállításának biztosítása "figyelő Endpoint Protection" nem "Letiltva"

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az Endpoint Protection engedélyezve legyen a virtuális gépeken.

- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 annak biztosítása, hogy az ASC alapértelmezett házirendje "a lemez titkosításának figyelése" beállítás ne legyen "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a virtuális gépek lemezének titkosítását.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 Az ASC alapértelmezett házirend-beállítás "a hálózati biztonsági csoportok figyelése" beállítása nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az internetre irányuló virtuális gépek biztonságában.

- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Az ASC alapértelmezett házirend-beállításának biztosítása a "webalkalmazási tűzfal figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a webalkalmazásokat futtató virtuális gépek biztonságában.

- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 Az ASC alapértelmezett házirend-beállításának biztosítása "a következő generációs tűzfal (NGFW) figyelésének engedélyezése" nem "Letiltva"

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek segítenek az alhálózatok és a virtuális gépek védelméhez a hozzáférés korlátozásával. Az ezen CIS által hivatkozott Security Center szabályzat Microsoft Azure Founds benchmark ajánlását két új javaslat váltotta fel. Az alább hivatkozott szabályzatok az új ajánlásokat ismertetik.

- Az alhálózatokat hálózati biztonsági csoporttal kell társítani
- A virtuális gépeket hálózati biztonsági csoporttal kell társítani

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 az ASC alapértelmezett házirend-beállításának biztosítása "a sebezhetőségi felmérés figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a biztonsági rések észlelésében és szervizelésében.

- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 az ASC alapértelmezett házirend-beállításának biztosítása a "tárolási blob titkosításának figyelése" nem "Letiltva"

Az Azure Storage-titkosítás minden új és meglévő Storage-fiók esetében engedélyezve van, és nem tiltható le. (Ez egy alapértelmezett Azure-képesség, nincs szabályzat-hozzárendelés.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Az ASC alapértelmezett házirend-beállításának biztosítása a "JIT hálózati hozzáférés figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépekhez való hozzáférés szabályozásában.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 az ASC alapértelmezett házirend-beállításának biztosítása "az adaptív alkalmazás engedélyezési listájának figyelése" nem "Letiltva"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani az adaptív alkalmazások vezérlését a virtuális gépeken.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 gondoskodjon az ASC alapértelmezett házirend-beállításáról: "az SQL audit figyelése" nem "Letiltva"

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi az SQL Server-naplózás engedélyezését.

- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 gondoskodjon az ASC alapértelmezett házirend-beállításáról: "az SQL-titkosítás figyelése" nem "Letiltva"

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az transzparens adattitkosítás engedélyezve legyen az SQL-adatbázisokban.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 ellenőrizze, hogy a "biztonsági kapcsolattartási e-mailek" be van-e állítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a biztonsági értesítések megfelelően engedélyezve legyenek

- Az előfizetéshez meg kell adni egy biztonsági kapcsolattartási e-mail címet

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 gondoskodjon arról, hogy a biztonsági Kapcsolattartó telefonszáma be legyen állítva

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a biztonsági értesítések megfelelően engedélyezve legyenek

- Az előfizetéshez meg kell adni egy biztonsági kapcsolatfelvételi telefonszámot

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 ellenőrizze, hogy az "e-mail-értesítések küldése a nagy súlyosságú riasztásokhoz" beállítás értéke "on"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a biztonsági értesítések megfelelően engedélyezve legyenek

- A magas súlyosságú riasztások e-mail-értesítését engedélyezni kell

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 győződjön meg arról, hogy az "e-mail küldése az előfizetés-tulajdonosoknak" beállítás értéke "on"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a biztonsági értesítések megfelelően engedélyezve legyenek

- Engedélyezni kell az e-mailes értesítést a nagy súlyosságú riasztások előfizetésének tulajdonosának

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Győződjön meg arról, hogy a "biztonságos átvitel szükséges" beállítás értéke "enabled" (engedélyezve)

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a nem biztonságos kapcsolatokat engedélyező tárolási fiókok figyelésében.

- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 ellenőrizze, hogy a Storage-fiókok alapértelmezett hálózati hozzáférési szabálya megtagadás értékű-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a korlátlan hozzáférést engedélyező Storage-fiókok figyelésében.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 gondoskodjon arról, hogy a megbízható Microsoft-szolgáltatások engedélyezve legyenek a Storage-fiókhoz való hozzáféréshez

Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a megbízható Microsoft-szolgáltatásokból való hozzáférést nem tevő Storage-fiókok figyelésében.

- A Storage-fiókoknak engedélyezniük kell a megbízható Microsoft-szolgáltatások elérését

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Győződjön meg arról, hogy a "naplózás" beállítás "on" értékre van beállítva

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi az SQL Server-naplózás engedélyezését. 

- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Ellenőrizze, hogy a "AuditActionGroups" az SQL Server rendszerhez tartozó naplózási szabályzatban megfelelően van-e beállítva

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani az SQL Server naplózásának megfelelő konfigurálását.

- Az SQL naplózási beállításainak a kritikus tevékenységek rögzítéséhez konfigurált műveleti csoportokkal kell rendelkezniük

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Győződjön meg róla, hogy a "naplózás" megőrzés "nagyobb, mint 90 nap"

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy az SQL Server-naplók legalább 90 napig megmaradjanak.

- Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Győződjön meg róla, hogy az SQL Server "Advanced Security" beállítása "on" értékre van állítva

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az SQL-kiszolgálókon és az SQL felügyelt példányain engedélyezve legyen a speciális adatbiztonság.

- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 győződjön meg arról, hogy a "veszélyforrások észlelésének típusai" értéke "all".

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy az összetett veszélyforrások elleni védelem megfelelően legyen KONFIGURÁLVA az SQL-kiszolgálókon és az SQL felügyelt példányain.

- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 ellenőrizze, hogy be van-e állítva a "riasztások küldése ide"

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a speciális adatbiztonsági értesítések megfelelően engedélyezve legyenek.

- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 gondoskodjon arról, hogy az "e-mail-szolgáltatás és a társ-rendszergazdák" engedélyezve legyen

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a speciális adatbiztonsági értesítések megfelelően engedélyezve legyenek.

- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.
- Az SQL-kiszolgáló speciális adatbiztonsági beállításainál engedélyezni ajánlott a rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 ellenőrizze, hogy Azure Active Directory rendszergazda konfigurálva van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy az SQL-kiszolgálók Azure Active Directory rendszergazda legyen kiépítve.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 gondoskodjon arról, hogy az "adattitkosítás" beállítása "on" értékre legyen SQL Database

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az transzparens adattitkosítás engedélyezve legyen az SQL-adatbázisokban.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 gondoskodjon arról, hogy az SQL Server TDE Protector titkosítva legyen a BYOK (saját kulcs használata)

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy az SQL-kiszolgálók és az SQL-felügyelt példányok transzparens titkosított védelme a saját kulccsal legyen titkosítva.

- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani
- Az SQL-kiszolgáló TDE-védelmet saját kulcs használatával ajánlott titkosítani

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 gondoskodjon arról, hogy az "SSL-kapcsolat érvényesítése" beállítás értéke "ENABLED" legyen a MySQL adatbázis-kiszolgáló számára

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segítséget nyújt a MySQL-adatbázis-kiszolgálók számára az SSL-kapcsolatok betartatásában.

- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4,12 ellenőrizze, hogy a (z) "log_checkpoints" kiszolgálói paraméter "ON" értékre van-e állítva a PostgreSQL adatbázis-kiszolgáló esetében

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a PostgreSQL-adatbázis-kiszolgálók naplójának ellenőrzőpontját.

- A naplózási ellenőrzőpontokat engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 gondoskodjon arról, hogy az "SSL-kapcsolat érvényesítése" beállítás "engedélyezve" legyen a PostgreSQL adatbázis-kiszolgáló számára.

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a PostgreSQL adatbázis-kiszolgálóinak az SSL-kapcsolatokat.

- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4,14 ellenőrizze, hogy a (z) "log_connections" kiszolgálói paraméter "ON" értékre van-e állítva a PostgreSQL adatbázis-kiszolgáló esetében

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a PostgreSQL-adatbázis-kiszolgálók naplózási kapcsolatait.

- A naplózási kapcsolatokat engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4,15 ellenőrizze, hogy a (z) "log_disconnections" kiszolgálói paraméter "ON" értékre van-e állítva a PostgreSQL adatbázis-kiszolgáló esetében

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a PostgreSQL-adatbázis-kiszolgálók naplójának leválasztását.

- A leválasztásokat a PostgreSQL adatbázis-kiszolgálóihoz kell naplózni.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4,16 ellenőrizze, hogy a (z) "log_duration" kiszolgálói paraméter "ON" értékre van-e állítva a PostgreSQL adatbázis-kiszolgáló esetében

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a PostgreSQL adatbázis-kiszolgálóinak a befejezett utasítások időtartamának naplózását.

- A naplózási időtartamot engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 ellenőrizze, hogy a (z) "connection_throttling" kiszolgálói paraméter "ON" értékre van-e állítva a PostgreSQL adatbázis-kiszolgáló esetében

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a PostgreSQL-adatbázis-kiszolgálókon a találgatásos támadásokkal kapcsolatos támadások enyhítésében.

- A kapcsolatok szabályozását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 ellenőrizze, hogy Azure Active Directory rendszergazda konfigurálva van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy az SQL-kiszolgálók Azure Active Directory rendszergazda legyen kiépítve. A CIS Microsoft Azure alapítványok Teljesítményteszte tartalmazza ezt a javaslatot; azonban a 4,8-es [ajánlás](#48-ensure-that-azure-active-directory-admin-is-configured)duplikálása.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Ellenőrizze, hogy létezik-e a log profil

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy az összes Azure-előfizetéshez egy log-profil létezik. 

- Az Azure-előfizetéseknek log-profillal kell rendelkezniük a műveletnapló számára

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 ügyeljen arra, hogy a műveletnapló megtartásának beállítása 365 nap vagy nagyobb legyen

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a tevékenységek naplói legalább egy évig megmaradjanak.

- A tevékenység naplóját legalább egy évig meg kell őrizni

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 a naplózási profil összes tevékenységének rögzítése

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a napló profilja megfelelően legyen konfigurálva.

- Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 gondoskodjon arról, hogy a napló profilja rögzítse a tevékenység naplóit minden régióhoz, beleértve a globális

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a napló profilja megfelelően legyen konfigurálva.

- Azure Monitor az összes régióból gyűjti a tevékenység naplóit

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>Győződjön meg arról, hogy az Azure kulcstartó naplózása engedélyezve van a 5.1.7

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a diagnosztikai naplók engedélyezve legyenek a Key vaultok számára.

- A Key Vault lévő diagnosztikai naplókat engedélyezni kell

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 ellenőrizze, hogy az Network Watcher engedélyezve van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy Network Watcher engedélyezve legyen minden olyan régióban, ahol az erőforrások telepítve vannak. Ehhez a Szabályzathoz az összes vonatkozó régiót megadó paraméter-tömb szükséges. Ebben a házirend-kezdeményezési definícióban az alapértelmezett érték a "eastus".

- Network Watcher engedélyezni kell

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Ellenőrizze, hogy az operációs rendszer lemeze titkosítva van-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a lemezek titkosítása engedélyezve legyen a virtuális gépeken.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Ellenőrizze, hogy az adatlemezek titkosítva vannak-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a lemezek titkosítása engedélyezve legyen a virtuális gépeken.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 Ellenőrizze, hogy a "nem csatlakoztatott lemezek" titkosítva vannak-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a nem csatolt lemezek titkosításának biztosításában.

- A nem csatolt lemezeket titkosítani kell

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 Győződjön meg arról, hogy csak a jóváhagyott bővítmények vannak telepítve

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy csak a jóváhagyott virtuálisgép-bővítmények legyenek telepítve. Ehhez a Szabályzathoz az összes jóváhagyott virtuálisgép-bővítményt megadó paraméter-tömb szükséges. Ez a házirend-kezdeményezési definíció az ügyfelek által érvényesített ajánlott alapértékeket tartalmazza. 

 - Csak a jóváhagyott virtuálisgép-bővítményeket kell telepíteni

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 ellenőrizze, hogy az összes Virtual Machines legújabb operációsrendszer-javításait alkalmazza-e a rendszer

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a rendszerfrissítések telepítve legyenek a virtuális gépeken.

- A számítógépekre telepíteni kell a rendszerfrissítéseket

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 ellenőrizze, hogy telepítve van-e az összes Virtual Machines Endpoint Protection

Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely lehetővé teszi, hogy az Endpoint Protection engedélyezve legyen a virtuális gépeken.

- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 Ellenőrizze, hogy a kulcstartó helyreállítható-e

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a kulcstároló-objektumok véletlen törlés esetén helyreállítható legyenek.

- Key Vault objektumoknak helyreállítható kell lenniük

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure Kubernetes-szolgáltatásokon belül

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani a szerepköralapú hozzáférés-vezérlést a Kubernetes Service-fürtökben lévő felügyelt engedélyekhez.

- \[előzetes verzió\]: szerepköralapú Access Control (RBAC) használata a Kubernetes-szolgáltatásokban

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Ellenőrizze, hogy a webalkalmazás átirányítja-e az összes HTTP-forgalmat a HTTPS-re Azure App Service

Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít biztosítani, hogy a webalkalmazások csak biztonságos kapcsolatokon keresztül legyenek elérhetők.

- A webalkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9,3 Ellenőrizze, hogy a webalkalmazás a TLS-titkosítás legújabb verzióját használja-e

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a legújabb TLS-verziót használja.

- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- A legújabb TLS-verziót kell használni a webalkalmazásban

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9,4 Győződjön meg arról, hogy a webalkalmazáshoz "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" be van állítva a következőre: "on"

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy csak az érvényes tanúsítvánnyal rendelkező ügyfelek tudják elérni a webalkalmazásokat.

- Győződjön meg arról, hogy az API-alkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értékre van állítva
- Győződjön meg arról, hogy a Function app "Client certificates (bejövő Ügyféltanúsítványok)" beállítása "on" értékre van állítva
- Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9,5 ellenőrizze, hogy engedélyezve van-e a regisztráció a Azure Active Directory App Service

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a webalkalmazások felügyelt identitást használjanak.

- Győződjön meg arról, hogy a regisztráció a Azure Active Directory engedélyezve van az API-alkalmazásban
- Győződjön meg arról, hogy engedélyezve van-e a regisztráció a Azure Active Directory függvényalkalmazás
- Győződjön meg arról, hogy a regisztráció a Azure Active Directory engedélyezve van a webalkalmazásban

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9,6 győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a webalkalmazás részeként van használatban

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a .NET-keretrendszer legújabb verzióját használja.

- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a függvényalkalmazás részeként van használatban
- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a webalkalmazás részeként van használatban

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9,7 ellenőrizze, hogy a "PHP-verzió" a legújabb-e, ha a webalkalmazás futtatására használatos

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a PHP legújabb verzióját használja.

- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9,8 ellenőrizze, hogy a "Python-verzió" a legújabb-e, ha a webalkalmazás futtatásához használatos

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a Python legújabb verzióját használja.

- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9,9 győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás futtatására használatos

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a Java legújabb verzióját használja.

- Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha a funtion alkalmazás részeként van használatban
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9,10 ellenőrizze, hogy a "HTTP-verzió" a legújabb-e, ha a webalkalmazás futtatásához használatos

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek segítenek biztosítani, hogy a Web Apps a http legújabb verzióját használja.

- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az API-alkalmazás futtatásához használatos
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Function app futtatásához használatos
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatására használatos


## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a CIS Microsoft Azure Founds teljesítményteszt-tervezetének vezérlési leképezését, az alábbi cikkekben megismerheti a tervet, vagy megtekintheti a Azure Policy a Azure Portal a kezdeményezés hozzárendeléséhez:

> [!div class="nextstepaction"]
> [Cis Microsoft Azure alapítványok teljesítményteszt – áttekintés](./index.md)
> [CIS Microsoft Azure alapítványok teljesítményteszt-terv – a lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.