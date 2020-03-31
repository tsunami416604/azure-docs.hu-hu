---
title: Az Azure-szabályzatbiztonsági házirendek létrehozása és szerkesztése a REST API használatával
description: Ismerje meg az Azure Policy policy management egy REST API-n keresztül.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430941"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Biztonsági házirend konfigurálása az Azure-szabályzatban a REST API használatával

Az Azure Security Center az Azure Security Centerrel való natív integráció részeként lehetővé teszi, hogy kihasználja az Azure Policy REST API-ját a házirend-hozzárendelések létrehozásához. A következő utasítások végigvezetik a házirend-hozzárendelések létrehozásán, valamint a meglévő hozzárendelések testreszabásán. 

Fontos fogalmak az Azure-szabályzatban: 

- A **házirend-definíció** egy szabály 

- A **kezdeményezés** a szakpolitikai definíciók (szabályok) gyűjteménye 

- A **hozzárendelés** egy kezdeményezés vagy szabályzat alkalmazása egy adott hatókörhöz (felügyeleti csoport, előfizetés stb.) 

A Security Center beépített kezdeményezéssel rendelkezik, amely tartalmazza az összes biztonsági házirendjét. A Security Center szabályzatainak az Azure-erőforrásokon való értékeléséhez hozzon létre egy hozzárendelést a felügyeleti csoportban vagy a felmérni kívánt előfizetésben.

A beépített kezdeményezés alapértelmezés szerint a Security Center összes házirendjével rendelkezik. Dönthet úgy, hogy letilt bizonyos házirendeket a beépített kezdeményezésből. Ha például a **webalkalmazás tűzfalán**kívül a Security Center összes házirendjének alkalmazásához a házirend hatásparaméterének értékét **letiltva**értékre szeretné módosítani. 

## <a name="api-examples"></a>API-példák

A következő példákban cserélje le ezeket a változókat:

- **{scope}** adja meg annak a felügyeleti csoportnak vagy előfizetésnek a nevét, amelyre a házirendet alkalmazza.
- **{policyAssignmentName}** írja be [a megfelelő házirend-hozzárendelés nevét.](#policy-names)
- **{name}** írja be a nevét vagy a házirend-módosítást jóváhagyó rendszergazda nevét.

Ez a példa bemutatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetési vagy felügyeleti csoporthoz.
 
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

Ez a példa bemutatja, hogyan rendelheti hozzá a beépített Security Center kezdeményezést egy előfizetéshez, ha a következő házirendek le vannak tiltva: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Végpontvédelem ("endpointProtectionMonitoringEffect") 

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
Ez a példa bemutatja, hogyan távolíthatja el a hozzárendeléseket:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Házirendnevek hivatkozása<a name="policy-names"></a>

|Házirend neve a Biztonsági központban|Az Azure-szabályzatban megjelenő házirend neve |Házirend-effektus paraméterének neve|
|----|----|----|
|SQL-titkosítás |Titkosítatlan SQL-adatbázis figyelése az Azure Security Centerben |sqlEncryptionMonitoringEffect| 
|SQL Auditing (SQL-naplózás) |Nem auditált SQL-adatbázis figyelése az Azure Security Centerben |sqlAuditingMonitoringEffect|
|System updates (Rendszerfrissítések) |Hiányzó rendszerfrissítések figyelése az Azure Security Centerben |systemUpdatesMonitoringEffect|
|Storage-titkosítás |Naplózni kell a tűrfiókok hiányzó blobtitkosítását |storageEncryptionMonitoringEffect|
|JIT hálózati hozzáférés |A lehetséges hálózati hozzáférés figyelése az Azure Security Centerben |jitNetworkAccessMonitoringEffect |
|Adaptív alkalmazásvezérlők |Lehetséges alkalmazásengedélyezési lista figyelése az Azure Security Centerben |adaptiveApplicationControlsMonitoringEffect|
|Network security groups (Hálózati biztonsági csoportok) |Megengedő hálózati hozzáférés figyelése az Azure Security Centerben |networkSecurityGroupsMonitoringEffect| 
|Biztonsági konfigurációk |Operációs rendszer biztonsági réseinek figyelése az Azure Security Centerben |systemConfigurationsMonitoringEffect| 
|Endpoint protection (Végpontok védelme) |Hiányzó végpontvédelem figyelése az Azure Security Centerben |endpointProtectionMonitoringEffect |
|Disk encryption (Lemeztitkosítás) |Titkosítatlan virtuálisgép-lemezek figyelése az Azure Security Centerben |diskEncryptionMonitoringEffect|
|Sebezhetőségi felmérés |Virtuálisgép-biztonsági rések figyelése az Azure Security Centerben |biztonsági résAssessmentAssessmentMonitoringEffect|
|Web application firewall (Webalkalmazási tűzfal) |Nem védett webalkalmazás figyelése az Azure Security Centerben |webApplicationFirewallMonitoringEffect |
|Next generation firewall (Új generációs tűzfal) |Nem védett hálózati végpontok figyelése az Azure Security Centerben| |


## <a name="next-steps"></a>További lépések

Egyéb kapcsolódó anyagokról lásd a következő cikkeket: 

- [Egyéni biztonsági házirendek](custom-security-policies.md)
- [Biztonsági házirend – áttekintés](tutorial-security-policy.md)