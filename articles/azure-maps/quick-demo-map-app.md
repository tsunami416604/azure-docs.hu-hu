---
title: Interaktív térképes keresés a Azure Mapskal | Microsoft Docs
description: Azure rövid útmutató – bemutató interaktív térképes keresés létrehozása Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 092da53c3179d939ebf0472219338b8d8392fda6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554286"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>Interaktív keresési Térkép létrehozása Azure Maps használatával

Ez a cikk bemutatja az Azure Maps képességeit, amelyekkel interaktív keresési felhasználói élményt nyújtó térkép készíthető. Végigvezeti a következő alapvető lépéseken:
* Hozzon létre egy saját Azure Maps fiókot.
* Kérje meg a fiók kulcsát a bemutató webalkalmazásban való használathoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Fiók létrehozása és kulcs beszerzése

1. Az [Azure Portal](https://portal.azure.com)bal felső sarkában válassza az **erőforrás létrehozása**lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a **Maps**kifejezést.
3. Az **Eredmények** részben válassza a **Maps** lehetőséget. Válassza a **Létrehozás** gombot, amely megjelenik a Térkép alatt.
4. A **Azure Maps fiók létrehozása** lapon adja meg a következő értékeket:
   - Az új fiók **neve**.
   - A fiókhoz használni kívánt **előfizetés**.
   - A fiókhoz tartozó **erőforráscsoport**. Dönthet úgy, hogy **újat hoz létre** , vagy **meglévő erőforráscsoportot használ** .
   - Válassza ki az Ön által választott **díjszabási szintet** .
   - Olvassa el a **licenc** -és **adatvédelmi nyilatkozatot**. Jelölje be a jelölőnégyzetet a feltételek elfogadásához.
   - Végül kattintson a **Létrehozás** gombra.

     ![Azure Maps-fiók létrehozása a portálon](./media/quick-demo-map-app/create-account.png)

5. A fiók sikeres létrehozása után nyissa meg, és keresse meg a fiók menü Beállítások szakaszát. Válassza a **hitelesítés** lehetőséget a Azure Maps fiók elsődleges és másodlagos kulcsainak megtekintéséhez. Másolja az **Elsődleges kulcs** értékét a vágólapra, mert a következő szakaszban használnia kell majd.

## <a name="download-the-application"></a>Az alkalmazás letöltése

1. Töltse le vagy másolja ki az [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) fájl tartalmát.
2. Mentse a fájl tartalmát helyileg **AzureMapDemo. html**néven. Nyissa meg egy szövegszerkesztőben.
3. Keresse meg a `<Your Azure Maps Key>` karakterláncot. Cserélje le az elemet az előző szakasz **elsődleges kulcs** értékére.

## <a name="open-the-application"></a>Az alkalmazás megnyitása

1. Nyissa meg az **AzureMapDemo.html** fájlt egy tetszőleges böngészőben.
2. Figyelje meg a Los Angeles városának látható térképét. Nagyítson és kicsinyítsen, ekkor a térkép automatikusan több vagy kevesebb információt jelenít meg a nagyítás mértékétől függően. 
3. Módosítsa a térkép alapértelmezett középpontját. Az **AzureMapDemo.html** fájlban keresse meg a **center** nevű változót. A változó szélesség–hosszúság értékpárt cserélje le a következő új értékre: **[-74.0060, 40.7128]** . Mentse a fájlt, és frissítse a böngészőt.
4. Próbálja ki az interaktív keresést. A bemutató webalkalmazás bal felső sarkában található keresőmezőbe keressen az **éttermek**kifejezésre.
5. Vigye az egérmutatót a keresési mező alatt megjelenő címek és helyszínek listájára. Figyelje meg, hogy a térképen a megfelelő PIN-kód jelenik meg. A magánvállalkozások adatainak védelme érdekében az itt látható nevek és címek nem valósak.

    ![Interaktív keresési webalkalmazás](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok részletesen ismertetik, hogyan használhatók és konfigurálhatók Azure Maps a fiókjával. Ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat, ha azt tervezi, hogy folytatja az oktatóanyagokat. Ha nem folytatja a folytatást, hajtsa végre az alábbi lépéseket az erőforrások törléséhez:

1. A **AzureMapDemo. html** -webalkalmazást futtató böngésző bezárásához.
2. A Azure Portal bal oldali menüjében válassza az **összes erőforrás**lehetőséget. Ezután válassza ki a Azure Maps fiókját. A **minden erőforrás** panel tetején válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta Azure Maps-fiókját, és létrehozott egy bemutató alkalmazást. Tekintse meg a következő oktatóanyagokat a Azure Maps megismeréséhez:

> [!div class="nextstepaction"]
> [Közeli érdekes helyek keresése Azure Maps használatával](tutorial-search-location.md)

További példákat és egy interaktív kódolási élményt a következő útmutatók tartalmaznak:

> [!div class="nextstepaction"]
> [Címek keresése a Azure Maps Search szolgáltatás használatával](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Használja a Azure Maps térképkezelés](how-to-use-map-control.md)
