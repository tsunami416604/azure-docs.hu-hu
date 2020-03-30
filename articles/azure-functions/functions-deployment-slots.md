---
title: Az Azure Functions központi telepítési bővítőhelyei
description: Ismerje meg a telepítési tárolóhelyek létrehozását és használatát az Azure Functions segítségével
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769217"
---
# <a name="azure-functions-deployment-slots"></a>Az Azure Functions központi telepítési bővítőhelyei

Az Azure Functions központi telepítési tárolóhelyekkel lehetővé teszi, hogy a függvényalkalmazás különböző példányokat futtatjon "bővítőhelynek". A tárolóhelyek különböző környezetek nyilvánosan elérhető végponton keresztül elérhető. Egy alkalmazáspéldány mindig le van képezve az éles tárolóhelyre, és igény szerint cserélheti a tárolóhelyhez rendelt példányokat. Az Apps Service-csomag alatt futó függvényalkalmazások több bővítőhellyel rendelkezhetnek, míg a Felhasználási terv alatt csak egy tárolóhely engedélyezett.

A következők ben azt tükrözik, hogy a cserehelyek hogyan befolyásolják a funkciókat:

- A forgalom átirányítása zökkenőmentes; a csere miatt nem dobnak el kéréseket.
- Ha egy függvény fut a csere közben, a végrehajtás folytatódik, és az azt követő eseményindítók a felcserélt alkalmazáspéldányhoz leszirányítva.

> [!NOTE]
> A bővítőhelyek jelenleg nem érhetők el a Linux-felhasználási csomaghoz.

## <a name="why-use-slots"></a>Miért érdemes résidőket használni?

A központi telepítési tárolóhelyek használatának számos előnye van. A következő forgatókönyvek a tárolóhelyek gyakori felhasználási céljait ismertetik:

- **Különböző környezetek különböző célokra:** A különböző bővítőhelyek használatával megkülönböztetheti az alkalmazáspéldányokat, mielőtt éles környezetre vagy átmeneti tárolóhelyre cserélne.
- **Előmelegítés:** A közvetlenül éles környezetben való üzembe helyezés lehetővé teszi az alkalmazás felmelegedését, mielőtt éleskörnyezetbe menne. Emellett a tárolóhelyek használatával csökkenti a http-aktivált számítási feladatok késését. A példányok felmelegedése az üzembe helyezés előtt bemelegedik, ami csökkenti az újonnan üzembe helyezett függvények hidegindítását.
- **Egyszerű tartalék:** A termeléssel való csere után a korábban előkészített alkalmazással rendelkező tárolóhely már rendelkezik az előző éles alkalmazással. Ha a változások a termelési tárolóhelyre cserélve nem a várt módon, azonnal visszafordíthatja a swapot, hogy visszakapja az "utolsó ismert jó példányt".

## <a name="swap-operations"></a>Csereműveletek

A csere során az egyik tárolóhely a forrás, a másik pedig a cél. A forrástároló hely rendelkezik a céltárolóra alkalmazott alkalmazás példányával. A következő lépések biztosítják, hogy a céltárolóhely nem tapasztal állásidőt a csere során:

