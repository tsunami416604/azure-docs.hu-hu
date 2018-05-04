---
title: Az Azure Policy használata szabályzatok létrehozásához és kezeléséhez a vállalati megfelelőségi szabályzatok kikényszerítéséhez | Microsoft Docs
description: Az Azure Policy szabványok kikényszerítésére, előírt megfelelőségi és naplózási követelmények teljesítésére, költségek szabályozására, a konzisztens biztonság és teljesítmény fenntartására és az egész vállalatra kiterjedő tervezési alapelvek meghatározására használható.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/19/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 0b6e77d080ce4e4483709b9a5e47dca21c22e1d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából

Fontos megismernie, hogy hogyan hozhat létre és kezelhet szabályzatokat az Azure-ban annak érdekében, hogy megfeleljen a vállalati szabványoknak és a szolgáltatói szerződéseknek. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Policyt a vállalaton belüli szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez kapcsolódó gyakori feladatok elvégzésére, például a következőkre:

> [!div class="checklist"]
> * Szabályzat hozzárendelése egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> * Kezdeményezési definíció létrehozása és hozzárendelése több erőforrás megfelelőségének nyomon követése céljából
> * Nem megfelelő vagy elutasított erőforrás feloldása
> * Új szabályzat megvalósítása a vállalaton belül

Ha szeretne hozzárendelni egy szabályzatot a meglévő erőforrások aktuális megfelelőségi állapotának azonosításához, a rövid útmutató cikkei ismertetik ennek módját. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="assign-a-policy"></a>Szabályzat hozzárendelése

A megfelelőség Azure Policy használatával történő kikényszerítésének első lépése egy szabályzatdefiníció hozzárendelése. A szabályzatdefiníció határozza meg, hogy milyen feltétel teljesülése esetében lesz kikényszerítve a szabályzat, illetve milyen művelet lesz végrehajtva. Ebben a példában rendeljen hozzá egy *Az SQL Server 12.0-ás verziójának megkövetelése* nevű beépített szabályzatdefiníciót. Ezzel kikényszeríti azt a feltételt, hogy minden SQL Server-adatbázisnak 12.0-ás verziójúnak kell lennie ahhoz, hogy megfelelő legyen.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez keresse meg, majd válassza ki a **Szabályzat** elemet a bal oldali panelen.

   ![Szabályzat keresése](media/assign-policy-definition/search-policy.png)

2. Válassza ki a **Hozzárendelések** elemet az Azure Policy webhely bal oldali panelén. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.
3. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Hozzárendelések** panel tetején.

   ![Szabályzatdefiníció hozzárendelése](media/create-manage-policy/select-assign-policy.png)

4. A **Szabályzat hozzárendelése** oldalon kattintson a ![Szabályzatdefiníció gombra](media/assign-policy-definition/definitions-button.png) a **Szabályzat** mező mellett az elérhető definíciók listájának megnyitásához. A szabályzatdefiníció **Típus** tulajdonságát szűrheti *Beépített* érték alapján az összes megtekintéséhez és leírásaik elolvasásához.

   ![Elérhető szabályzatdefiníciók megnyitása](media/create-manage-policy/open-policy-definitions.png)

5. Válassza az **Az SQL Server 12.0-ás verziójának megkövetelése** lehetőséget. Ha azonnal nem találja, írja be az **Az SQL Server 12.0-ás verziójának megkövetelése** kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

   ![Szabályzat megkeresése](media/create-manage-policy/select-available-definition.png)

6. A megjelenő **Név** mezőt automatikusan kitölti a rendszer, de ezt módosíthatja. Ebben a példában használja az *Az SQL Server 12.0-ás verziójának megkövetelése* nevet. Ha szeretné hozzáadhat egy **Leírást**. A leírás információt nyújt arról, hogyan biztosítja ez a szabályzat-hozzárendelés azt, hogy a környezetben létrehozott minden SQL-kiszolgáló 12.0-ás verziójú legyen.

