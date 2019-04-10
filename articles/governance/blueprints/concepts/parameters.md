---
title: A dinamikus tervezetek létrehozása-paraméterek használata
description: Ismerje meg a statikus és dinamikus paraméterek, és hogyan használja őket hozza létre a dinamikus tervek.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9b5b151c62c4294563f704dc9a0cf7daeaca874f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279975"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Paramétereknek dinamikus tervezetek létrehozása

A gyors létrehozása és a konzisztens Azure-on belüli objektumok létrehozásának a különböző összetevők (például az erőforráscsoportok, erőforrás-kezelő sablonokat, szabályzatokat vagy szerepkör-hozzárendelések) egy teljes mértékben meghatározott tervezet érhetők el. Ahhoz, hogy a rugalmas ezek újrafelhasználható tervezési minták és a tárolók használatát, az Azure tervezetek paramétereket támogatja. A paraméter a rugalmasságot, mind a definíció- és a hozzárendelés a tervezet által telepített összetevők tulajdonságainak módosítása során hoz létre.

Egy egyszerű példája az erőforrás-csoport összetevőben. Amikor egy erőforráscsoportot kell létrehozni, két szükséges értékeket, amelyek meg kell adni rendelkezik: nevét és helyét. Való hozzáadásakor egy erőforráscsoportot a tervezet, ha paraméterek nem létezik, akkor a nevét és a tervezet minden használatra helyét határozza meg. Az ismétlődés okozna minden használatát a tervezet összetevőket hozhat létre ugyanabban az erőforráscsoportban. Adott erőforráscsoporton belüli erőforrásokhoz lenne duplikált válnak, és ütközés miatt.

> [!NOTE]
> Két különböző tervezetek egy azonos nevű erőforráscsoport tartalmazza a probléma nem.
> Ha már szerepel a tervezet erőforráscsoport már létezik, a tervezet továbbra is a kapcsolódó összetevők felhasználásával az erőforráscsoport létrehozásához. Ez ütközést okozhat, két, azonos nevű erőforrás, és az erőforrástípus nem létezik egy előfizetésen belül.

A megoldás a probléma a paramétereket. Tervezetek lehetővé teszi a lehívandó összetevő minden egyes tulajdonság értékének meghatározása során hozzárendelés előfizetést. A paraméter, amely létrehoz egy erőforráscsoportot és az egyéb erőforrások egyetlen előfizetésben jön létre ütközés nélkül tervrajz újból lehetővé teszi.

## <a name="blueprint-parameters"></a>Tervparaméterek

