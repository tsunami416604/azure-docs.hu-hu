---
title: Paraméterek használata dinamikus tervrajzok létrehozásához
description: Ismerje meg a statikus és dinamikus paramétereket, és hogyan használhatja őket biztonságos és dinamikus tervrajzok létrehozásához.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: ed596db2050ac788c2d98c63cb7314de473b5f4e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383618"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Dinamikus tervrajzok létrehozása paramétereken keresztül

A különböző összetevőkkel (például erőforráscsoportokkal, Erőforrás-kezelő sablonokkal, szabályzatokkal vagy szerepkör-hozzárendelésekkel) rendelkező, teljesen definiált tervrajzok az Azure-on belüli objektumok gyors létrehozását és konzisztens létrehozását kínálják. Az újrafelhasználható tervezési minták és tárolók rugalmas használatának lehetővé tétele érdekében az Azure Blueprints támogatja a paramétereket. A paraméter rugalmasságot hoz létre mind a definíció, mind a hozzárendelés során a terv által üzembe helyezett összetevők tulajdonságainak módosításához.

Egy egyszerű példa az erőforráscsoport-összetevő. Erőforráscsoport létrehozásakor két szükséges értéket kell megadni: nevet és helyet. Erőforráscsoport hozzáadásakor a tervrajzhoz, ha a paraméterek nem léteznek, akkor adja meg ezt a nevet és helyet a terv minden használatához. Ez az ismétlés a terv minden használatát az ugyanabban az erőforráscsoportban összetevők létrehozásához eredményezné. Az erőforráscsoporton belüli erőforrások duplikálva lesznek, és ütközést okoznak.

> [!NOTE]
> Nem probléma két különböző tervrajz, hogy egy erőforráscsoport azonos nevű.
> Ha egy tervrajzban szereplő erőforráscsoport már létezik, a terv folytatja a kapcsolódó összetevők létrehozását az adott erőforráscsoportban. Ez ütközést okozhat, mivel két azonos nevű és erőforrástípussal rendelkező erőforrás nem létezhet egy előfizetésen belül.

A probléma megoldása a paraméterek. Az Azure Blueprints lehetővé teszi, hogy meghatározza az érték a műtermék minden egyes tulajdonsága az előfizetés hozzárendelése során. A paraméter lehetővé teszi, hogy újra egy terv, amely létrehoz egy erőforráscsoportot és más erőforrásokat egyetlen előfizetésütközés nélkül egyetlen előfizetésen belül.

## <a name="blueprint-parameters"></a>Tervparaméterek

