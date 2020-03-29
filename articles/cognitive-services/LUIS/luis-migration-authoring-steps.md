---
title: Áttelepítés Azure-alapú szerzői erőforrásra
titleSuffix: Azure Cognitive Services
description: Áttelepíthető egy Azure-szerzői erőforrásra.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194638"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Az Azure szerzői erőforrásra való áttelepítés lépései

A Language Understanding (LUIS) portálon áttelepítheti az összes saját alkalmazások az Azure szerzői erőforrás használatához.

## <a name="prerequisites"></a>Előfeltételek

* **Szükség esetén**készítsen biztonsági másolatot az alkalmazásokról a LUIS-portál alkalmazáslistájáról az egyes alkalmazások exportálásával vagy az exportálási [API használatával.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)
* **Szükség esetén**mentse az egyes alkalmazások munkatársainak listáját. Az áttelepítési folyamat részeként minden közreműködő e-mailt küldhet.
* **Kötelező**, [Azure-előfizetéssel kell rendelkeznie.](https://azure.microsoft.com/free/) Az előfizetési folyamat egy része számlázási adatokat igényel. Azonban használhatja az ingyenes (F0) tarifacsomagok, ha a LUIS használata. Előfordulhat, hogy a használat növekedésével egy fizetős szintre van szüksége.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon.](https://azure.microsoft.com/free/)

## <a name="access-the-migration-process"></a>Az áttelepítési folyamat elérése

A rendszer hetente kéri az alkalmazások áttelepítését. Ezt az ablakot átgrés nélkül is megszakíthatja. Ha a következő ütemezett időszak előtt szeretne áttérni, megkezdheti az áttelepítési folyamatot az **Azure** ikonról a LUIS-portál felső eszköztárán.

> [!div class="mx-imgBorder"]
> ![Áttelepítés ikon](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Az alkalmazás tulajdonosa megkezdi az áttelepítési folyamatot

Az áttelepítési folyamat akkor érhető el, ha Ön a tulajdonosa bármely LUIS-alkalmazások.

1. Jelentkezzen be a [LUIS-portálra,](https://www.luis.ai) és fogadja el a használati feltételeket.
1. Az áttelepítés előugró ablaka lehetővé teszi az áttelepítés folytatását vagy későbbi áttelepítését. Válassza **az Áttelepítés most**lehetőséget. Ha úgy dönt, hogy később migrál, 9 hónap áll rendelkezésére az azure-beli új szerzői kulcsra való áttelepítésre.

    ![Az áttelepítési folyamat első előugró ablaka, válassza az Áttelepítés most lehetőséget.](./media/migrate-authoring-key/migrate-now.png)

1. Ha valamelyik alkalmazásrendelkezik együttműködőkkel, a rendszer kéri, hogy **küldjön nekik egy e-mailt,** amely ből tudakadni az áttelepítésről. Ez egy nem kötelező lépés.

    Miután áttelepítette fiókját az Azure-ba, alkalmazásai már nem lesznek elérhetők az együttműködők számára.

    Minden közreműködő és alkalmazás esetében az alapértelmezett e-mail alkalmazás egy enyhén formázott e-maillel nyílik meg. Elküldés előtt szerkesztheti az e-mailt.

    Az e-mail sablon tartalmazza a pontos alkalmazásazonosítót és az alkalmazás nevét.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Válassza ki, hogy hozzon létre egy LUIS szerzői erőforrás kiválasztásával egy meglévő szerzői erőforrás, vagy hozzon létre egy új szerzői erőforrás.

    > [!div class="mx-imgBorder"]
    > ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. A következő ablakban adja meg az erőforráskulcs adatait. Az adatok megadása után válassza az **Erőforrás létrehozása**lehetőséget. Régiónként és előfizetésenként 10 ingyenes szerzői erőforrást kaphat.

    ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új szerzői erőforrás létrehozásakor**adja meg a következő információkat:

    * **Erőforrás neve** – egyéni név, amelyet a szerzői és előrejelzési végpontlekérdezések URL-címének részeként használ.
    * **Bérlő** – a bérlő, amelyhez az Azure-előfizetés e társított.
    * **Előfizetés neve** – az erőforrásért kiszámlázott előfizetés.
    * **Erőforráscsoport** – egyéni erőforráscsoport által választott vagy létrehozott név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáférés hez és a kezeléshez.
    * **Hely** – a helyválasztás az **erőforráscsoport** kiválasztásán alapul.
    * **Tarifacsomag** – a tarifacsomag határozza meg a maximális tranzakciót másodpercenként és havonta.

1. Ellenőrizze a szerzői erőforrást, és telepítse most az **áttelepítést.**

    ![Szerzői erőforrás létrehozása](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. A szerzői erőforrás létrehozásakor megjelenik a sikeres üzenet. Az előugró ablak bezárásához válassza a **Bezárás** gombot.

    ![A szerzői erőforrás létrehozása sikeresen megtörtént.](./media/migrate-authoring-key/migration-success.png)

    A **Saját alkalmazások** lista az új szerzői erőforrásba áttelepített alkalmazásokat jeleníti meg.

    Nem kell ismernie a szerzői erőforrás kulcsát, hogy továbbra is szerkesztheti az alkalmazásokat a LUIS portálon. Ha az alkalmazásokat programozott módon kívánja szerkeszteni, szüksége van a szerzői kulcsértékekre. Ezek az értékek a **LUIS-portálon a -> Azure-erőforrások kezelése** lapon jelennek meg, és az erőforrás **kulcsai** lapon is elérhetők az Azure Portalon.

1. Az alkalmazások elérése előtt válassza ki az előfizetést és a LUIS szerzői erőforrást a létrehozható alkalmazások megtekintéséhez.

    ![Válassza ki az előfizetés és a LUIS szerzői erőforrás megtekintéséhez az alkalmazásokat a hozhat létre.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Az alkalmazásközreműködő megkezdi az áttelepítési folyamatot

Kövesse ugyanazokat a lépéseket, mint az alkalmazás tulajdonosa az áttelepítéshez. A folyamat létrehoz egy új `LUIS.Authoring`szerzői erőforrást.

Át kell telepítenie a fiókját ahhoz, hogy közreműködőként hozzálehessen adni a mások tulajdonában lévő áttelepített alkalmazásokhoz.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Az áttelepítési folyamat után közreműködők hozzáadása a szerzői erőforráshoz

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

További információ [a közreműködők hozzáadásáról.](luis-how-to-collaborate.md)

## <a name="troubleshooting-errors-with-the-migration-process"></a>Az áttelepítési folyamat hibáinak elhárítása

Ha hibaüzenetet `MissingSubscriptionRegistration` kap a LUIS portálon egy piros értesítési sávaz áttelepítési folyamat során, hozzon létre egy Cognitive Service-erőforrást az [Azure Portalon](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-ben.](luis-how-to-azure-subscription.md#create-resources-in-azure-cli) További információ [a hiba okairól](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>További lépések


* A szerzői és futásidejű kulcsokra szóló [fogalmak áttekintése](luis-concept-keys.md)
* A [kulcsok hozzárendelésének](luis-how-to-azure-subscription.md) és [közreműködők hozzáadásának áttekintése](luis-how-to-collaborate.md)
