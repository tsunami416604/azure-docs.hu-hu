---
title: Üzembe helyezés az Azure Analysis Servicesben az SSDT-vel | Microsoft Docs
description: Megismerheti, hogyan helyezhet üzembe egy táblázatos modellt az Azure Analysis Servicesre az SSDT-vel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5cbad33bf98e4138b94916a8f87cc7e34901b3da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595658"
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

## <a name="get-the-server-name"></a>A kiszolgáló nevét

Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Az SSDT telepítése

1. Az SSDT > **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd kattintson a **Tulajdonságok** lehetőségre. Ezután az **Üzembe helyezés** > **Kiszolgáló** területre illessze be a kiszolgáló nevét.   
   
    ![Az üzembehelyezési kiszolgáló tulajdonságához illessze be a kiszolgáló nevét.](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. A **Megoldáskezelőben** kattintson a jobb gombbal a **Tulajdonságok** elemre, majd kattintson az **Üzembe helyezés** lehetőségre. Lehet, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-ba.
   
    ![Üzembe helyezés kiszolgálóra](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Az üzembe helyezés állapota látható a kimeneti ablakban és az Üzembe helyezés területen is.
   
    ![Üzembe helyezés állapota](./media/analysis-services-deploy/aas-deploy-status.png)

Ennyi az egész!


## <a name="troubleshooting"></a>Hibaelhárítás
Ha a metaadatok telepítésekor a telepítés sikertelen, annak valószínűleg az az oka, hogy az SSDT nem tudott csatlakozni a kiszolgálóhoz. Győződjön meg róla, hogy tud csatlakozni a kiszolgálóhoz az SSMS használatával. Ezt követően ellenőrizze, hogy helyes a projekt Üzembehelyezési kiszolgáló tulajdonsága.

Ha a telepítés egy táblán sikertelen, annak valószínűleg az az oka, hogy a kiszolgálója nem tudott csatlakozni egy adatforráshoz. Ha a szervezete hálózatában helyszíni adatforrás található, mindenképp telepítsen egy [helyszíni adatátjárót](analysis-services-gateway.md).

## <a name="next-steps"></a>További lépések
Miután sikeresen telepítette a kiszolgálóra a táblázatos modellt, azonnal csatlakozhat is hozzá. A kezeléséhez [csatlakozzon hozzá az SSMS-sel](analysis-services-manage.md). Továbbá [csatlakozhat hozzá ügyféleszközzel](analysis-services-connect.md) is, például Power BI, Power BI Desktop vagy Excel segítségével, és megkezdheti a jelentések létrehozását.

