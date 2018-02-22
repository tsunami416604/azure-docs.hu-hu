---
title: "Üzembe helyezés az Azure Analysis Servicesben az SSDT-vel | Microsoft Docs"
description: "Megismerheti, hogyan helyezhet üzembe egy táblázatos modellt az Azure Analysis Servicesre az SSDT-vel."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: f356b5d8e2b047add41873ab2676bd46db8d1fd2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-a-model-from-ssdt"></a>Modell üzembe helyezése SSDT-ről
Miután létrehozott egy kiszolgálót az Azure-előfizetésében, készen áll a táblázatos modelladatbázis üzembe helyezésére. Az SQL Server Data Tools (SSDT) segítségével létrehozhatja és üzembe helyezheti a táblázatosmodell-projektet, amelyen dolgozik. 

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következők szükségesek:

* **Analysis Services-kiszolgáló** az Azure-ban. További információkért lásd [az Azure Analysis Services-kiszolgáló létrehozásával kapcsolatos](analysis-services-create-server.md) témakört.
* **Táblázatosmodell-projekt** az SSDT-n, vagy egy meglévő táblázatos modell az 1200-as vagy magasabb kompatibilitási szinten. Korábban még nem hozott létre egyet sem? Próbálkozzon [az Adventure Works internetes értékesítési modell központi telepítésének útmutatójával](https://msdn.microsoft.com/library/hh231691.aspx).
* **Helyszíni átjáró** – Ha a szervezete hálózatában egy vagy több helyszíni adatforrás található, telepítenie kell egy [helyszíni adatátjárót](analysis-services-gateway.md). Az átjáróra azért van szükség, hogy a felhőben található kiszolgálója csatlakozni tudjon a helyszíni adatforrásaihoz a modellben található adatok feldolgozásához és frissítéséhez.

> [!TIP]
> Az üzembe helyezés előtt győződjön meg róla, hogy a tábláiban található adatok feldolgozhatók. Az SSDT-ben kattintson a **Modell** > **Feldolgozás** > **Az összes feldolgozása** lehetőségre. Ha a feldolgozás meghiúsul, nem fog sikerülni a telepítés.
> 
> 

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Táblázatos modell üzembe helyezése az SSDT-ből

1. Az üzembe helyezés előtt kérje le a kiszolgáló nevét. Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
    ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Az SSDT > **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd kattintson a **Tulajdonságok** lehetőségre. Ezután az **Üzembe helyezés** > **Kiszolgáló** területre illessze be a kiszolgáló nevét.   
   
    ![Az üzembehelyezési kiszolgáló tulajdonságához illessze be a kiszolgáló nevét.](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. A **Megoldáskezelőben** kattintson a jobb gombbal a **Tulajdonságok** elemre, majd kattintson az **Üzembe helyezés** lehetőségre. Lehet, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-ba.
   
    ![Üzembe helyezés kiszolgálóra](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Az üzembe helyezés állapota látható a kimeneti ablakban és az Üzembe helyezés területen is.
   
    ![Üzembe helyezés állapota](./media/analysis-services-deploy/aas-deploy-status.png)

Ennyi az egész!


## <a name="troubleshooting"></a>Hibaelhárítás
Ha a metaadatok telepítésekor a telepítés sikertelen, annak valószínűleg az az oka, hogy az SSDT nem tudott csatlakozni a kiszolgálóhoz. Győződjön meg róla, hogy tud csatlakozni a kiszolgálóhoz az SSMS használatával. Ezt követően ellenőrizze, hogy helyes a projekt Üzembehelyezési kiszolgáló tulajdonsága.

Ha a telepítés egy táblán sikertelen, annak valószínűleg az az oka, hogy a kiszolgálója nem tudott csatlakozni egy adatforráshoz. Ha a szervezete hálózatában helyszíni adatforrás található, mindenképp telepítsen egy [helyszíni adatátjárót](analysis-services-gateway.md).

## <a name="next-steps"></a>További lépések
Miután sikeresen telepítette a kiszolgálóra a táblázatos modellt, azonnal csatlakozhat is hozzá. A kezeléséhez [csatlakozzon hozzá az SSMS-sel](analysis-services-manage.md). Továbbá [csatlakozhat hozzá ügyféleszközzel](analysis-services-connect.md) is, például Power BI, Power BI Desktop vagy Excel segítségével, és megkezdheti a jelentések létrehozását.

