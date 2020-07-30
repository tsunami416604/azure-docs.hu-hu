---
title: Példány és hitelesítés beállítása (CLI)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás egy példányának beállítása a parancssori felület használatával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 287ee62acf3a078c4b47803060f61c9dd4134ab7
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408288"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure digitális Twins-példány és-hitelesítés beállítása (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának**lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A cikk jelen verziója ezeket a lépéseket manuálisan, egyenként, a parancssori felület használatával hajtja végre.
* Ha ezeket a lépéseket manuálisan szeretné elvégezni a Azure Portal használatával, tekintse meg a jelen cikk portáljának verzióját: [*útmutató: példány és hitelesítés beállítása (portál)*](how-to-set-up-instance-portal.md).
* Ha az üzembe helyezési parancsfájl mintájának használatával szeretne automatikus telepítést végezni, tekintse meg a jelen cikk parancsfájlokkal ellátott verzióját: [*útmutató: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-role-cli.md](../../includes/digital-twins-setup-role-cli.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Az Azure Digital Twins-példány létrehozása

Ebben a szakaszban az **Azure Digital Twins új példányát fogja létrehozni** a Cloud Shell parancs használatával. A következőket kell megadnia:
* Egy erőforráscsoport, amelybe a üzembe helyezése megtörténjen. Ha még nem rendelkezik már meglévő erőforráscsoporthoz, létrehozhat egyet ezzel a paranccsal:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Egy régió az üzemelő példányhoz. Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [*Azure-termékek területre*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* A példány neve. Az új példány nevének a régión belül egyedinek kell lennie az előfizetéshez (ami azt jelenti, hogy ha az előfizetés egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már használja a választott nevet), a rendszer kérni fogja, hogy válasszon másik nevet.

A példány létrehozásához használja ezeket az értékeket a következő parancsban:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success"></a>Sikeres ellenőrzés

Ha a példány létrehozása sikeresen megtörtént, a Cloud Shell eredmény a következőképpen néz ki: a létrehozott erőforrással kapcsolatos információk kihelyezése:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Parancsablak az erőforráscsoport és az Azure Digital Twins-példány sikeres létrehozásával":::

Jegyezze fel az Azure Digital Twins-példány *állomásneve*, *neve*és *resourceGroup* a kimenetből. Ezek mind olyan fontos értékek, amelyekre szüksége lehet az Azure Digital Twins-példánnyal való munka folytatásához, a hitelesítés és a kapcsolódó Azure-erőforrások beállításához. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket meg kell osztani velük.

> [!TIP]
> Ezeket a tulajdonságokat, valamint a példány összes tulajdonságát a futtatásával bármikor megtekintheti `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra. Ezután meg kell adnia a megfelelő Azure-felhasználói engedélyeket a kezeléséhez.

## <a name="set-up-user-access-permissions"></a>Felhasználói hozzáférési engedélyek beállítása

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

A következő parancs használatával rendelje hozzá a szerepkört (az Azure-előfizetés tulajdonosának kell futnia):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ.

> [!TIP]
> Ha a *400: BadRequest* hiba jelenik meg, futtassa a következő parancsot a felhasználó *ObjectId* lekéréséhez:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Ezután ismételje meg a szerepkör-hozzárendelési parancsot az e-mail-cím helyett a felhasználó *objektum-azonosítójával* .

### <a name="verify-success"></a>Sikeres ellenőrzés

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra, és hozzárendelt engedélyekkel rendelkezik a kezeléséhez. Ezután be kell állítania egy ügyfélalkalmazás engedélyeit, hogy hozzáférjenek az alkalmazáshoz.

## <a name="set-up-access-permissions-for-client-applications"></a>Ügyfélalkalmazások hozzáférési engedélyeinek beállítása

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Alkalmazás-regisztráció létrehozásához meg kell adnia az Azure digitális Twins API-k erőforrás-azonosítóit, valamint az API-hoz való alapkonfigurációt.

A munkakönyvtárában hozzon létre egy új fájlt, és adja meg a következő JSON-kódrészletet a részletek konfigurálásához: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Mentse ezt a fájlt _**manifest.jsként**_.

> [!NOTE] 
> Vannak olyan helyek, ahol a "felhasználóbarát" emberi olvashatóságú karakterlánc `https://digitaltwins.azure.net` használható az Azure digitális Twins erőforrás-alkalmazás azonosítójára a GUID helyett `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . A dokumentációban szereplő számos példa például a MSAL-könyvtárral történő hitelesítést használja, és a felhasználóbarát karakterláncot is használhatja erre a célra. Az alkalmazás regisztrációjának létrehozása során azonban az azonosító GUID formáját kell megadnia, mivel a fenti ábrán látható. 

Ezután töltse fel ezt a fájlt Cloud Shellba. A Cloud Shell ablakban kattintson a "fájlok feltöltése/letöltése" ikonra, és válassza a "feltöltés" lehetőséget.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="A feltöltési lehetőség kiválasztását ábrázoló Cloud Shell ablak":::
Nyissa meg az imént létrehozott *manifest.js* , és nyomja meg a "Megnyitás" lehetőséget.

Ezután futtassa a következő parancsot egy alkalmazás regisztrációjának létrehozásához (szükség szerint cserélje le a helyőrzőket):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Íme a parancs kimenetének részlete, amely a létrehozott regisztrációval kapcsolatos információkat jeleníti meg:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Cloud Shell új Azure AD-alkalmazás regisztrációjának kimenete":::

### <a name="verify-success"></a>Sikeres ellenőrzés

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Először ellenőrizze, hogy a feltöltött *manifest.js* beállításai megfelelően vannak-e beállítva a regisztrációhoz. Ehhez a menüsávban válassza a *jegyzékfájl* lehetőséget az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a *manifest.jsmezőket a* következő alatt `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Fontos értékek gyűjtése

Ezután válassza az *Áttekintés* lehetőséget a menüsávon az alkalmazás regisztrációjának részleteinek megtekintéséhez:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Az alkalmazás regisztrációjának fontos értékeit a portálon tekintheti meg":::

Jegyezze **fel a lapon** megjelenő *alkalmazás (ügyfél) azonosítóját* és *KÖNYVTÁRát (bérlői azonosítóját)* . Ezekre az értékekre később szükség lesz az [ügyfélalkalmazás hitelesítéséhez az Azure digitális Twins API](how-to-authenticate-client.md)-kon. Ha nem az a személy, aki az ilyen alkalmazások kódját fogja írni, meg kell osztania ezeket az értékeket a következő személlyel:.

### <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan csatlakoztatható az ügyfélalkalmazás a példányhoz az ügyfélalkalmazás hitelesítési kódjának megírásával:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)