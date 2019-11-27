---
title: Azure Functions üzembe helyezési pontok
description: Ismerje meg, hogyan hozhat létre és használhat üzembe helyezési tárolóhelyeket Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: a59b62e19ac1e470dcdaaf0281dde9904a70b583
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230673"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions üzembe helyezési pontok

Azure Functions üzembe helyezési pontok lehetővé teszik, hogy a Function alkalmazás különböző példányokat futtasson "bővítőhelyek" néven. A tárolóhelyek egy nyilvánosan elérhető végponton keresztül kitett különböző környezetek. Az egyik alkalmazás példánya mindig az üzemi tárolóhelyre van leképezve, és igény szerint egy tárolóhelyhez rendelt példányok is felcserélhetők. Az Apps Service-csomag alatt futó Function apps több tárolóhelytel rendelkezhet, míg a felhasználás alatt csak egy tárolóhely engedélyezett.

A következő, a függvények a tárolóhelyek cseréjével kapcsolatos hatásait mutatja be:

- A forgalom átirányítása zökkenőmentes; a swap miatt a rendszer nem távolítja el a kérelmeket.
- Ha egy függvény a swap során fut, a végrehajtás folytatódik, és az azt követő triggerek átirányítva lesznek a felcserélt alkalmazás-példányra.

> [!NOTE]
> A Linux-használati csomag jelenleg nem érhető el.

## <a name="why-use-slots"></a>Miért érdemes tárolóhelyeket használni?

Számos előnnyel jár az üzembe helyezési pontok használata. A következő forgatókönyvek a bővítőhelyek gyakori felhasználási módjait ismertetik:

- Különböző **környezetek különböző célokra**: a különböző tárolóhelyek használata lehetővé teszi az alkalmazás-példányok megkülönböztetését, mielőtt az éles környezetbe vagy egy átmeneti tárolóhelyre cseréli őket.
- **Előmelegítés**: a közvetlenül az éles környezetbe való üzembe helyezése lehetővé teszi az alkalmazás számára, hogy az élő működés előtt belépjen. Emellett a tárolóhelyek használata csökkenti a HTTP-triggert használó munkaterhelések késését. A példányokat a rendszer az üzembe helyezés előtt felmelegszik, ami csökkenti az újonnan üzembe helyezett függvények hideg indítását.
- **Egyszerű tartalékok**: az éles használat után a korábban előkészített alkalmazáshoz tartozó tárolóhely már az előző éles alkalmazásban van. Ha az éles tárolóhelyre való váltás nem a várt módon történik, akkor a swap azonnal visszafordítható az "utolsó ismert jó példány" visszaszerzéséhez.

## <a name="swap-operations"></a>Swap-műveletek

A csere során az egyik tárolóhely a forrás és a másik cél. A forrás tárolóhely az alkalmazás azon példányát alkalmazza, amely a cél tárolóhelyre van alkalmazva. A következő lépések gondoskodnak arról, hogy a cél tárolóhely ne tapasztaljon állásidőt a swap során:

