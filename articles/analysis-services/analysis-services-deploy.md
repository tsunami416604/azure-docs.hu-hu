---
title: Modell üzembe helyezése az Azure Analysis Services szolgáltatásban a Visual Studio használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan telepíthet táblázatos modellt egy Azure Analysis Services-kiszolgálóra a Visual Studio használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572883"
---
# <a name="deploy-a-model-from-visual-studio"></a>Modell üzembe helyezése a Visual Studióból

Miután létrehozott egy kiszolgálót az Azure-előfizetésében, készen áll a táblázatos modelladatbázis üzembe helyezésére. A Visual Studio és az Analysis Services-projektek segítségével létrehozhat és üzembe helyezhet egy táblázatos modellprojektet, amelyen dolgozik. 

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* **Analysis Services-kiszolgáló** az Azure-ban. További információkért lásd [az Azure Analysis Services-kiszolgáló létrehozásával kapcsolatos](analysis-services-create-server.md) témakört.
* **Táblázatos modellprojekt** a Visual Studio-ban vagy egy meglévő táblázatos modell 1200-as vagy magasabb kompatibilitási szinten. Korábban még nem hozott létre egyet sem? Próbálkozzon [az Adventure Works internetes értékesítési modell központi telepítésének útmutatójával](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Helyszíni átjáró** – Ha a szervezete hálózatában egy vagy több helyszíni adatforrás található, telepítenie kell egy [helyszíni adatátjárót](analysis-services-gateway.md). Az átjáróra azért van szükség, hogy a felhőben található kiszolgálója csatlakozni tudjon a helyszíni adatforrásaihoz a modellben található adatok feldolgozásához és frissítéséhez.

> [!TIP]
> Az üzembe helyezés előtt győződjön meg róla, hogy a tábláiban található adatok feldolgozhatók. A Visual Studio alkalmazásban kattintson a > **Modellfolyamat** > **folyamat összes parancsára.** **Model** Ha a feldolgozás meghiúsul, nem fog sikerülni a telepítés.
> 
> 

## <a name="get-the-server-name"></a>A kiszolgáló nevének beszereznie

Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Üzembe helyezés a Visual Studióból

1. A Visual Studio > **Solution Explorer**programban kattintson a jobb gombbal a projekt **tulajdonságai**>. Ezután a **Deployment** > **Server programban** illessze be a kiszolgáló nevét.   
   
    ![Az üzembehelyezési kiszolgáló tulajdonságához illessze be a kiszolgáló nevét.](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. A **Megoldáskezelőben** kattintson a jobb gombbal a **Tulajdonságok** elemre, majd kattintson az **Üzembe helyezés** lehetőségre. Lehet, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-ba.
   
    ![Üzembe helyezés kiszolgálóra](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Az üzembe helyezés állapota látható a kimeneti ablakban és az Üzembe helyezés területen is.
   
    ![Üzembe helyezés állapota](./media/analysis-services-deploy/aas-deploy-status.png)

Ennyi az egész!


## <a name="troubleshooting"></a>Hibaelhárítás

Ha a központi telepítés nem sikerül a metaadatok telepítésekor, annak valószínűleg az az oka, hogy a Visual Studio nem tudott csatlakozni a kiszolgálóhoz. Győződjön meg róla, hogy tud csatlakozni a kiszolgálóhoz az SSMS használatával. Ezt követően ellenőrizze, hogy helyes a projekt Üzembehelyezési kiszolgáló tulajdonsága.

Ha a telepítés egy táblán sikertelen, annak valószínűleg az az oka, hogy a kiszolgálója nem tudott csatlakozni egy adatforráshoz. Ha a szervezete hálózatában helyszíni adatforrás található, mindenképp telepítsen egy [helyszíni adatátjárót](analysis-services-gateway.md).

## <a name="next-steps"></a>További lépések

Miután sikeresen telepítette a kiszolgálóra a táblázatos modellt, azonnal csatlakozhat is hozzá. Az [SQL Server Management Studio (SSMS) segítségével csatlakozhat](analysis-services-manage.md) hozzá a kezeléséhez. Továbbá [csatlakozhat hozzá ügyféleszközzel](analysis-services-connect.md) is, például Power BI, Power BI Desktop vagy Excel segítségével, és megkezdheti a jelentések létrehozását.

