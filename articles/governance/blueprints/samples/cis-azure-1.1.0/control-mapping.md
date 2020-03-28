---
title: CIS Microsoft Azure Foundations Benchmark mintavezérlők
description: A CIS Microsoft Azure Foundations Blueprint minta ajánlási leképezése az Azure Policy-hez.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74707425"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>A CIS Microsoft Azure Foundations Benchmark minta ajánlásának leképezése

A következő cikk bemutatja, hogy az Azure Blueprints CIS Microsoft Azure Foundations Benchmark minta minta leképezi a CIS Microsoft Azure Foundations benchmark javaslatokat. A javaslatokról további információt a [CIS Microsoft Azure Foundations benchmark című](https://www.cisecurity.org/benchmark/azure/)témakörben talál.

Az alábbi leképezések a **CIS Microsoft Azure Foundations Benchmark 1.1.0-s verziós javaslatai.** A jobb oldali navigációs sávsegítségével közvetlenül egy adott ajánlásleképezésre ugorhat.
A leképezett javaslatok közül sok egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósítja meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki a ** \[CIS előzetes\] naplózási Microsoft Azure Foundations Benchmark 1.1.0-s verzióbeli javaslatait, és telepítsen konkrét virtuálisgép-bővítményeket** a beépített házirend-kezdeményezés naplózási követelményeinek támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Annak biztosítása, hogy a többtényezős hitelesítés minden kiemelt felhasználó számára engedélyezve legyen

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével figyelheti, ha a többtényezős hitelesítés nincs engedélyezve a kiemelt Azure Active Directory-fiókok. [Azure Policy](../../../policy/overview.md)

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-t engedélyezni kell az előfizetéshez írási engedéllyel rendelkező fiókokon

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Annak biztosítása, hogy a többtényezős hitelesítés minden nem kiemelt jogosultságú felhasználó számára engedélyezve legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít figyelni, ha a többtényezős hitelesítés nincs engedélyezve a nem kiemelt Azure Active Directory-fiókok. [Azure Policy](../../../policy/overview.md)

- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Annak biztosítása, hogy ne legyenek vendégfelhasználók

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével figyelheti a vendégfiókok, amelyek esetleg el kell távolítani. [Azure Policy](../../../policy/overview.md)

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Annak biztosítása, hogy egyéni előfizetés-tulajdonosi szerepkörök ne jönnek létre

Ez a tervezet [hozzárendeli az Azure Policy-definíciók,](../../../policy/overview.md) amelyek segítségével figyelheti az egyéni előfizetés-tulajdonosi szerepkörök, amelyek et el kell távolítani.

- Egyéni előfizetés-tulajdonosi szerepkörök nem létezhetnek

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Annak biztosítása, hogy a standard tarifacsomag legyen kiválasztva

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a hálózatok és a virtuális gépek figyelésében, ahol a Security Center standard szint nincs engedélyezve. [Azure Policy](../../../policy/overview.md)

 - A Security Center szokásos tarifacsomagját kell kiválasztani

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Annak biztosítása, hogy a "monitoring ügynök automatikus ellátása" "Be"

Ez a tervezet hozzárendel egy [Azure-szabályzat-definíciót,](../../../policy/overview.md) amely segít biztosítani a Log Analytics-ügynök automatikus kiépítését.

- A Log Analytics-figyelőügynök automatikus kiépítését engedélyezni kell az előfizetésben

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Annak biztosítása, hogy a "Rendszerfrissítések figyelése" alapértelmezett ASC-házirend-beállítás nem "letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a rendszerfrissítések telepítve vannak a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- A rendszerfrissítéseket telepíteni kell a gépeken

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása " Az operációs rendszer biztonsági réseinek figyelése" nem "letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a nem azonnal közvetített virtuálisgép-biztonsági rések figyelésében. [Azure Policy](../../../policy/overview.md)

- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Annak biztosítása, hogy a "Végpontvédelem figyelője" alapértelmezett ASC-házirend-beállítás nem "Letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a végpontvédelem engedélyezve van a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Annak biztosítása, hogy a "Lemeztitkosítás figyelője" alapértelmezett asc-házirend-beállítás nem "letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a virtuális gép lemezei titkosítását. [Azure Policy](../../../policy/overview.md)

- A lemeztitkosítást virtuális gépeken kell alkalmazni

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Annak biztosítása, hogy a "Hálózati biztonsági csoportok figyelése" alapértelmezett ASC-házirend-beállítás nem "Letiltva"

Ez a tervezet hozzárendel egy [Azure-szabályzat-definíciót,](../../../policy/overview.md) amely segít megvédeni az internetre néző virtuális gépek.

- A virtuális gépek internetre néző hálózati biztonsági csoportszabályait meg kell erősíteni

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Annak biztosítása, hogy a "WebApplication Firewall figyelő" alapértelmezett asc-házirend-beállítás nem "letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít megvédeni a webalkalmazásokat futtató virtuális gépeket. [Azure Policy](../../../policy/overview.md)

- Az IaaS-alapú webes alkalmazásokra vonatkozó NSG-szabályokat

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "A következő generációs tűzfal (NGFW) figyelésengedélyezése" nem "Letiltva"

Ez a tervezet [olyan Azure Policy-definíciókat](../../../policy/overview.md) rendel hozzá, amelyek a hozzáférés korlátozásával segítenek megvédeni az alhálózatokat és a virtuális gépeket a fenyegetésektől. A Security Center szabályzat a CIS Microsoft Azure Foundations Benchmark javaslat váltotta fel két új javaslatokat. Az alábbiakban hivatkozott irányelvek az új ajánlásokkal foglalkoznak.

- Az alhálózatokat hálózati biztonsági csoporthoz kell társítani
- A virtuális gépeket hálózati biztonsági csoporthoz kell társítani

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "A biztonsági rés felmérésének figyelése" nem "letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a biztonsági rések észlelését és kiújulását. [Azure Policy](../../../policy/overview.md)

- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Annak biztosítása, hogy a "Storage Blob Encryption figyelése" alapértelmezett ASC-házirend-beállítás nem "letiltva"

Az Azure Storage titkosítása engedélyezve van az összes meglévő és új tárfiókon, és nem tiltható le. (Ez egy alapértelmezett Azure-képesség; nincs házirend-hozzárendelés.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "A JIT hálózati hozzáférés figyelése" nem "Letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a virtuális gépekhez való hozzáférés szabályozásában. [Azure Policy](../../../policy/overview.md)

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "Adaptív alkalmazások engedélyezési listájának figyelése" nem "Letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az Adaptív alkalmazásvezérlők engedélyezve legyenek a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "Az SQL naplózásának figyelése" nem "Letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani az SQL-kiszolgáló naplózásának engedélyezését. [Azure Policy](../../../policy/overview.md)

- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Annak biztosítása, hogy az ASC alapértelmezett házirend-beállítása "Az SQL-titkosítás figyelése" nem "Letiltva"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az átlátszó adattitkosítás engedélyezve legyen az SQL-adatbázisokban. [Azure Policy](../../../policy/overview.md)

- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Annak biztosítása, hogy a "Biztonsági kapcsolati e-mailek"

Ez a tervezet olyan [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá, amely segít biztosítani a biztonsági értesítések megfelelő engedélyezését

- Az előfizetéshez meg kell adni egy biztonsági kapcsolattartó e-mail címét.

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Annak biztosítása, hogy a "Telefonszám" biztonsági kapcsolattartó be legyen állítva

Ez a tervezet olyan [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá, amely segít biztosítani a biztonsági értesítések megfelelő engedélyezését

- Az előfizetéshez biztonsági telefonszámát kell megadni

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Annak biztosítása, hogy az "E-mail értesítés küldése a magas súlyosságú riasztásokhoz" beállítás "Be"

Ez a tervezet olyan [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá, amely segít biztosítani a biztonsági értesítések megfelelő engedélyezését

- A magas súlyosságú riasztásokról szóló e-mail értesítést engedélyezni kell

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Annak biztosítása, hogy az "E-mail küldése az előfizetés-tulajdonosoknak is" beállítás "Be"

Ez a tervezet olyan [Azure Policy-definíciót](../../../policy/overview.md) rendel hozzá, amely segít biztosítani a biztonsági értesítések megfelelő engedélyezését

- Engedélyezni kell az előfizetés tulajdonosának küldött e-mail értesítést a súlyos riasztásokról

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Annak biztosítása, hogy a "Biztonságos átvitel szükséges" beállítás "Engedélyezve"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a nem biztonságos kapcsolatokat lehetővé tenni tartalmazó tárfiókok figyelésében. [Azure Policy](../../../policy/overview.md)

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Annak biztosítása, hogy a tárfiókok alapértelmezett hálózati hozzáférési szabálya megtagadásra van beállítva

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a korlátlan hozzáférést lehetővé tenni tárfiókok figyelésében. [Azure Policy](../../../policy/overview.md)

- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Annak biztosítása, hogy a "Megbízható Microsoft-szolgáltatások" engedélyezve legyenak a tárfiókhoz való hozzáféréshez

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a megbízható Microsoft-szolgáltatásokból való hozzáférést nem engedélyező tárfiókok figyelésében. [Azure Policy](../../../policy/overview.md)

- A tárfiókoknak lehetővé kell tenniük a hozzáférést a megbízható Microsoft-szolgáltatásokból

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Annak biztosítása, hogy a "könyvvizsgálat" "Be"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani az SQL-kiszolgáló naplózásának engedélyezését. [Azure Policy](../../../policy/overview.md) 

- A naplózást engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Annak biztosítása, hogy az SQL-kiszolgáló "auditálási" házirendjében az "AuditActionGroups" megfelelően legyen beállítva

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani az SQL-kiszolgáló naplózásának megfelelő konfigurálását. [Azure Policy](../../../policy/overview.md)

- Az SQL naplózási beállításoknak a műveletcsoportokat a kritikus tevékenységek rögzítésére kell konfigurálniuk

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Annak biztosítása, hogy a "könyvvizsgálat" megőrzése "90 napnál hosszabb" legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az SQL-kiszolgáló naplók legalább 90 napig megmaradnak. [Azure Policy](../../../policy/overview.md)

- Az SQL-kiszolgálókat 90 napnál hosszabb naplózási megőrzési nappal kell konfigurálni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Annak biztosítása, hogy az SQL-kiszolgálón a "speciális adatbiztonság" "Be"

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a speciális adatbiztonság engedélyezve van az SQL-kiszolgálókon és az SQL felügyelt példányokon. [Azure Policy](../../../policy/overview.md)

- A speciális adatbiztonságot engedélyezni kell az SQL által felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Annak biztosítása, hogy a "fenyegetésészlelési típusok" "Minden"

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja a speciális veszélyforrások elleni védelem megfelelően konfigurálva az SQL-kiszolgálók és az SQL felügyelt példányok. [Azure Policy](../../../policy/overview.md)

- A komplex veszélyforrások elleni védelem típusait az SQL-kiszolgáló speciális adatbiztonsági beállításaiban "All" típusúra kell állítani.
- A komplex veszélyforrások elleni védelem típusait "All" típusúra kell állítani az SQL felügyelt példányok speciális adatbiztonsági beállításaiban.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Annak biztosítása, hogy a "Riasztások küldése" beállítás

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a speciális adatbiztonsági értesítések megfelelő engedélyezését. [Azure Policy](../../../policy/overview.md)

- Az SQL által felügyelt példány speciális adatbiztonsági beállításainak tartalmazniuk kell egy e-mail címet a biztonsági riasztások fogadásához
- Az SQL-kiszolgáló speciális adatbiztonsági beállításainak tartalmazniuk kell egy e-mail címet a biztonsági riasztások fogadásához

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Annak biztosítása, hogy az "E-mail szolgáltatás és a társadminisztrátorok" "Engedélyezve" legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a speciális adatbiztonsági értesítések megfelelő engedélyezését. [Azure Policy](../../../policy/overview.md)

- A rendszergazdáknak és az előfizetés-tulajdonosoknak küldött e-mail-értesítéseket engedélyezni kell az SQL felügyelt példányok speciális adatbiztonsági beállításaiban
- A rendszergazdáknak és az előfizetés-tulajdonosoknak küldött e-mailértesítéseket engedélyezni kell az SQL server speciális adatbiztonsági beállításaiban

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Annak biztosítása, hogy az Azure Active Directory rendszergazdája konfigurálva van

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az Azure Active Directory rendszergazdája ki van építve az SQL-kiszolgálókhoz. [Azure Policy](../../../policy/overview.md)

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Annak biztosítása, hogy az "Adattitkosítás" "Be" legyen beállítva egy SQL-adatbázisban

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az átlátszó adattitkosítás engedélyezve legyen az SQL-adatbázisokban. [Azure Policy](../../../policy/overview.md)

- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Győződjön meg róla, hogy az SQL szerver TDE-védője byok-tal van titkosítva (Használja a saját kulcsát)

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja az SQL-kiszolgálók és az SQL által felügyelt példányok transzparens adatok titkosítását a saját kulccsal. [Azure Policy](../../../policy/overview.md)

- Az SQL felügyelt példány TDE-védőjét saját kulccsal kell titkosítani
- AZ SQL server TDE protector-t saját kulccsal kell titkosítani

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Annak biztosítása, hogy az "SSL-kapcsolat kényszerítése" "ENABLED" értékre van állítva a MySQL Adatbázis-kiszolgáló számára

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a MySQL adatbázis-kiszolgálók ssl-kapcsolatokat kényszerítsenek ki. [Azure Policy](../../../policy/overview.md)

- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a MySQL adatbázis-kiszolgálókon

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Annak biztosítása, hogy a "log_checkpoints" kiszolgálóparaméter "ON" lesz a PostgreSQL Database Server esetében

Ez a tervezet hozzárendel egy [Azure-szabályzat-definíciót,](../../../policy/overview.md) amely segít biztosítani a PostgreSQL adatbázis-kiszolgálók naplózási ellenőrzőpontok.

- A PostgreSQL adatbázis-kiszolgálókon engedélyezni kell a naplóellenőrzőpontokat

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Annak biztosítása, hogy az "SSL-kapcsolat kényszerítése" "ENABLED" értékre van állítva a PostgreSQL Adatbázis-kiszolgálóhoz

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a PostgreSQL adatbázis-kiszolgálók ssl-kapcsolatokat kényszerítsenek ki. [Azure Policy](../../../policy/overview.md)

- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a PostgreSQL adatbázis-kiszolgálókon

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Annak biztosítása, hogy a "log_connections" kiszolgálóparaméter "ON" lesz a PostgreSQL Database Server esetében

Ez a tervezet hozzárendel egy [Azure-szabályzat-definíciót,](../../../policy/overview.md) amely segít biztosítani a PostgreSQL adatbázis-kiszolgálók naplókapcsolatait.

- A PostgreSQL adatbázis-kiszolgálókon engedélyezni kell a naplókapcsolatokat

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Annak biztosítása, hogy a "log_disconnections" kiszolgálóparaméter "ON" lesz a PostgreSQL Database Server esetében

Ez a tervezet hozzárendel egy [Azure-szabályzat-definíciót,](../../../policy/overview.md) amely segít biztosítani a PostgreSQL adatbázis-kiszolgálók naplózási kapcsolatbontását.

- A PostgreSQL adatbázis-kiszolgálók on-zt.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Annak biztosítása, hogy a "log_duration" kiszolgálóparaméter "ON" lesz a PostgreSQL Database Server esetében

Ez a tervezet hozzárendel egy [Azure-szabályzat](../../../policy/overview.md) definícióját, amely segít biztosítani, hogy a PostgreSQL adatbázis-kiszolgálók naplózzák a befejezett utasítások időtartamát.

- A PostgreSQL adatbázis-kiszolgálók naplóidőtartamát engedélyezni kell

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Annak biztosítása, hogy a "connection_throttling" kiszolgálóparaméter "ON" lesz a PostgreSQL Database Server esetében

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít enyhíteni a PostgreSQL adatbázis-kiszolgálók elleni találgatásos támadásokat. [Azure Policy](../../../policy/overview.md)

- A PostgreSQL adatbázis-kiszolgálók on-ra engedélyezni kell a kapcsolatszabályozást

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Annak biztosítása, hogy az Azure Active Directory rendszergazdája konfigurálva van

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy az Azure Active Directory rendszergazdája ki van építve az SQL-kiszolgálókhoz. [Azure Policy](../../../policy/overview.md) A CIS Microsoft Azure Foundations Benchmark tartalmazza ezt a javaslatot; ez azonban a [4.8.](#48-ensure-that-azure-active-directory-admin-is-configured)

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Naplóprofil létezése

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy egy naplóprofil létezik az összes Azure-előfizetéshez. [Azure Policy](../../../policy/overview.md) 

- Az Azure-előfizetések nek rendelkezniük kell egy naplóprofillal a tevékenységnaplóhoz

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Annak biztosítása, hogy a tevékenységnapló-megőrzés 365 napos vagy annál hosszabb legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a tevékenységnaplók legalább egy évig megmaradnak. [Azure Policy](../../../policy/overview.md)

- A tevékenységnaplót legalább egy évig meg kell őrizni

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Annak biztosítása, hogy az ellenőrzési profil minden olyan tevékenységet megragad,

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a naplóprofil megfelelően konfigurálva van. [Azure Policy](../../../policy/overview.md)

- Az Azure Monitor naplóprofiljának naplót kell gyűjtenie az "írás", a "törlés" és a "művelet" kategóriákhoz.

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Annak biztosítása, hogy a naplóprofil minden régióban rögzíti a tevékenységnaplókat, beleértve a globális

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a naplóprofil megfelelően konfigurálva van. [Azure Policy](../../../policy/overview.md)

- Az Azure Monitornak minden régióból össze kell gyűjtenie a tevékenységnaplókat

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Annak biztosítása, hogy az Azure KeyVault naplózása "Engedélyezve" legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a diagnosztikai naplók engedélyezve vannak a kulcstartókhoz. [Azure Policy](../../../policy/overview.md)

- A Key Vault diagnosztikai naplóit engedélyezni kell

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Annak biztosítása, hogy a Hálózatfigyelő "Engedélyezve" legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a Network Watcher engedélyezve van minden olyan régióban, ahol erőforrások vannak telepítve. [Azure Policy](../../../policy/overview.md) Ehhez a házirendhez olyan paramétertömbre van szükség, amely az összes megfelelő régiót meghatározza. Ebben a házirend-kezdeményezés definíciójában az alapértelmezett érték az "eastus".

- A Hálózatfigyelőt engedélyezni kell

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Az "OPERÁCIÓS rendszer lemezének" titkosítása

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a lemeztitkosítás engedélyezése a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- A lemeztitkosítást virtuális gépeken kell alkalmazni

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Az "adatlemezek" titkosításának biztosítása

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a lemeztitkosítás engedélyezése a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- A lemeztitkosítást virtuális gépeken kell alkalmazni

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 A "nem csatlakoztatott lemezek" titkosításának biztosítása

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít a nem csatlakoztatott lemezek titkosításának biztosításában. [Azure Policy](../../../policy/overview.md)

- A nem csatlakoztatott lemezeket titkosítani kell

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Annak biztosítása, hogy csak jóváhagyott bővítmények legyenek telepítve

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy csak a jóváhagyott virtuálisgép-bővítmények vannak telepítve. [Azure Policy](../../../policy/overview.md) Ehhez a házirendhez olyan paramétertömbre van szükség, amely megadja az összes jóváhagyott virtuálisgép-bővítményt. Ez a házirend-kezdeményezés definíciója tartalmazza a javasolt alapértelmezett értékeket, amelyeket az ügyfeleknek ellenőrizniük kell. 

 - Csak jóváhagyott virtuálisgép-bővítményeket kell telepíteni

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Annak biztosítása, hogy az összes virtuális géphez a legújabb operációsrendszer-javításokat alkalmazzák

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a rendszerfrissítések telepítve vannak a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- A rendszerfrissítéseket telepíteni kell a gépeken

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Annak biztosítása, hogy az összes virtuális gép végpontvédelme telepítve legyen

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani a végpontvédelem engedélyezve van a virtuális gépeken. [Azure Policy](../../../policy/overview.md)

- Hiányzó végpontvédelem figyelése az Azure Security Centerben

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 A kulcstartó helyreállításának biztosítása

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a key vault-objektumok véletlen törlés esetén helyreállíthatók. [Azure Policy](../../../policy/overview.md)

- A Key Vault-objektumoknak helyreállíthatónak kell lenniük

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Szerepköralapú hozzáférés-vezérlés engedélyezése (RBAC) az Azure Kubernetes-szolgáltatásokon belül

Ez a tervezet hozzárendel egy [Azure Policy-definíciót,](../../../policy/overview.md) amely segít biztosítani, hogy a szerepköralapú hozzáférés-vezérlés a Kubernetes szolgáltatásfürtök felügyelt engedélyeihez használható legyen.

- \[Előzetes\]verzió : A szerepköralapú hozzáférés-vezérlést (RBAC) a Kubernetes-szolgáltatásokon kell használni

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Annak biztosítása, hogy a webalkalmazás az összes HTTP-forgalmat HTTPS-re irányítsa át az Azure App Service-ben

Ez a tervezet hozzárendel egy Azure Policy-definíciót, amely segít biztosítani, hogy a webalkalmazások csak biztonságos kapcsolatokon keresztül érhetők el. [Azure Policy](../../../policy/overview.md)

- A webalkalmazás csak HTTPS-en keresztül érhető el

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Annak biztosítása, hogy a webalkalmazás a TLS titkosítás legújabb verzióját használja

Ez a tervezet hozzárendeli az Azure Policy-definíciókat, amelyek segítségével biztosíthatja, hogy a webalkalmazások a legújabb TLS-verziót használják. [Azure Policy](../../../policy/overview.md)

- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS verziót kell használni a Függvényalkalmazásban
- A legújabb TLS-verziót a Web App ban kell használni

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Annak biztosítása, hogy a webalkalmazás "Ügyféltanúsítványok (Bejövő ügyféltanúsítványok)" beállítással "Be" értékre van állítva

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja, hogy csak az ügyfelek érvényes tanúsítványok at tudnak elérni egy webalkalmazás. [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy az API-alkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítással rendelkezik"
- Győződjön meg arról, hogy a Function alkalmazás "Ügyféltanúsítványok (bejövő ügyféltanúsítványok)" beállítással rendelkezik"
- Annak biztosítása, hogy a WEB alkalmazás "Ügyféltanúsítványok (Bejövő ügyféltanúsítványok)" beállítását "Be" értékre állítsa

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Annak biztosítása, hogy az Azure Active Directoryval való regisztráció engedélyezve van az App Service szolgáltatásban

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja, hogy a webalkalmazások felügyelt identitást használnak. [Azure Policy](../../../policy/overview.md)

- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval az API-alkalmazásban
- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval a Függvényalkalmazásban
- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval a WEB App alkalmazásban

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Annak biztosítása, hogy a ".Net Framework" verzió a legújabb, ha a webalkalmazás részeként használják

Ez a tervezet hozzárendeli az Azure Policy-definíciókat, amelyek segítségével biztosíthatja, hogy a webalkalmazások a . [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy a ".Net Framework" verzió a legújabb, ha az API-alkalmazás részeként használja
- Győződjön meg arról, hogy a ".Net Framework" verzió a legújabb, ha a Függvényalkalmazás részeként használják
- Győződjön meg arról, hogy a ".Net Framework" verzió a legújabb verzió, ha a webalkalmazás részeként használja

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha a webalkalmazás futtatásához használják

Ez a tervezet hozzárendeli az Azure Policy-definíciókat, amelyek segítségével biztosíthatja, hogy a webalkalmazások a PHP legújabb verzióját használják. [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha az Api alkalmazás részeként használják
- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha a Függvény alkalmazás részeként használják
- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha a WEB alkalmazás részeként használják

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Annak biztosítása, hogy a "Python verzió" a legújabb, ha a webalkalmazás futtatásához használják

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja, hogy a webalkalmazások a Python legújabb verzióját használják. [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az Api-alkalmazás részeként használják
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Függvény alkalmazás részeként használják
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként használja

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Annak biztosítása, hogy a "Java verzió" a legújabb, ha a webalkalmazás futtatásához használják

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja, hogy a webalkalmazások a Java legújabb verzióját használják. [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha az Api alkalmazás részeként használják
- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha a Funtion alkalmazás részeként használják
- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha a webalkalmazás részeként használja

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Annak biztosítása, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatásához használják

Ez a tervezet hozzárendeli az Azure Policy-definíciók, amelyek segítségével biztosíthatja, hogy a webalkalmazások a HTTP legújabb verzióját használják. [Azure Policy](../../../policy/overview.md)

- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha az Api alkalmazás futtatásához használják
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a Függvény alkalmazás futtatásához használják
- Győződjön meg arról, hogy a "HTTP-verzió" a legújabb, ha a webalkalmazás futtatásához használják


## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a CIS Microsoft Azure Foundations Benchmark tervezet vezérlőleképezését, az alábbi cikkekben megtudhatja a tervezetet, vagy az Azure Portalon az Azure Policy webhelyen a kezdeményezés hozzárendeléséhez:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark blueprint – Áttekintés a](./index.md)
> [CIS Microsoft Azure Foundations Benchmark tervezetében – Lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)