---
title: Paraméterek használata dinamikus tervrajzok létrehozásához
description: Tudjon meg többet a statikus és dinamikus paraméterekről, valamint arról, hogyan hozza létre a dinamikus tervrajzokat.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 9670433284ae963783b655322c4b18f748df52c5
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231954"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Dinamikus tervrajzok létrehozása paraméterek használatával

Egy teljes körűen definiált terv különböző összetevőkkel (például erőforráscsoportok, Resource Manager-sablonok, házirendek vagy szerepkör-hozzárendelésekkel) biztosítja az objektumok gyors létrehozását és konzisztens létrehozását az Azure-on belül. Az újrafelhasználható tervezési minták és tárolók rugalmas használatának lehetővé tételéhez az Azure tervrajzai támogatják a paramétereket. A paraméter a definíció és a hozzárendelés során is rugalmasságot hoz létre a terv által központilag telepített összetevők tulajdonságainak módosításához.

Egyszerű példa az erőforráscsoport-összetevő. Egy erőforráscsoport létrehozásakor két kötelező értéket kell megadni: a nevet és a helyet. Ha a tervhez hozzáad egy erőforráscsoportot, ha a paraméterek nem léteznek, a terv minden használatához meg kell adnia a nevet és a helyet. Ez az ismétlődés azt eredményezi, hogy a terv minden használata során összetevők hozhatók létre ugyanabban az erőforráscsoporthoz. Az erőforráscsoport erőforrásai duplikálva lesznek, és ütközést okozhatnak.

> [!NOTE]
> Nem jelent problémát két különböző tervezet esetében, hogy egy azonos nevű erőforráscsoportot tartalmazzon.
> Ha már létezik egy, a tervben szereplő erőforráscsoport, a terv továbbra is létrehozza a kapcsolódó összetevőket az adott erőforráscsoporthoz. Ez ütközést okozhat, mivel a két erőforrás ugyanazzal a névvel és erőforrástípus nem létezhet egy előfizetésen belül.

A probléma megoldása a paraméterek. A tervrajzok segítségével meghatározhatja az adott összetevő minden tulajdonságának értékét az előfizetéshez való hozzárendelés során. A paraméter lehetővé teszi egy olyan terv újrafelhasználását, amely egy adott erőforrás-csoportot és más erőforrásokat hoz létre egy előfizetésen belül, ütközés nélkül.

## <a name="blueprint-parameters"></a>Terv paraméterei