A REST API-val paraméterek meg magát a tervezet hozható létre. Ezeket a paramétereket a paramétereket az egyes támogatott összetevők eltérnek. Egy paraméter az a tervezet létrehozásakor azt használhatják az összetevők adott tervezetben. Egy példa az előtag, az erőforráscsoport elnevezési lehet. A lehívandó összetevő a tervezet paraméter használatával hozzon létre egy "többnyire dinamikus" paramétert. A paraméter is lehetőség van a hozzárendelés során, mivel ez a minta lehetővé teszi, hogy elnevezési szabályok előfordulhat, hogy ezeket a konzisztencia. Útmutató: [beállítás statikus paraméterek - szint paraméter tervezetet](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString és secureObject paraméterek használatával

Miközben a Resource Manager-sablonnal _összetevő_ paramétereket támogatja a **secureString** és **secureObject** típusok, Azure tervezetek igényel minden egyes kell csatlakoztatni egy Azure Key Vault.
Ez a biztonsági intézkedés megakadályozza, hogy a nem biztonságos gyakorlat, és a tervezet titkok tárolásával, és arra ösztönzi a biztonságos minták alkalmazási. Az Azure tervezetek támogatja ez biztonsági okokból a Resource Manager-sablon felvételét vagy biztonságos paramétert észlelése _összetevő_. A szolgáltatás a telepítéseket hozzárendelés a következő Key Vault tulajdonságok észlelt biztonságos paraméterenként során:

- A Key Vault erőforrás-azonosító
- A Key Vault titkos neve
- A Key Vault titkos kód verziója

Ha a tervezet-hozzárendelést használ egy **alapértelmezett felügyelt identitás**, a hivatkozott Key Vault _kell_ létezik ugyanabban az előfizetésben a tervezetdefiníciót hozzá van rendelve.

Ha a tervezet-hozzárendelést használ egy **felhasználó által hozzárendelt felügyelt identitás**, a hivatkozott Key Vault _előfordulhat, hogy_ egy központosított az előfizetéshez. A felügyelt identitás megfelelő jogosultságokkal a Key Vault tervezet-hozzárendelés előtt meg kell adni.

Mindkét esetben rendelkeznie kell a Key Vault **engedélyezze a hozzáférést az Azure Resource Manager-sablon telepítése** konfigurálva a **hozzáférési házirendek** lapot. Ez a funkció engedélyezése az irányban, lásd: [Key Vault - Enable sablonalapú telepítés](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Azure Key Vaulttal kapcsolatos további információkért lásd: [Key Vault – áttekintés](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Paramétertípusok

### <a name="static-parameters"></a>Statikus paraméterek

A paraméter értéke a tervrajz-definícióban meghatározott nevezzük egy **statikus paraméter**, mert minden használatát a tervezet telepíti az összetevő, állandó érték használatával. Az erőforrás csoport példában amíg azt nem számára jelentéssel bírnak az erőforráscsoport nevét célszerű helyéhez. Ezt követően minden hozzárendelését a tervezet erőforráscsoport hozna létre ugyanazon a helyen a hozzárendelés során nevezzük függetlenül. Ezt a rugalmasságot lehetővé teszi a szelektív kell mit határoz meg szükséges vs, mire a hozzárendelés során módosítható.

#### <a name="setting-static-parameters-in-the-portal"></a>Statikus paramétereinek beállítása a portálon

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki **definíciók tervezetet** a lap bal oldalán.

1. Kattintson egy meglévő tervezet a, és kattintson a **szerkesztési tervezet** , vagy kattintson **+ létrehozás tervezet** , és adja meg az adatokat a a **alapjai** fülre.

1. Kattintson a **tovább: Összetevők** , vagy kattintson az a **összetevők** fülre.

1. Megjeleníti a paraméter közül a tervezet hozzáadott összetevők **X, Y paraméter kitöltve** a a **paraméterek** oszlop. Kattintson az összetevő sor összetevő paramétereit.

   ![Tervezet paramétereinek a tervezetdefiníciót](../media/parameters/parameter-column.png)

1. A **szerkesztése összetevő** lap megjeleníti az összetevőben, amire kattintott a megfelelő értéket beállítások. Minden egyes az az összetevő paraméterének egy cím, egy érték mezőbe és egy jelölőnégyzetet. Állítsa a mező nincs bejelölve, hogy egy **statikus paraméter**. Az alábbi példában, csak _hely_ van egy **statikus paraméter** , mert nincs bejelölve, és _erőforráscsoport-név_ be van jelölve.

   ![Tervezet statikus paraméter az a tervezet-összetevő](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>REST API-ból származó statikus paramétereinek beállítása

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourMG}` – Cserélje le a felügyeleti csoport neve
- `{subscriptionId}` – Cserélje le az előfizetés-azonosító

##### <a name="blueprint-level-parameter"></a>Tervrajz szint paraméter

REST API-val tervrajz létrehozásakor, azt is létrehozhat [paraméterek tervezetet](#blueprint-parameters). Ehhez használja a következő REST API URI-t és a törzs formátumot:

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

Egy szint tervezetparaméter létrehozása után, amely a tervezet hozzáadott összetevők használható.
Az alábbi REST API-példa egy szerepkör-hozzárendelési összetevője a tervezet hoz létre, és a tervezet szint paraméter használja.

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

Ebben a példában a **principalIds** tulajdonságot használ a **tulajdonosok** tervezetet szint paraméter értéke használatával `[parameters('owners')]`. Egy paraméter beállítása egy összetevő szintű tervezetparaméter használatával még mindig egy példát egy **statikus paraméter**. A tervezet szint paraméter nem állítható be a tervezet-hozzárendelés során, és ugyanazt az értéket a minden hozzárendelés.

##### <a name="artifact-level-parameter"></a>Összetevő szint paraméter

Létrehozás **statikus paraméterek** összetevőt a hasonló, de használata helyett közvetlenül értéket vesz fel a `parameters()` függvény. Az alábbi példa létrehoz két statikus paraméterekkel, **tagName** és **tagValue**. Az érték minden egyes közvetlenül biztosítja, és nem használja a függvényhívást.

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

Ennek az ellenkezője, egy statikus paraméter egy **dinamikus paraméterek**. Ez a paraméter nincs definiálva a tervezet, de ehelyett minden egyes a tervezet-hozzárendelés során van definiálva. Használja az erőforrás csoport példában egy **dinamikus paraméterek** megfelel az erőforráscsoport nevét. Biztosít minden a tervezet-hozzárendelés egy másik nevet.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>A portál dinamikus paraméterek beállítása

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki **definíciók tervezetet** a lap bal oldalán.

1. A tervezet, amelyet szeretne hozzárendelni a jobb gombbal. Válassza ki **tervezet hozzárendelése** vagy a tervezet hozzárendelni kívánt elemére, majd kattintson a **tervezet hozzárendelése** gombra.

1. Az a **tervezet hozzárendelése** lapon, keresse meg a **összetevő paraméterek** szakaszban. Minden egyes összetevő legalább egy **dinamikus paraméterek** jeleníti meg összetevő és a konfigurációs beállításokat. A tervezet hozzárendelése előtt adja meg a szükséges értékeket a paraméterekhez. Az alábbi példában _neve_ van egy **dinamikus paraméterek** tervezet-hozzárendelés végrehajtásához, meg kell határozni.

   ![A dinamikus tervezetparaméter során tervezet-hozzárendelést](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API-ból a dinamikus paraméterek beállítása

Beállítás **dinamikus paraméterek** a hozzárendelés során végezhető el közvetlenül írja be az értéket.
Függvény, mint például helyett `parameters()`, a megadott érték egy megfelelő karakterláncot.
Összetevők erőforráscsoport van megadva a "sablon neve" **neve**, és **hely** tulajdonságait. A csomagban foglalt összetevő összes többi paraméter meghatározása a **paraméterek** az egy **\<neve\>** és **érték** kulcspárt. Ha a dinamikus paraméterek, amelyek a hozzárendelés során nincs megadva a tervezet van konfigurálva, a hozzárendelés sikertelen lesz.

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

- Tudnivalók a [tervek életciklusáról](lifecycle.md).
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).