A REST API-n keresztül paraméterek hozhatók létre a tervtervezeten. Ezek a paraméterek eltérnek az egyes támogatott összetevők paramétereitől. Amikor egy paraméter jön létre a tervrajzon, a tervben szereplő összetevők használhatják. Egy példa lehet az erőforráscsoport elnevezésének előtagja. A műtermék használhatja a tervezet paraméter egy "többnyire dinamikus" paraméter létrehozásához. Mivel a paraméter hozzárendelés során is definiálható, ez a minta lehetővé teszi a konzisztenciát, amely betarthatja az elnevezési szabályokat. Lépésekért lásd: [Statikus paraméterek beállítása - tervezetszint paraméter](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString és secureObject paraméterek használata

Míg a Resource Manager-sablon _műtermék_ támogatja a **secureString** és **secureObject** típusok paramétereit, az Azure Blueprints megköveteli, hogy mindegyik hez egy Azure Key Vault. Ez a biztonsági intézkedés megakadályozza a titkok tárolásának nem biztonságos gyakorlatát a blueprint-el együtt, és ösztönzi a biztonságos minták foglalkoztatását. Az Azure Blueprints támogatja ezt a biztonsági intézkedést, észlelve, hogy a beépített biztonságos paraméter egy Resource Manager sablon _műtermék._ A szolgáltatás ezután a következő Key Vault-tulajdonságok hozzárendelése során kéri az észlelt biztonságos paraméterenkénti lekérdezést:

- Key Vault-erőforrás azonosítója
- Key Vault titkos neve
- Key Vault titkos verziója

Ha a tervezet-hozzárendelés **egy rendszer által hozzárendelt felügyelt identitást**használ, a hivatkozott Key _Vaultnak_ ugyanabban az előfizetésben kell léteznie, amelyhez a tervezetdefiníció hozzá van rendelve.

Ha a tervezet hozzárendelés használ **egy felhasználó által hozzárendelt felügyelt identitás,** a hivatkozott Key Vault _létezhet_ egy központi előfizetésben. A felügyelt identitást megfelelő jogosultságokkal kell megadni a Key Vault-on a tervhozzárendelés előtt.

> [!IMPORTANT]
> Mindkét esetben a Key Vault hozzáférés **engedélyezése az Azure Resource Manager a sablon üzembe helyezését** konfigurálva az Access **szabályzatok** lapon. A szolgáltatás engedélyezésére vonatkozó útmutatásért olvassa el a Key Vault – Sablon telepítésének engedélyezése című [témakört.](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment)

Az Azure Key Vault ról a [Key Vault áttekintése című témakörben olvashat bővebben.](../../../key-vault/key-vault-overview.md)

## <a name="parameter-types"></a>Paramétertípusok

### <a name="static-parameters"></a>Statikus paraméterek

A tervezet definíciójában definiált paraméterértéket **statikus paraméternek**nevezzük, mert a terv minden használata a műtermék et a statikus értékkel telepíti. Az erőforráscsoport példában, bár nincs értelme az erőforráscsoport neve, lehet, hogy van értelme a hely. Ezután a terv minden hozzárendelése létrehozza az erőforráscsoportot, függetlenül attól, hogy hívják a hozzárendelés során, ugyanazon a helyen. Ez a rugalmasság lehetővé teszi, hogy szelektív, amit meghatározott szükséges vs mit lehet változtatni a hozzárendelés során.

#### <a name="setting-static-parameters-in-the-portal"></a>Statikus paraméterek beállítása a portálon

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. Kattintson egy meglévő tervrajzra, majd kattintson a **Tervrajz szerkesztése parancsra,** vagy kattintson **a + Tervezet létrehozása** **gombra,** és töltse ki az információkat az Alapok lapon.

1. Kattintson a **Tovább: Műtermékek** vagy kattintson a **Műtárgyak** fülre.

1. A tervezethez hozzáadott, paraméterbeállításokkal rendelkező összetevők **x y paramétert jelenítenek meg** a **Paraméterek** oszlopban. Kattintson a műtermék sorra a műtermék-paraméterek szerkesztéséhez.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Tervrajz-paraméterek egy tervezetdefinícióban" border="false":::

1. A **Műtermék szerkesztése** lap megjeleníti a leadott műterméknek megfelelő értékbeállításokat. A műtermék minden paraméterének van egy címe, egy értékmezője és egy jelölőnégyzetje. Állítsa be a négyzetet bejelölve, hogy **statikus paraméter**legyen. Az alábbi példában csak a _Hely_ **egy statikus paraméter,** mivel nincs bejelölve, és az _erőforráscsoport neve_ be van jelölve.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="A tervezet összetevőjének statikus paramétereinek tervezetrajza" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Statikus paraméterek beállítása a REST API-ból

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourMG}` – Cserélje le a felügyeleti csoport nevére
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

##### <a name="blueprint-level-parameter"></a>Tervezetszint paraméter

Amikor egy tervrajzot hoz létre a REST API-n keresztül, lehetőség van [tervezetparaméterek létrehozására.](#blueprint-parameters) Ehhez használja a következő REST API URI-t és törzsformátumot:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- A kérelem törzse

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

A tervezetszint-paraméter létrehozása után használható a tervtervhez hozzáadott összetevőken.
A következő REST API-példa létrehoz egy szerepkör-hozzárendelési műtermék a tervezetben, és a tervezet szint paramétert használ.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- A kérelem törzse

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

Ebben a példában a **property** property a **tulajdonosok** tervezetszint paraméterét használja a `[parameters('owners')]`használatával. Egy paraméter beállítása egy műterméken egy tervezetszint paraméter használatával továbbra is **egy statikus paraméter**példája. A tervezet szint paraméter nem állítható be a tervezet hozzárendelése során, és ugyanaz lesz az érték minden hozzárendelés.

##### <a name="artifact-level-parameter"></a>Műtermék szint paramétere

Statikus **paraméterek** létrehozása egy műterméken hasonló, de a `parameters()` függvény használata helyett egyenes értéket vesz igénybe. A következő példa két statikus paramétert hoz létre: **a tagName** és **a tagValue**paramétert. Az egyes értékek közvetlenül biztosított, és nem használ függvényhívás.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- A kérelem törzse

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

A statikus paraméter ellentéte a **dinamikus paraméter**. Ez a paraméter nincs definiálva a tervrajzon, hanem a terv minden egyes hozzárendelése során van definiálva. Az erőforráscsoport ban példában egy **dinamikus paraméter** használata van értelme az erőforráscsoport nevének. A terv minden hozzárendeléséhez más nevet ad. A tervezet függvények listáját a [tervezet függvények](../reference/blueprint-functions.md) hivatkozási.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Dinamikus paraméterek beállítása a portálon

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. Kattintson a jobb gombbal a hozzárendelni kívánt tervrajzra. Válassza **a Tervrajz hozzárendelése** vagy kattintson a hozzárendelni kívánt tervrajzra, majd kattintson a **Tervrajz hozzárendelése** gombra.

1. A **Blueprint hozzárendelése** lapon keresse meg a **Műtermék paramétereit szakaszt.** Minden legalább egy **dinamikus paraméterrel** rendelkező műtermék megjeleníti a műtermék és a konfigurációs beállításokat. Adja meg a szükséges értékeket a paraméterekhez a terv hozzárendelése előtt. Az alábbi példában a _Név_ egy **dinamikus paraméter,** amelyet meg kell határozni a tervhozzárendelés befejezéséhez.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="A tervezet dinamikus paramétere a tervezet hozzárendelése során" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Dinamikus paraméterek beállítása a REST API-ból

A **dinamikus paraméterek** beállítása a hozzárendelés során az érték közvetlen megadásával történik. Függvény, például [parameters()](../reference/blueprint-functions.md#parameters)használata helyett a megadott érték megfelelő karakterlánc. Az erőforráscsoportok összetevői "sablonnévvel", **névvel**és **helytulajdonságokkal** vannak definiálva. A mellékelt műtermék összes többi **parameters** paramétere ** \<név-\> ** és értékkulcs-párral rendelkező paraméterek alatt van definiálva. **value** Ha a tervezet olyan dinamikus paraméterhez van konfigurálva, amely nincs megadva a hozzárendelés során, a hozzárendelés sikertelen lesz.

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- A kérelem törzse

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

- Lásd a [tervezet függvényeinek](../reference/blueprint-functions.md)listáját .
- További információ a [tervterv életciklusáról.](lifecycle.md)
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.