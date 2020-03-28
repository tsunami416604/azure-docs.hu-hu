---
title: 'Oktatóanyag: Szabályzatok létrehozása a megfelelőség kényszerítéséhez'
description: Ebben az oktatóanyagban a szabályzatok a szabványok érvényesítésére, a költségek szabályozására, a biztonság fenntartására és a vállalati szintű tervezési elvek előírására használhatók.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: 15a6c3df26938332d42ecbcfff43f958577062c4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239956"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez

Fontos megismernie, hogy hogyan hozhat létre és kezelhet szabályzatokat az Azure-ban annak érdekében, hogy megfeleljen a vállalati szabványoknak és a szolgáltatói szerződéseknek. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Policyt a vállalaton belüli szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez kapcsolódó gyakori feladatok elvégzésére, például a következőkre:

> [!div class="checklist"]
> - Szabályzat hozzárendelése egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Kezdeményezési definíció létrehozása és hozzárendelése több erőforrás megfelelőségének nyomon követése céljából
> - Nem megfelelő vagy elutasított erőforrás feloldása
> - Új szabályzat megvalósítása a vállalaton belül

Ha szeretne hozzárendelni egy szabályzatot a meglévő erőforrások aktuális megfelelőségi állapotának azonosításához, a rövid útmutató cikkei ismertetik ennek módját.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="assign-a-policy"></a>Szabályzat hozzárendelése

A megfelelőség Azure Policy használatával történő kikényszerítésének első lépése egy szabályzatdefiníció hozzárendelése. A szabályzatdefiníció határozza meg, hogy milyen feltétel teljesülése esetében lesz kikényszerítve a szabályzat, illetve milyen hatása lesz. Ebben a példában rendelje hozzá a beépített házirend-definíciót, amelynek neve _Címke öröklése az erőforráscsoportból, ha hiányzik,_ hogy hozzáadja a megadott címkét a szülő erőforráscsoportból származó értékével az új vagy frissített erőforrásokhoz, amelyek nem rendelkeznek címkével.

1. A szabályzatok hozzárendeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza a **Házirend**lehetőséget.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Házirend keresése a keresősávban" border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Hozzárendelések kiválasztása a Házirend áttekintése lapon" border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Házirend-definíció hozzárendelése a Hozzárendelések lapról" border="false":::

1. A **Házirend hozzárendelése** és **az Alapok** lapon válassza a **Hatókör** lehetőséget a három pont kiválasztásával, illetve a felügyeleti csoport vagy az előfizetés kiválasztásával. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve.
   Ezután válassza a **Kijelölés** lehetőséget a **Hatókör** lap alján.

   Ebben a példában a **Contoso** előfizetést használjuk. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. A szabályzatdefiníció **Típus** tulajdonságát szűrheti _Beépített_ érték alapján az összes megtekintéséhez és a leírásaik elolvasásához.

1. Válassza **a Címke öröklése az erőforráscsoportból lehetőséget, ha hiányzik.** Ha nem találja azonnal, írja be az **öröklési címkét** a keresőmezőbe, majd nyomja le az ENTER billentyűt, vagy válassza ki a keresőmezőt.
   Miután megtalálta és kiválasztotta a házirend-definíciót, válassza a **Kijelölés** lehetőséget az **Elérhető definíciók** lap alján.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Házirend keresése keresési szűrővel":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. Ebben a példában hagyja _a Címke öröklése az erőforráscsoportból, ha hiányzik._ Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.

