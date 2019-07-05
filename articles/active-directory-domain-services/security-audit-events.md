---
title: A biztonsági naplózás engedélyezése az Azure AD Domain Services |} A Microsoft Docs
description: A biztonsági naplózás engedélyezése az Azure AD tartományi szolgáltatásokhoz
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566494"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>A biztonsági naplózás engedélyezése az Azure AD Domain Services (előzetes verzió)
Az Azure AD tartományi szolgáltatás a biztonsági naplózás segítségével az ügyfelek az Azure AD tartományi stream biztonsági naplózási eseményeket a célzott erőforrásokhoz portált használja. Ezek az események fogadására erőforrások közé tartoznak a az Azure Storage, Azure Log Analytics-munkaterületek vagy Azure-Eseményközpontba. Kis türelmet kérünk, miután engedélyezte a biztonsági naplózási eseményeket, az Azure AD tartományi szolgáltatások a naplózott események a kiválasztott kategória küld a célzott erőforráson. Biztonsági naplózási eseményeket ügyfeleink archív naplózott események az Azure storage-bA. Ezenkívül az ügyfelek biztonsági információkat és esemény (SIEM) felügyeleti szoftver (vagy ezzel egyenértékű) az event hubs segítségével eseménysorozatot események, vagy saját elemzési és az Azure Log Analytics használatával az Azure Portalról. 

> [!IMPORTANT]
> Az Azure AD tartományi szolgáltatások a biztonsági naplózás csak az Azure Resource Manager-alapú példányok az Azure AD tartományi szolgáltatásokhoz érhető el.
>
>

## <a name="auditing-event-categories"></a>Naplózási esemény kategóriája
Az Azure AD tartományi szolgáltatások biztonsági naplózási igazítja a hagyományos naplózás, amely az Active Directory Domain Services tartományvezérlőt. Újbóli felhasználása a meglévő naplózási minták biztosítja, hogy ugyanazt a logikát alkalmazni kell, ha az események elemzését. Az Azure AD tartományi szolgáltatások biztonsági naplózási tartalmaz a következő esemény kategóriák.