1. **Beállítások alkalmazása:** A céltárolóhely beállításai a forrástároló összes példányára vonatkoznak. Például a termelési beállítások at az átmeneti példányra alkalmazza. Az alkalmazott beállítások a következő kategóriákat tartalmazzák:
    - [Slot-specifikus](#manage-settings) alkalmazásbeállítások és kapcsolati karakterláncok (ha vannak ilyenek)
    - [Folyamatos üzembe helyezési](../app-service/deploy-continuous-deployment.md) beállítások (ha engedélyezve van)
    - [Az App Service hitelesítési](../app-service/overview-authentication-authorization.md) beállításai (ha engedélyezve van)

1. **Várja meg az újraindításokat és a rendelkezésre állást:** A swap vár minden példány a forrás tárolóhely újraindítása, és a kérelmek elérhetővé. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrástárolóhely összes módosítását, és leállítja a műveletet.

1. **Útválasztási terv frissítése:** Ha a forrástárolón lévő összes példány sikeresen bemelegedett, a két bővítőhely az útválasztási szabályok közötti váltással befejezi a felcserélést. Ezt a lépést követően a céltárolóhely (például az éles tárolóhely) rendelkezik az alkalmazás, amely korábban felmelegedett a forrástárolóban.

1. **Művelet ismétlése:** Most, hogy a forrástároló valameddig rendelkezik a csere előtti alkalmazással korábban a céltárolóban, hajtsa végre ugyanazt a műveletet az összes beállítás alkalmazásával, és újraindítja a forrástárolóhely példányait.

Tartsa szem előtt az alábbi szempontokat:

- A csereművelet bármely pontján a kicserélt alkalmazások inicializálása a forrástárolóhelyen történik. A céltároló online marad a forrástároló hely előkészítése közben, függetlenül attól, hogy a swap sikeres vagy sikertelen lesz.The target slot remains online while the source slot is being prepared, whether the swap succeeds or fails.

- Átmeneti tárolóhely az éles tárolóhellyel való csere, győződjön meg arról, hogy az éles tárolóhely *mindig* a céltároló. Ily módon a csereművelet nincs hatással az éles alkalmazás.

- A swap kezdeményezése előtt az eseményforrásokhoz és kötésekhez kapcsolódó beállításokat [telepítési helybeállításokként](#manage-settings) kell konfigurálni. *before you initiate a swap* Jelölés őket "ragadós" idő előtt biztosítja az események és kimenetek irányul a megfelelő példányt.

## <a name="manage-settings"></a>Beállítások kezelése

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Központi telepítési beállítás létrehozása

A beállításokat telepítési beállításként jelölheti meg, ami "ragadóssá" teszi. A ragadós beállítás nem cseréli fel az alkalmazáspéldányt.

Ha egy központi telepítési beállítást hoz létre egy tárolóhelyen, győződjön meg arról, hogy ugyanazt a beállítást hozza létre egy egyedi értékkel a cserebármely más tárolóhelyen. Így, bár a beállítás értéke nem változik, a beállítás nevek konzisztensek maradnak a tárolóhelyek között. Ez a névkonzisztencia biztosítja, hogy a kód ne próbáljon meg hozzáférni egy olyan beállításhoz, amely az egyik tárolóhelyen van definiálva, de egy másikban nem.

A telepítési beállítások létrehozásához kövesse az alábbi lépéseket:

- Navigálás a *bővítőhelyek* re a függvényalkalmazásban
- Kattintson a bővítőhely nevére
- A *Platform jellemzők > általános beállítások csoportban*kattintson a **Konfiguráció** gombra
- Kattintson a beállítás nevét szeretne ragaszkodni az aktuális slot
- Kattintson a **Központi telepítéshely beállításjelölőnégyzetére**
- Kattintson **az OK gombra**
- Ha a panel beállítása eltűnik, kattintson a **Mentés** gombra a módosítások megtartásához

![Központi telepítési bővítőhely beállítása](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Környezet

A bővítőhelyek üresek, amikor létrehoz egy helyet. A támogatott központi [telepítési technológiák](./functions-deployment-technologies.md) bármelyikével telepítheti az alkalmazást egy tárolóhelyre.

## <a name="scaling"></a>Méretezés

Minden bővítőhely a termelési tárolóhelyekkel megegyező számú dolgozóra méretezhető.

- A felhasználási tervek, a résidő skálázása a függvény alkalmazás méretezése.
- Az App Service-csomagok esetében az alkalmazás rögzített számú dolgozóra méretezhető. A tárolóhelyek ugyanannyi dolgozón futnak, mint az alkalmazáscsomag.

## <a name="add-a-slot"></a>Pont felvétele

Hozzáadhat egy bővítőhelyet a [CLI-n](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) vagy a portálon keresztül. A következő lépések bemutatják, hogyan hozhat létre új bővítőhelyet a portálon:

1. Keresse meg a függvényalkalmazást, és kattintson a **plusz jel** mellett *Slots*.

    ![Azure Functions telepítési helyének hozzáadása](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Írjon be egy nevet a szövegmezőbe, és nyomja le a **Létrehozás** gombot.

    ![Név Az Azure Functions telepítési hely](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Bővítőhelyek cseréje

A bővítőhelyeket a [CLI-n](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) vagy a portálon keresztül cserélheti. A következő lépések bemutatják, hogyan lehet résidőket cserélni a portálon:

1. Navigálás a függvényalkalmazásra
1. Kattintson a cserélni kívánt forrásbővítőhely nevére
1. Az *Áttekintés* lapon kattintson az ![Azure Functions **felcserélése** telepítési hely ének felcserélése gombra.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Ellenőrizze a csere konfigurációs beállításait, és kattintson a **Swap** ![Swap Azure Functions telepítési helyére.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

A művelet eltarthat egy ideig, amíg a csereművelet végrehajtása folyamatban van.

## <a name="roll-back-a-swap"></a>Csere visszaállítása

Ha egy swap hibát eredményez, vagy egyszerűen csak vissza szeretné vonni a swapot, visszaállíthatja a kezdeti állapotot. Az előre cserélt állapotba való visszatéréshez tegyen egy másik swapot a swap visszafordításához.

## <a name="remove-a-slot"></a>Bővítőhely eltávolítása

A bővítőhelyet eltávolíthatja a [CLI-n](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) vagy a portálon keresztül. A következő lépések bemutatják, hogyan távolíthatja el a tárolóhelyet a portálon:

1. Keresse meg a függvényalkalmazást – áttekintés

1. Kattintson a **Törlés** gombra

    ![Azure Functions telepítési helyének hozzáadása](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>A bővítőhely-kezelés automatizálása

Az [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)használatával automatizálhatja a következő műveleteket egy tárolóhelyhez:

- [Létrehozása](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [Töröl](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [lista](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatikus csere](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Az App Service-csomag módosítása

Egy függvényalkalmazás, amely fut egy App Service-csomag alatt, lehetősége van arra, hogy módosítsa az alapul szolgáló App Service-csomag egy tárolóhely.

> [!NOTE]
> A felhasználási csomag alatt nem módosíthatja a tárolóhely App Service-csomagját.

A következő lépésekkel módosíthatja a tárolóhely App Service-csomagját:

1. Navigálás egy tárolóhelyre

1. A *Platform szolgáltatásai*csoportban kattintson a Minden beállítás **elemre.**

    ![Alkalmazásszolgáltatási csomag módosítása](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Kattintson az **App Service-csomagra**

1. Új App Service-csomag kiválasztása vagy új csomag létrehozása

1. Kattintson **az OK gombra**

    ![Alkalmazásszolgáltatási csomag módosítása](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Korlátozások

Az Azure Functions központi telepítési bővítőhelyekkel a következő korlátozások vannak:

- Az alkalmazás számára elérhető tárolóhelyek száma a csomagtól függ. A felhasználási terv csak egy központi telepítési hely engedélyezett. További bővítőhelyek érhetők el az App Service-csomag alatt futó alkalmazások hoz létre.
- A tárolóhely lecserélése visszaállítja a billentyűket azon alkalmazások esetében, amelyek `AzureWebJobsSecretStorageType` alkalmazásbeállítása megegyezik a beállítással. `files`
- A bővítőhelyek nem érhetők el a Linux-felhasználási csomaghoz.

## <a name="support-levels"></a>Támogatási szintek

A telepítési helyek támogatásának két szintje van:

- **Általános elérhetőség (GA)**: Teljes mértékben támogatott és termelési használatra jóváhagyott.
- **Előzetes verzió:** Még nem támogatott, de várhatóan a jövőben eléri a GA-állapotot.

| OS/Hosting terv           | A támogatás szintje     |
| ------------------------- | -------------------- |
| Windows-felhasználás       | Általános elérhetőség |
| Windows Prémium rendszer           | Általános elérhetőség  |
| Dedikált Windows         | Általános elérhetőség |
| Linux-fogyasztás         | Nem támogatott          |
| Linux Prémium             | Általános elérhetőség  |
| Linux dedikált           | Általános elérhetőség |

## <a name="next-steps"></a>További lépések

- [Telepítési technológiák az Azure Functionsben](./functions-deployment-technologies.md)