1. Hagyja **a házirend-kényszerítést** _engedélyezve._ _Letiltva_ez a beállítás lehetővé teszi a házirend kimenetelének tesztelését a hatás aktiválása nélkül. További információt a [Kényszerítési mód című témakörben talál.](../concepts/assignment-structure.md#enforcement-mode)

1. **A hozzárendelt rendszer** automatikusan kitölti a bejelentkezett eket. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Válassza a **Paraméterek** lapot a varázsló tetején.

1. A **Címkeneve mezőbe**írja be a Környezet mezőbe a _Környezet_et.

1. Válassza a **Szervizelés** lapot a varázsló tetején.

1. Hagyja **bejelölve a Szervizelési feladat létrehozása** című területet. Ebben a mezőben létrehozhat egy olyan tevékenységet, amely módosítja a meglévő erőforrásokat az új vagy frissített erőforrások mellett. További információt az [erőforrások kijavítása](../how-to/remediate-resources.md)című témakörben talál.

1. **Felügyelt identitás létrehozása** automatikusan ellenőrzi, mivel ez a házirend-definíció a [módosítási](../concepts/effects.md#modify) hatást használja. **Az engedélyek** beállítása _közreműködői_ beállítás a házirend-definíció alapján. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Válassza a **Véleményezés + létrehozás** lapot a varázsló tetején.

1. Tekintse át a beállításokat, majd válassza a lap alján található **Létrehozás** lehetőséget.

## <a name="implement-a-new-custom-policy"></a>Új egyéni szabályzat megvalósítása

Most, hogy hozzárendelt egy beépített szabályzatdefiníciót, még többet végezhet el az Azure Policyvel. Ezután hozzon létre egy új egyéni szabályzatot a költségek csökkentése érdekében, ha ellenőrzi, hogy a környezetben létrehozott virtuális gépek nem lehetnek a G sorozatban. Így valahányszor a cég egy felhasználója megpróbál létrehozni egy G sorozatú virtuális gépet, a rendszer megtagadja a kérelmet.

1. Válassza **a Definíciók csoportban** **Szerzői** az Azure Policy lap bal oldalán.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definíciólap a Szerzői csoport csoportban" border="false":::

1. Kattintson a **+ Szabályzatdefiníció** elemre a lap tetején. Ez a gomb a **Házirend-definíció** lapra nyílik meg.

1. Adja meg a következő információkat:

   - Az a felügyeleti csoport vagy előfizetés, amelyben a szabályzatdefiníció mentve lett. A kiválasztáshoz kattintson a **Definíció helye** alatt található három pontra.

     > [!NOTE]
     > Ha ezt a szabályzatdefiníciót több előfizetésre szeretné alkalmazni, a helynek egy olyan felügyeleti csoportnak kell lennie, amely tartalmazza azokat az előfizetéseket, amelyekhez hozzárendeli a szabályzatot. Ugyanez vonatkozik a kezdeményezési definíciókra.

   - A házirend-definíció neve - _*_A G-sorozatnál kisebb vm-sorozatok megkövetelése_
   - A szabályzatdefiníciók céljának leírása – _Ez a szabályzatdefiníció kikényszeríti, hogy a költségek csökkentése érdekében az ebben a hatókörben létrehozott minden virtuális gép termékváltozata a G sorozat alatti legyen._
   - Válasszon a meglévő lehetőségek közül (például _Compute_), vagy hozzon létre egy új kategóriát ehhez a szabályzatdefinícióhoz.
   - Másolja le az alábbi JSON-kódot, és frissítse az igényeinek megfelelően az alábbiakkal:
      - A szabályzat paraméterei.
      - A szabályzat szabályai/feltételei, ebben az esetben: a virtuális gép termékváltozata megegyezik a G sorozattal
      - A szabályzat hatása, ebben az esetben: **Megtagadás**.

   A JSON-kódnak így kell kinéznie. Illessze be a módosított kódot az Azure Portalra.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   A _field_ házirend-szabálymező tulajdonságának támogatott értéknek kell lennie. A [házirend-definíciós struktúra mezőkben](../concepts/definition-structure.md#fields)található értékek teljes listája megtalálható. Példa egy aliasra: `"Microsoft.Compute/VirtualMachines/Size"`.

   További Azure-szabályzatmintákért lásd: [Az Azure Policy mintái](../samples/index.md).

1. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-definition-with-rest-api"></a>Szabályzatdefiníció létrehozása REST API-val

Létrehozhat egy szabályzatot a REST API-val az Azure-szabályzatdefiníciókhoz. A REST API lehetővé teszi a szabályzatdefiníciók létrehozását és törlését, valamint a meglévő definíciók információinak lekérését. Szabályzatdefiníció létrehozásához használja a következő példát:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Használjon az alábbi példában láthatóhoz hasonló kéréstörzset:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Szabályzatdefiníció létrehozása a PowerShell használatával

Mielőtt folytatná a PowerShell-példát, győződjön meg arról, hogy telepítette az Azure PowerShell Az modul legújabb verzióját.

Szabályzatdefiníciót a `New-AzPolicyDefinition` parancsmag használatával is létrehozhat.

Szabályzatdefiníció fájlból történő létrehozásához adja meg a fájl elérési útját. Külső fájl esetében használja az alábbi példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Helyi fájl esetében használja a következő példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

A kimenetet egy `$definition` objektumban tárolja a rendszer, amely a szabályzat-hozzárendelés során használatos. Az alábbi példában paramétereket tartalmazó szabályzatdefiníciót hozunk létre:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Szabályzatdefiníciók megtekintése a PowerShell használatával

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Szabályzatdefiníció létrehozása az Azure CLI használatával

Létrehozhat egy szabályzatdefiníciót az Azure `az policy definition` CLI használatával a paranccsal. Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Szabályzatdefiníciók megtekintése az Azure CLI használatával

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```azurecli-interactive
az policy definition list
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Kezdeményezési definíció létrehozása és hozzárendelése

A kezdeményezési definícióval több szabályzatdefiníciót csoportosíthat egy átfogó cél eléréséhez. A kezdeményezés kiértékeli a hozzárendelés hatókörén belüli erőforrásokat a mellékelt szabályzatok nak való megfelelés szempontjából. További információt a kezdeményezési definíciókról az [Azure Policy – áttekintés](../overview.md) című részben talál.

### <a name="create-an-initiative-definition"></a>Kezdeményezési definíció létrehozása

1. Válassza **a Definíciók csoportban** **Szerzői** az Azure Policy lap bal oldalán.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definíció kiválasztása a Definíciók lapon" border="false":::

1. A **Kezdeményezési definíció** oldal megnyitásához kattintson a **+ Kezdeményezési definíció** elemre az oldal tetején.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Kezdeményezésdefiníciós lap áttekintése" border="false":::

1. Kattintson a **Definíció helye** melletti három pontra a definíciót tároló felügyeleti csoport vagy előfizetés kiválasztásához. Ha az előző lap hatóköre egyetlen felügyeleti csoportra vagy előfizetésre terjed ki, a **Definíció helye** automatikusan kitöltődik. Miután kiválasztotta, **az Elérhető definíciók** vannak feltöltve.

1. Adja meg a kezdeményezés **nevét** és **leírását**.

   Ez a példa ellenőrzi, hogy az erőforrások megfelelnek-e a biztonságos sálatházirend-definícióknak. Adja a kezdeményezésnek a **Biztonságossá tétel** nevet, és állítsa be a következő leírást: **Ez a kezdeményezés az erőforrások biztosításához kapcsolódó szabályzatdefiníciók kezelésére lett létrehozva**.

1. A **Kategória** megadásakor válasszon a meglévő lehetőségek közül, vagy hozzon létre új kategóriát.

1. Tekintse át az **Elérhető definíciók** listáját a **Kezdeményezési definíció** oldal jobb oldalán, és válassza ki azt a szabályzatdefiníciót (vagy definíciókat), amelye(ke)t hozzá szeretne adni a kezdeményezéshez. A **Biztonságos betöltés** kezdeményezéshez adja hozzá a következő **+** beépített házirend-definíciókat a házirend-definíciós adatok melletti gombra kattintva, vagy jelöljön ki egy házirend-definíciós sort, majd a **+ Add** opciót a részletek lapon:

   - Engedélyezett helyek
   - Hiányzó végpontvédelem figyelése az Azure Security Centerben
   - A virtuális gépek internetre néző hálózati biztonsági csoportszabályait meg kell erősíteni
   - Az Azure Backup biztonsági mentést engedélyezni kell a virtuális gépekhez
   - A lemeztitkosítást virtuális gépeken kell alkalmazni

   Miután kiválasztotta a házirend-definíciót a listából, mindegyik a Kategória alá **kerül.**

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Kezdeményezésdefiníciói paraméterek áttekintése" border="false":::

1. Ha a kezdeményezéshez hozzáadott házirend-definíció paramétereket tartalmaz, azok a **Kategória** terület területén a házirend neve alatt jelennek meg. Az _érték_ beállítható „Megadott érték” (nem módosítható érték az adott kezdeményezés minden hozzárendelésében) vagy „A kezdeményezési paraméter használata” (az egyes kezdeményezés-hozzárendelésekkor kell megadni az értéket) állapotra. Ha az "Érték beállítása" lehetőséget választja, az _Érték(ek)_ jobb oldalán található legördülő lista lehetővé teszi az érték(ek) megadását vagy kiválasztását. Ha „A kezdeményezési paraméter használata” lehetőséget választja, megjelenik egy új, **Kezdeményezési paraméterek** szakasz, amelyben meghatározható a kezdeményezés hozzárendelésekor beállítandó paraméter. Az ebben a kezdeményezési paraméterben szereplő engedélyezett értékek tovább korlátozhatják, hogy mi adható meg a kezdeményezés hozzárendelésekor.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Kezdeményezésdefinícióparamétereinek módosítása az engedélyezett értékekből" border="false":::

   > [!NOTE]
   > Egyes `strongType` paraméterek esetében az értékek listája nem határozható meg automatikusan. Ezekben az esetekben három pont jelenik meg a paraméterek sorától jobbra. A bejelölése megnyitja&lt;a&gt;"Paraméterhatókör ( paraméter neve )" lapot. Ezen az oldalon válassza ki az értéklehetőségek biztosítására szolgáló előfizetést. Ez a paraméter-hatókör kizárólag a kezdeményezés hozzárendelésének létrehozásakor használatos, és hozzárendelésekor nincs hatással a szabályzat-kiértékelésre vagy a kezdeményezés hatókörére.

   Állítsa az "Engedélyezett helyek" paramétert "Usa keleti része 2" értékre, a többit pedig hagyja alapértelmezett "AuditifNotExists" értékre.

1. Kattintson a **Mentés** gombra.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Szakpolitikai kezdeményezés definíciójának létrehozása az Azure CLI-vel

Létrehozhat egy szabályzati kezdeményezés definícióját az `az policy set-definition` Azure CLI használatával a paranccsal. Ha meglévő házirend-definícióval szeretne létrehozni egy házirendkezdeményezés-definíciót, használja a következő példát:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Házirend-kezdeményezés definíciójának létrehozása az Azure PowerShell használatával

Létrehozhat egy szabályzati kezdeményezés definícióját az `New-AzPolicySetDefinition` Azure PowerShell használatával a parancsmaggal. Ha meglévő házirend-definícióval rendelkező házirendkezdeményezés-definíciót szeretne létrehozni, használja a következő házirendkezdeményezés-definíciós `VMPolicySet.json`fájlt:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Kezdeményezési definíció hozzárendelése

1. Válassza **a Definíciók csoportban** **Szerzői** az Azure Policy lap bal oldalán.

1. Keresse meg és válassza ki a korában létrehozott **Biztonságossá tétel** kezdeményezési definíciót. Válassza a **Hozzárendelés** lehetőséget a lap tetején, ha meg szeretné nyitni a **Get Secure: Assign initiative** lapot.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Definíció hozzárendelése az Initiative definíciós lapjáról" border="false":::

   A jobb gombbal a kijelölt sorra is kattinthat, vagy a sor végén lévő három pontot kijelölheti egy helyi menühöz. Ott válassza a **Hozzárendelés** parancsot.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternatív lehetőségek a kezdeményezés" border="false":::

1. A **Biztonságossá tétel: kezdeményezés hozzárendelése** oldalon adja meg a következő példaadatokat. A saját adatait is használhatja.

   - Hatókör: A felügyeleti csoport vagy előfizetés, ahová a kezdeményezést mentette lesz az alapértelmezett.
     A hatókör módosításához mentse a kezdeményezést egy előfizetésbe vagy erőforráscsoportba a mentési helyen belül.
   - Kizárások: a hatókörön belül bármely erőforrást konfigurálhatja úgy, hogy a kezdeményezési hozzárendelés ne legyen alkalmazva rá.
   - Kezdeményezési definíció és hozzárendelés neve: Biztonságossá tétel (a rendszer automatikusan a hozzárendelt kezdeményezés nevével tölti fel a mezőt).
   - Leírás: Ez a kezdeményezési hozzárendelés ennek a szabályzatdefiníció-csoportnak a kikényszerítésére lett létrehozva.
   - Házirend-kényszerítés: Hagyja alapértelmezettként _engedélyezve._
   - Hozzárendelte: A kitöltése automatikus az éppen bejelentkezett felhasználó alapján. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Válassza a **Paraméterek** lapot a varázsló tetején. Ha az előző lépésekben konfigurált egy kezdeményezési paramétert, itt állítson be egy értéket.

1. Válassza a **Szervizelés** lapot a varázsló tetején. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a jelölőnégyzetet be _kell_ jelölni, ha a hozzárendelt házirend vagy kezdeményezés tartalmaz egy házirendet a [deployIfNotExists](../concepts/effects.md#deployifnotexists) vagy [módosító](../concepts/effects.md#modify) effektusok. Mivel az oktatóanyaghoz használt házirend nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Válassza a **Véleményezés + létrehozás** lapot a varázsló tetején.

1. Tekintse át a beállításokat, majd válassza a lap alján található **Létrehozás** lehetőséget.

## <a name="check-initial-compliance"></a>Kezdeti megfelelőség ellenőrzése

1. Válassza a **Megfelelőség** lehetőséget az Azure Policy lap bal oldalán.

1. Keresse meg a **Get Secure** kezdeményezést. Valószínűleg még mindig a Nem indított _megfelelőségi állapotban_ **van.**
   Válassza ki a kezdeményezést, hogy részletesen megismersd a hozzárendelés előrehaladását.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Kezdeményezés megfelelőségi oldala – az értékelések nem kezdődtek meg" border="false":::

1. A kezdeményezés hozzárendelésének befejezését követően a megfelelőségi lap frissül a **Megfelelő**_Megfelelőségi állapottal_.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Kezdeményezés megfelelőségi oldala - erőforrások kompatibilisek" border="false":::

1. A kezdeményezés megfelelőségi lapján bármelyik szabályzat kiválasztása megnyitja az adott szabályzat megfelelőségi részleteit tartalmazó lapot. Ez a lap az erőforrás szintjén tartalmaz részleteket a megfelelőséghez.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Nem megfelelő vagy elutasított erőforrás mentesítése Kizárás használatával

Miután egy házirend-kezdeményezést egy adott hely megköveteléséhez rendelt, a program minden más helyen létrehozott erőforrást megtagad. Ebben a szakaszban végigvezeti az erőforrás létrehozására vonatkozó elutasított kérelem feloldását azáltal, hogy egyetlen erőforráscsoporton létrehoz kizárást. A kizárás megakadályozza a házirend (vagy kezdeményezés) érvényesítését az adott erőforráscsoporton. A következő példában bármely hely engedélyezett a kizárt erőforráscsoportban. Kizárás vonatkozhat előfizetésre, erőforráscsoportra vagy egyéni erőforrásokra.

A hozzárendelt házirend vagy kezdeményezés által megakadályozott központi telepítések megtekinthetők a központi telepítés által megcélzott erőforráscsoportban: Válassza ki a lap bal oldalán **a központi telepítéseket,** majd válassza ki a sikertelen központi telepítés **központi telepítési nevét.** Az elutasított erőforrás _Tiltott_ állapotúként jelenik meg a listában. Az erőforrást megtagadó házirend vagy kezdeményezés és hozzárendelés meghatározásához válassza a **Sikertelen lehetőséget. Kattintson ide a ->a** Telepítés áttekintése lapon. A lap jobb oldalán megnyílik egy ablak a hibára vonatkozó információkkal. A **Hiba részletei csoportban** a kapcsolódó házirendobjektumok GUID azonosítói találhatók.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Szabályzat-hozzárendelés által elutasított üzembe helyezés" border="false":::

Az Azure Policy oldalon: Válassza ki a **megfelelőségi** a lap bal oldalán, és válassza ki a Biztonságos házirend **beszerez** kezdeményezés. Ezen a lapon a letiltott erőforrások **megtagadási** száma növekszik. Az **Események** lapon találhatók azok a részletek, amelyek a házirend-definíció által megtagadott erőforrást próbálták létrehozni vagy telepíteni.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Hozzárendelt szabályzatok megfelelőségének áttekintése" border="false":::

Ebben a példában Trent Baker, a Contoso egyik sr. virtualizációs szakembere végezte a szükséges munkát. Teret kell adnunk Trentnek egy kivételre. Létrehozott egy új erőforráscsoportot, **a LocationsExcluded**és a next grant it a exceptionetet a házirend-hozzárendelés alól.

### <a name="update-assignment-with-exclusion"></a>Hozzárendelés frissítése egy kizárással

1. Válassza **a Hozzárendelések** **csoportban Szerzői** az Azure Policy lap bal oldalán.

1. Böngésszen az összes házirend-hozzárendelés között, és nyissa meg a _Biztonságos házirend-hozzárendelés beírását._

1. Állítsa be a **kizárást** a három pont kiválasztásával és a kizárandó erőforráscsoport kiválasztásával, ebben a példában _kizárt Helyek._ Válassza **a Hozzáadás a kijelölt hatókörhöz lehetőséget,** majd a **Mentés gombot.**

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Kizárt erőforráscsoport hozzáadása a házirend-hozzárendeléshez" border="false":::

   > [!NOTE]
   > A házirend-definíciótól és annak hatásától függően a kizárás a hozzárendelés hatókörén belül egy erőforráscsoporton belüli erőforrásokszámára is megadható. Megtagadási **Deny** effektusként ebben az oktatóanyagban használták, nem lenne értelme a kizárást egy adott erőforrásra állítani, amely már létezik.

1. Válassza **a Véleményezés + Mentés,** majd a **Mentés**lehetőséget.

Ebben a szakaszban úgy oldotta meg a visszautasított kérelmet, hogy egyetlen erőforráscsoporton hozott létre kizárást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyagból származó erőforrásokkal, az alábbi lépésekkel törölheti a fent létrehozott házirend-hozzárendeléseket vagy -definíciókat:

1. Válassza **a Definíciók** (vagy **hozzárendelések,** ha egy hozzárendelést próbál törölni) lehetőséget az Azure Policy lap bal oldalán található **Szerzői** műveletek területen.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Hozzárendelt egy szabályzatot egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Létrehozott és hozzárendelt egy kezdeményezési definíciót több erőforrás megfelelőségének nyomon követése céljából
> - Feloldott egy nem megfelelő vagy elutasított erőforrást
> - Megvalósított egy új szabályzatot a vállalaton belül

## <a name="next-steps"></a>További lépések

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)