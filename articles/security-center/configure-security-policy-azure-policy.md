---
title: Azure Policy biztonsági szabályzatok létrehozása és szerkesztése a REST API használatával
description: Ismerkedjen meg Azure Policy házirend-kezelés REST API használatával.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77430941"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Biztonsági házirend konfigurálása Azure Policy a REST API használatával

A Azure Policyrel való natív integráció részeként Azure Security Center lehetővé teszi, hogy kihasználhassa a szabályzat-hozzárendelések létrehozásához szükséges Azure Policy REST API előnyeit. A következő utasítások végigvezetik a szabályzat-hozzárendelések létrehozásán, valamint a meglévő hozzárendelések testreszabásán. 

Fontos fogalmak a Azure Policyban: 

- A **házirend-definíció** egy szabály 

- A **kezdeményezés** szabályzat-definíciók (szabályok) gyűjteménye. 

- A **hozzárendelés** egy adott hatókörre irányuló kezdeményezés vagy házirend alkalmazása (felügyeleti csoport, előfizetés stb.) 

Security Center rendelkezik egy beépített kezdeményezéssel, amely tartalmazza az összes biztonsági házirendjét. Az Security Center az Azure-erőforrásokra vonatkozó házirendjeinek értékeléséhez létre kell hoznia egy hozzárendelést a felügyeleti csoporton, vagy az előfizetést, amelyet fel szeretne mérni.

A beépített kezdeményezés alapértelmezés szerint a Security Center összes házirendjét engedélyezte. Dönthet úgy, hogy letilt bizonyos házirendeket a beépített kezdeményezésből. Ha például a **webalkalmazási tűzfal**kivételével a Security Center összes házirendjét szeretné alkalmazni, módosítsa a házirend Effect paraméterének értékét **Letiltva**értékre. 

## <a name="api-examples"></a>API-példák

Az alábbi példákban cserélje le ezeket a változókat:

- **{scope}** adja meg annak a felügyeleti csoportnak vagy előfizetésnek a nevét, amelyre alkalmazni kívánja a szabályzatot.
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

## <a name="policy-names-reference"></a>Házirend-nevek leírása<a name="policy-names"></a>

|Házirend neve Security Center|A szabályzat neve Azure Policyban jelenik meg |Házirend-effektus paraméterének neve|
|----|----|----|
|SQL-titkosítás |Titkosítatlan SQL-adatbázis figyelése Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (SQL-naplózás) |Nem naplózott SQL-adatbázis figyelése Azure Security Center |sqlAuditingMonitoringEffect|
|System updates (Rendszerfrissítések) |Hiányzó rendszerfrissítések figyelése Azure Security Center |systemUpdatesMonitoringEffect|
|Storage-titkosítás |Naplózni kell a tűrfiókok hiányzó blobtitkosítását |storageEncryptionMonitoringEffect|
|JIT hálózati hozzáférés |A lehetséges hálózati igény szerinti (JIT) hozzáférés figyelése Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptív alkalmazásvezérlők |A lehetséges alkalmazások engedélyezési listájának figyelése Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Network security groups (Hálózati biztonsági csoportok) |A megengedhető hálózati hozzáférés figyelése Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Biztonsági konfigurációk |Operációs rendszer biztonsági réseinak figyelése Azure Security Center |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Hiányzó Endpoint Protection figyelése Azure Security Center |endpointProtectionMonitoringEffect |
|Disk encryption (Lemeztitkosítás) |Titkosítatlan virtuálisgép-lemezek figyelése Azure Security Center |diskEncryptionMonitoringEffect|
|Sebezhetőségi felmérés |VIRTUÁLIS gépek biztonsági Réseinak figyelése Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Web application firewall (Webalkalmazási tűzfal) |Nem védett webalkalmazás figyelése Azure Security Center |webApplicationFirewallMonitoringEffect |
|Next generation firewall (Új generációs tűzfal) |Nem védett hálózati végpontok figyelése Azure Security Center| |


## <a name="next-steps"></a>További lépések

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [Egyéni biztonsági házirendek](custom-security-policies.md)
- [Biztonsági házirend áttekintése](tutorial-security-policy.md)