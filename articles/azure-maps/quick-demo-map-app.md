---
title: Interaktív térképes keresés az Azure Mapsszel | Microsoft Docs
description: Azure rövid útmutató – Bemutató interaktív térképes keresés indítása az Azure Mapsszel
author: walsehgal
ms.author: v-musehg
ms.date: 10/26/2018
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d2aa3385dd7556cfc169b42b36d94a3e1d9ee3dd
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209972"
---
# <a name="launch-an-interactive-search-map-using-azure-maps"></a>Interaktív térképes keresés indítása az Azure Mapsszel

Ez a cikk bemutatja az Azure Maps képességeit, amelyekkel interaktív keresési felhasználói élményt nyújtó térkép készíthető. Ismerteti, hogyan hozhat létre saját Maps-fiókot, szerezheti be a fiók kulcsát és használhatja azt a bemutató webalkalmazásban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Fiók létrehozása és kulcs beszerzése

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a Marketplace-en* mezőbe írja be a következőt: **Maps**.
3. Az *Eredmények* részben válassza a **Maps** lehetőséget. Kattintson a térkép alatt megjelenő **Létrehozás** gombra.
4. A **Maps-fiók létrehozása** lapon adja meg a következő értékeket:
    - Az új fiók *neve*.
    - A fiókhoz használni kívánt *előfizetés*.
    - A fiókhoz tartozó *erőforráscsoport*. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    - Válassza ki az *erőforráscsoport helyét*.
    - Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    - Végül kattintson a **Létrehozás** gombra.

    ![Maps-fiók létrehozása a portálon](./media/quick-demo-map-app/create-account.png)

5. Miután sikeresen létrehozta a fiókot, nyissa meg és keresse meg a fiókmenü beállítások szakaszát. Kattintson a **Kulcsok** elemre az Azure Maps-fiók elsődleges és másodlagos kulcsainak megtekintéséhez. Másolja az **Elsődleges kulcs** értékét a vágólapra, mert a következő szakaszban használnia kell majd.

## <a name="download-the-application"></a>Az alkalmazás letöltése

1. Töltse le vagy másolja ki az [interactiveSearch.html](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/interactiveSearch.html) fájl tartalmát.
2. Mentse a fájl tartalmát helyileg **AzureMapDemo.html** néven, és nyissa meg egy szövegszerkesztőben.
3. Keresse meg benne az `<insert-key>` sztringet, és cserélje le az előző szakaszban kimásolt **Elsődleges kulcs** értékére.

## <a name="launch-the-application"></a>Az alkalmazás indítása

1. Nyissa meg az **AzureMapDemo.html** fájlt egy tetszőleges böngészőben.
2. Vegye szemügyre a megjelenő térképen Los Angeles városát. Nagyítson és kicsinyítsen, ekkor a térkép automatikusan több vagy kevesebb információt jelenít meg a nagyítás mértékétől függően. 
3. Módosítsa a térkép alapértelmezett középpontját. Az **AzureMapDemo.html** fájlban keresse meg a **center** nevű változót. A változó szélesség–hosszúság értékpárt cserélje le a következő új értékre: **[-74.0060, 40.7128]**. Mentse a fájlt, és frissítse a böngészőt.
4. Próbálja ki az interaktív keresést. A bemutató webalkalmazás bal felső sarkában lévő keresőmezőben keressen rá a **restaurants** (éttermek) kifejezésre.
5. Húzza az egérkurzort a keresőmező alatt megjelenő listában egy cím vagy hely fölé, és figyelje meg, hogy a térképen az adott helyhez tartozó jelölő a helyre vonatkozó információkat jelenít meg. A magánvállalkozások adatainak védelme érdekében az itt látható nevek és címek nem valósak.

    ![Interaktív keresés webalkalmazás](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok részletesen leírják, hogyan használhatja és konfigurálhatja fiókjával a Maps szolgáltatást. Ha azt tervezi, hogy ezekkel az oktatóanyaggal folytatja, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be a böngészőt, amelyben az **AzureMapDemo.html** webalkalmazást megnyitotta.
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, majd válassza ki a saját Maps-fiókját. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a saját Maps-fiókját, és elindított egy bemutató alkalmazást. Ha szeretné megtudni, hogyan hozhatja létre a saját alkalmazását a Maps API-k használatával, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Hasznos helyek keresése a Maps szolgáltatással](./tutorial-search-location.md)

További példakódokért és az interaktív kódolási élmény bemutatásáért tekintse át az alábbi útmutatókat.

> [!div class="nextstepaction"]
> [Címek keresése az Azure Maps REST API-k használatával](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Az Azure Maps térkép vezérlőelemek használata](./how-to-use-map-control.md)
