---
title: Az Azure Policy használata szabályzatok a vállalati megfelelőség kikényszerítése érdekében történő létrehozásához és kezeléséhez
description: Az Azure Policy szabványok kikényszerítésére, előírt megfelelőségi és naplózási követelmények teljesítésére, költségek szabályozására, a konzisztens biztonság és teljesítmény fenntartására és az egész vállalatra kiterjedő tervezési alapelvek meghatározására használható.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a7495ca1153fa6b84902423ee79c69f24316cb40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980957"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából

Fontos megismernie, hogy hogyan hozhat létre és kezelhet szabályzatokat az Azure-ban annak érdekében, hogy megfeleljen a vállalati szabványoknak és a szolgáltatói szerződéseknek. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Policyt a vállalaton belüli szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez kapcsolódó gyakori feladatok elvégzésére, például a következőkre:

> [!div class="checklist"]
> - Szabályzat hozzárendelése egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Kezdeményezési definíció létrehozása és hozzárendelése több erőforrás megfelelőségének nyomon követése céljából
> - Nem megfelelő vagy elutasított erőforrás feloldása
> - Új szabályzat megvalósítása a vállalaton belül

Ha szeretne hozzárendelni egy szabályzatot a meglévő erőforrások aktuális megfelelőségi állapotának azonosításához, a rövid útmutató cikkei ismertetik ennek módját. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="assign-a-policy"></a>Szabályzat hozzárendelése

A megfelelőség Azure Policy használatával történő kikényszerítésének első lépése egy szabályzatdefiníció hozzárendelése. A szabályzatdefiníció határozza meg, hogy milyen feltétel teljesülése esetében lesz kikényszerítve a szabályzat, illetve milyen hatása lesz. Ebben a példában rendeljen hozzá egy, *Az SQL Server 12.0-s verziójának megkövetelése* nevű beépített szabályzatdefiníciót. Ezzel kikényszeríti azt a feltételt, hogy minden SQL Server-adatbázisnak 12.0-s verziójúnak kell lennie ahhoz, hogy teljesüljenek a megfelelőségi elvárások.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

   ![Szabályzat keresése](../media/create-and-manage/search-policy.png)

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   ![Hozzárendelések kiválasztása](../media/create-and-manage/select-assignments.png)

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   ![Szabályzatdefiníció hozzárendelése](../media/create-and-manage/select-assign-policy.png)

1. A **Szabályzat hozzárendelése** oldalon a **Hatókör** kiválasztásához kattintson a három pontra, majd válasszon ki egy felügyeleti csoportot vagy egy előfizetést. Egy erőforráscsoportot is kijelölhet. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve.  Ezután kattintson a **Kiválasztás** gombra a **Hatókör** oldal alján.

   Ebben a példában a **Contoso** előfizetést használjuk. Saját előfizetése ettől eltérhet.

1. Az erőforrások a **hatókör** alapján zárhatók ki.  A **Kizárások** egy szinttel alacsonyabban kezdődnek, mint a **hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. A szabályzatdefiníció **Típus** tulajdonságát szűrheti *Beépített* érték alapján az összes megtekintéséhez és a leírásaik elolvasásához.

