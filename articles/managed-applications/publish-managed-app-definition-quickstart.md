---
title: Azure-beli felügyelt alkalmazás definíciójának létrehozása | Microsoft Docs
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804912"
---
# <a name="publish-an-azure-managed-application-definition"></a>Azure-beli felügyelt alkalmazásdefiníció közzététele

Ebben a rövid útmutatóban a felügyelt alkalmazások használatával ismerkedhet meg. Először hozzáad egy felügyeltalkalmazás-definíciót a szervezete felhasználóinak belső katalógusához. A bevezetés megkönnyítése érdekében előre létrehoztuk a felügyelt alkalmazás fájljait. Ezek a fájlok a GitHubon érhetők el. A fájlok létrehozását a [szolgáltatáskatalógus-alkalmazás létrehozását ismertető](publish-service-catalog-app.md) oktatóanyagból tanulhatja meg.

Ha elkészült, rendelkezni fog egy **appDefinitionGroup** nevű erőforráscsoporttal, amely tartalmazza a felügyelt alkalmazás definícióját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Erőforráscsoport létrehozása definícióhoz

A felügyelt alkalmazás definíciója egy erőforráscsoportban létezik. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Egy erőforráscsoport létrehozásához használja a következő parancsot:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Egy felügyelt alkalmazás meghatározásakor ki kell választani egy a fogyasztó számára az erőforrásokat felügyelő felhasználót, csoportot vagy alkalmazást. Ez az identitás engedélyeket kap a felügyelt erőforráscsoporthoz a kijelölt szerepkörnek megfelelően. Ilyenkor a leggyakoribb megoldás az, hogy létrehoz egy Azure Active Directory-csoportot az erőforrások felügyeletére. Ebben a cikkben azonban használja a saját identitását.

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
* **authorizations**: A felügyelt erőforráscsoporthoz való engedély biztosításához használt résztvevő-azonosítót és szerepkördefiníció-azonosítót ismerteti. `<principalId>:<roleDefinitionId>` formátumban van megadva. Ha egynél több érték szükséges, adja meg azokat a következő formátumban: `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Az értékeket szóközzel kell elválasztani egymástól.
* **package-file-uri**: A szükséges fájlokat tartalmazó .zip csomag helye. A csomagnak tartalmaznia kell a **mainTemplate.json** és a **createUiDefinition.json** fájlt. A **mainTemplate.json** határozza meg a felügyelt alkalmazás részeként létrehozott Azure-erőforrásokat. A sablon pont olyan, mint egy átlagos Resource Manager-sablon. A **createUiDefinition.json** hozza létre a felhasználói felületet a felügyelt alkalmazást a portálon keresztül létrehozó felhasználók számára.

## <a name="next-steps"></a>További lépések

Sikeresen közzétette a felügyelt alkalmazás definícióját. Most megismerheti, hogyan helyezheti üzembe a definíció egyik példányát.

> [!div class="nextstepaction"]
> [Rövid útmutató: Szolgáltatáskatalógusban elérhető alkalmazás telepítése](deploy-service-catalog-quickstart.md)