---
title: Bejelentkezés a LUIS portálra
description: Ha Ön egy új felhasználó, aki bejelentkezik a LUIS portálra, a bejelentkezési élmény kis mértékben eltérhet az aktuális felhasználói fióktól.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 58212fc35df7f230682d4f80b5eadf6cd8ca0902
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90008069"
---
# <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

Ha Ön egy új felhasználó, aki bejelentkezik a LUIS portálra, a bejelentkezési élmény kis mértékben eltérhet az aktuális felhasználói fióktól:
  * Azure-előfizetéshez társítva
  * Nincs Azure-előfizetéshez társítva

## <a name="determine-account-type"></a>Fiók típusának meghatározása

Amikor először jelentkezik be a LUIS-portálra, a következő vizualizációs mutatók segítségével határozza meg a fiók típusát.

### <a name="account-without-azure-subscription"></a>Fiók Azure-előfizetés nélkül

Egy olyan fiók, amely nincs Azure-előfizetéshez társítva, az Azure ikon jelenik meg a jobb felső navigációs sávon. Miután áttelepítette a társított fiók típusát, az ikon már nem jelenik meg.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="A LUIS navigációs sávjának részleges képernyőképe az Azure Icon ikonnal.":::

### <a name="account-with-azure-subscription"></a>Fiók Azure-előfizetéssel

Az Azure-előfizetéshez társított fiók lehetővé teszi, hogy kiválassza a használni kívánt előfizetést és erőforrást.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Részleges képernyőfelvétel a LUIS-portálról erőforrás-kiválasztás legördülő listából.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Bejelentkezés az Azure-előfizetéshez társított fiókkal

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai) , és fogadja el a használati feltételeket.

1. Két lehetőség regisztrálása:

    * Folytassa az Azure-erőforrások használatát, amely az ajánlott elérési út, és hamarosan az egyetlen elérhető útvonal lesz. Ez az elérési út lehetővé teszi, hogy a LUIS-fiókot egy Azure authoring-erőforrással társítsa, vagy egy meglévő erőforrás kiválasztásával az előfizetésben, vagy egy új erőforrást hoz létre. Ez egyenértékű az áttelepített Migrálás nélkül, anélkül, hogy az [áttelepítési folyamat](luis-migration-authoring.md#what-is-migration) későbbi szakaszában kellene lennie. Az összes felhasználónak 2020 november 2-án kell áttérnie.

    * Folytassa a kezdő vagy a próbaverziós kulcs használatát. Ez az elérési út lehetővé teszi, hogy bejelentkezzen a LUIS-be a kezdő vagy a próbaverziós erőforrással, amelyet anélkül kell létrehoznia, hogy erőforrásokat kellene létrehoznia. Ha ezt az elérési utat választja, végül [át kell telepítenie a fiókját](luis-migration-authoring.md#migration-steps) , és csatolnia kell az alkalmazásait egy authoring-erőforráshoz. Ezért érdemes kiválasztani az elérési utat, ahol az Azure-erőforrást folytatja.

    [További információ a szerzői műveletek és a kezdő kulcsok használatáról](luis-how-to-azure-subscription.md#luis-resources). Mindkét erőforrás 1 000 000 ingyenes szerzői tranzakciókat és 1000 ingyenes előrejelzési végponti tranzakciókat biztosít.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Részleges képernyőfelvétel a Language Understanding authoring Resource típusának kiválasztásához.":::

1. Meglévő authoring-erőforrás használata

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Szerzői erőforrás kiválasztása":::

    Ha már rendelkezik az előfizetésben található LUIS authoring-erőforrásokkal, és a bejelentkezés során társít egyet a LUIS-fiókjához, válassza a **meglévő szerzői erőforrás használata** lehetőséget, és adja meg a következő információkat:

    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van. A bérlőket nem lehet átváltani a meglévő ablakból. A bérlőket átválthatja a jobb szélső ikon kiválasztásával, amely a felső sávon lévő monogramot tartalmazza.
    * **Előfizetés neve** – az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
    * **Erőforrás neve** – az a szerzői erőforrás, amelyhez társítani szeretné a fiókot.

    > [!Note]
    > Ha a keresett authoring-erőforrás szürkén jelenik meg a legördülő listában, ez azt jelenti, hogy bejelentkezett egy másik regionális portálra. [Ismerje meg a regionális portálok koncepcióját](luis-reference-regions.md#luis-authoring-regions).

1. Új authoring-erőforrás létrehozása

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Szerzői erőforrás létrehozása":::

    **Új authoring-erőforrás létrehozásakor**adja meg a következő információkat:

    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van. A bérlőket nem lehet átváltani a meglévő ablakból. A bérlőket átválthatja a jobb szélső ikon kiválasztásával, amely a felső sávon lévő monogramot tartalmazza.
    * **Erőforrás neve** – az Ön által választott egyéni név, amelyet a szerzői műveletek URL-címének részeként használ a rendszer. Az erőforrás neve csak alfanumerikus karaktereket és "-" karaktert tartalmazhat, és nem kezdődhet vagy végződhet "-" karakterrel. Ha bármilyen más szimbólum szerepel a névben, az erőforrás létrehozása sikertelen lesz.
    * **Előfizetés neve** – az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
    * **Erőforráscsoport** – az előfizetésben kiválasztott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez. Ha jelenleg nincs erőforráscsoport az előfizetésben, nem lehet létrehozni egyet a LUIS portálon. A bejelentkezési folyamat folytatásához lépjen a [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , és hozzon létre egyet a Luis-ben.

1. Az elérési út kiválasztása után eltarthat néhány másodpercig, amíg meg nem jelenik a "fiókja sikeres migrálása. Fejezze be a **Continue (folytatás**) lehetőséget.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Erőforrás-létrehozás megerősítése":::

    > [!Note]
    > Ha van előfizetése, és legalább egy szerzői erőforrás abban a régióban, ahol a portálon regisztrál, akkor előfordulhat, hogy a rendszer automatikusan bejelentkezik a LUIS-ba, és hozzárendelte az erőforráshoz, és nem kell kiválasztania, hogy melyik elérési utat kell megadnia.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Bejelentkezés olyan felhasználói fiókkal, amely nincs Azure-előfizetéshez társítva

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai) , és győződjön meg arról, hogy elfogadja a használati feltételeket.

1. Fejezze be a **Continue (folytatás**) lehetőséget. A rendszer automatikusan bejelentkezik egy próbaverziós/Starter-kulccsal. Ez azt jelenti, hogy végül [át kell telepítenie a fiókját](luis-migration-authoring.md#migration-steps) , és csatolnia kell az alkalmazásait egy authoring-erőforráshoz. Az áttelepítési folyamat elvégzéséhez be kell jelentkeznie egy [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Nincs előfizetési forgatókönyv":::

## <a name="troubleshooting"></a>Hibaelhárítás

* Ha egy szerzői erőforrást hoz létre a Azure Portal egy másik régióban, mint a portálon, amelybe bejelentkezik, a szerzői erőforrás szürkén jelenik meg.
* Új erőforrás létrehozásakor győződjön meg arról, hogy az erőforrás neve csak alfanumerikus karaktereket, "-" karaktert tartalmaz, és nem kezdődhet vagy végződhet "-" karakterrel. Ellenkező esetben a művelet sikertelen lesz.
* Győződjön meg arról, hogy rendelkezik a [megfelelő engedélyekkel az előfizetéséhez egy Azure-erőforrás létrehozásához](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba az előfizetés rendszergazdájával, és adja meg a megfelelő engedélyeket.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [indíthat el új alkalmazást](luis-how-to-start-new-app.md)