7. Módosítsa a tarifacsomagot a **Standard** szintre, hogy a szabályzat kiterjedjen a meglévő erőforrásokra.

   Az Azure Policy-ban két tarifacsomag létezik – *Ingyenes* és *Standard*. Az Ingyenes szinten csak a jövőbeli erőforrások esetében kényszeríthet szabályzatokat, míg a Standard szinten már meglévő erőforrások esetében is, hogy jobban átlássa a megfelelőségi állapotot. Mivel az Azure Policy előzetes verzióban érhető el, még nincs kibocsátott díjszabási modell, ezért nem fog számlát kapni a *Standard* szint kiválasztásakor. A díjszabással kapcsolatos további információkért tekintse meg a következőt: [Azure Policy díjszabás](https://azure.microsoft.com/pricing/details/azure-policy).

8. Válassza ki a **Hatókört** – az előzőleg regisztrált előfizetést (vagy erőforráscsoportot). A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet.

   Ebben a példában az **Azure Analytics Capacity Dev** előfizetést használjuk. Saját előfizetése ettől eltérhet.

10. Válassza a **Hozzárendelés** elemet.

## <a name="implement-a-new-custom-policy"></a>Új egyéni szabályzat megvalósítása

Most, hogy hozzárendelt egy beépített szabályzatdefiníciót, még többet végezhet el az Azure Policyvel. Következő lépésként hozzon létre egy új egyéni szabályzatot a költségek csökkentéséhez, amely megszabja, hogy a környezetben létrehozott virtuális gépek nem lehetnek G sorozatúak. Így valahányszor a cég egy felhasználója megpróbál létrehozni egy G sorozatú virtuális gépet, a rendszer megtagadja a kérelmet.

1. A bal oldali panel **Létrehozás** területén válassza a **Definíció** lehetőséget.

   ![Definíció elem a létrehozás területen](media/create-manage-policy/definition-under-authoring.png)

2. Válassza a **+ Szabályzatdefiníció** lehetőséget.
3. Adja meg a következőket:

   - A szabályzatdefiníció neve – *A G sorozat alatti termékváltozat megkövetelése a virtuális gépek esetében*
   - A szabályzatdefiníciók céljának leírása – Ez a szabályzatdefiníció kikényszeríti, hogy az ebben a hatókörben létrehozott minden virtuális gép termékváltozata a G sorozat alatti legyen a költségek csökkentése érdekében.
   - Az előfizetés, amelyben a szabályzatdefiníció található. Ebben az esetben a szabályzatdefiníció az **Advisor Analytics Capacity Dev** helyen található. Saját előfizetéseinek listája ettől eltérhet.
   - Válasszon a meglévő lehetőségek közül, vagy hozzon létre egy új kategóriát ehhez a szabályzatdefinícióhoz.
   - Másolja az alábbi JSON-kódot, és frissítse az igényeinek megfelelően az alábbiakkal:
      - A szabályzat paraméterei.
      - A szabályzat szabályai/feltételei, ebben az esetben: a virtuális gép termékváltozata megegyezik a G sorozattal
      - A szabályzat hatása, ebben az esetben: **Megtagadás**.

    A JSON-kódnak így kell kinéznie. Illessze be a módosított kódot az Azure Portalra.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
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

    A szabályzat szabályában a *mezőtulajdonság* értékének a következők egyikének kell lennie: Név, Típus, Hely, Címkék vagy alias. Például: `"Microsoft.Compute/VirtualMachines/Size"`.

    További JSON-mintakódok megtekintéséhez olvassa el a [Sablonok az Azure Policyhez](json-samples.md) című cikket.

4. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-definition-with-rest-api"></a>Szabályzatdefiníció létrehozása REST API-val

Létrehozhat egy szabályzatot a szabályzatdefiníciókhoz tartozó REST API-val. A REST API lehetővé teszi a szabályzatdefiníciók létrehozását és törlését, valamint a meglévő definíciók információinak lekérését.
Szabályzatdefiníció létrehozásához használja a következő példát:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Használjon az alábbi példában láthatóhoz hasonló kéréstörzset:

```
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

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Helyi fájl esetében használja a következő példát:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

A kimenetet egy `$definition` objektumban tárolja a rendszer, amely a szabályzat-hozzárendelés során használatos.
Az alábbi példában paramétereket tartalmazó szabályzatdefiníciót hozunk létre:

```
$policy = '{
    "if": {
        "allOf": [
            {
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

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Szabályzatdefiníciók megtekintése

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```
Get-AzureRmPolicyDefinition
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```
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

A szabályzatdefiníció paranccsal az Azure CLI használatával is létrehozhat egy szabályzatdefiníciót.
Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Szabályzatdefiníciók megtekintése

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```
az policy definition list
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```
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

A kezdeményezési definícióval több szabályzatdefiníciót csoportosíthat egy átfogó cél eléréséhez. Létrehozhat egy kezdeményezési definíciót annak biztosítása érdekében, hogy a definíció hatókörén belüli erőforrások megfelelők maradjanak a kezdeményezési definíciót alkotó szabályzatdefinícióknak.  További információt a kezdeményezési definíciókról az [Azure Policy – áttekintés](./azure-policy-introduction.md) című részben talál.

### <a name="create-an-initiative-definition"></a>Kezdeményezési definíció létrehozása

1. A bal oldali panel **Létrehozás** területén válassza a **Definíciók** lehetőséget.

   ![Definíciók kiválasztása](media/create-manage-policy/select-definitions.png)

2. A lap tetején válassza a **Kezdeményezési definíció** lehetőséget. Ekkor megnyílik a **Kezdeményezési definíció** űrlap.
3. Adja meg a kezdeményezés nevét és leírását.

   Ebben a példában győződjön meg arról, hogy az erőforrások megfelelnek a biztonságossá tételről szóló szabályzatdefinícióknak. Tehát a kezdeményezés neve **Biztonságossá tétel** lesz, a leírás pedig a következő: **Ez a kezdeményezés az erőforrások biztosításához kapcsolódó szabályzatdefiníciók kezelésére lett létrehozva**.

   ![Kezdeményezési definíció](media/create-manage-policy/initiative-definition.png)

4. Tekintse át az **Elérhető definíciók** listáját, és válassza ki azt a szabályzatdefiníciót (vagy definíciókat), amelyet hozzá szeretne adni a kezdeményezéshez. A **Biztonságossá tétel** kezdeményezéshez adja hozzá az alábbi beépített szabályzatdefiníciókat a **Hozzáadás** lehetőséggel.
   - Az SQL Server 12.0-ás verziójának megkövetelése
   - Nem védett webalkalmazások monitorozása a Security Centerben.
   - Megengedő hálózat monitorozása a Security Centerben
   - Alkalmazások lehetséges engedélyezési listáinak monitorozása a Security Centerben.
   - Titkosítatlan virtuálisgép-lemezek monitorozása a Security Centerben.

   ![Kezdeményezési definíciók](media/create-manage-policy/initiative-definition-2.png)

   Miután kiválasztotta a listáról a szabályzatdefiníciókat, azok a **Szabályzatok és paraméterek** területen jelennek meg, ahogy a fenti képen látható.

5. Használja a **Definíció helye** lehetőséget a definíciót tároló előfizetés kiválasztásához. Kattintson a **Mentés** gombra.

### <a name="assign-an-initiative-definition"></a>Kezdeményezési definíció hozzárendelése

1. Lépjen a **Létrehozás** terület **Definíciók** lapjára.
2. Keresse meg az Ön által létrehozott **Biztonságossá tétel** kezdeményezési definíciót.
3. Válassza ki a kezdeményezési definíciót, majd kattintson a **Hozzárendelés** gombra.

   ![Definíció hozzárendelése](media/create-manage-policy/assign-definition.png)

4. Töltse ki a **Hozzárendelés** űrlapot a következő példaadatok megadásával. A saját adatait is használhatja.
   - Név: Biztonságossá tétel hozzárendelése
   - Leírás: Ez a kezdeményezési hozzárendelés a jelen szabályzatdefiníció-csoport az **Azure Advisor Capacity Dev** előfizetésben való kikényszerítésére lett létrehozva.
   - Tarifacsomag: Standard
   - A hatókör, amelyben a hozzárendelést alkalmazni szeretné: **Azure Advisor Capacity Dev**. Választhat saját előfizetést és erőforráscsoportot.

5. Válassza a **Hozzárendelés** elemet.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Nem megfelelő vagy elutasított erőforrás mentesítése Kizárás használatával

A fenti példát követve, miután hozzárendelte a szabályzatdefiníciót az SQL Server 12.0-s verziójának megköveteléséhez, egy 12.0-stól eltérő verziójú SQL-kiszolgáló létrehozását a rendszer elutasítja. Ez a szakasz egy SQL-kiszolgáló létrehozására tett elutasított kísérlet meghatározott erőforrások kizárásának kérelmezésével történő feloldásán vezeti végig. A kizárás lényegében a szabályzat kikényszerítését akadályozza meg. A következő példában bármely SQL-kiszolgálóverzió megengedett. A kizárás alkalmazható erőforráscsoportra, vagy leszűkíthető egyéni erőforrásokra is.

1. Válassza ki a **Hozzárendelések** elemet a bal oldali panelen.
2. Tallózzon az összes szabályzat-hozzárendelés között, majd nyissa meg az *Az SQL Server 12.0-ás verziójának megkövetelése* hozzárendelést.
3. **Válasszon ki** egy kizárást az abban az erőforráscsoportokban található erőforrásokhoz, amelyben az SQL-kiszolgálót próbálja létrehozni. Ebben a példában zárja ki a Microsoft.Sql/servers/databases: *azuremetrictest/testdb* és az *azuremetrictest/testdb2* erőforrást.

   ![Kizárás kérelmezése](media/create-manage-policy/request-exclusion.png)

   Egyéb lehetőségek az elutasított erőforrás feloldására: Lépjen kapcsolatba a szabályzathoz tartozó kapcsolattartóval, ha megalapozott indoka van az SQL-kiszolgáló létrehozására, vagy közvetlenül módosítsa a szabályzatot, ha rendelkezik hozzáféréssel.

4. Kattintson a **Hozzárendelés** gombra.

Ebben a szakaszban feloldotta az SQL-kiszolgáló létrehozására tett kísérlet elutasítását az erőforrások kizárásának kérelmezésével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben az útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a fent létrehozott hozzárendeléseket vagy definíciókat:

1. Válassza a **Definíciók** (vagy ha hozzárendelést próbál meg törölni, a **Hozzárendelések**) lehetőséget a bal oldali panelen.
2. Keresse meg az imént létrehozott új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).
3. Kattintson a definíció vagy a szabályzat végén található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> * Hozzárendelt egy szabályzatot egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> * Létrehozott és hozzárendelt egy kezdeményezési definíciót több erőforrás megfelelőségének nyomon követése céljából
> * Feloldott egy nem megfelelő vagy elutasított erőforrást
> * Megvalósított egy új szabályzatot a vállalaton belül

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](policy-definition.md)
