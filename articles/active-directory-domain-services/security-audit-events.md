---
title: Biztonsági naplózás engedélyezése az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti a biztonsági naplózást az események naplózásának központosításához elemzésre és riasztásokra az Azure AD tartományi szolgáltatásokban
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654665"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Biztonsági naplózás engedélyezése az Azure Active Directory tartományi szolgáltatásokhoz

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) biztonsági naplózása lehetővé teszi az Azure számára, hogy a biztonsági eseményeket célzott erőforrásokba továbbítsa. Ezek az erőforrások közé tartozik az Azure Storage, az Azure Log Analytics-munkaterületek vagy az Azure Event Hub. Miután engedélyezte a biztonsági naplózási eseményeket, az Azure AD DS elküldi a kiválasztott kategória összes naplózott eseményét a megcélzott erőforrásnak.

Az Azure-tárolóba archiválhatja az eseményeket, és eseményeket streamelhet biztonsági információkba és eseménykezelési (SIEM) szoftverekbe (vagy azzal egyenértékű) az Azure Event Hubs használatával, vagy saját elemzést végezhet, és használhatja az Azure Log Analytics-munkaterületeket az Azure Portalról.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózása csak az Azure Resource Manager-alapú példányok érhetők el. Az áttelepítésről az [Azure AD DS áttelepítése a klasszikus virtuális hálózati modellről az Erőforrás-kezelőbe][migrate-azure-adds]című témakörben talál.

## <a name="security-audit-destinations"></a>Biztonsági ellenőrzési célok

Használhatja az Azure Storage, az Azure Event Hubs vagy az Azure Log Analytics-munkaterületeket az Azure AD DS biztonsági naplózásainak célerőforrásaként. Ezek az úti célok kombinálhatók. Például használhatja az Azure Storage-t a biztonsági naplózási események archiválásához, de egy Azure Log Analytics-munkaterületet az információk rövid távú elemzéséhez és jelentéséhez.

Az alábbi táblázat az egyes célerőforrás-típusokforgatókönyveit ismerteti.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózásának engedélyezése előtt létre kell hoznia a célerőforrást. Ezeket az erőforrásokat az Azure Portalon, az Azure PowerShellben vagy az Azure CLI-n keresztül hozhatja létre.