1. Válassza **Az SQL Server 12.0-s verziójának megkövetelése** lehetőséget. Ha azonnal nem találja, írja be a **verziójának megkövetelése** kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt, vagy kattintson a keresőmezőn kívülre. Ha megtalálta és kijelölte a szabályzatdefiníciót, kattintson a **Kiválasztás** gombra az **Elérhető definíciók** oldal alján.

   ![Szabályzat megkeresése](../media/create-and-manage/select-available-definition.png)

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. Ebben a példában tartsa meg *Az SQL Server 12.0-s verziójának megkövetelése* nevet. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.  A **Hozzárendelte** mező kitöltése automatikus az éppen bejelentkezett felhasználó alapján. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Hagyja bejelöletlenül a **Felügyelt identitás létrehozása** mezőt. Ezt _kötelező_ bejelölni, ha a hozzárendelt szabályzat vagy kezdeményezés [deployIfNotExists](../concepts/effects.md#deployifnotexists) hatású szabályzatot tartalmaz. Mivel az ebben az oktatóanyagban használt szabályzat ilyet nem tartalmaz, hagyja üresen a mezőt. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a **Hozzárendelés** gombra.

## <a name="implement-a-new-custom-policy"></a>Új egyéni szabályzat megvalósítása

Most, hogy hozzárendelt egy beépített szabályzatdefiníciót, még többet végezhet el az Azure Policyvel. Következő lépésként hozzon létre egy új egyéni szabályzatot a költségek csökkentéséhez, amely megszabja, hogy a környezetben létrehozott virtuális gépek nem lehetnek G sorozatúak. Így valahányszor a cég egy felhasználója megpróbál létrehozni egy G sorozatú virtuális gépet, a rendszer megtagadja a kérelmet.

1. A **Létrehozás** területen válassza a **Definíciók** elemet az Azure Policy lap bal oldalán.

   ![Definíció elem a létrehozás területen](../media/create-and-manage/definition-under-authoring.png)

1. Kattintson a **+ Szabályzatdefiníció** elemre a lap tetején. Ezzel megnyitja a **Szabályzatdefiníció** oldalt.

1. Adja meg a következőket:

   - Az a felügyeleti csoport vagy előfizetés, amelyben a szabályzatdefiníció mentve lett. A kiválasztáshoz kattintson a **Definíció helye** alatt található három pontra.

     > [!NOTE]
     > Ha ezt a szabályzatdefiníciót több előfizetésre szeretné alkalmazni, a helynek egy olyan felügyeleti csoportnak kell lennie, amely tartalmazza azokat az előfizetéseket, amelyekhez hozzárendeli a szabályzatot. Ugyanez vonatkozik a kezdeményezési definíciókra.

   - A szabályzatdefiníció neve – *A G sorozat alatti termékváltozat megkövetelése a virtuális gépek esetében*
   - A szabályzatdefiníciók céljának leírása – *Ez a szabályzatdefiníció kikényszeríti, hogy a költségek csökkentése érdekében az ebben a hatókörben létrehozott minden virtuális gép termékváltozata a G sorozat alatti legyen.*
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

    A szabályzat szabályában a *mező* tulajdonság értékének a következők egyikének kell lennie: Név, Típus, Hely, Címkék vagy alias. Példa egy aliasra: `"Microsoft.Compute/VirtualMachines/Size"`.

    További Azure-szabályzatmintákért lásd: [Az Azure Policy mintái](../samples/index.md).

1. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-definition-with-rest-api"></a>Szabályzatdefiníció létrehozása REST API-val

Létrehozhat egy szabályzatot a szabályzatdefiníciókhoz tartozó REST API-val. A REST API lehetővé teszi a szabályzatdefiníciók létrehozását és törlését, valamint a meglévő definíciók információinak lekérését. Szabályzatdefiníció létrehozásához használja a következő példát:

```http-interactive
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

A PowerShell-példára való továbblépés előtt győződjön meg arról, hogy telepítette az Azure PowerShell legújabb verzióját. A szabályzatparaméterek a 3.6.0-ás verzióban lettek hozzáadva. Ha egy korábbi verzióval rendelkezik, a példák hibaüzenetet adnak vissza, amely tájékoztatja, hogy a paraméter nem található.

Szabályzatdefiníciót a `New-AzureRmPolicyDefinition` parancsmag használatával is létrehozhat.

Szabályzatdefiníció fájlból történő létrehozásához adja meg a fájl elérési útját. Külső fájl esetében használja az alábbi példát:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Helyi fájl esetében használja a következő példát:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
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

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Szabályzatdefiníciók megtekintése a PowerShell használatával

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmPolicyDefinition
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

A szabályzatdefiníció paranccsal az Azure CLI használatával is létrehozhat egy szabályzatdefiníciót. Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

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

A kezdeményezési definícióval több szabályzatdefiníciót csoportosíthat egy átfogó cél eléréséhez. Létrehozhat egy kezdeményezési definíciót annak biztosítása érdekében, hogy a definíció hatókörén belüli erőforrások megfelelők maradjanak a kezdeményezési definíciót alkotó szabályzatdefinícióknak. További információt a kezdeményezési definíciókról az [Azure Policy – áttekintés](../overview.md) című részben talál.

### <a name="create-an-initiative-definition"></a>Kezdeményezési definíció létrehozása

1. A **Létrehozás** területen válassza a **Definíciók** elemet az Azure Policy lap bal oldalán.

   ![Definíciók kiválasztása](../media/create-and-manage/definition-under-authoring.png)

1. A **Kezdeményezési definíció** oldal megnyitásához kattintson a **+ Kezdeményezési definíció** elemre az oldal tetején.

   ![Kezdeményezési definíció](../media/create-and-manage/initiative-definition.png)

1. Kattintson a **Definíció helye** melletti három pontra a definíciót tároló felügyeleti csoport vagy előfizetés kiválasztásához. Ha az előző lap hatóköre egyetlen felügyeleti csoportra vagy előfizetésre terjed ki, a **Definíció helye** automatikusan kitöltődik.

1. Adja meg a kezdeményezés **nevét** és **leírását**.

   Ebben a példában győződjön meg arról, hogy az erőforrások megfelelnek a biztonságossá tételről szóló szabályzatdefinícióknak. Adja a kezdeményezésnek a **Biztonságossá tétel** nevet, és állítsa be a következő leírást: **Ez a kezdeményezés az erőforrások biztosításához kapcsolódó szabályzatdefiníciók kezelésére lett létrehozva**.

1. A **Kategória** megadásakor válasszon a meglévő lehetőségek közül, vagy hozzon létre új kategóriát.

1. Tekintse át az **Elérhető definíciók** listáját a **Kezdeményezési definíció** oldal jobb oldalán, és válassza ki azt a szabályzatdefiníciót (vagy definíciókat), amelye(ke)t hozzá szeretne adni a kezdeményezéshez. A **Biztonságossá tétel** kezdeményezéshez adja hozzá az alábbi beépített szabályzatdefiníciókat a szabályzatdefiníció részletei mellett látható **+** gombra kattintva, vagy kattintson a szabályzatdefiníció sorára, majd a **+ Hozzáadás** lehetőségre a részleteket tartalmazó oldalon:

   - Az SQL Server 12.0-ás verziójának megkövetelése
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   A listáról kiválasztott szabályzatdefiníció a **Szabályzatok és paraméterek** területre kerül.

   ![Kezdeményezési definíciók](../media/create-and-manage/initiative-definition-2.png)

1. Ha a kezdeményezéshez adott valamely szabályzatdefiníció rendelkezik paraméterekkel, ezek a szabályzat neve alatt jelennek meg a **Szabályzatok és paraméterek** területen. Az _érték_ beállítható „Megadott érték” (nem módosítható érték az adott kezdeményezés minden hozzárendelésében) vagy „A kezdeményezési paraméter használata” (az egyes kezdeményezés-hozzárendelésekkor kell megadni az értéket) állapotra. Ha a „Megadott érték” lehetőséget választja, az _Értékek_ jobb oldalán lévő legördülő menüben megadható(k) vagy kiválasztható(k) a kívánt érték(ek). Ha „A kezdeményezési paraméter használata” lehetőséget választja, megjelenik egy új, **Kezdeményezési paraméterek** szakasz, amelyben meghatározható a kezdeményezés hozzárendelésekor beállítandó paraméter. Az ebben a kezdeményezési paraméterben szereplő engedélyezett értékek tovább korlátozhatják, hogy mi adható meg a kezdeményezés hozzárendelésekor.

   ![Kezdeményezési definíciós paraméterek](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > Egyes `strongType` paraméterek esetében az értékek listája nem határozható meg automatikusan. Ezekben az esetekben három pont jelenik meg a paraméterek sorától jobbra. Erre kattintva megjelenik a „Paraméter hatóköre (&lt;paraméter neve&gt;)” oldal. Ezen az oldalon válassza ki az értéklehetőségek biztosítására szolgáló előfizetést. Ez a paraméter-hatókör kizárólag a kezdeményezés hozzárendelésének létrehozásakor használatos, és hozzárendelésekor nincs hatással a szabályzat-kiértékelésre vagy a kezdeményezés hatókörére.

1. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-an-initiative-definition"></a>Kezdeményezési definíció hozzárendelése

1. A **Létrehozás** területen válassza a **Definíciók** elemet az Azure Policy lap bal oldalán.

1. Keresse meg a korában létrehozott **Biztonságossá tétel** kezdeményezési definíciót, és kattintson rá. Kattintson az oldal tetején található **Hozzárendelés** lehetőségre a **Biztonságossá tétel: kezdeményezés hozzárendelése** oldal megnyitásához.

   ![Definíció hozzárendelése](../media/create-and-manage/assign-definition.png)

   Vagy a helyi menü megnyitásához kattintson a jobb egérgombbal a kiválasztott sorra, vagy a bal egérgombbal a sor szélén látható három pontra.  Ott válassza a **Hozzárendelés** parancsot.

   ![Kattintás a jobb gombbal egy sorra.](../media/create-and-manage/select-right-click.png)

1. A **Biztonságossá tétel: kezdeményezés hozzárendelése** oldalon adja meg a következő példaadatokat. A saját adatait is használhatja.

   - Hatókör: A felügyeleti csoport vagy előfizetés, ahová a kezdeményezést mentette lesz az alapértelmezett.  A hatókör módosításához mentse a kezdeményezést egy előfizetésbe vagy erőforráscsoportba a mentési helyen belül.
   - Kizárások: a hatókörön belül bármely erőforrást konfigurálhatja úgy, hogy a kezdeményezési hozzárendelés ne legyen alkalmazva rá.
   - Kezdeményezési definíció és hozzárendelés neve: Biztonságossá tétel (a rendszer automatikusan a hozzárendelt kezdeményezés nevével tölti fel a mezőt).
   - Leírás: Ez a kezdeményezési hozzárendelés ennek a szabályzatdefiníció-csoportnak a kikényszerítésére lett létrehozva.
   - Hozzárendelte: A kitöltése automatikus az éppen bejelentkezett felhasználó alapján. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Hagyja bejelöletlenül a **Felügyelt identitás létrehozása** mezőt. Ezt _kötelező_ bejelölni, ha a hozzárendelt szabályzat vagy kezdeményezés [deployIfNotExists](../concepts/effects.md#deployifnotexists) hatású szabályzatot tartalmaz. Mivel az ebben az oktatóanyagban használt szabályzat ilyet nem tartalmaz, hagyja üresen a mezőt. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a **Hozzárendelés** gombra.

## <a name="check-initial-compliance"></a>Kezdeti megfelelőség ellenőrzése

1. Válassza a **Megfelelőség** lehetőséget az Azure Policy lap bal oldalán.

1. Keresse meg a **Forrás beolvasása** kezdeményezést. Valószínű, hogy a _Megfelelőségi állapota_ továbbra is **Nincs elindítva**. Kattintson a kezdeményezésre a hozzárendelés előrehaladására vonatkozó részletes információkért.

   ![Megfelelőség – nincs elindítva](../media/create-and-manage/compliance-status-not-started.png)

1. A kezdeményezés hozzárendelésének befejezését követően a megfelelőségi lap frissül a **Megfelelő** _Megfelelőségi állapottal_.

   ![Megfelelőség – megfelelő](../media/create-and-manage/compliance-status-compliant.png)

1. Ha rákattint bármelyik szabályzatra a kezdeményezés megfelelősége lapon, akkor megnyílik a megfelelőség részletei lap a szabályzathoz. Ez a lap az erőforrás szintjén tartalmaz részleteket a megfelelőséghez.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Nem megfelelő vagy elutasított erőforrás mentesítése Kizárás használatával

A fenti példát követve, miután hozzárendelte a szabályzatdefiníciót az SQL Server 12.0-s verziójának megköveteléséhez, egy 12.0-stól eltérő verziójú SQL-kiszolgáló létrehozását a rendszer elutasítja. Ez a szakasz egy SQL-kiszolgáló létrehozására tett elutasított kísérlet egy erőforráscsoportra vonatkozó kizárás létrehozásával történő feloldásán vezeti végig. A kizárás megakadályozza a szabályzat (vagy kezdeményezés) kikényszerítését az adott erőforráson.
A következő példában bármely SQL-kiszolgálóverzió megengedett egyetlen erőforráscsoportban. A kizárás alkalmazható előfizetésre, erőforráscsoportra, vagy leszűkíthető egyéni erőforrásokra is.

Egy hozzárendelt szabályzat vagy kezdeményezés miatt meghiúsult üzembe helyezés két helyen tekinthető meg:

- Az üzembe helyezés által megcélzott erőforráscsoportban: Az oldal bal oldalán válassza az **Üzembe helyezések** elemet, és kattintson a sikertelen üzembe helyezés **Üzembe helyezés neve** tulajdonságára. Az elutasított erőforrás _Tiltott_ állapotúként jelenik meg a listában. Ha tudni szeretné, hogy melyik szabályzat vagy kezdeményezés és hozzárendelés utasította el az erőforrás használatát, az üzembe helyezés áttekintési oldalán kattintson a **Sikertelen. A részletekért kattintson ide ->** elemre. A lap jobb oldalán megnyílik egy ablak a hibára vonatkozó információkkal. A **Hiba részletei** pontban találhatók a kapcsolódó szabályzatobjektumok GUID-értékei.

  ![Szabályzat-hozzárendelés által elutasított üzembe helyezés](../media/create-and-manage/rg-deployment-denied.png)

- Az Azure Policy oldalon: Az oldal bal oldalán válassza a **Megfelelőség** elemet, és kattintson **Az SQL Server 12.0-s verziójának megkövetelése** szabályzatra. A megnyíló oldalon látni fogja, hogy nőtt a **Megtagadás** értéke. Az **Események** lapon azt is megtekintheti, hogy ki kísérelte meg a szabályzat által elutasított üzembe helyezést.

  ![Hozzárendelt szabályzatok megfelelőségének áttekintése](../media/create-and-manage/compliance-overview.png)

Ebben a példában Trent Baker, a Contoso egyik vezető virtualizálási szakembere végezte el a kijelölt feladatot. Egy kivételt kell létrehozni a számára, de nem szeretnénk, ha a nem 12.0-s verziójú SQL-kiszolgálók bármelyik erőforráscsoporthoz hozzáférnének. Létrehoztunk egy új erőforráscsoportot **SQLServers_Excluded** néven, és most kivételt biztosítunk a számára a szabályzat-hozzárendelés alól.

### <a name="update-assignment-with-exclusion"></a>Hozzárendelés frissítése egy kizárással

1. A **Létrehozás** területen válassza a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán.

1. Tallózzon az összes szabályzat-hozzárendelés között, majd nyissa meg az *Az SQL Server 12.0-ás verziójának megkövetelése* hozzárendelést.

1. A **Kizárás** beállításához kattintson a három pontra, majd válassza ki a kizárni kívánt erőforráscsoportot, ami ebben a példában az *SQLServers_Excluded*.

   ![Kizárás kérelmezése](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > A szabályzattól és annak hatásától függően a kizárás vonatkozhat a hozzárendelés hatókörén belül található erőforráscsoportok egyes erőforrásaira is. Mivel az oktatóanyagban egy **Megtagadás** hatást alkalmaztunk, nem lenne értelme kizárást beállítani egy már létező erőforrásra.

1. Kattintson a **Kiválasztás**, majd a **Mentés** gombra.

Ebben a szakaszban megoldotta a tiltott verziószámú SQL-kiszolgáló létrehozására tett kísérlet elutasítását egy egyetlen erőforráscsoportra vonatkozó kizárás létrehozásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem kíván tovább dolgozni az oktatóanyag keretében létrehozott erőforrásokkal, akkor a következő lépésekkel törölheti a fent létrehozott hozzárendeléseket vagy definíciókat:

1. A **Létrehozás** területen válassza a **Definíciók** (vagy ha egy hozzárendelést kíván törölni, a **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Hozzárendelt egy szabályzatot egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Létrehozott és hozzárendelt egy kezdeményezési definíciót több erőforrás megfelelőségének nyomon követése céljából
> - Feloldott egy nem megfelelő vagy elutasított erőforrást
> - Megvalósított egy új szabályzatot a vállalaton belül

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)