A REST APIon a paraméterek létrehozhatók a tervrajzon. Ezek a paraméterek eltérnek az egyes támogatott összetevők paramétereivel. Amikor létrehoznak egy paramétert a tervrajzon, azt az adott terv összetevői használhatják. Ilyen lehet például az erőforráscsoport elnevezésének előtagja. Az összetevő a terv paraméter használatával "többnyire dinamikus" paramétert hozhat létre. Mivel a paraméter a hozzárendelés során is meghatározható, ez a minta lehetővé teszi egy olyan konzisztencia használatát, amely megtarthatja az elnevezési szabályokat. A lépéseket lásd: [statikus paraméterek beállítása – terv szintű paraméter](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString és secureObject paraméterek használata

Míg a Resource Manager- sablonok összetevői támogatják a **SecureString** és a **secureObject** -típusok paramétereit, az Azure-tervrajzok mindegyikének Azure Key Vaulthoz kell csatlakoznia.
Ez a biztonsági mérték megakadályozza, hogy a titkokat a tervvel együtt tárolják, és a biztonságos minták foglalkoztatására is ösztönözze a biztonságot. Az Azure-tervrajzok támogatják ezt a biztonsági mértéket, és észlelik, ha egy Resource Manager-sablonban található egy biztonságos paraméter. A szolgáltatás ezután a következő Key Vault tulajdonságok kiosztásakor kéri az észlelt biztonságos paramétert:

- Erőforrás-azonosító Key Vault
- Key Vault titkos kód neve
- Key Vault Secret-verzió

Ha a terv **-hozzárendelés rendszer által hozzárendelt felügyelt identitást**használ, a hivatkozott Key Vaultnak ugyanabban az előfizetésben _kell lennie_ , mint a terv definíciója.

Ha a terv-hozzárendelés **felhasználó által hozzárendelt felügyelt identitást**használ, _akkor_ a hivatkozott Key Vault központi előfizetésben létezhet. A felügyelt identitásnak megfelelő jogosultságokat kell biztosítania a Key Vault a terv hozzárendelése előtt.

> [!IMPORTANT]
> Mindkét esetben a Key Vaultnak engedélyeznie kell a hozzáférést a Azure Resource Manager számára a **hozzáférési házirendek** lapon konfigurált **sablonok telepítéséhez** . A szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: [Key Vault – a sablonok telepítésének engedélyezése](../../../managed-applications/key-vault-access.md#enable-template-deployment).

További információ a Azure Key Vaultről: [Key Vault áttekintése](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Paraméterek típusai

### <a name="static-parameters"></a>Statikus paraméterek

A terv definíciójában definiált paraméterérték **statikus paraméternek**nevezzük, mert a terv minden használata az adott statikus értéket használó összetevőt fogja telepíteni. Az erőforráscsoport példájában, míg az erőforráscsoport neve nem jelent értelmet, érdemes lehet a helyet is megtenni. Ezt követően a terv minden hozzárendelése létrehozza az erőforráscsoportot, amit a hozzárendelés során meghívott, ugyanazon a helyen. Ez a rugalmasság lehetővé teszi, hogy szelektíven határozza meg, hogy mit kell megadnia a szükséges módon, és hogy mi módosítható a hozzárendelés során.

#### <a name="setting-static-parameters-in-the-portal"></a>Statikus paraméterek beállítása a portálon

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válasszaki a tervrajzokat.

1. A bal oldali oldalon válassza a **tervezet** -definíciók lehetőséget.

1. Kattintson egy meglévő tervre, majd kattintson a **terv szerkesztése** vagy a **+ terv létrehozása** lehetőségre, és adja meg az alapvető tudnivalókat az **alapok** lapon.

1. Kattintson **a Tovább gombra: Összetevők elemre ,vagykattintsonazösszetevők** fülre.

1. A tervhez hozzáadott összetevők, amelyekben a paraméter beállításai a **Paraméterek** oszlopban kitöltött **Y paraméterek X betűjét** jelenítik meg. Az összetevő paramétereinek szerkesztéséhez kattintson a lelet sorra.

   ![Tervrajz paramétereinek meghatározása](../media/parameters/parameter-column.png)

1. Az összetevő **szerkesztése** oldalon a rákattintott összetevőnek megfelelő érték beállítások jelennek meg. Az összetevő minden paramétere rendelkezik egy címmel, egy érték mezővel és egy jelölőnégyzettel. Jelölje be a jelölőnégyzetet, hogy ne legyen bejelölve **statikus paraméterként**. Az alábbi példában csak a _hely_ **statikus paraméter** , mert nincs bejelölve, és az _erőforráscsoport neve_ be van jelölve.

   ![Statikus paraméterek tervrajza tervrajzok esetében](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Statikus paraméterek beállítása REST API

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourMG}` – Cserélje le a felügyeleti csoport nevére
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

##### <a name="blueprint-level-parameter"></a>Terv szintű paraméter

Ha REST APIon keresztül hoz létre tervrajzot, a [terv paramétereinek](#blueprint-parameters)létrehozása lehetséges. Ehhez használja a következő REST API URI és szövegtörzs formátumot:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

A tervrajzi szint paraméterének létrehozása után az adott tervhez hozzáadott összetevőkön is használható.
A következő REST API példa létrehoz egy szerepkör-hozzárendelési összetevőt a tervrajzon, és a terv szintjének paramétert használja.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Kérelem törzse

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Ebben a példában a **principalIds** tulajdonság a tulajdonosi terv szintje paramétert használja a értékének `[parameters('owners')]`használatával. Ha a paramétert egy olyan összetevőn állítja be, amely egy terv szintű paramétert használ, továbbra is egy **statikus paraméterre**mutat. A tervrajzi szint paraméter nem állítható be a terv hozzárendelése során, és az egyes hozzárendelések esetében ugyanaz az érték lesz.

##### <a name="artifact-level-parameter"></a>Összetevő szintje paraméter

A **statikus paraméterek** egy összetevőn való létrehozása hasonló, de a `parameters()` függvény használata helyett egyenes értéket vesz igénybe. A következő példa két statikus paramétert hoz létre: **TagName** és **tagValue**. Az egyes értékek értéke közvetlenül van megadva, és nem használja a függvény hívását.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Kérelem törzse

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dinamikus paraméterek

A statikus paraméter ellentéte egy **dinamikus paraméter**. Ez a paraméter nincs definiálva a tervrajzon, hanem a terv minden egyes hozzárendelésében meg van határozva. Az erőforráscsoport példájában a **dinamikus paraméterek** használata az erőforráscsoport nevének értelmezését teszi lehetővé. A terv minden hozzárendeléséhez más nevet biztosít. A tervrajzi függvények listáját a [Blueprint functions](../reference/blueprint-functions.md) dokumentációjában tekintheti meg.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Dinamikus paraméterek beállítása a portálon

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válasszaki a tervrajzokat.

1. A bal oldali oldalon válassza a **tervezet** -definíciók lehetőséget.

1. Kattintson a jobb gombbal a hozzárendelni kívánt tervre. Válassza a **terv** kiosztása lehetőséget, vagy kattintson a hozzárendelni kívánt tervre, majd kattintson a **terv** kiosztása gombra.

1. A **terv** kiosztása lapon keresse meg az összetevő **paramétereinek** szakaszát. Minden olyan összetevő, amely legalább egy **dinamikus paraméterrel** rendelkezik, megjeleníti az összetevőt és a konfigurációs beállításokat. Adja meg a szükséges értékeket a paraméterekhez a terv kiosztása előtt. Az alábbi példában a _Name_ egy **dinamikus paraméter** , amelyet meg kell határozni a terv hozzárendelésének befejezéséhez.

   ![Tervezet-hozzárendelés dinamikus paramétere a terv hozzárendelése során](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Dinamikus paraméterek beállítása REST API

A **dinamikus paraméterek** a hozzárendelés során történő beállítása közvetlenül az érték megadásával történik. A függvények, például a [Parameters ()](../reference/blueprint-functions.md#parameters)függvény helyett a megadott érték egy megfelelő karakterlánc. Az erőforráscsoport összetevői a "sablon neve", a **név**és a **hely** tulajdonságaiban vannak meghatározva. A befoglalt összetevő összes többi paramétere a  **\<\> Name** és az **Value** kulcspár **paraméterei** alapján van definiálva. Ha a terv olyan dinamikus paraméterre van konfigurálva, amely nincs megadva a hozzárendelés során, a hozzárendelés sikertelen lesz.

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>További lépések

- Tekintse meg a [terv függvények](../reference/blueprint-functions.md)listáját.
- A [terv életciklusának](lifecycle.md)megismerése.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).