| Naplózási kategória neve | Leírás |
|:---|:---|
| Bejelentkezési fiók|Kísérletek hitelesítéséhez fiókadatai tartományvezérlőn és a egy helyi biztonsági fiókkezelő (SAM) naplózza.</p>Bejelentkezési és kijelentkezési házirend-beállítások és események nyomon követése megpróbál hozzáférni egy adott számítógépen. Beállítások és ebbe a kategóriába tartozó eseményeket koncentrálhat, a fiók adatbázis szolgálja ki. Ez a kategória a következő alkategóriákkal:<ul><li>[Hitelesítő adatok érvényesítésének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos hitelesítési szolgáltatás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[A Kerberos szolgáltatás Jegyműveleteinek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Fiókkezelés|Naplózás a felhasználói és számítógépes fiókok és csoportok módosításait. Ez a kategória a következő alkategóriákkal:<ul><li>[Alkalmazáscsoport felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Számítógépes fiókok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Terjesztési csoportok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Egyéb fiókok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Biztonsági csoportok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Felhasználói fiókok felügyeletének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| A részletes nyomon követése|Naplózza a tevékenységeket, az egyes alkalmazások és a felhasználók ezen a számítógépen, és a egy számítógép használatának megértéséhez. Ez a kategória a következő alkategóriákkal:<ul><li>[DPAPI-tevékenység naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP-tevékenység naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Folyamat-létrehozás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Folyamatmegszakítás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Címtárszolgáltatások hozzáférés|Naplózás próbál meg hozzáférni, és módosíthatja az objektumokat az Active Directory Domain Servicesben (AD DS). A naplózási eseményeket naplózza csak tartományvezérlőkön. Ez a kategória a következő alkategóriákkal:<ul><li>[Részletes replikáció naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Címtárszolgáltatás-hozzáférés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Módosításainak naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Replikáció naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Bejelentkezés – kijelentkezés|Jelentkezzen be egy számítógépre interaktív módon vagy a hálózaton keresztül való próbálkozások eseményeket. Ezek az események hasznosak lehetnek a felhasználói tevékenység nyomon követése, és azonosítja a potenciális támadások, a hálózati erőforrásokat. Ez a kategória a következő alkategóriákkal:<ul><li>[Fiókzárolás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Felhasználói és Eszközjogcímek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec kiterjesztett mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Naplózási csoporttagság](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec fő mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec gyors mód naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Kijelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Bejelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Naplózási hálózati házirend-kiszolgáló](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Különleges bejelentkezés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objektum-hozzáférés| Eseményeket megpróbál hozzáférni az adott objektumokat vagy a hálózaton vagy a számítógép objektumtípusokat. Ez a kategória a következő alkategóriákkal:<ul><li>[Létrehozott alkalmazás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Tanúsítványszolgáltatások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Részletes fájlmegosztás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Fájlmegosztás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Fájlrendszer naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Szűrőplatform-kapcsolat naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Szűrőplatform Csomagvesztéseinek naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Leírókezelés naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernel-objektum naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Egyéb objektum-hozzáférési események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Beállításjegyzék naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Cserélhető tároló naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Központi hozzáférési házirend tesztelésének naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Szabályzat módosítása|Fontos biztonsági házirendek egy helyi vagy hálózati naplók változik. Házirendek általában állítja be a rendszergazdák számára biztonságos hálózati erőforrásokhoz. Figyelés megváltozik, vagy ezek a szabályzatok módosítására tett kísérletek, lehet, hogy fontos szempont a biztonsági felügyeleti hálózat. Ez a kategória a következő alkategóriákkal:<ul><li>[Naplózási házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Hitelesítési házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Engedélyezési házirend változásának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Szűrőplatform házirend-módosításainak naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC szabályszintű házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Egyéb házirend-módosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Használati jog| Egy vagy több rendszer bizonyos engedélyeket használatát naplózza. Ez a kategória a következő alkategóriákkal:<ul><li>[Nem érzékeny jogosultságok használatának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Érzékeny jogosultságok használatának naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Egyéb Jogosultsághasználati események naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Rendszer| Naplózás rendszerszintű módosításai nem szerepel a többi között, és hogy a számítógép potenciális biztonsági következményekkel. Ez a kategória a következő alkategóriákkal:<ul><li>[IPsec-illesztőprogram naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Egyéb Rendszeresemények naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Biztonsági Állapotmódosítások naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Biztonsági Rendszerbővítmény naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Rendszer-integritás naplózása](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Kategória szerinti eseményazonosítók
 Az Azure AD tartományi szolgáltatások biztonsági naplózási rögzíti az alábbi eseményazonosítók, ha az adott műveletet aktivál egy naplózható esemény.

| Esemény kategória neve | Eseményazonosítók |
|:---|:---|
|Fiók bejelentkezési biztonság|4767, 4774, 4775, 4776, 4777|
|Fiók felügyeleti biztonsági|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Részletes követési biztonsági|None|
|Tartományi szolgáltatások elérése biztonsági|5136, 5137, 5138, 5139, 5141|
|Bejelentkezés – kijelentkezés biztonsági|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objektum-hozzáférés-biztonságot|None|
|Szabályzat módosítása biztonsági|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Jogosultság-biztonság használata|4985|
|Rendszerbiztonság|4612, 4621|

## <a name="enable-security-audit-events"></a>Biztonsági naplózási eseményeket engedélyezése
Az alábbi útmutató segítségével sikeresen fizessen elő az Azure AD tartományi szolgáltatások biztonsági naplózási eseményeket.

> [!IMPORTANT]
> Az Azure AD tartományi szolgáltatások biztonsági eseményeket, amelyek nem visszamenőleges. Már nem események lekérése az elmúlt vagy események az elmúlt visszajátszani. A szolgáltatás csak küldhet eseményeket, amelyek az engedélyezése után.
>

### <a name="choose-the-target-resource"></a>A célként megadott erőforrás kiválasztása
Használhatja az Azure Storage, Azure Event Hubs vagy Azure Log Analytics-munkaterületek tetszőleges kombinációját a célként megadott erőforrás számára a biztonsági naplózás. Vegye figyelembe az alábbi táblázatban a használati esetekhez a legjobb erőforrás.

> [!IMPORTANT]
> Azure AD tartományi szolgáltatások a biztonsági naplózás engedélyezése előtt a célként megadott erőforrás létrehozásához szükséges.
>

| Célerőforrás | Forgatókönyv |
|:---|:---|
|Azure Storage|Fontolja meg a cél az elsődleges szükség esetén a archiválási célokból tárolni a biztonsági naplózási eseményeket. Más tárolók is használható archiválási célokból; azonban az ezen célok archiválás elsődleges szükség képességeinél több képességet biztosít. Azure Storage-fiók létrehozásához kövesse az [hozzon létre egy tárfiókot.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|Fontolja meg a cél az elsődleges szükség esetén a biztonsági naplózási eseményeket megosztása további szoftvereket, például az adatok elemzési vagy biztonsági információk & esemény (SIEM) felügyeleti szoftvert. Egy eseményközpont létrehozásához kövesse az [a rövid útmutató: Létrehoz egy eseményközpontot, az Azure portal használatával.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics Workspace|Fontolja meg a cél elemzéséhez, és tekintse át az Azure Portalról biztonságos eseményeket közvetlenül az elsődleges szükség esetén.  Log Analytics-munkaterület létrehozásához hajtsa végre az [Log Analytics-munkaterület létrehozása az Azure Portalon.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Biztonsági naplózási eseményeket engedélyezése az Azure portal használatával 
1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.  Az Azure Portalon kattintson az összes szolgáltatás. Az erőforrások listájába írja be a **tartomány**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **az Azure AD Domain Services**.
2. Kattintson az Azure AD Domain Services-példány a listából.
3. Kattintson a **diagnosztikai beállítások (előzetes verzió)** műveleteket a bal oldali listájában.</p>
![diagnosztikai beállítás művelet](./media/security-audit-events/diagnostic-settings-action.png)
4. Diagnosztikai konfiguráció neve (**aadds naplózás** példaként).</p>
![Diagnosztikai beállítások lap](./media/security-audit-events/diagnostic-settings-page.png)
5. Jelölje be a megfelelő mellett szeretné használni, a biztonsági naplózási eseményeket a célzott erőforrásokhoz.
    > [!NOTE]
    > Célerőforrások ezen a lapon nem hozható létre.
    >
    
    **az Azure storage:**</p>
    Válassza ki **archiválás tárfiókba**. Kattintson a **Konfigurálás** elemre. Válassza ki a **előfizetés** és a **tárfiók** archiválnia a biztonsági naplózási eseményeket szeretné. Kattintson az **OK** gombra.</p>
    
    ![diagnosztikai tárfiók-beállítások](./media/security-audit-events/diag-settings-storage.png)
    
    **Az Azure event hubs:**</p>
    Válassza ki **egy eseményközpontba Stream**. Kattintson a **Konfigurálás** elemre. Az a **válassza eseményközpontba oldal**, jelölje be a **előfizetés** az eseményközpont létrehozásához használt. Ezután válassza ki a **eseményközpont-névtér**, **eseményközpont neve**, és **eseményközpont szabályzatának neve**. Kattintson az **OK** gombra.</p>
    ![diagnosztikai event hub beállításai](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Az Azure Log Analytics-munkaterületek:**</p>
    Válassza ki **Küldés a Log Analyticsnek**. Válassza ki a **előfizetés** és **Log Analytics-munkaterület** fogja tárolni a biztonsági naplózási eseményeket.</p>
    ![diagnosztikai munkaterület beállításai](./media/security-audit-events/diag-settings-log-analytics.png)

6. Válassza ki a kívánt része az adott célerőforrás naplókategóriák. Storage-fiókok használata esetén konfigurálhatja a megtartási házirendeket.

    > [!NOTE]
    > Választhat másik naplókategóriák egyetlen konfigurációval belül az egyes megcélzott erőforrások. Ez lehetővé teszi, hogy válassza ki, amely naplózza, hogy meg szeretné tartani a Log Analytics, és amely naplózza a kategóriák archiválása a kívánt kategóriákat.
    >

7. Kattintson a **mentése** a módosítások véglegesítéséhez. A célerőforrások fog kapni az Azure AD Domain Services biztonsági naplózási eseményeket, mentse a konfigurációt követően rövid időn belül.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Azure PowerShell-lel való engedélyezése biztonsági naplózási eseményeket
 
### <a name="prerequisites"></a>Előfeltételek

Kövesse a cikkben szereplő utasításokat [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>A biztonsági naplózás engedélyezése

1. Az Azure Resource Manager a megfelelő bérlő és előfizetés használatával hitelesíti a **Connect-AzAccount** Azure PowerShell-parancsmagot.
2. Hozzon létre a célként megadott erőforrás esetében a biztonsági naplózási eseményeket.</p>
    **az Azure storage:**</p>
    Hajtsa végre a [hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) a tárfiók létrehozásához.</p>
    **Az Azure event hubs:**</p>
    Hajtsa végre a [a rövid útmutató: Létrehoz egy eseményközpontot, az Azure PowerShell-lel](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) az eseményközpont létrehozásához. Előfordulhat, hogy is kell használnia a [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell-parancsmag használatával létrehozhat egy engedélyezési szabályt, hogy az Active Directory AD tartományi szolgáltatások engedélyek az event hubs **névtér**. Az engedélyezési szabályt tartalmaznia kell a **kezelés**, **figyelésére**, és **küldése** rights.
    > [!IMPORTANT]
    > Győződjön meg arról, az eseményközpont-névtér és az event hubs nem állítsa be az engedélyezési szabályt.
       
    </p>
    
    **Az Azure Log Analytics-munkaterületek:**</p>
    Hajtsa végre a [Log Analytics-munkaterület létrehozása az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) hozhat létre a munkaterületet.
3. Az Azure AD Domain Services-példány beolvasása az erőforrás-azonosítója. Egy megnyitott, hitelesített Windows PowerShell-konzolt írja be a következő parancsot. Használja a **$aadds. Erőforrás-azonosító** változó az Azure AD tartományi szolgáltatások erőforrás-azonosítóhoz a jövőbeli parancsmagok paraméterként.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Használja a **Set-AzDiagnosticSetting** parancsmagot, hogy a célként megadott erőforrás használható az Azure AD Domain Services biztonsági naplózási eseményeket az Azure diagnosztikai beállítások konfigurálása. Az alábbi, a változó $aadds példák. Erőforrás-azonosító jelöli az erőforrás-azonosító az Azure AD Domain Services-példány (lásd a 3. lépés).</p>
    **az Azure storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Cserélje le *storageAccountId* a tárolási fiók azonosítóját írja.</p>
    
    **Az Azure event hubs:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Cserélje le *eventHubName* az eseményközpont nevével. Cserélje le *eventHubRuleId* az engedélyezési szabály azonosítójú korábban hozott létre.</p>
    
    **Az Azure Log Analytics-munkaterületek:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Cserélje le *munkaterület azonosítója* azonosítójú, korábban létrehozott Log Analytics-munkaterületen. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Biztonsági naplózási események megtekintése az Azure Monitor használatával
Log Analytics munkaterületek lehetővé teszi megtekintése és elemzése az Azure Monitor és a Kusto-lekérdezési nyelv segítségével biztonsági naplózási eseményeket. A lekérdezési nyelv a csak olvasható használja az energiaellátási analitikai lehetőségek egy könnyen olvasható szintaxissal büszkélkedhet lett tervezve.
Íme néhány forrás, Kusto-lekérdezés nyelv – első lépések segítségével.
* [Az Azure Monitor dokumentációja](https://docs.microsoft.com/azure/azure-monitor/)
* [Ismerkedés a Log Analytics az Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Az Azure Monitor log-lekérdezések használatának első lépései](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Az adatok Log Analytics irányítópultok létrehozása és megosztása](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Mintalekérdezések

### <a name="sample-query-1"></a>Mintalekérdezés 1
Az összes a fiók zárolása események az elmúlt hét nap.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Mintalekérdezés 2
A fiók zárolása közé eső összes (4740) 2019. június 26. 9-kor 2019. július 1. éjfél és, rendezett növekvő dátum és idő alapján.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Mintalekérdezés 3
Fiók naplózási események hét napja (most) nevű felhasználói fiók.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Mintalekérdezés 4
A fiókbejelentkezési események hét napja, most már a fiókjához tartozó nevű felhasználót, hogy megpróbált bejelentkezni helytelen jelszóval (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Mintalekérdezés 5
Bejelentkezési események hét napja, most már a fiókjához tartozó nevű felhasználót, hogy megpróbált bejelentkezni, amíg a fiók (0xC0000234) lett zárolva.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Mintalekérdezés 6
Bejelentkezési események száma a hét napja, most már az összes jelentkezzen be, amely az összes felhasználó zárolva történt kísérlet.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Kapcsolódó tartalom
* [Áttekintés](https://docs.microsoft.com/azure/kusto/query/) a Kusto-lekérdezési nyelv.
* [Kusto-oktatóanyag](https://docs.microsoft.com/azure/kusto/query/tutorial) és ismerje meg, a lekérdezés alapjaival.
* [Példa a lekérdezésekre](https://docs.microsoft.com/azure/kusto/query/samples) , amelyek révén megismerheti, hogy új módokon meg az adatokat.
* Kusto [ajánlott eljárások](https://docs.microsoft.com/azure/kusto/query/best-practices) – optimalizálni a lekérdezéseket, a sikeres.














 