1. **Beállítások alkalmazása:** A cél tárolóhelyről származó beállításokat a rendszer a forrás tárolóhely összes példányára alkalmazza. Az éles környezet beállításait például az átmeneti példányra alkalmazza a rendszer. Az alkalmazott beállítások a következő kategóriákat tartalmazzák:
    - A [tárolóhely-specifikus](#manage-settings) Alkalmazásbeállítások és a kapcsolatok karakterláncai (ha vannak ilyenek)
    - [Folyamatos üzembe helyezési](../app-service/deploy-continuous-deployment.md) beállítások (ha engedélyezve vannak)
    - [App Service hitelesítési](../app-service/overview-authentication-authorization.md) beállítások (ha engedélyezve vannak)

1. **Várakozás az újraindításra és a rendelkezésre állásra:** A swap megvárja a forrás tárolóhely minden példányát, hogy elvégezze az újraindítást, és hogy elérhető legyen a kérésekhez. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrás tárolóhelyének összes módosítását, és leállítja a műveletet.

1. **Útválasztás frissítése:** Ha a forrás tárolóhelyén lévő összes példány sikeresen befejeződik, a két bővítőhely az útválasztási szabályok váltásával hajtja végre a cserét. Ezt a lépést követően a cél tárolóhely (például az éles tárolóhely) azt az alkalmazást alkalmazza, amely korábban a forrás tárolóhelyen van bemelegítve.

1. **Ismétlési művelet:** Most, hogy a forrás tárolóhelye korábban a cél tárolóhelyen lévő előzetes swap alkalmazást használja, végezze el ugyanezt a műveletet úgy, hogy az összes beállítást alkalmazza, majd újraindítja a példányokat a forrás tárolóhelyén.

Tartsa szem előtt az alábbi szempontokat:

- A swap-művelet bármely pontján a felcserélt alkalmazások inicializálása a forrás tárolóhelyen történik. A cél tárolóhely online marad, amíg a forrás tárolóhelye elkészült, vagy a swap sikeres vagy sikertelen lesz.

- Ha egy átmeneti tárolóhelyet szeretne cserélni az üzemi tárolóhelyre, győződjön meg arról, hogy az éles tárolóhely *mindig* a cél tárolóhely. Így a swap művelet nem befolyásolja az üzemi alkalmazást.

- Az esemény-forrásokhoz és kötésekhez kapcsolódó beállításokat [üzembe helyezési tárolóhelyként](#manage-settings) kell konfigurálni, *mielőtt elkezdené a swap-eljárást*. Ha az idő előtt "Sticky"-ként jelöli meg őket, az események és a kimenetek a megfelelő példányra vannak irányítva.

## <a name="manage-settings"></a>Beállítások kezelése

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Központi telepítési beállítás létrehozása

Megadhatja a beállításokat központi telepítési beállításként, amely a "Sticky" értéket jelöli. Az alkalmazás-példány nem cseréli le a Sticky beállítást.

Ha egy tárolóhelyen létrehoz egy központi telepítési beállítást, mindenképpen hozzon létre egy egyedi értéket a swap-ben részt vevő többi tárolóhelyen. Így a beállítások értéke nem változik, a beállítások nevei konzisztensek maradnak a tárolóhelyek között. Ez a név konzisztencia biztosítja, hogy a kód ne próbáljon meg hozzáférni egy olyan beállításhoz, amely egy tárolóhelyen van definiálva, de még nem.

A következő lépésekkel hozhat létre központi telepítési beállítást:

- A Function alkalmazásban navigáljon a *tárolóhelyekhez*
- Kattintson a tárolóhely nevére
- A *platform szolgáltatásai > általános beállítások*területen kattintson a **Konfigurálás** elemre.
- Kattintson arra a beállításra, amelyet az aktuális tárolóhelyhez szeretne ragasztani
- Kattintson az **üzembe helyezési tárolóhely beállítása** jelölőnégyzetre
- Kattintson az **OK** gombra
- Ha a beállítás panel eltűnik, kattintson a **Save (Mentés** ) gombra a módosítások megtartásához

![Üzembe helyezési pont beállítása](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Környezet

Tárolóhelyek létrehozásakor a tárolóhelyek üresek. A [támogatott üzembe helyezési technológiák](./functions-deployment-technologies.md) bármelyikével üzembe helyezheti az alkalmazást egy tárolóhelyen.

## <a name="scaling"></a>Méretezés

Az összes tárolóhely az üzemi tárolóhelytel azonos számú feldolgozóra méretezhető.

- A használati csomagok esetében a tárolóhely a függvény alkalmazási skálájának megfelelően méretezhető.
- App Service csomagok esetében az alkalmazás egy rögzített számú feldolgozóra méretezhető. A bővítőhelyek ugyanazon a számú feldolgozón futnak, mint az alkalmazási csomag.

## <a name="add-a-slot"></a>Tárolóhely hozzáadása

Hozzáadhat egy tárolóhelyet a [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) -n keresztül, vagy a portálon keresztül is. A következő lépések bemutatják, hogyan hozhat létre egy új tárolóhelyet a portálon:

1. Navigáljon a Function alkalmazáshoz, és kattintson a *bővítőhelyek*melletti **plusz jelre** .

    ![Azure Functions üzembe helyezési pont hozzáadása](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Írjon be egy nevet a szövegmezőbe, majd kattintson a **Létrehozás** gombra.

    ![Név Azure Functions üzembe helyezési pont](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Tárolóhelyek cseréje

A tárolóhelyeket a [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) -n keresztül vagy a portálon keresztül lehet cserélni. A következő lépések bemutatják, hogyan lehet felcserélni a tárolóhelyeket a portálon:

1. Navigáljon a Function alkalmazáshoz
1. Kattintson a felcserélni kívánt forrás tárolóhely nevére
1. Az *Áttekintés* lapon kattintson a **Csere** gombra ![swap Azure functions üzembe helyezési pontra](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Ellenőrizze a swap konfigurációs beállításait, majd kattintson a **swap** ![swap Azure functions üzembe helyezési pontra](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

A művelet eltarthat egy kis ideig, amíg a rendszer végrehajtja a swap-műveletet.

## <a name="roll-back-a-swap"></a>Swap visszaállítása

Ha a swap hibát jelez, vagy egyszerűen vissza kívánja állítani a cserét, visszaállíthatja a kezdeti állapotot. Ha vissza szeretne térni az előre lecserélt állapotba, hajtson végre egy újabb cserét a swap megfordításához.

## <a name="remove-a-slot"></a>Tárolóhely eltávolítása

Egy tárolóhelyet a [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) -n keresztül vagy a portálon lehet eltávolítani. A következő lépések bemutatják, hogyan távolíthat el egy tárolóhelyet a portálon:

1. Navigáljon a Function app áttekintése oldalra

1. Kattintson a **Törlés** gombra

    ![Azure Functions üzembe helyezési pont hozzáadása](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>A tárolóhelyek kezelésének automatizálása

Az [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)használatával automatizálhatja a következő műveleteket egy tárolóhelyen:

- [létrehozás](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [törlés](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatikus felcserélés](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service-csomag módosítása

Ha egy App Service csomag alatt futó Function alkalmazást használ, lehetősége van módosítani egy tárolóhely mögöttes app Service-csomagot.

> [!NOTE]
> A tárolóhelyek App Service terve nem módosítható a használati tervben.

A bővítőhely app Service-csomagjának módosításához kövesse az alábbi lépéseket:

1. Nyisson meg egy tárolóhelyet

1. A *platform-szolgáltatások*területen kattintson a **minden beállítás** elemre.

    ![App Service-csomag módosítása](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Kattintson **app Service csomagra**

1. Válasszon új App Service csomagot, vagy hozzon létre egy új csomagot

1. Kattintson az **OK** gombra

    ![App Service-csomag módosítása](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Korlátozások

Azure Functions üzembe helyezési pontok a következő korlátozásokkal rendelkeznek:

- Az alkalmazás számára elérhető tárolóhelyek száma a csomagtól függ. A használati terv csak egy üzembe helyezési pont számára engedélyezett. További tárolóhelyek érhetők el a App Service csomag alatt futó alkalmazásokhoz.
- A tárolóhelyek cseréje visszaállítja a kulcsokat olyan alkalmazások számára, amelyeken a `AzureWebJobsSecretStorageType` alkalmazás beállítása `files`.
- A Linux-használati tervhez nem érhetők el tárolóhelyek.

## <a name="support-levels"></a>Támogatási szintek

Az üzembe helyezési pontok két szinten támogatottak:

- **Általánosan elérhető (GA)** : teljes mértékben támogatott és jóváhagyott éles használatra.
- **Előzetes**verzió: még nem támogatott, de a jövőben várhatóan el kell érnie a ga-állapotot.

| Operációs rendszer/üzemeltetési csomag           | Támogatási szint     |
| ------------------------- | -------------------- |
| Windows-felhasználás       | Általános elérhetőség |
| Windows Premium           | Általános elérhetőség  |
| Dedikált Windows         | Általános elérhetőség |
| Linux-felhasználás         | Nem támogatott          |
| Linux Premium             | Általános elérhetőség  |
| Linux dedikált           | Általános elérhetőség |

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezési technológiák Azure Functions](./functions-deployment-technologies.md)
