---
title: Az Azure-tervek paramétereknek dinamikus tervezetek létrehozása
description: Ismerje meg a statikus és dinamikus paraméterek, és hogyan használja őket hozza létre a dinamikus tervek.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993578"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Paramétereknek dinamikus tervezetek létrehozása

A gyors létrehozása és a konzisztens kiépítése az Azure-on belüli objektumok különböző összetevőkkel (például az erőforráscsoportok, erőforrás-kezelő sablonokat, szabályzatokat vagy szerepkör-hozzárendelések) egy teljes mértékben meghatározott tervezet érhetők el. Ahhoz, hogy a rugalmas ezek újrafelhasználható tervezési minták és a tárolók használatát, az Azure tervezetek paramétereket támogatja. A paraméter a rugalmasságot, mind a definíció- és a hozzárendelés a tervezet által telepített összetevők tulajdonságainak módosítása során hoz létre.

Egy egyszerű példája az erőforrás-csoport összetevőben. Amikor egy erőforráscsoportot kell létrehozni, két szükséges értékeket, amelyek meg kell adni rendelkezik: nevét és helyét. Való hozzáadásakor egy erőforráscsoportot a tervezet, ha paraméterek nem létezik, akkor a nevét és a tervezet minden használatra helyét határozza meg. Emiatt minden használatát a tervezet összetevőket hozhat létre ugyanabban az erőforráscsoportban. Amíg nem magát az erőforráscsoportot, erőforrások adott erőforráscsoporton belüli problémáját lenne duplikált válnak, és ütközés miatt.

> [!NOTE]
> Két különböző tervezetek egy azonos nevű erőforráscsoport tartalmazza a probléma nem.
> Ha már szerepel a tervezet erőforráscsoport már létezik, a tervezet továbbra is a kapcsolódó összetevők felhasználásával az erőforráscsoport létrehozásához. Ez ütközést okozhat, két, azonos nevű erőforrás, és az erőforrástípus nem létezik egy előfizetésen belül.

Ez az, ahol paraméterek elfér. Ezen tulajdonságok esetén az erőforráscsoport nevét és a location tulajdonság értékét, tervek lehetővé teszi, nem adhat meg hozzájuk a tervezet meghatározása során, de ehelyett meghatározása során hozzárendelés előfizetést azok értékeit. Így újból felhasználhatja a tervezet, amely létrehoz egy erőforráscsoportot és az egyéb erőforrások egyetlen előfizetésben jön létre ütközés nélkül.

## <a name="blueprint-parameters"></a>Tervezet paramétereinek megadása

