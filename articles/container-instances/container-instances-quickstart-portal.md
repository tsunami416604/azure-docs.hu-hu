---
title: "Gyors üzembe helyezés – az első Azure tároló példányok tároló létrehozása az Azure portállal"
description: "Az Azure Container Instances üzembe helyezése és az első lépések"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0179107ece1e150246ab40836783d810425be3ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Az első tároló létrehozása az Azure Container Instances szolgáltatásban

Az Azure Container Instances segítségével egyszerűen hozhat létre és felügyelhet tárolókat az Azure-ban. A gyors üzembe helyezés a tároló létrehozása az Azure-ban, és tegye elérhetővé azt az interneten, egy nyilvános IP-címmel. Ez a művelet az Azure portál használatával. Néhány kattintással jelenik meg ez a böngészőben:

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-container-instance"></a>Egy tároló példányának létrehozása

Válassza ki a **új** > **tárolók** > **Azure tároló példányok (előzetes verzió)**.

![Új tároló példányt az Azure portálon létrehozásának megkezdése][aci-portal-01]

Adja meg a következő értékeket a **Tárolónév**, **tároló kép**, és **erőforráscsoport** szövegmezőket. A többi érték hagyja meg az alapértelmezett beállításokat, majd kattintson az **OK**.

* Tároló neve:`mycontainer`
* Tároló lemezképet:`microsoft/aci-helloworld`
* Erőforráscsoport:`myResourceGroup`

![Új tároló példány alapvető beállítások konfigurálása az Azure-portálon][aci-portal-03]

Windows- és Linux-tárolókon hozhat létre Azure tároló példányát. A gyors üzembe helyezés, az alapértelmezett hagyja nyitva **Linux** mivel azt egy Linux-alapú tárolót adott (`microsoft/aci-helloworld`) az előző lépésben.

Hagyja meg az egyéb beállításokat a **konfigurációs** az alapértelmezett beállításokat, majd kattintson **OK** a konfiguráció érvényesítése céljából.

![Egy új tároló-példány konfigurálása az Azure-portálon][aci-portal-04]

Az érvényesítés befejezését követően a tároló beállítások összefoglalása jelennek meg. Válassza ki **OK** számára a tároló központi telepítési kérelmét.

![Beállításainak összegzése egy új tároló példányát az Azure portálon][aci-portal-05]

Amikor elindul a központi telepítés, egy csempe, amely jelzi, a telepítés előrehaladását a portál irányítópultján helyezkedik el. Telepítés után a csempe van frissül, hogy az új **mycontainer-myc1** a tárolócsoport.

![Azure portál egy új tároló példányának létrehozásának folyamata][aci-portal-08]

Válassza ki a **mycontainer-myc1** tároló csoport tulajdonságainak megjelenítéséhez a tárolócsoport. Vegye figyelembe a **IP-cím** tároló csoport, valamint a **állapot** a tároló.

![Az Azure-portálon a tároló csoport – áttekintés][aci-portal-06]

Ha áthelyezi a tároló a **futtató** állapot, keresse meg az IP-cím, az alkalmazást az új tároló megjelenítése az előző lépésben feljegyzett.

![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, a létrehozott Azure-tároló példánya egy nyilvános Docker központi tárházban kép. Ha azt szeretné, megpróbálhatja felépíteni a tároló saját magának, és központilag telepítenie kell az Azure-tároló példányok az Azure-tároló beállításjegyzékkel, továbbra is az Azure-tároló példányok oktatóanyag.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](./container-instances-tutorial-prepare-app.md)