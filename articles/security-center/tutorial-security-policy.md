---
title: Szerkesztheti a biztonsági szabályzatokat az Azure Policy |} A Microsoft Docs
description: Biztonsági szabályzatok az Azure Policy szerkesztése.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: d6cc216f71efcd3b3973cd37349dd5145237f02f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839330"
---
# <a name="edit-security-policies-in-azure-policy"></a>Biztonsági szabályzatok az Azure Policy szerkesztése
Biztonsági szabályzatok és az alkalmazásuk módját a számítási feladatok állapotának megtekintése a Security Center segítségével. Az Azure Security Center automatikusan hozzárendeli a [beépített biztonsági házirendek](security-center-policy-definitions.md) minden egyes előfizetés, amely regisztrálva van. Konfigurálhatja azokat a [Azure Policy](../azure-policy/azure-policy-introduction.md), vagy a REST API, amely lehetővé teszi szabályzatok beállítását a felügyeleti csoportokhoz, és több előfizetésre kiterjedő használatával. További információ: [A Security Center biztonsági szabályzatainak integrálása az Azure Policyvel](security-center-azure-policy.md). Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A REST API-val biztonsági szabályzat konfigurálása
> * Az erőforrások biztonságának felmérése

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Megpróbálhatja Security Center Standard költségek nélkül. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="configure-a-security-policy-using-the-rest-api"></a>A REST API-val biztonsági szabályzat konfigurálása

A natív integrációt olyan Azure-szabályzat részeként az Azure Security Center lehetővé teszi, hogy használja ki az Azure Policy REST API-t hozhat létre szabályzat-hozzárendelések. Az alábbi utasításokat követve lépésről lépésre szabályzat-hozzárendelések létrehozását, valamint a meglévő hozzárendelések testre szabhatja. 

Az Azure Policy alapfogalmaival: 

- A **szabályzatdefiníció** szabály 

- Egy **kezdeményezés** szabályzatdefiníciók (szabályok) gyűjteménye 

- Egy **hozzárendelés** egy alkalmazás egy szabályzatot vagy kezdeményezést, amelyek egy adott hatókörhöz (felügyeleti csoport, előfizetési, stb.) 

A Security Center rendelkezik egy beépített kezdeményezés, amely tartalmazza az összes annak biztonsági házirendjei. Annak érdekében, hogy a Security Center-szabályzatok az Azure-erőforrások felmérése, a hozzárendelés a felügyeleti csoport, vagy az értékelni kívánt előfizetést kell létrehoznia.  

A beépített kezdeményezés rendelkezik minden alapértelmezés szerint engedélyezve van a Security Center-szabályzat. Dönthet úgy, hogy letiltja a beépített kezdeményezés az egyes szabályzatok, például alkalmazhatja minden kivételével a Security Center-szabályzat **webalkalmazási tűzfal**, a házirend érvénybe paraméter értékének módosításával , **Letiltott**. 

### <a name="api-examples"></a>API-példák

A következő példákban cserélje le ezeket a változókat:

- **{hatókör}**  adja meg a felügyeleti csoport nevét, vagy az előfizetés lépnek életbe a szabályzatot.
- **{poicyAssignmentName}**  adja meg a [a megfelelő szabályzat-hozzárendelés neve](#policy-names).
- **a(z) {name}**  adja meg a nevét, vagy a rendszergazda a házirend-módosítások jóváhagyó neve.

Ez a példa bemutatja, hogyan rendelje hozzá a beépített a Security Center-kezdeményezéshez egy előfizetést vagy a felügyeleti csoport
 
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

Ez a példa bemutatja, hogyan rendelje hozzá a beépített a Security Center-kezdeményezéshez egy adott előfizetés le van tiltva a következő szabályzatokkal: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Az Endpoint protection ("endpointProtectionMonitoringEffect") 

 
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

Ez a példa bemutatja, hogyan-hozzárendelés eltávolítása:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


## Nevek házirend-referencia <a name="policy-names"></a>

|Házirend neve a Security Centerben|Házirend neve jelenik meg az Azure Policy |A házirend érvénybe paraméter neve|
|----|----|----|
|SQL-titkosítás |Az Azure Security Centerben a titkosítatlan SQL database monitorozása |sqlEncryptionMonitoringEffect| 
|SQL Auditing (SQL-naplózás) |Az Azure Security Center nem naplózott SQL database monitorozása |sqlAuditingMonitoringEffect|
|System updates (Rendszerfrissítések) |Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése |systemUpdatesMonitoringEffect|
|Storage-titkosítás |Tárfiókok hiányzó blobtitkosításának naplózása |storageEncryptionMonitoringEffect|
|JIT hálózati hozzáférés |Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése |jitNetworkAccessMonitoringEffect |
|Adaptív alkalmazásvezérlők |Alkalmazások az Azure Security Center engedélyezésének figyelése |adaptiveApplicationControlsMonitoringEffect|
|Network security groups (Hálózati biztonsági csoportok) |Az Azure Security Center megengedő hálózati hozzáférés figyelése |networkSecurityGroupsMonitoringEffect| 
|Biztonsági konfigurációk |Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése |systemConfigurationsMonitoringEffect| 
|Endpoint protection (Végpontok védelme) |Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása |endpointProtectionMonitoringEffect |
|Disk encryption (Lemeztitkosítás) |Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása |diskEncryptionMonitoringEffect|
|Sebezhetőségi felmérés |A figyelő virtuális gépek biztonsági réseinek az Azure Security Centerben |vulnerabilityAssesmentMonitoringEffect|
|Web application firewall (Webalkalmazási tűzfal) |Az Azure Security Center nem védett webalkalmazás figyelése |webApplicationFirewallMonitoringEffect |
|Next generation firewall (Új generációs tűzfal) |Az Azure Security Center nem védett hálózati végpontok figyelése| |





## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett az Azure Policy biztonsági szabályzatok szerkesztése. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Bérlőszintű láthatóság az Azure Security Centerben](security-center-management-groups.md): Megismerheti a felügyeleti csoportok beállításának módját az Azure Security Centerben.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