A REST API-val paraméterek magát a tervezet a támogatott összetevők mellett a hozható létre. Egy paraméter az a tervezet létrehozásakor azt használhatják az összetevők adott tervezetben. Egy példa az előtag, az erőforráscsoport elnevezési lehet. A lehívandó összetevő felhasználhatja a tervezetparaméter létrehozni a "többnyire dinamikus" paramétert, a paraméter hozzárendelés során továbbra is definiálni, de előfordulhat, hogy ezeket a szervezet elnevezési szabályokat konzisztencia kell. Útmutató: [beállítás statikus paraméterek - szint paraméter tervezetet](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString és secureObject paraméterek használatával

Miközben a Resource Manager-sablonnal _összetevő_ paramétereket támogatja a **secureString** és **secureObject** típusok, Azure tervezetek igényel minden egyes kell csatlakoztatni egy Azure Key Vault.
Ez megakadályozza, hogy a nem biztonságos gyakorlat, és a tervezet titkok tárolásával, és arra ösztönzi a biztonságos minták alkalmazási. Azure tervezetek megkönnyíti ezt a Resource Manager-sablon felvételét vagy biztonságos paramétert észlelésével _összetevő_ és kérő üzenet során tervezet-hozzárendelést a következő a Key vault tulajdonságok / észlelt biztonságos a paraméter:

- A Key Vault erőforrás-azonosító
- A Key Vault titkos neve
- A Key Vault titkos kód verziója

A hivatkozott Key Vault ugyanabban az előfizetésben léteznie kell, ahogy a tervezet lesz hozzárendelve, és rendelkeznie kell **engedélyezze a hozzáférést az Azure Resource Manager-sablon telepítése** a Key Vault konfigurált **hozzáférés házirendek** lapot. Ez a funkció engedélyezése az irányban, lásd: [Key Vault - Enable sablonalapú telepítés](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Azure Key Vaulttal kapcsolatos további információkért lásd: [Key Vault – áttekintés](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Paramétertípusok

### <a name="static-parameters"></a>Statikus paraméterek

A paraméter értéke a tervrajz-definícióban meghatározott nevezzük egy **statikus paraméter**. Ennek oka az, minden használatát a tervezet telepíti az összetevő, állandó érték használatával. Az erőforrás csoport példában míg ez nincs értelme megtiltani az erőforráscsoport neve, előfordulhat, hogy számára jelentéssel bírnak a helyet. Ezt követően minden hozzárendelését a tervezet erőforráscsoport hozna létre ugyanazon a helyen a hozzárendelés során nevezzük függetlenül. Ez lehetővé teszi, hogy legyen körültekintő, amit határoz meg szükséges vs, mire a hozzárendelés során módosítható.

#### <a name="setting-static-parameters-in-the-portal"></a>Statikus paramétereinek beállítása a portálon

1. Indítsa el az Azure-tervek szolgáltatás az Azure Portalon kattintson a **minden szolgáltatás** keresése és kiválasztása **házirend** a bal oldali panelen. Az a **házirend** lapon **tervezetek**.

1. Válassza ki **Tervezetdefiníciók** a lap bal oldalán.

1. Kattintson egy meglévő tervezet a, és kattintson a **tervrajz szerkesztése** , vagy kattintson **+ létrehozás tervezet** , és adja meg az adatokat a a **alapjai** fülre.

1. Kattintson a **tovább: összetevők** , vagy kattintson az a **összetevők** fülre.

1. Megjeleníti a paraméter közül a tervezet hozzáadott összetevők **X, Y paraméter kitöltve** a a **paraméterek** oszlop. Kattintson az összetevő sor összetevő paramétereit.

   ![Tervezet paramétereinek megadása](../media/parameters/parameter-column.png)

1. A **szerkesztése összetevő** lap megjeleníti az összetevőben, amire kattintott a megfelelő értéket beállítások. Minden egyes az az összetevő paraméterének egy cím, egy érték mezőbe és egy jelölőnégyzetet. Állítsa a mező nincs bejelölve, hogy egy **statikus paraméter**. Az alábbi példában, csak _hely_ van egy **statikus paraméter** ez nincs bejelölve, és _erőforráscsoport-név_ be van jelölve.

   ![Statikus tervezet paraméterei](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>REST API-ból származó statikus paramétereinek beállítása

Az egyes REST API URI van, hogy a saját értékeire cserélni kell használt változók:

- `{YourMG}` – Cserélje le a felügyeleti csoport neve
- `{subscriptionId}` – Cserélje le az előfizetés-azonosító

##### <a name="blueprint-level-parameter"></a>Tervrajz szint paraméter

REST API-val tervrajz létrehozásakor, azt is létrehozhat [paraméterek tervezetet](#blueprint-parameters). Ehhez használja a következő REST API URI-t és a törzs formátumot:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
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
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
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

Ebben a példában a **principalIds** végzett tulajdonság használatát a **tulajdonosok** tervezetet szint paraméter értékének megadásával `[parameters('owners')]`. Egy paraméter beállítása egy összetevő szintű tervezetparaméter használatával még mindig egy példát egy **statikus paraméter** tervezet-hozzárendelés során nem állítható be, majd minden hozzárendelés érték lesz.

##### <a name="artifact-level-parameter"></a>Összetevő szint paraméter

Létrehozás **statikus paraméterek** összetevőt a hasonló, de használata helyett közvetlenül értéket vesz fel a `parameters()` függvény. Az alábbi példa létrehoz két statikus paraméterekkel, **tagName** és **tagValue**. Az érték minden egyes közvetlenül biztosított, és nem használja a függvényhívást.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
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

Ennek az ellenkezője, egy statikus paraméter egy **dinamikus paraméterek**. Ez az a paraméter nincs definiálva a következő a tervezet, de ehelyett minden egyes a tervezet-hozzárendelés során van definiálva. Az erőforrás csoport a példában ez logikus az erőforráscsoport nevéhez, minden a tervezet-hozzárendelés egy másik nevet.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>A portál dinamikus paraméterek beállítása

1. Indítsa el az Azure-tervek szolgáltatás az Azure Portalon kattintson a **minden szolgáltatás** keresése és kiválasztása **házirend** a bal oldali panelen. Az a **házirend** lapon **tervezetek**.

1. Válassza ki **Tervezetdefiníciók** a lap bal oldalán.

1. Kattintson a jobb gombbal a tervezet, amelyet szeretne hozzárendelni, és válassza ki a **tervezet hozzárendelése** vagy a tervezet hozzárendelni kívánt elemére, majd kattintson a **tervezet hozzárendelése** gombra.

1. Az a **tervezet hozzárendelése** lapon, keresse meg a **összetevő paraméterek** szakaszban. Minden egyes összetevő legalább egy **dinamikus paraméterek** jeleníti meg összetevő és a konfigurációs beállításokat. A tervezet hozzárendelése előtt adja meg a szükséges értékeket a paraméterekhez. Az alábbi példában _neve_ van egy **dinamikus paraméterek** tervezet-hozzárendelés végrehajtásához, meg kell határozni.

   ![A dinamikus tervezetparaméter](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API-ból a dinamikus paraméterek beállítása

Beállítás **dinamikus paraméterek** a hozzárendelés során végezhető el közvetlenül biztosít a kívánt értéket. Függvény, mint például helyett `parameters()`, a megadott érték egy megfelelő karakterláncot. Összetevők erőforráscsoport van megadva a "sablon neve", és **neve** és **hely** tulajdonságait. Minden más paramétereket, az egyes belefoglalt összetevő definiálva a **paraméterek** együtt egy **\<neve\>** és **érték** kulcspárt. Ha a tervezet, amely nem szerepel a hozzárendelés során dinamikus paraméterre van konfigurálva, a hozzárendelés sikertelen lesz.

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
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

- További információ a [tervezetet életciklus](lifecycle.md)
- Ismerje meg, szabhatja testre a [tervezetet alkalmazás-előkészítés sorrend](sequencing-order.md)
- Ismerje meg, győződjön meg arról, hogyan használhatja az [tervezetet erőforrás zárolása](resource-locking.md)
- Ismerje meg, hogyan [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md)
- A tervrajz hozzárendelésének során felmerülő problémák megoldása [általános hibaelhárítási](../troubleshoot/general.md)