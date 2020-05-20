---
title: Áttelepítés Azure authoring-erőforrásra
titleSuffix: Azure Cognitive Services
description: Migrálás egy Azure authoring-erőforrásba.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 2c28e6c1edf4188cf3ea80c14565785dcf1dcbba
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653811"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Az Azure authoring-erőforrásba való Migrálás lépései

A Language Understanding (LUIS) portálon telepítse át az összes olyan alkalmazást, amelyet az Azure authoring Resource használatára használ.

## <a name="prerequisites"></a>Előfeltételek

* **Optionally**A Luis portál alkalmazások listájáról is készíthet biztonsági másolatot az alkalmazásokról az egyes alkalmazások exportálásával vagy az exportálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)használatával.
* **Szükség**esetén mentse az egyes alkalmazások collaborator's listáját. Minden közreműködő e-mailt küldhet az áttelepítési folyamat részeként.
* **Szükséges**, rendelkeznie kell egy Azure- [előfizetéssel](https://azure.microsoft.com/free/). Az előfizetési folyamat egy része számlázási adatokat igényel. Ha azonban a LUIS-t használja, használhatja az ingyenes (F0) díjszabási szintet is. Előfordulhat, hogy végül egy fizetős szintet kell használnia, mivel a használat növekszik.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Hozzáférés az áttelepítési folyamathoz

Heti rendszerességgel megkéri, hogy telepítse át az alkalmazásokat. Ezt az ablakot Migrálás nélkül is megszakíthatja. Ha a következő ütemezett időszak előtt kíván áttelepítést végezni, megkezdheti az áttelepítési folyamatot az **Azure** ikonján a Luis portál felső sávján.

> [!div class="mx-imgBorder"]
> ![Áttelepítés ikonja](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Az alkalmazás tulajdonosa megkezdi az áttelepítési folyamatot

Az áttelepítési folyamat akkor érhető el, ha Ön a LUIS-alkalmazások tulajdonosa.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai) , és fogadja el a használati feltételeket.
1. Az áttelepítés előugró ablakával folytathatja az áttelepítést, vagy később is áttelepítheti azokat. Válassza az **áttelepítés most**lehetőséget. Ha később szeretné áttelepíteni a-t, 9 hónapja van, hogy áttelepítse az új szerzői kulcsba az Azure-ban.

    ![Első előugró ablak az áttelepítési folyamatban válassza az áttelepítés most lehetőséget.](./media/migrate-authoring-key/migrate-now.png)

1. Ha bármelyik alkalmazás rendelkezik közreműködővel, a rendszer arra kéri, hogy **küldjön nekik egy e-mailt** , amely tájékoztatja őket az áttelepítésről. Ez egy választható lépés.

    Miután áttelepítette fiókját az Azure-ba, az alkalmazások már nem lesznek elérhetők a közreműködők számára.

    Az egyes közreműködők és alkalmazások esetében az alapértelmezett e-mail-alkalmazás egy egyszerűsített formátumú e-mail-címmel nyílik meg. A küldés előtt szerkesztheti az e-maileket.

    Az e-mail-sablon tartalmazza az alkalmazás AZONOSÍTÓját és az alkalmazás nevét.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Hozzon létre egy LUIS authoring-erőforrást úgy, hogy egy meglévő szerzői erőforrást használ, vagy új authoring-erőforrást szeretne létrehozni.

    > [!div class="mx-imgBorder"]
    > ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. A következő ablakban adja meg az erőforrás-kulcs adatait. Az adatok megadása után válassza az **erőforrás létrehozása**lehetőséget. Régiónként akár 10 ingyenes Author-erőforrást is használhat, előfizetések szerint.

    ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új authoring-erőforrás létrehozásakor**adja meg a következő információkat:

    * **Erőforrás neve** – a kiválasztott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van.
    * **Előfizetés neve** – az erőforráshoz számlázandó előfizetés.
    * **Erőforráscsoport** – a kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
    * **Hely** – a hely kiválasztása az **erőforráscsoport** kiválasztása alapján történik.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.

1. Érvényesítse a szerzői erőforrást, és **telepítse át most**.

    ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. A szerzői erőforrás létrehozásakor megjelenik a siker üzenet. Az előugró ablak bezárásához kattintson a **Bezárás** gombra.

    ![A szerzői erőforrás létrehozása sikeresen megtörtént.](./media/migrate-authoring-key/migration-success.png)

    A **saját alkalmazások** lista az új szerzői erőforrásba áttelepített alkalmazásokat jeleníti meg.

    Nem kell tudnia, hogy az authoring Resource kulcsa az alkalmazások a LUIS Portalon való szerkesztésének folytatásához szükséges. Ha programozott módon szeretné szerkeszteni az alkalmazásokat, szüksége lesz a szerzői kulcs értékeire. Ezek az értékek a LUIS **-portál Manage-> Azure-erőforrások** lapján jelennek meg, és az erőforrás **kulcsai** oldalának Azure Portal is elérhetők.

1. Az alkalmazásokhoz való hozzáférés előtt válassza ki az előfizetés és a LUIS authoring Resource elemet a megjelenő alkalmazások megtekintéséhez.

    > [!div class="mx-imgBorder"]
    > ![Válassza ki az előfizetés és a LUIS authoring Resource elemet a megadható alkalmazások megtekintéséhez.](./media/create-app-in-portal-select-subscription-luis-resource.png)

## <a name="app-contributor-begins-the-migration-process"></a>Az alkalmazás közreműködője megkezdi az áttelepítési folyamatot

Kövesse az alkalmazás tulajdonosának áttelepítéséhez szükséges lépéseket. A folyamat létrehoz egy új authoring Resource típusú erőforrást `LUIS.Authoring` .

A fiókját át kell telepíteni ahhoz, hogy közreműködőként hozzá lehessen adni a mások tulajdonában lévő áttelepített alkalmazásokat.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Az áttelepítési folyamat után vegyen fel közreműködőket a szerzői erőforrásba

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Ismerje meg [, hogyan vehet fel közreműködőket](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Hibák elhárítása az áttelepítési folyamattal

Ha az `MissingSubscriptionRegistration` áttelepítési folyamat során hibaüzenetet kap a Luis-portálon egy piros értesítési sávon, hozzon létre egy kognitív szolgáltatási erőforrást a [Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)-ben. További információ a [hiba okairól](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>További lépések


* A szerzői és futtatókörnyezeti kulcsokkal kapcsolatos [fogalmak](luis-concept-keys.md) áttekintése
* [A kulcsok hozzárendelésének és a](luis-how-to-azure-subscription.md) [közreműködők](luis-how-to-collaborate.md) hozzáadásának áttekintése
