---
title: 'Rövid útmutató: Interaktív térképkeresés az Azure Maps segítségével | Microsoft Azure Maps'
description: Ismerje meg, hogyan hozhat létre bemutató webalkalmazást interaktív térképkereséshez a Microsoft Azure Maps webes SDK használatával.
author: philmea
ms.author: philmea
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 13dc5f6c7175e1ed568199abcbaa4c5d9a20fa7f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80334423"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Rövid útmutató: Interaktív keresési térkép létrehozása az Azure Maps használatával

Ez a cikk bemutatja az Azure Maps képességeit, amelyekkel interaktív keresési felhasználói élményt nyújtó térkép készíthető. Végigvezeti az alábbi alapvető lépéseken:
* Hozzon létre saját Azure Maps-fiókot.
* Szerezd meg a fiók kulcsot használni a demo webes alkalmazás.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps-fiók létrehozása

Hozzon létre egy új Maps-fiókot az alábbi lépésekkel:

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a Marketplace-en* mezőbe írja be a következőt: **Maps**.
3. Az *Eredmények* részben válassza a **Maps** lehetőséget. Kattintson a térkép alatt megjelenő **Létrehozás** gombra.
4. A **Maps-fiók létrehozása** lapon adja meg a következő értékeket:
    * A fiókhoz használni kívánt *előfizetés*.
    * A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * Az új fiók *neve*.
    * A fiók *díjszabási szintje.*
    * Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    * Kattintson a **Létrehozás** gombra.

![Maps-fiók létrehozása a portálon](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

A Maps-fiók sikeres létrehozását követően kérje le azt a kulcsot, amely lehetővé teszi a Maps API-k lekérdezését. Azt javasoljuk, hogy az Azure Maps-szolgáltatások hívásakor használja a fiók elsődleges kulcsát előfizetési kulcsként.

1. Nyissa meg a Maps-fiókot a portálon.
2. A beállítások csoportban válassza a **Hitelesítés**lehetőséget.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse a helyi gépre, hogy később felhasználhassa ebben az oktatóanyagban.

![Elsődleges kulcs Azure Maps-kulcs beszerezni e-kulcs átaz Azure Portalon](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Az alkalmazás letöltése

1. Nyissa meg az [interaktívSearch.html fájlt,](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) és kattintson rá a GitHub felhasználói felületén lévő tartalom megtekintéséhez. Kattintson a jobb gombbal a **Raw** gombra, és másolja a fájl tartalmát, vagy a "Mentés másként" parancsot a fájl letöltéséhez.
2. Mentse a fájl tartalmát helyileg **AzureMapDemo.html néven.** Nyissa meg egy szövegszerkesztőben.
3. Keresse meg `<Your Azure Maps Key>`a karakterláncot. Cserélje le az előző szakasz **elsődleges kulcsértékére.**

## <a name="open-the-application"></a>Az alkalmazás megnyitása

1. Nyissa meg az **AzureMapDemo.html** fájlt egy tetszőleges böngészőben.
2. Figyeljük meg a térképlátható a City of Los Angeles. Nagyítson és kicsinyítsen, ekkor a térkép automatikusan több vagy kevesebb információt jelenít meg a nagyítás mértékétől függően. 
3. Módosítsa a térkép alapértelmezett középpontját. Az **AzureMapDemo.html** fájlban keresse meg a **center** nevű változót. A változó szélesség–hosszúság értékpárt cserélje le a következő új értékre: **[-74.0060, 40.7128]**. Mentse a fájlt, és frissítse a böngészőt.
4. Próbálja ki az interaktív keresést. A bemutató webalkalmazás bal felső sarkában található keresőmezőben keressen **éttermeket.**
5. Vigye az egeret a keresőmező alatt megjelenő címek és helyek listájára. Figyelje meg, hogy a megfelelő pin a térképen bukkan fel információt az adott helyen. A magánvállalkozások adatainak védelme érdekében az itt látható nevek és címek nem valósak.

    ![Interaktív térképkereső webes alkalmazás](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok részletesen ismertetik, hogyan használhatja és konfigurálhatja az Azure Mapset a fiókjával. Ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat, ha folytatni kívánja az oktatóanyagokat. Ha nem tervezi a folytatást, tegye meg az alábbi lépéseket az erőforrások karbantartásához:

1. Zárja be az **AzureMapDemo.html** webalkalmazást futtató böngészőt.
2. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás lehetőséget.** Ezután válassza ki az Azure Maps-fiókot. A **Minden erőforrás** panel tetején válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta az Azure Maps-fiókot, és létrehozott egy bemutató alkalmazást. Az Azure Mapsről az alábbi oktatóanyagokból megtudhatja:

> [!div class="nextstepaction"]
> [Közeli látnivalók keresése az Azure Maps használatával](tutorial-search-location.md)

További kódpéldákat és interaktív kódolási élményt az alábbi útmutatókban talál:

> [!div class="nextstepaction"]
> [Cím keresése az Azure Maps keresési szolgáltatásával](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Az Azure Maps map vezérlő használata](how-to-use-map-control.md)
