---
title: Az Azure Security Benchmark mintavezérlők
description: Az Azure Security Benchmark blueprint minta az Azure Policy-hez való hozzárendelésének vezérlése.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538732"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Az Azure Security Benchmark tervezetminta leképezésének vezérlése

A következő cikk bemutatja, hogy az Azure Blueprints Azure Security Benchmark minta minta leképezi az Azure Security Benchmark vezérlők. A vezérlőkről további információt az Azure Security Benchmark című [témakörben talál.](https://docs.microsoft.com/azure/security/benchmarks/overview)

A következő leképezések az **Azure Security Benchmark** vezérlők. A jobb oldali navigációs sávsegítségével közvetlenül egy adott vezérlőleképezésre ugorhat. A leképezett vezérlők egy [Azure Policy](../../../policy/overview.md) kezdeményezéssel vannak megvalósítva. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** az Azure Portalon, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az ** \[előzetes verzió:\]Audit Azure Security Benchmark javaslatokat, és üzembe helyezése konkrét támogató virtuálisgép-bővítmények** beépített szabályzat kezdeményezés.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure-szabályzat-definícióhoz](../../../policy/overview.md) vannak társítva. Ezek a szabályzatok segíthetnek [az ellenőrzésnek való megfelelés értékelésében;](../../../policy/how-to/get-compliance-data.md) azonban gyakran nincs 1:1 vagy teljes egyezés egy vezérlő és egy vagy több házirend között. Az Azure-szabályzatban **megfelelő** csak magukra a szabályzatokra vonatkozik; ez nem biztosítja, hogy teljes mértékben megfelel-e a vezérlő összes követelményének. Emellett a megfelelőségi szabvány tartalmazhat olyan vezérlőket, amelyeket jelenleg egyetlen Azure-szabályzat-definíció sem kezel. Ezért az Azure Policy-ben való megfelelőség csak részleges nézet az általános megfelelőségi állapotról. A vezérlők és az Azure Policy-definíciók közötti társítások a megfelelőségi tervezet minta idővel változhat. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md)

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

- Az alhálózatokat hálózati biztonsági csoporthoz kell társítani
- Az adaptive network edzési javaslatokat az internetfelé néző virtuális gépeken kell alkalmazni
- A virtuális gépeket jóváhagyott virtuális hálózathoz kell csatlakoztatni
- Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- A Service Bus nak virtuális hálózati szolgáltatás végpontját kell használnia
- Az App Service-nek virtuális hálózati szolgáltatás végpontját kell használnia
- Az SQL Server nek virtuális hálózati szolgáltatásvégpontot kell használnia
- Az Event Hubnak virtuális hálózati szolgáltatás végpontját kell használnia
- A Cosmos DB-nek virtuális hálózati szolgáltatás végpontját kell használnia
- A Key Vaultnak virtuális hálózati szolgáltatás végpontját kell használnia
- Tárfiókokhoz való korlátlan hálózati hozzáférés naplózása
- A tárfiókoknak virtuális hálózati szolgáltatás végpontját kell használniuk
- A tárolóbeállításnak virtuális hálózati szolgáltatás végpontját kell használnia
- A virtuális hálózatoknak meghatározott virtuális hálózati átjárót kell használniuk
- Az engedélyezett IP-tartományokat a Kubernetes-szolgáltatásokban kell meghatározni
- \[Előzetes\]verzió: Az IP-továbbítást le kell tiltani a virtuális gépen
- Az internetre néző virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A felügyeleti portokat be kell zárni a virtuális gépeken

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

- A Hálózatfigyelőt engedélyezni kell

## <a name="13-protect-critical-web-applications"></a>1.3 A kritikus webes alkalmazások védelme

- Annak biztosítása, hogy a WEB alkalmazás "Ügyféltanúsítványok (Bejövő ügyféltanúsítványok)" beállítását "Be" értékre állítsa
- A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a webalkalmazásokhoz
- A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a Függvényalkalmazásokhoz
- A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokesetében
- A távoli hibakeresést ki kell kapcsolni a függvényalkalmazások esetében
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazásokesetében

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

- A DDoS Protection Standard-ot engedélyezni kell
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- Az adaptive network edzési javaslatokat az internetfelé néző virtuális gépeken kell alkalmazni

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Hálózati csomagok és folyamatnaplók rögzítése

- A Hálózatfigyelőt engedélyezni kell

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

- Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Hálózati hozzáférés" szolgáltatásban
- A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Microsoft hálózati ügyfél" párbeszédpanelen
- Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Hálózati biztonság" szolgáltatásban
- A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Hálózati biztonság" területen
- Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Biztonsági beállítások – Microsoft Network Server" szolgáltatásban
- A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Biztonsági beállítások – Microsoft Network Server" párbeszédpanelen
- Előfeltételek telepítése a Windows virtuális gépek konfigurációinak naplózásához a "Felügyeleti sablonok - Hálózat" szolgáltatásban
- A Windows virtuális gépek konfigurációinak naplózási eredményeinek megjelenítése a "Felügyeleti sablonok - Hálózat" területen

## <a name="22-configure-central-security-log-management"></a>2.2 A központi biztonsági napló kezelésének konfigurálása

- A Log Analytics-ügynököt virtuális gépekre kell telepíteni
- A Log Analytics-ügynököt telepíteni kell a virtuálisgép-méretezési készletekre
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyeken a Log Analytics-ügynök nem a várt módon csatlakozik
- A Naplókezelő ügynök által várt módon nem csatlakoztatott Windows-virtuális gépek naplózási eredményeinek megjelenítése
- Az Azure Monitor naplóprofiljának naplót kell gyűjtenie az "írás", a "törlés" és a "művelet" kategóriákhoz.
- Az Azure Monitornak minden régióból össze kell gyűjtenie a tevékenységnaplókat
- A Log Analytics-figyelőügynök automatikus kiépítését engedélyezni kell az előfizetésben

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Az Azure-erőforrások naplózásának engedélyezése

- Az Azure Data Lake Store diagnosztikai naplóit engedélyezni kell
- A Logic Apps diagnosztikai naplóit engedélyezni kell
- Az IoT Hub diagnosztikai naplóit engedélyezni kell
- A Batch-fiókok diagnosztikai naplóit engedélyezni kell
- A virtuálisgép-méretezési készletek diagnosztikai naplóit engedélyezni kell
- Az Event Hub diagnosztikai naplóit engedélyezni kell
- A keresési szolgáltatások diagnosztikai naplóit engedélyezni kell
- Az App Services diagnosztikai naplóit engedélyezni kell
- A Data Lake Analytics diagnosztikai naplóit engedélyezni kell
- A Key Vault diagnosztikai naplóit engedélyezni kell
- A Service Bus diagnosztikai naplóit engedélyezni kell
- Az Azure Stream Analytics diagnosztikai naplóit engedélyezni kell
- Az SQL-kiszolgálón történő naplózást engedélyezni kell
- Diagnosztikai beállítás naplózása

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Biztonsági naplók gyűjtése az operációs rendszerekről

- A Log Analytics-figyelőügynök automatikus kiépítését engedélyezni kell az előfizetésben
- A Log Analytics-ügynököt virtuális gépekre kell telepíteni
- A Log Analytics-ügynököt telepíteni kell a virtuálisgép-méretezési készletekre
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyeken a Log Analytics-ügynök nem a várt módon csatlakozik
- A Naplókezelő ügynök által várt módon nem csatlakoztatott Windows-virtuális gépek naplózási eredményeinek megjelenítése

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Riasztás engedélyezése rendellenes tevékenységre

- A Security Center szokásos tarifacsomagját kell kiválasztani
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- A speciális adatbiztonságot engedélyezni kell az SQL által felügyelt példányokon
- A komplex veszélyforrások elleni védelem típusait az SQL-kiszolgáló speciális adatbiztonsági beállításaiban "All" típusúra kell állítani.
- A komplex veszélyforrások elleni védelem típusait "All" típusúra kell állítani az SQL felügyelt példányok speciális adatbiztonsági beállításaiban.

## <a name="28-centralize-anti-malware-logging"></a>2.8 A kártevőirtó naplózás központosítása

- Az Azure-hoz létrehozott Microsoft Antimalware programot úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat
- Hiányzó végpontvédelem figyelése az Azure Security Centerben
- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Az adminisztratív számlák leltárának fenntartása

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Dedikált felügyeleti fiókok használata

- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyekben a Rendszergazdák csoport nem csak a megadott tagokat tartalmazza
- A Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyben a Rendszergazdák csoport nem csak a megadott tagokat tartalmazza
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Olyan Windows virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a Rendszergazdák csoport nem tartalmazza az összes megadott tagot
- Előfeltételek telepítése olyan Windows virtuális gépek naplózásához, amelyekben a Rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- A Rendszergazdák csoport által a megadott tagok bármelyikét tartalmazó Windows virtuális gépek naplózási eredményeinek megjelenítése
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Többtényezős hitelesítés használata az Azure Active Directory-alapú hozzáféréshez

- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban
- Az MFA-nak engedélyeznie kell az előfizetéshez írási engedélyekkel rendelkező fiókokat
- Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

- A Security Center szokásos tarifacsomagját kell kiválasztani

## <a name="39-use-azure-active-directory"></a>3.9 Az Azure Active Directory használata

- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz
- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez
- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval az API-alkalmazásban
- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval a WEB App alkalmazásban
- Annak ellenőrzése, hogy engedélyezve van-e a Regisztráció az Azure Active Directoryval a Függvényalkalmazásban

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Az érzékeny információk leltárának karbantartása

- Az SQL-adatbázisokban lévő bizalmas adatokat

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Az összes bizalmas információ titkosítása az átvitel során

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót a Web App ban kell használni
- A legújabb TLS verziót kell használni a Függvényalkalmazásban
- A Függvényalkalmazás csak HTTPS-en keresztül érhető el
- A webalkalmazás csak HTTPS-en keresztül érhető el
- Az API-alkalmazás csak HTTPS-en keresztül érhető el
- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a PostgreSQL adatbázis-kiszolgálókon
- Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Aktív felderítési eszköz használata a bizalmas adatok azonosítására

- Az SQL-adatbázisokban lévő bizalmas adatokat
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- A speciális adatbiztonságot engedélyezni kell az SQL által felügyelt példányokon

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Az Azure RBAC használata az erőforrásokhoz való hozzáférés szabályozásához

- A szerepköralapú hozzáférés-vezérlést (RBAC) a Kubernetes-szolgáltatásokon kell használni
- Egyéni RBAC-szabályok használatának naplózása

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Bizalmas információk titkosítása nyugalmi

- Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell
- A lemeztitkosítást virtuális gépeken kell alkalmazni
- A nem csatlakoztatott lemezeket titkosítani kell
- AZ SQL server TDE protector-t saját kulccsal kell titkosítani
- Az SQL felügyelt példány TDE-védőjét saját kulccsal kell titkosítani
- Az automatizálási fiókváltozókat titkosítani kell
- A Service Fabric-fürtöknek encryptAndSign-re kell állítaniuk a ClusterProtectionLevel tulajdonságot

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Naplózás és riasztás a kritikus Azure-erőforrások változásairól

- Az Azure Monitornak minden régióból össze kell gyűjtenie a tevékenységnaplókat

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Automatikus biztonsági résellenőrző eszközök futtatása

- A biztonsági rés felmérését engedélyezni kell az SQL-kiszolgálókon
- A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon
- \[Az\] előzetes biztonsági rés felmérését engedélyezni kell a virtuális gépeken
- Az SQL-kiszolgáló biztonsági résértékelési beállításainak e-mail címet kell tartalmazniuk a vizsgálati jelentések fogadásához

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Automatikus operációsrendszer-javítás-kezelési megoldás telepítése

- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell
- Győződjön meg arról, hogy a ".NET Framework" verzió a legújabb, ha a Függvényalkalmazás részeként használja
- Győződjön meg arról, hogy a ".NET Framework" verzió a legújabb verzió, ha a webalkalmazás részeként használja
- Győződjön meg arról, hogy a ".NET Framework" verzió a legújabb, ha az API-alkalmazás részeként használja

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha az Api alkalmazás részeként használják
- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha a WEB alkalmazás részeként használják
- Győződjön meg arról, hogy a "PHP verzió" a legújabb, ha a Függvény alkalmazás részeként használják
- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha a webalkalmazás részeként használja
- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha a Függvény alkalmazás részeként használják
- Győződjön meg arról, hogy a "Java verzió" a legújabb, ha az Api alkalmazás részeként használják
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként használja
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Függvény alkalmazás részeként használják
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az Api-alkalmazás részeként használják
- A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

- A biztonsági réseket a biztonsági résfelmérési megoldással kell orvosolni
- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani
- A tárolóbiztonsági konfigurációk biztonsági réseit ki kell újítani
- Az SQL-adatbázisok biztonsági réseit ki kell újítani

## <a name="68-use-only-approved-applications"></a>6.8 Csak jóváhagyott alkalmazásokat használjon

- A Security Center szokásos tarifacsomagját kell kiválasztani
- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="69-use-only-approved-azure-services"></a>6.9 Csak jóváhagyott Azure-szolgáltatások használata

- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba
- A tárfiókokat át kell telepíteni az új Azure Resource Manager-erőforrásokba

