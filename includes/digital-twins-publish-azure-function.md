---
author: baanders
description: fájl belefoglalása az Azure-függvények Visual studióból való közzétételének folyamatához
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701159"
---
Ha közzé szeretné tenni a projektet egy Azure-beli Function alkalmazásban, kattintson a jobb gombbal *megoldáskezelő* a projektre, és válassza a **Közzététel** lehetőséget.

> [!IMPORTANT] 
> Az Azure-beli Function-alkalmazásokban az előfizetése további díjakat is felszámít az Azure Digital ikrektől függetlenül.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="A Visual Studio képernyőképe, amely a jobb gombbal válassza a megoldás menüt. A közzététel ki van emelve a menüben.":::

Az alábbi *közzétételi* oldalon hagyja meg az **Azure** alapértelmezett céljának kijelölését, és kattintson a *Tovább gombra*. 

Egy adott célpont esetében válassza az **Azure függvényalkalmazás (Windows)** lehetőséget, és kattintson a *Tovább gombra*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Az Azure függvényalkalmazás (Windows) az adott cél oldalon van kiválasztva.":::

A *functions példány* lapon válassza ki az előfizetését. A mezőnek fel kell töltenie egy mezőt az előfizetésében található *erőforráscsoportok* alapján.

Válassza ki a példánya erőforráscsoportot, és kattintson *+* az új Azure-függvény létrehozásához.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Új függvény létrehozásához a + gomb jelenik meg a functions példány lapon.":::

A *függvényalkalmazás (Windows) – új ablak létrehozása* ablakban töltse ki a mezőket a következő módon:
* A **Name (név** ) annak a használati tervnek a neve, amelyet az Azure a Azure functions alkalmazás üzemeltetéséhez fog használni. Ez lesz a függvény alkalmazásának neve is, amely a tényleges függvényt tárolja. Megadhatja a saját egyedi értékét, vagy meghagyhatja az alapértelmezett javaslatot.
* Győződjön meg arról, hogy az **előfizetés** megfelel a használni kívánt előfizetésnek 
* Győződjön meg arról, hogy az **erőforráscsoport** a használni kívánt erőforráscsoporthoz van
* A **csomag típusának** elhagyása *felhasználásként*
* Válassza ki azt a **helyet** , amely megfelel az erőforráscsoport helyének.
* Hozzon létre egy új **Azure Storage** -erőforrást az *új.* .. hivatkozás használatával. Állítsa be úgy a helyet, hogy egyezzen az erőforráscsoporthoz, használja a többi alapértelmezett értéket, és nyomja meg az "OK" gombot.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Az új Function-alkalmazás részletei kitöltése folyamatban van, beleértve a nevet, az előfizetést, az erőforráscsoportot, a csomag típusát, a helyet és az Azure Storage-t.":::

Ezután kattintson a **Létrehozás** elemre.

Ekkor vissza kell térnie a *functions-példány* lapra, ahol az új Function alkalmazás már látható az erőforráscsoport alatt. Nyomja meg a *Finish (Befejezés*).

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure-függvény közzététele a Visual Studióban: functions instance (a Function app után)":::

A Visual Studio fő ablakában megnyíló *Közzététel* ablaktáblán győződjön meg arról, hogy az összes információ helyesnek tűnik, majd válassza a **Közzététel** lehetőséget.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="A Visual Studio képernyőképe az Azure-függvény közzététele párbeszédpanelen. Megjelenik az új Function alkalmazás a Function apps listájában, és van egy Befejezés gomb.":::

> [!NOTE]
> Ha a következőhöz hasonló előugró ablak jelenik meg: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="képernyőkép a Visual Studio előugró ablakban a közzétételi hitelesítő adatok nevű ablakban. Tartalmaz egy felhasználónevet és egy jelszót, valamint egy gombot, amely megkísérli beolvasni a hitelesítő adatokat az Azure-ból." border="false":::
> Válassza a **kísérlet a hitelesítő adatok lekérése az Azure-ból és a** **Mentés** lehetőséget.
>
> Ha a *functions verziójának frissítése az Azure* -ban vagy a *functions Runtime verziója nem egyezik az Azure-ban futó verzióval*:
>
> Az utasításokat követve frissítsen a legújabb Azure Functions futtatókörnyezet-verzióra. Ez a probléma akkor fordulhat elő, ha a Visual Studio egy régebbi verzióját használja.

Ahhoz, hogy a Function alkalmazás hozzáférhessen az Azure digitális Twins-hoz, rendelkeznie kell egy rendszer által felügyelt identitással, és engedéllyel kell rendelkeznie az Azure Digital Twins-példányhoz való hozzáféréshez. Ezt követően állítsa be a következőt.
