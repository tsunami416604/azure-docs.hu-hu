---
title: Azure-beli felügyelt alkalmazás létrehozása az Azure CLI-vel | Microsoft Docs
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: f84fdd421ec6857cd940108546a16eb47770c766
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/18/2018
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Azure-beli felügyelt alkalmazás létrehozása és üzembe helyezése az Azure CLI-vel

Ebben a cikkben a felügyelt alkalmazások használatával ismerkedhet meg. Először hozzáad egy felügyeltalkalmazás-definíciót a szervezete felhasználóinak belső katalógusához. Ezután üzembe helyezi ezt a felügyelt alkalmazást az előfizetésén. A bevezetés megkönnyítése érdekében előre létrehoztuk a felügyelt alkalmazás fájljait. Egy fájl meghatározza a megoldás infrastruktúráját. Egy második fájl, amely meghatározza a felhasználói felületet a felügyelt alkalmazás a portálon keresztüli üzembe helyezéséhez. Ezek a fájlok a GitHubon érhetők el. A fájlok létrehozását a [szolgáltatáskatalógus-alkalmazás létrehozását ismertető](publish-service-catalog-app.md) oktatóanyagból tanulhatja meg.

Amikor kész van, három, a felügyelt alkalmazás különböző részeit tartalmazó erőforráscsoportja lesz.

| Erőforráscsoport | Contains | Leírás |
| -------------- | -------- | ----------- |
| appDefinitionGroup | A felügyelt alkalmazás definíciója. | A közzétevő hozza létre ezt az erőforráscsoportot és a felügyelt alkalmazás definícióját. A felügyelt alkalmazást bárki üzembe helyezheti, aki hozzáféréssel rendelkezik ahhoz. |
| applicationGroup | A felügyelt alkalmazás példánya. | A fogyasztó hozza létre ezt az erőforráscsoportot és a felügyelt alkalmazás példányát. A fogyasztó ezen a példányon keresztül frissítheti a felügyelt alkalmazást. |
| infrastructureGroup | A felügyelt alkalmazás erőforrásai. | Ez az erőforráscsoport automatikusan létrejön a felügyelt alkalmazás létrejöttekor. A közzétevő hozzáféréssel rendelkezik ehhez az erőforráscsoporthoz az alkalmazás kezelése céljából. A fogyasztó korlátozott hozzáféréssel rendelkezik az erőforráscsoporthoz. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Erőforráscsoport létrehozása definícióhoz

A felügyelt alkalmazás definíciója egy erőforráscsoportban létezik. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Egy erőforráscsoport létrehozásához használja a következő parancsot:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Egy felügyelt alkalmazás meghatározásakor ki kell választani egy, a fogyasztó nevében az erőforrásokat felügyelő felhasználót, csoportot vagy alkalmazást. Ez az identitás engedélyeket kap a felügyelt erőforráscsoporthoz a kijelölt szerepkörnek megfelelően. Ilyenkor a leggyakoribb megoldás az, hogy létrehoz egy Azure Active Directory-csoportot az erőforrások felügyeletére. Ebben a cikkben azonban használja a saját identitását.

Az identitás objektumazonosítójának lekéréséhez adja meg az egyszerű felhasználónevét a következő parancsban:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Ezután annak a beépített RBAC-szerepkörnek a szerepkördefiníció-azonosítójára van szükség, amelyhez hozzáférést szeretne biztosítani a felhasználó számára. Az alábbi parancs bemutatja, hogyan kérheti le a tulajdonos szerepkör szerepkördefiníció-azonosítóját:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Most hozza létre a felügyelt alkalmazás definíciójához tartozó erőforrást. A felügyelt alkalmazás csak egy tárfiókot tartalmaz.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

A parancs befejeződésekor egy felügyeltalkalmazás-definíció található az erőforráscsoportban. 

Íme néhány az előző példában használt paraméterek közül:

* **resource-group**: Annak az erőforráscsoportnak a neve, ahol létrejött a felügyelt alkalmazás definíciója.
* **lock-level**: A felügyelt erőforráscsoporton elhelyezett zárolás típusa. Megakadályozza, hogy az ügyfél nemkívánatos műveleteket hajtson végre ezen az erőforráscsoporton. Jelenleg a ReadOnly az egyetlen támogatott zárolási szint. Amikor a ReadOnly van megadva, az ügyfél csak olvashatja a felügyelt erőforráscsoportban lévő erőforrásokat. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.
* **authorizations**: A felügyelt erőforráscsoporthoz való engedély biztosításához használt résztvevő-azonosítót és szerepkördefiníció-azonosítót ismerteti. `<principalId>:<roleDefinitionId>` formátumban van megadva. Ehhez a tulajdonsághoz több érték is megadható. Ha több értékre van szükség, `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` formátumban kell megadni őket. Az értékeket szóközzel kell elválasztani egymástól.
* **package-file-uri**: A szükséges fájlokat tartalmazó .zip csomag helye. A csomagnak tartalmaznia kell legalább a **mainTemplate.json** és a **createUiDefinition.json** fájlt. A **mainTemplate.json** határozza meg a felügyelt alkalmazás részeként kiépített Azure-erőforrásokat. A sablon pont olyan, mint egy átlagos Resource Manager-sablon. A **createUiDefinition.json** hozza létre a felhasználói felületet a felügyelt alkalmazást a portálon keresztül létrehozó felhasználók számára.

## <a name="create-resource-group-for-managed-application"></a>Erőforráscsoport létrehozása felügyelt alkalmazáshoz

Most már készen áll a felügyelt alkalmazás üzembe helyezésére. 

Szüksége van egy erőforráscsoportra, amely az üzembe helyezett alkalmazást tartalmazza. Ennek a helye legyen **westcentralus**.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>A felügyelt alkalmazás üzembe helyezése

Az alkalmazás a következő parancsokkal helyezhető üzembe:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Íme néhány az előző példában használt paraméterek közül:

* **managedapp-definition-id**: A cikkben a korábbiakban létrehozott definíció azonosítója.
* **managed-rg-id**: A felügyelt alkalmazáshoz társított erőforrások erőforráscsoportjának azonosítója. A parancs hozza létre ezt az erőforráscsoportot. A csoport **nem létezhet a parancs futtatása előtt**. Ezt az erőforráscsoportot a közzétevő felügyeli. 
* **resource-group**: Az az erőforráscsoport, ahol létrejött a felügyelt alkalmazás erőforrása.
* **parameters**: A felügyelt alkalmazáshoz társított erőforrásokhoz szükséges paraméterek.

Az üzembe helyezés sikeres befejeződése után láthatja, hogy az alkalmazás létrejött az applicationGroup csoportban. Létrejött a storageAccount erőforrás az infrastructureGroup csoportban.

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Példák a fájlokra: [Minta felügyelt alkalmazások](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.
