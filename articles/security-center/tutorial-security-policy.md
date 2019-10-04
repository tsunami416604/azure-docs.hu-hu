---
title: A biztonsági szabályzatok használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a biztonsági házirendek a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: memildin
ms.openlocfilehash: 488210e4f2c5d3a8a978079d0c7293ce091d998b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338771"
---
# <a name="working-with-security-policies"></a>Biztonsági szabályzatok használata

Ez a cikk ismerteti a biztonsági házirendek konfigurálásának módját, valamint azt, hogy miként lehet megtekinteni őket a Security Centerban. A Azure Security Center automatikusan hozzárendeli a [beépített biztonsági szabályzatokat](security-center-policy-definitions.md) az egyes előfizetésekhez. Azure Policyban is konfigurálhatja [](../governance/policy/overview.md)őket, ami lehetővé teszi a házirendek felügyeleti csoportokon és több előfizetésen keresztüli beállítását is.

A szabályzatok PowerShell használatával történő beállításával kapcsolatos útmutatásért [lásd: gyors útmutató: Hozzon létre egy szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához](../governance/policy/assign-policy-powershell.md)a Azure PowerShell modul használatával.

>[!NOTE]
> Security Center megkezdte az integrációt a Azure Policyával. A meglévő ügyfeleket a rendszer automatikusan áttelepíti a Azure Policy új beépített kezdeményezésére a Security Center korábbi biztonsági házirendjei helyett. Ez a változás nem befolyásolja az erőforrásokat vagy a környezetet, kivéve az új kezdeményezés Azure Policyban való jelenlétét.

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Azure Policy az Azure-előfizetésekre vonatkozó szabályzatokat határozhat meg, és testre szabhatja azokat a számítási feladatok vagy az adatok érzékenysége alapján. Például a szabályozott adatokkal, például a személyes adatokkal vagy az ügyféladatokat használó alkalmazások magasabb szintű biztonságot igényelhetnek, mint más számítási feladatok. Ha a szabályzatot előfizetések vagy felügyeleti csoportok között szeretné beállítani, állítsa be őket [Azure Policy](../governance/policy/overview.md).

A biztonsági szabályzatok a Azure Security Center beérkező biztonsági javaslatokat hajtják majd meg. A megfelelőséget figyelheti velük, hogy könnyebben azonosítsa a lehetséges sebezhetőségeket, és enyhítse a fenyegetéseket. Az Ön számára legmegfelelőbb lehetőség meghatározásával kapcsolatos további információkért tekintse meg a [beépített biztonsági szabályzatok](security-center-policy-definitions.md)listáját.

A Security Center engedélyezésekor a Security Center beépített biztonsági házirend Azure Policy a Security Center kategóriába tartozó beépített kezdeményezésként jelenik meg. A beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez (ingyenes vagy standard szintű csomag). A beépített kezdeményezés csak naplózási házirendeket tartalmaz.


### <a name="management-groups"></a>Felügyeleti csoport
Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási szabályzatokat alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Minden címtárhoz tartozik egy legfelső szintű, „gyökér” felügyeleti csoportnak nevezett felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. A felügyeleti csoportok Azure Security Centerhoz való használatának beállításához kövesse a teljes bérlőre [kiterjedő láthatóság a Azure Security Center számára](security-center-management-groups.md)című témakör utasításait.

