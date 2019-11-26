---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
ms.date: 11/25/2019
ms.topic: overview
ms.openlocfilehash: db6a7c592213b0ef8a17466300c37c859e96476b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484007"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Governance validates that your organization can achieve its goals through effective and efficient use of IT. It meets this need by creating clarity between business goals and IT projects.

A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik? A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását a problémák kezelése és megelőzése érdekében. This strategic need is where Azure Policy comes in.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Azure Policy meets this need by evaluating your resources for non-compliance with assigned policies. Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos méretű virtuálisgép-termékváltozatot engedélyez a környezetben. Once this policy is implemented, new and existing resources are evaluated for compliance. With the right type of policy, existing resources can be brought into compliance. Later in this documentation, we'll go over more details on how to create and implement policies with Azure Policy.

> [!IMPORTANT]
> Az Azure Policy megfelelőségértékelése mostantól tarifacsomagtól függetlenül minden hozzárendeléshez elérhető. Ha a hozzárendeléseknél nem láthatók a megfelelőségi adatok, győződjön meg róla, hogy az előfizetés regisztrálva van a Microsoft.PolicyInsights erőforrás-szolgáltatón.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

There are a few key differences between Azure Policy and role-based access control (RBAC). Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. You might be added to the contributor role for a resource group, allowing you to make changes to that resource group. Azure Policy focuses on resource properties during deployment and for already existing resources. Azure Policy controls properties such as the types or locations of resources. Unlike RBAC, Azure Policy is a default allow and explicit deny system.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. The **Resource Policy Contributor** role includes most Azure Policy operations. **Owner** has full rights. Both **Contributor** and **Reader** can use all read Azure Policy operations, but **Contributor** can also trigger remediation.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Every policy definition has conditions under which it's enforced. And, it has a defined effect that takes place if the conditions are met.

In Azure Policy, we offer several built-in policies that are available by default. Példa:

- **Allowed Storage Account SKUs**: Determines if a storage account being deployed is within a set of SKU sizes. Its effect is to deny all storage accounts that don't adhere to the set of defined SKU sizes.
- **Allowed Resource Type**: Defines the resource types that you can deploy. Its effect is to deny all resources that aren't part of this defined list.
- **Allowed Locations**: Restricts the available locations for new resources. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Allowed Virtual Machine SKUs**: Specifies a set of virtual machine SKUs that you can deploy.
- **Add a tag to resources**: Applies a required tag and its default value if it's not specified by the deploy request.
- **Enforce tag and its value**: Enforces a required tag and its value to a resource.
- **Not allowed resource types**: Prevents a list of resource types from being deployed.

To implement these policy definitions (both built-in and custom definitions), you'll need to assign them. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

Policy evaluation happens with several different actions, such as policy assignment or policy updates. For a complete list, see [Policy evaluation triggers](./how-to/get-compliance-data.md#evaluation-triggers).

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör bármi lehet egy [felügyeleti csoporttól](../management-groups/overview.md) egy erőforráscsoportig. A *hatókör* kifejezés az összes olyan erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyekhez a szabályzatdefiníció hozzá lett rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. This design means that a policy applied to a resource group is also applied to resources in that resource group. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon.

Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, You could exclude a resource group in that subscription that is intended for networking infrastructure. You then grant access to this networking resource group to users that you trust with creating networking resources.

In another example, you might want to assign a resource type allow list policy at the management group level. Ezután hozzárendelne egy megengedőbb (több erőforrástípust engedélyező) szabályzatot egy alárendelt felügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez azonban nem működne, mivel a szabályzat egy explicit tiltási rendszer. A megoldás az, hogy kizárja az alárendelt felügyeleti csoportot vagy az előfizetést a felügyeleti csoport szintjén hozzárendelt szabályzatból, majd hozzárendeli a megengedőbb szabályzatot az alárendelt felügyeleti csoportok vagy az előfizetések szintjén. If any policy results in a resource getting denied, then the only way to allow the resource is to modify the denying policy.

További, a szabályzatdefiníciók és -hozzárendelések a portálon keresztül történő beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-portal.md) foglalkozó cikket. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

Parameters are defined when creating a policy definition. When a parameter is defined, it's given a name and optionally given a value. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

For more information about policy parameters, see [Definition structure - Parameters](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

> [!NOTE]
> The SDK, such as Azure CLI and Azure PowerShell, use properties and parameters named **PolicySet** to refer to initiatives.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- **Monitor OS vulnerabilities in Security Center** – For monitoring servers that don't satisfy the configured baseline.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés

A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör szintén bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig.

Each initiative is assignable to different scopes. One initiative can be assigned to both **subscriptionA** and **subscriptionB**.

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. Initiative parameters are parameters being used by the policy definitions within the initiative.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az *allowedLocations* és az *allowedSingleLocation* lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. In this example, you can provide a list of locations to **policyA's parameter – allowedLocations** and **policyB's parameter – allowedSingleLocation**. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

When creating value options in an initiative definition, you're unable to input a different value during the initiative assignment because it's not part of the list.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum count of Azure Policy objects

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Here are a few pointers and tips to keep in mind:

- Start with an audit effect instead of a deny effect to track impact of your policy definition on the resources in your environment. If you have scripts already in place to autoscale your applications, setting a deny effect may hinder such automation tasks already in place.

- Consider organizational hierarchies when creating definitions and assignments. We recommend creating definitions at higher levels such as the management group or subscription level. Then, create the assignment at the next child level. If you create a definition at a management group, the assignment can be scoped down to a subscription or resource group within that management group.

- We recommend creating and assigning initiative definitions even for a single policy definition.
  For example, you have policy definition *policyDefA* and create it under initiative definition *initiativeDefC*. If you create another policy definition later for *policyDefB* with goals similar to *policyDefA*, you can add it under *initiativeDefC* and track them together.

- Once you've created an initiative assignment, policy definitions added to the initiative also become part of that initiatives assignments.

- When an initiative assignment is evaluated, all policies within the initiative are also evaluated.
  If you need to evaluate a policy individually, it's better to not include it in an initiative.

## <a name="video-overview"></a>Video overview

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. For slides or video download, visit [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) on Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Assign a policy definition using the portal](./assign-policy-portal.md).
- [Assign a policy definition using the Azure CLI](./assign-policy-azurecli.md).
- [Assign a policy definition using PowerShell](./assign-policy-powershell.md).