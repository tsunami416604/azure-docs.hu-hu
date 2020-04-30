---
title: Az Azure Security teljesítményteszt tervezetének mintája
description: Az Azure biztonsági teljesítményteszt tervének Azure Policyra való leképezésének vezérlése.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81538732"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Az Azure Security teljesítményteszt tervrajzi mintájának vezérlése

A következő cikk azt ismerteti, hogyan jelennek meg az Azure-tervezetek az Azure Security teljesítményteszt tervezetének mintája az Azure Security teljesítményteszt-vezérlőkhöz. További információ a vezérlőkről: [Azure biztonsági teljesítményteszt](https://docs.microsoft.com/azure/security/benchmarks/overview).

A következő leképezések az **Azure Security teljesítményteszt** -vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki ** \[az\]előnézetet: az Azure Security teljesítményteszt-javaslatok naplózása és a speciális támogató virtuálisgép-bővítmények** beépített házirend-kezdeményezéssel történő üzembe helyezése.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmazhat, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1 az erőforrások védelme hálózati biztonsági csoportokkal vagy a Virtual Network Azure Firewall használatával

- Az alhálózatokat hálózati biztonsági csoporttal kell társítani
- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni
- A virtuális gépeket egy jóváhagyott virtuális hálózathoz kell csatlakoztatni
- Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Service Bus virtuális hálózati szolgáltatás végpontját kell használnia
- App Service virtuális hálózati szolgáltatás végpontját kell használnia
- SQL Server virtuális hálózati szolgáltatás végpontját kell használnia
- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia
- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia
- Key Vault virtuális hálózati szolgáltatás végpontját kell használnia
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk
- Container Registry virtuális hálózati szolgáltatás végpontját kell használnia
- A virtuális hálózatoknak a megadott virtuális hálózati átjárót kell használniuk
- A Kubernetes-szolgáltatásokban meg kell határozni a jóváhagyott IP-tartományokat
- \[Előzetes\]verzió: a virtuális gépen lévő IP-továbbítást le kell tiltani
- Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A felügyeleti portokat be kell zárni a virtuális gépeken

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2 a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

- Network Watcher engedélyezni kell

## <a name="13-protect-critical-web-applications"></a>1,3 a kritikus webalkalmazások elleni védelem

- Győződjön meg arról, hogy a (z) "on" értékre van állítva a webalkalmazás "Ügyféltanúsítványok (bejövő Ügyféltanúsítványok)" értéke
- A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését
- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz
- A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni API Apps

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4 ismert kártékony IP-címmel folytatott kommunikáció megtagadása

- DDoS Protection a standardot engedélyezni kell
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni

## <a name="15-record-network-packets-and-flow-logs"></a>1,5 hálózati csomagok és adatforgalmi naplók rögzítése

- Network Watcher engedélyezni kell

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11 automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

- A Windows rendszerű virtuális gépek konfigurációinak naplózása a "biztonsági beállítások – hálózati hozzáférés" című cikkben ismertetett előfeltételek központi telepítése
- A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – Microsoft hálózati ügyfél"
- Előfeltételek telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – hálózati biztonság" című szakaszban
- A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – hálózati biztonság" lehetőségnél
- Előfeltételek telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – Microsoft hálózati kiszolgáló"
- A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – Microsoft hálózati kiszolgáló"
- Előfeltételek telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a Felügyeleti sablonok hálózatban
- A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a következőben: "Felügyeleti sablonok-Network"

## <a name="22-configure-central-security-log-management"></a>2,2 a központi biztonsági naplók felügyeletének konfigurálása

- A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken
- A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets
- A Windows rendszerű virtuális gépek naplózásának előfeltételei, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva
- Azure Monitor log-profilnak össze kell gyűjtenie a naplókat a "Write", "Delete" és "Action" kategóriákba.
- Azure Monitor az összes régióból gyűjti a tevékenység naplóit
- Az előfizetéshez engedélyezni kell a Log Analytics figyelő ügynök automatikus üzembe helyezését

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3 Azure-erőforrások naplózásának engedélyezése

- A Azure Data Lake Store lévő diagnosztikai naplókat engedélyezni kell
- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell
- A IoT Hub lévő diagnosztikai naplókat engedélyezni kell
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell
- A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell
- Az Event hub diagnosztikai naplóit engedélyezni kell
- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.
- A App Services lévő diagnosztikai naplókat engedélyezni kell
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell
- A Key Vault lévő diagnosztikai naplókat engedélyezni kell
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- Az SQL Server naplózását engedélyezni kell
- Diagnosztikai beállítás naplózása

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4 biztonsági naplók gyűjtése az operációs rendszerekből

- Az előfizetéshez engedélyezni kell a Log Analytics figyelő ügynök automatikus üzembe helyezését
- A Log Analytics ügynöknek telepítve kell lennie a virtuális gépeken
- A Log Analytics ügynöknek telepítve kell lennie a Virtual Machine Scale Sets
- A Windows rendszerű virtuális gépek naplózásának előfeltételei, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken a Log Analytics ügynök nem a várt módon van csatlakoztatva

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7 riasztások engedélyezése rendellenes tevékenységhez

- Security Center Standard díjszabási szintet kell kiválasztani
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál

## <a name="28-centralize-anti-malware-logging"></a>2,8 kártevő szoftverek elleni naplózása

- Az Azure-hoz készült Microsoft antimalware-t úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat
- Hiányzó Endpoint Protection figyelése Azure Security Center
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1 a rendszergazdai fiókok leltárának karbantartása

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="33-use-dedicated-administrative-accounts"></a>3,3 dedikált rendszergazdai fiókok használata

- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmaz csak a megadott tagokat.
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmaz csak a megadott tagokat
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5 többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7 naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

- Security Center Standard díjszabási szintet kell kiválasztani

## <a name="39-use-azure-active-directory"></a>3,9 Azure Active Directory használata

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez
- Győződjön meg arról, hogy a regisztráció a Azure Active Directory engedélyezve van az API-alkalmazásban
- Győződjön meg arról, hogy a regisztráció a Azure Active Directory engedélyezve van a webalkalmazásban
- Győződjön meg arról, hogy engedélyezve van-e a regisztráció a Azure Active Directory függvényalkalmazás

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10 a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1 bizalmas adatok leltárának fenntartása

- Az SQL-adatbázisokban lévő bizalmas adatokat osztályozni kell

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4 minden bizalmas adat titkosítása az átvitel során

- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5 a bizalmas adatok azonosítására szolgáló aktív felderítési eszköz használata

- Az SQL-adatbázisokban lévő bizalmas adatokat osztályozni kell
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6 az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

- Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban
- Egyéni RBAC-szabályok használatának naplózása

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8 bizalmas adatok titkosítása a nyugalmi állapotban

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- A nem csatolt lemezeket titkosítani kell
- Az SQL Server TDE-védőt a saját kulccsal kell titkosítani
- A felügyelt SQL-példányok TDE-védőjét a saját kulcsával kell titkosítani
- Az Automation-fiók változóit titkosítani kell
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9 a kritikus Azure-erőforrások változásainak naplózása és riasztása

- Azure Monitor az összes régióból gyűjti a tevékenység naplóit

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1 automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- \[Az\] előnézeti sebezhetőségek felmérését engedélyezni kell a Virtual machines
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2 az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a függvényalkalmazás részeként van használatban
- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a .NET-keretrendszer verziója a legújabb, ha az API-alkalmazás részeként van használatban

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3 automatikus, külső gyártótól származó szoftverfrissítési megoldás telepítése

- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a "PHP-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Java-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a Java-verzió a legújabb, ha az API-alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a webalkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha a Function alkalmazás részeként van használatban
- Győződjön meg arról, hogy a "Python-verzió" a legújabb, ha az API-alkalmazás részeként van használatban
- A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5 kockázatkezelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell

## <a name="68-use-only-approved-applications"></a>6,8 csak jóváhagyott alkalmazások használata

- Security Center Standard díjszabási szintet kell kiválasztani
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="69-use-only-approved-azure-services"></a>6,9 csak jóváhagyott Azure-szolgáltatások használata

- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra

## <a name="610-implement-approved-application-list"></a>6,10 jóváhagyott alkalmazások listájának implementálása

- Security Center Standard díjszabási szintet kell kiválasztani
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3 biztonságos Azure-erőforrás-konfigurációk karbantartása

- \[Előzetes\]verzió: a pod biztonsági szabályzatokat meg kell határozni a Kubernetes-szolgáltatásokban

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4 biztonságos operációsrendszer-konfigurációk karbantartása

- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9 Az Azure-szolgáltatások automatizált konfigurációs monitorozásának megvalósítása

- \[Előzetes\]verzió: a pod biztonsági szabályzatokat meg kell határozni a Kubernetes-szolgáltatásokban

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10 az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell

## <a name="711-manage-azure-secrets-securely"></a>7,11 Azure-titkok biztonságos kezelése

- Key Vault objektumoknak helyreállítható kell lenniük

## <a name="712-manage-identities-securely-and-automatically"></a>7,12 az identitások biztonságos és automatikus kezelése

- Felügyelt identitást kell használni a függvényalkalmazás
- A felügyelt identitást a webalkalmazásban kell használni
- A felügyelt identitást az API-alkalmazásban kell használni

## <a name="81-use-centrally-managed-anti-malware-software"></a>8,1 központilag felügyelt kártevő szoftverek használata

- Hiányzó Endpoint Protection figyelése Azure Security Center
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2 a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

- Security Center Standard díjszabási szintet kell kiválasztani

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3 a kártevő szoftverek és az aláírások frissítésének ellenőrzése

- Az Azure-hoz készült Microsoft antimalware-t úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat

## <a name="91-ensure-regular-automated-back-ups"></a>9,1 gondoskodjon a rendszeres automatizált Back UPS-ről

- A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB
- A Azure Backup engedélyezni kell a Virtual Machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2 a teljes rendszerbiztonsági másolatok és az ügyfelek által felügyelt kulcsok biztonsági mentése

- A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB
- A Azure Backup engedélyezni kell a Virtual Machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4 a biztonsági másolatok és az ügyfelek által felügyelt kulcsok védelmének biztosítása

- Key Vault objektumoknak helyreállítható kell lenniük

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2 incidens-pontozási és rangsorolási eljárás létrehozása

- Security Center Standard díjszabási szintet kell kiválasztani

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4 biztonsági incidensek elérhetőségi adatainak megadása és riasztási értesítések konfigurálása biztonsági incidensekhez

- Az előfizetéshez meg kell adni egy biztonsági kapcsolattartási e-mail címet
- Az előfizetéshez meg kell adni egy biztonsági kapcsolatfelvételi telefonszámot
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban
- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure biztonsági teljesítményteszt tervének vezérlési leképezését, keresse fel a Azure Policy a Azure Portal a kezdeményezés hozzárendeléséhez:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.