> [!NOTE]
> Fontos, hogy átlássa a felügyeleti csoportok és előfizetések hierarchiáját. További információt a felügyeleti csoportokról, a gyökérszintű felügyeletről és a felügyeleti csoportok hozzáféréséről [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../governance/management-groups/overview.md#root-management-group-for-each-directory) ismertető részben talál.
>

## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A Azure Policy szabályzatait a következő műveletek elvégzéséhez módosíthatja:
- Új szabályzatdefiníciók létrehozása.
- Szabályzatok hozzárendelése felügyeleti csoportokhoz és előfizetésekhez, amelyek jelölhetnek egy teljes vállalatot, vagy egy adott üzleti egységet a vállalaton belül.
- Szabályzatoknak való megfelelés figyelése.

Az Azure Policyvel kapcsolatos további információkért olvassa el [a megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](../governance/policy/tutorials/create-and-manage.md) foglalkozó témakört.

Egy Azure-szabályzat az alábbi összetevőkből áll:

- A **szabályzat** egy szabály.
- A **kezdeményezés** szabályzatok gyűjteménye.
- A **hozzárendelés** egy adott hatókörre irányuló kezdeményezés vagy házirend alkalmazása (felügyeleti csoport, előfizetés vagy erőforráscsoport).

## <a name="view-security-policies"></a>Biztonsági szabályzatok megtekintése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. A **Security Center** irányítópulton válassza a **biztonsági házirend**elemet.

    ![A Szabályzatkezelés panel](./media/security-center-policies/security-center-policy-mgt.png)

   A **házirend kezelése** képernyőn láthatja a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleti csoport struktúráját.

   > [!NOTE]
   > Az Security Center irányítópulton nagyobb számú előfizetés jelenhet meg az előfizetések lefedettsége alatt, mint a **házirendek**felügyelete alatt megjelenő előfizetések száma. Az Előfizetési lefedettség alatt a Standard, Ingyenes és „nem lefedett” előfizetések is megjelennek. A "not defedett" előfizetések nem rendelkeznek Security Center engedélyezve, és nem jelennek meg a **házirendek**felügyelete alatt.
   >

2. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a szabályzatait meg szeretné tekinteni.

   - A **biztonsági házirend** képernyő a kiválasztott előfizetéshez vagy felügyeleti csoporthoz hozzárendelt szabályzatok által végrehajtott műveletet tükrözi.
   - A felső részen használja a megadott hivatkozásokat az előfizetésre vagy a felügyeleti csoportra vonatkozó szabályzat- **hozzárendelések** megnyitásához. A hivatkozásokkal elérheti a hozzárendelést, és szerkesztheti vagy letilthatja a szabályzatot. Ha például úgy látja, hogy egy adott házirend-hozzárendelés ténylegesen megtagadja az Endpoint Protectiont, akkor a hivatkozásra kattintva elérheti a házirendet, és szerkesztheti vagy letilthatja azt.
   - A szabályzatok listájában megtekintheti az előfizetéséhez vagy a felügyeleti csoportjához tartozó házirend hatályos alkalmazását. Ez azt jelenti, hogy a hatókörre vonatkozó egyes szabályzatok beállításait figyelembe vesszük, és a szabályzat által végrehajtott művelet összesített eredményét kell megadnia. Ha például az egyik hozzárendelésben a házirend le van tiltva, de egy másikban a AuditIfNotExist értékre van állítva, akkor a kumulatív hatás a AuditIfNotExist alkalmazza. Az aktívabb hatás mindig elsőbbséget élvez.
   - A szabályzatok hatása a következő lehet: Hozzáfűzés, naplózás, AuditIfNotExists, megtagadás, DeployIfNotExists, letiltva. További információ a hatások alkalmazásáról: a szabályzatok [hatásainak megismerése](../governance/policy/concepts/effects.md).

   ![házirend képernyő](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> A hozzárendelt házirendek megtekintésekor több hozzárendelést láthat, és megtekintheti, hogy az egyes hozzárendelések hogyan legyenek konfigurálva.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az egyes Azure-előfizetések és felügyeleti csoportok alapértelmezett biztonsági szabályzatait az [Azure Policyban](../governance/policy/tutorials/create-and-manage.md) szerkesztheti. Biztonsági szabályzat módosításához az előfizetés vagy az azt tartalmazó felügyeleti csoport tulajdonosának vagy biztonsági rendszergazdájának kell lennie.

A biztonsági szabályzatok Azure Policyban való szerkesztésével kapcsolatos utasításokért tekintse meg és [hozza létre és kezelje a szabályzatokat a megfelelőség érvényesítéséhez](../governance/policy/tutorials/create-and-manage.md).

A biztonsági szabályzatokat a Azure Policy-portálon, REST API vagy a Windows PowerShell használatával szerkesztheti. Az alábbi példa a REST API használatával történő szerkesztésre vonatkozó utasításokat tartalmazza.


## <a name="disable-security-policies"></a>Biztonsági házirendek letiltása
Ha az alapértelmezett biztonsági házirend olyan javaslatot hoz létre, amely nem releváns a környezetében, leállíthatja a javaslatot küldő szabályzat-definíció letiltásával.
A javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

1. A Security Center a **házirend & megfelelősége** szakaszban kattintson a **biztonsági szabályzat**elemre.

   ![házirendek kezelése](./media/tutorial-security-policy/policy-management.png)

2. Kattintson arra az előfizetésre vagy felügyeleti csoportra, amelyhez le szeretné tiltani a javaslatot.

   > [!Note]
   > Ne feledje, hogy egy felügyeleti csoport alkalmazza a szabályzatokat az előfizetésekre. Ezért ha letilt egy előfizetési szabályzatot, és az előfizetés egy olyan felügyeleti csoporthoz tartozik, amely továbbra is ugyanazt a házirendet használja, akkor továbbra is megkapja a házirendre vonatkozó javaslatokat. A szabályzat továbbra is a felügyeleti szintjéről lesz alkalmazva, és a javaslatok továbbra is létrejönnek.

1. Kattintson a hozzárendelt házirendre.

   ![házirend letiltása](./media/tutorial-security-policy/security-policy.png)

1. A **Parameters (paraméterek** ) szakaszban keresse meg a letiltani kívánt javaslatot meghívó házirendet, és a legördülő listából válassza a Letiltva lehetőséget.

   ![házirend letiltása](./media/tutorial-security-policy/disable-policy.png)
1. Kattintson a **Save** (Mentés) gombra.
   > [!Note]
   > A házirend-letiltási módosítások érvénybe léptetéséhez akár 12 órát is igénybe vehet.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Biztonsági házirend konfigurálása a REST API használatával

A Azure Policyrel való natív integráció részeként Azure Security Center lehetővé teszi, hogy kihasználhassa a szabályzat-hozzárendelések létrehozásához szükséges Azure Policy REST API előnyeit. A következő utasítások végigvezetik a szabályzat-hozzárendelések létrehozásán, valamint a meglévő hozzárendelések testreszabásán. 

Fontos fogalmak a Azure Policyban: 

- A **házirend-definíció** egy szabály 

- A **kezdeményezés** szabályzat-definíciók (szabályok) gyűjteménye. 

- A **hozzárendelés** egy adott hatókörre irányuló kezdeményezés vagy házirend alkalmazása (felügyeleti csoport, előfizetés stb.) 

Security Center rendelkezik egy beépített kezdeményezéssel, amely tartalmazza az összes biztonsági házirendjét. Az Azure-erőforrásokra vonatkozó Security Center szabályzatának értékeléséhez létre kell hoznia egy hozzárendelést a felügyeleti csoporton vagy előfizetésben, amelyet fel szeretne mérni.

A beépített kezdeményezés alapértelmezés szerint a Security Center összes házirendjét engedélyezte. Dönthet úgy, hogy letilt bizonyos házirendeket a beépített kezdeményezésből, például a webalkalmazási **tűzfal**kivételével az összes Security Center házirendjét, ha a házirend Effect paraméterének értékét letiltva értékre módosítja. 

### <a name="api-examples"></a>API-példák

Az alábbi példákban cserélje le ezeket a változókat:

- **{scope}** adja meg annak a felügyeleti csoportnak vagy előfizetésnek a nevét, amelyre a szabályzatot alkalmazza.
- **{policyAssignmentName}** adja meg a [megfelelő szabályzat-hozzárendelés nevét](#policy-names).
- **{Name}** adja meg a nevét, vagy annak a rendszergazdának a nevét, aki jóváhagyta a szabályzatot.

Ebből a példából megtudhatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez vagy egy felügyeleti csoporthoz.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Ebből a példából megtudhatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez, a következő szabályzatok letiltásával: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Ebből a példából megtudhatja, hogyan távolíthat el egy hozzárendelést:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### Házirend-nevek leírása<a name="policy-names"></a>

|Házirend neve Security Center|A szabályzat neve Azure Policyban jelenik meg |Házirend-effektus paraméterének neve|
|----|----|----|
|SQL-titkosítás |Titkosítatlan SQL-adatbázis figyelése Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (SQL-naplózás) |Nem naplózott SQL-adatbázis figyelése Azure Security Center |sqlAuditingMonitoringEffect|
|Rendszerfrissítések |Hiányzó rendszerfrissítések figyelése Azure Security Center |systemUpdatesMonitoringEffect|
|Storage-titkosítás |Hiányzó blob-titkosítás naplózása a Storage-fiókoknál |storageEncryptionMonitoringEffect|
|JIT hálózati hozzáférés |A lehetséges hálózati hozzáférés (JIT) figyelése Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptív alkalmazásvezérlés |A lehetséges alkalmazások engedélyezési listájának figyelése Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Network security groups (Hálózati biztonsági csoportok) |A megengedhető hálózati hozzáférés figyelése Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Biztonsági konfigurációk |Operációs rendszer biztonsági réseinak figyelése Azure Security Center |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Hiányzó Endpoint Protection figyelése Azure Security Center |endpointProtectionMonitoringEffect |
|Lemeztitkosítás |Titkosítatlan virtuálisgép-lemezek figyelése Azure Security Center |diskEncryptionMonitoringEffect|
|Sebezhetőségi felmérés |VIRTUÁLIS gépek biztonsági Réseinak figyelése Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Webalkalmazási tűzfal |Nem védett webalkalmazás figyelése Azure Security Center |webApplicationFirewallMonitoringEffect |
|Next generation firewall (Új generációs tűzfal) |Nem védett hálózati végpontok figyelése Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Kik módosíthatják a biztonsági házirendeket?
A Security Center szerepköralapú Access Control (RBAC) használ, amely beépített szerepköröket biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban. Security Center megnyitásakor a felhasználók csak azokat az adatokat látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználók a tulajdonos, közreműködő vagy olvasó szerepkörhöz vannak rendelve ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- Biztonsági olvasó: Megtekintheti a Security Centerra vonatkozó jogosultságokat, amelyekben javaslatok, riasztások, szabályzatok és állapotok szerepelnek, de nem módosíthatók.
- Biztonsági rendszergazda: Ugyanazok a megtekintési jogosultságok, mint a biztonsági olvasó, továbbá frissíthetik a biztonsági házirendet, és eltekinthetik az ajánlásokat és a riasztásokat.



## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan szerkesztheti a biztonsági szabályzatokat a Azure Policyban. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md): Megtudhatja, hogyan tervezheti meg és értelmezheti a Azure Security Centerával kapcsolatos tervezési szempontokat.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása Azure Security Centerekkel](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [A Azure Security Center a bérlők számára széles körű láthatóságot nyerhet](security-center-management-groups.md): Megtudhatja, hogyan állíthat be Azure Security Center felügyeleti csoportokat.
* [Azure Security Center gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../governance/policy/overview.md)