| Cél erőforrás | Forgatókönyv |
|:---|:---|
|Azure Storage| Ezt a célt akkor kell használni, ha az elsődleges szükség van a biztonsági naplózási események archiválási célokra való tárolása. Más célok archiválási célokra is használhatók, azonban ezek a célok az archiválás elsődleges igényén túlmutató képességeket biztosítanak. <br /><br />Az Azure AD DS biztonsági naplózási eseményeinek engedélyezése előtt először [hozzon létre egy Azure Storage-fiókot.](../storage/common/storage-account-create.md)|
|Azure Event Hubs| Ezt a célt akkor kell használni, ha elsődleges en a biztonsági naplózási eseményeket további szoftverekkel, például adatelemző szoftverrel vagy biztonsági információ-& eseménykezelő szoftverrel kell megosztania.<br /><br />Az Azure AD DS biztonsági naplózási eseményeinek engedélyezése előtt [hozzon létre egy eseményközpontot az Azure Portal használatával](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics-munkaterület| Ezt a célt kell használni, ha az elsődleges igény az Azure Portalon közvetlenül az Azure Portalon végzett biztonságos naplózások elemzése és áttekintése.<br /><br />Mielőtt engedélyezne az Azure AD DS biztonsági naplózási eseményeit, [hozzon létre egy Log Analytics-munkaterületet az Azure Portalon.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Biztonsági naplózási események engedélyezése az Azure Portalon

Az Azure AD DS biztonsági naplózási eseményeinek az Azure Portalon való engedélyezéséhez hajtsa végre az alábbi lépéseket.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózása nem visszamenőleges hatállyal. A múltból származó eseményeket nem lehet visszaolvasni vagy visszajátszani. Az Azure AD DS csak a biztonsági naplózás engedélyezése után előforduló eseményeket küldhet.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Az Azure Portal tetején keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.** Válassza ki a felügyelt tartományt, például *a aaddscontoso.com.*
1. Az Azure AD DS ablakban válassza **diagnosztikai beállítások** a bal oldalon.
1. Alapértelmezés szerint nincs beállítva diagnosztika. Első lépésekhez válassza a **Diagnosztikai beállítás hozzáadása lehetőséget.**

    ![Diagnosztikai beállítás hozzáadása az Azure AD tartományi szolgáltatásokhoz](./media/security-audit-events/add-diagnostic-settings.png)

1. Adja meg a diagnosztikai konfiguráció nevét, például *az aadds-naplózást.*

    Jelölje be a kívánt biztonsági naplózási cél jelölőnégyzetet. Választhat egy Azure Storage-fiók, egy Azure-eseményközpont vagy egy Log Analytics-munkaterület közül. Ezeknek a célerőforrásoknak már létezniük kell az Azure-előfizetésben. Ebben a varázslóban nem hozhat létre célerőforrásokat.

    ![A szükséges célhely és naplózási események típusának engedélyezése](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure-tárhely**
        * Válassza **az Archiválás tárfiókba**lehetőséget, majd a **Konfigurálás**lehetőséget.
        * Válassza ki az **előfizetést** és a **storage-fiókot,** amelyet a biztonsági naplózási események archiválásához kíván használni.
        * Ha készen áll, válassza **az OK gombot.**
    * **Azure eseményközpontok**
        * Válassza **az Adatfolyam eseményközpontba**lehetőséget, majd a **Configure (Konfigurálás)** lehetőséget.
        * Válassza ki az **Előfizetés** és az **Eseményközpont névterét.** Szükség esetén válasszon egy **eseményközpont-nevet,** majd **az Eseményközpont házirendjének nevét**is.
        * Ha készen áll, válassza **az OK gombot.**
    * **Azure Log Analytic munkaterületek**
        * Válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget, majd válassza ki a biztonsági naplózási események tárolásához használni kívánt **Előfizetés-** és **LogAnalytics-munkaterületet.**

1. Válassza ki az adott célerőforráshoz felvenni kívánt naplókategóriákat. Ha elküldi a naplózási eseményeket egy Azure Storage-fiókba, konfigurálhat egy adatmegőrzési szabályzatot is, amely meghatározza az adatok megőrzéséhez. Az alapértelmezett *0* beállítás megőrzi az összes adatot, és egy idő után nem forgatja el az eseményeket.

    Az egyes megcélzott erőforrásokhoz egyetlen konfiguráción belül különböző naplókategóriákat választhat. Ezzel a funkcióval kiválaszthatja, hogy mely naplókat szeretné megtartani a Log Analytics szolgáltatáshoz, és mely kategóriákat szeretné archiválni, például.

1. Ha végzett, válassza a **Mentés** gombot a módosítások véglegesítéséhez. A célerőforrások a konfiguráció mentése után nem sokkal megkezdik az Azure AD DS biztonsági naplózási eseményeinek fogadását.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Biztonsági naplózási események engedélyezése az Azure PowerShell használatával

Az Azure AD DS biztonsági naplózási eseményeinek az Azure PowerShell használatával történő engedélyezéséhez hajtsa végre az alábbi lépéseket. Szükség esetén [telepítse az Azure PowerShell-modult, és csatlakozzon az Azure-előfizetéshez.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózása nem visszamenőleges hatállyal. A múltból származó eseményeket nem lehet visszaolvasni vagy visszajátszani. Az Azure AD DS csak a biztonsági naplózás engedélyezése után előforduló eseményeket küldhet.

1. Hitelesítse magát [Azure-előfizetésében a Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmag használatával. Amikor a rendszer kéri, adja meg a fiók hitelesítő adatait.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Hozza létre a célerőforrást a biztonsági naplózási eseményekhez.

    * **Azure storage** - [Tárfiók létrehozása az Azure PowerShell használatával](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Az Azure eseményközpontok** - [hozzon létre egy eseményközpontot az Azure PowerShell használatával.](../event-hubs/event-hubs-quickstart-powershell.md) Előfordulhat, hogy a [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) parancsmag használatával is létre kell hoznia egy engedélyezési szabályt, amely az Azure AD DS-engedélyeket ad az eseményközpont *névteréhez.* Az engedélyezési szabálynak **tartalmaznia**kell a Manage , **Listen**és **Send** jogokat.

        > [!IMPORTANT]
        > Győződjön meg arról, hogy az engedélyezési szabályt az eseményközpont névterében állítja be, és nem magát az eseményközpontot.

    * **Az Azure Log Analytic munkaterületek** - [Hozzon létre egy Log Analytics-munkaterületet az Azure PowerShell használatával.](../azure-monitor/learn/quick-create-workspace-posh.md)

1. Az Azure AD DS felügyelt tartományerőforrás-azonosítójának beszereznie a [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) parancsmag használatával. Hozzon létre egy $aadds nevű *változót. ResourceId* az érték megtartásához:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Konfigurálja az Azure Diagnosztikai beállításokat a [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) parancsmag használatával az Azure AD Tartományszolgáltatások biztonsági naplózási eseményeinek célerőforrásának használatához. A következő példákban a változó *$aadds. A ResourceId* az előző lépésből származik.

    * **Azure storage** – a *storageAccountId* lecserélése a tárfiók nevére:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure-eseményközpontok** – Cserélje *le az eventHubName-t* az eseményközpont és az *eventHubRuleId* nevére az engedélyezési szabály azonosítójával:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytic munkaterületek** – cserélje le a *munkaterület-azonosítót* a Log Analytics-munkaterület azonosítójával:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Biztonsági naplózási események lekérdezése és megtekintése az Azure Monitor használatával

Napló Analitikus munkaterületek segítségével megtekintheti és elemezheti a biztonsági naplózási események et az Azure Monitor és a Kusto lekérdezési nyelv használatával. Ez a lekérdezési nyelv írásvédett használatra készült, és könnyen olvasható szintaxissal rendelkezik. A Kusto lekérdezési nyelveinek első lépéseiről az alábbi cikkekben talál további információt:

* [Az Azure Monitor dokumentációja](https://docs.microsoft.com/azure/azure-monitor/)
* [Ismerkedés a Log Analytics szolgáltatással az Azure Monitorban](../azure-monitor/log-query/get-started-portal.md)
* [A naplólekérdezések első lépései az Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)
* [Irányítópultok létrehozása és megosztása Log Analytics-adatokból](../azure-monitor/learn/tutorial-logs-dashboards.md)

A következő mintalekérdezések segítségével az Azure AD DS biztonsági naplózási eseményeinek elemzésének megkezdéséhez használható.

### <a name="sample-query-1"></a>1. mintalekérdezés

Az elmúlt hét nap összes fiókzárolási eseményének megtekintése:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>2. mintalekérdezés

Tekintse meg az összes fiók zárolási események (*4740*) között február 3, 2020 at 9 és 2020. február 10-én éjfélkor, a nap és az idő szerint rendezve:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>3. mintalekérdezés

A fiókbejelentkezési események megtekintése hét nappal ezelőtt (mostantól) a felhasználó nevű fiókhoz:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>4. mintalekérdezés

Fiókbejelentkezési események megtekintése hét nappal ezelőtt azon a felhasználó nevű fiók esetében, amely rossz jelszóval próbált meg bejelentkezni (*0xC000006a):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>5. mintalekérdezés

Fiókbejelentkezési események megtekintése hét nappal ezelőtt azon a felhasználó nevű felhasználó esetében, aki a fiók zárolta közben próbált meg bejelentkezni (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>6. mintalekérdezés

Tekintse meg a fiókbejelentkezési események számát hét nappal ezelőttaz összes zárolt felhasználónál előforduló bejelentkezési kísérlet esetén:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Eseménykategóriák naplózása

Az Azure AD DS biztonsági naplózása igazodik a hagyományos AD DS-tartományvezérlők hagyományos naplózásához. Hibrid környezetekben újra felhasználhatja a meglévő naplózási mintákat, így ugyanazt a logikát lehet használni az események elemzésekor. A hibaelhárításhoz vagy elemzéshez szükséges forgatókönyvtől függően a különböző naplózási eseménykategóriákat kell megcélozni.

A következő naplózási eseménykategóriák érhetők el:

| Naplózási kategória neve | Leírás |
|:---|:---|
| Fiók bejelentkezése|A naplózás megkísérli a fiókadatok hitelesítését egy tartományvezérlőn vagy egy helyi biztonságifiók-kezelőn (SAM).</p>A bejelentkezési és kijelentkezési házirend-beállítások és események egy adott számítógép elérésére irányuló kísérleteket követik nyomon. Az ebben a kategóriában szereplő beállítások és események a használt fiókadatbázisra összpontosítanak. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Hitelesítő adatok érvényesítésének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos hitelesítési szolgáltatás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[A Kerberos szolgáltatás jegyműveleteinek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Fiókkezelés|Naplózza a felhasználói és számítógépfiókok és -csoportok változásait. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Alkalmazáscsoport felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Számítógépes fiókok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Terjesztési csoportok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Egyéb fiókkezelés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Biztonsági csoportok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Felhasználói fiókok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Részletek nyomon követése|Az adott számítógépen lévő egyes alkalmazások és felhasználók tevékenységeinek naplózása, valamint a számítógép használatának megismerése. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[DPAPI-tevékenység naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP-tevékenység naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Folyamat-létrehozás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Folyamatmegszakítás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Címtárszolgáltatások elérése|A naplózás i. műveletek az Active Directory tartományi szolgáltatások (AD DS) objektumainak elérésére és módosítására irányulnak. Ezek a naplózási események csak tartományvezérlőkön kerülnek naplózásra. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Részletes könyvtárszolgáltatás-replikáció naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Könyvtárszolgáltatás hozzáférésének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Könyvtárszolgáltatás módosításainak naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Könyvtárszolgáltatás-replikáció naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Kijelentkezés|A naplózás iményt próbál meg interaktívan vagy hálózaton keresztül bejelentkezni. Ezek az események hasznosak a felhasználói tevékenységek nyomon követéséhez és a hálózati erőforrások elleni esetleges támadások azonosításához. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Fiókzárolás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Felhasználói/eszközjogcímek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec kiterjesztett mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Audit csoporttagság](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec fő mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec gyors mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Kijelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Bejelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Hálózati házirend-kiszolgáló naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Különleges bejelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objektum-hozzáférés| A naplózás i. kísérletek a hálózaton vagy a számítógépen lévő objektumok vagy objektumtípusok elérésére. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Létrehozott alkalmazás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Tanúsítványszolgáltatások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Részletes fájlmegosztás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Fájlmegosztás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Naplózati a fájlrendszert](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Szűrőplatform-kapcsolat naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Szűrőplatform csomagvesztéseinek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Naplóleíró-kezelés](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernel objektum naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Egyéb objektum-hozzáférési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Napló-nyilvántartás](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Cserélhető tároló naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Központi hozzáférési házirend átmeneti állapotának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Házirend módosítása|A helyi rendszer vagy hálózat fontos biztonsági házirendjeinek módosításait naplózza. A házirendeket általában a rendszergazdák hozzák létre a hálózati erőforrások védelme érdekében. A változások figyelése vagy a házirendek módosítására tett kísérletek a hálózat biztonságkezelésének fontos szempontja lehet. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Naplózási házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Hitelesítési házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Engedélyezési házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Szűrőplatform házirend-módosításainak naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC szabályszintű házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Egyéb házirend-módosítás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Jogosultság használata| Egy vagy több rendszeren bizonyos engedélyek használatát ellenőrzi. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[Nem érzékeny jogosultságok használatának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Érzékeny jogosultságok használatának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Egyéb jogosultsághasználati események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Rendszer| Naplózza a más kategóriákba nem tartozó és potenciális biztonsági következményekkel járó számítógépek rendszerszintű módosításait. Ez a kategória a következő alkategóriákat foglalja magában:<ul><li>[IPsec-illesztőprogram naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Egyéb rendszeresemények naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Biztonsági állapotmódosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Biztonsági rendszerbővítmény naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Rendszer-integritás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Eseményazonosítók kategóriánként

 Az Azure AD DS biztonsági naplózása rögzíti a következő eseményazonosítókat, amikor az adott művelet naplózható eseményt indít el:

| Eseménykategória neve | Eseményazonosítók |
|:---|:---|
|Fiók bejelentkezésbiztonsága|4767, 4774, 4775, 4776, 4777|
|Fiókkezelés biztonsága|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Részletkövetés biztonsága|None|
|DS Access biztonság|5136, 5137, 5138, 5139, 5141|
|Kijelentkezés biztonsága|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objektum-hozzáférés biztonsága|None|
|Házirend-módosítás biztonsága|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Jogosultság: Biztonság használata|4985|
|Rendszerbiztonság|4612, 4621|

## <a name="next-steps"></a>További lépések

A Kusto-val kapcsolatos konkrét információk a következő cikkekben találhatóak:

* A Kusto lekérdezési nyelvének [áttekintése.](/azure/kusto/query/)
* [Kusto oktatóanyag,](/azure/kusto/query/tutorial) hogy megismerkedjen a lekérdezés alapjait.
* [Mintalekérdezések,](/azure/kusto/query/samples) amelyek segítenek az adatok megtekintésének új módjainak megismeréséhez.
* Kusto [gyakorlati tanácsok optimalizálni](/azure/kusto/query/best-practices) a lekérdezések a siker érdekében.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