## <a name="610-implement-approved-application-list"></a>6.10 A jóváhagyott alkalmazáslista megvalósítása

- A Security Center szokásos tarifacsomagját kell kiválasztani
- Az Adaptív alkalmazásvezérlőket engedélyezni kell a virtuális gépeken

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Biztonságos Azure-erőforrás-konfigurációk karbantartása

- \[Előzetes\]verzió : Pod biztonsági házirendek meg kell határozni a Kubernetes Services

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Biztonságos operációsrendszer-konfigurációk karbantartása

- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- A tárolóbiztonsági konfigurációk biztonsági réseit ki kell újítani
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

- \[Előzetes\]verzió : Pod biztonsági házirendek meg kell határozni a Kubernetes Services

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

- A gépek biztonsági beállításainak biztonsági réseit ki kell újítani
- A tárolóbiztonsági konfigurációk biztonsági réseit ki kell újítani
- A virtuálisgép-méretezési csoportok biztonsági beállításainak biztonsági réseit ki kell újítani

## <a name="711-manage-azure-secrets-securely"></a>7.11 Az Azure-titkok biztonságos kezelése

- A Key Vault-objektumoknak helyreállíthatónak kell lenniük

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 A személyazonosságok biztonságos és automatikus kezelése

- Felügyelt identitást kell használni a Függvényalkalmazásban
- A felügyelt identitást a Webappban kell használni
- Felügyelt identitást kell használni az API-alkalmazásban

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Központilag kezelt kártevőirtó szoftverek használata

- Hiányzó végpontvédelem figyelése az Azure Security Centerben
- A végpontvédelmi megoldást virtuálisgép-méretezési csoportokra kell telepíteni

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 A nem számítási Azure-erőforrásokba feltöltendő fájlok elővizsgálata

- A Security Center szokásos tarifacsomagját kell kiválasztani

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 A kártevőirtó szoftverek és aláírások frissítésének biztosítása

- Az Azure-hoz létrehozott Microsoft Antimalware programot úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Rendszeres automatizált biztonsági másolatok biztosítása

- Hosszú távú georedundáns biztonsági mentést engedélyezni kell az Azure SQL-adatbázisokhoz
- A georedundáns biztonsági mentést engedélyezni kell a MySQL-hez készült Azure Database számára
- A georedundáns biztonsági mentést engedélyezni kell a PostgreSQL Azure Database számára
- A georedundáns biztonsági mentést engedélyezni kell a MariaDB Azure Database számára
- Az Azure Backup biztonsági mentést engedélyezni kell a virtuális gépekhez

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Teljes rendszerbiztonsági mentés végrehajtása és az ügyfelek által kezelt kulcsok biztonsági mentése

- Hosszú távú georedundáns biztonsági mentést engedélyezni kell az Azure SQL-adatbázisokhoz
- A georedundáns biztonsági mentést engedélyezni kell a MySQL-hez készült Azure Database számára
- A georedundáns biztonsági mentést engedélyezni kell a PostgreSQL Azure Database számára
- A georedundáns biztonsági mentést engedélyezni kell a MariaDB Azure Database számára
- Az Azure Backup biztonsági mentést engedélyezni kell a virtuális gépekhez

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

- A Key Vault-objektumoknak helyreállíthatónak kell lenniük

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Incidenspontozási és rangsorolási eljárás létrehozása

- A Security Center szokásos tarifacsomagját kell kiválasztani

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

- Az előfizetéshez meg kell adni egy biztonsági kapcsolattartó e-mail címét.
- Az előfizetéshez biztonsági telefonszámát kell megadni
- Az SQL-kiszolgáló speciális adatbiztonsági beállításainak tartalmazniuk kell egy e-mail címet a biztonsági riasztások fogadásához
- Az SQL által felügyelt példány speciális adatbiztonsági beállításainak tartalmazniuk kell egy e-mail címet a biztonsági riasztások fogadásához
- A rendszergazdáknak és az előfizetés-tulajdonosoknak küldött e-mailértesítéseket engedélyezni kell az SQL server speciális adatbiztonsági beállításaiban
- A rendszergazdáknak és az előfizetés-tulajdonosoknak küldött e-mail-értesítéseket engedélyezni kell az SQL felügyelt példányok speciális adatbiztonsági beállításaiban

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Security Benchmark tervezet vezérlőleképezését, az Azure Portalon az Azure Policy webhelyen a kezdeményezés hozzárendeléséhez látogasson el a következő hivatkozásra:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.