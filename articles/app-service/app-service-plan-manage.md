---
title: "Az Azure App Service-csomagot kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti az App Service-csomag különböző feladatok végrehajtására."
keywords: "App service, a azure app service, a méretezés, a app service-csomag módosítása, létrehozása, kezelése, felügyeleti"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>Az Azure App Service-csomagot kezelése

Egy [Azure App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) biztosít az erőforrásokat, amelyek egy App Service alkalmazást kell futtatni. Ez az útmutató bemutatja, hogyan felügyelheti az App Service-csomag.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha az App Service-környezetek, olvassa el [az App Service-csomag létrehozása az App Service-környezetek](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Üres App Service-csomagot is létrehozhat, vagy létrehozhat egy tervet alkalmazás létrehozásának részeként.

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **új** > **Web + mobil**, majd válassza ki **webalkalmazás** vagy egy másik App Service-alkalmazást.

2. Jelöljön ki egy meglévő App Service-csomagot, vagy hozzon létre egy csomagot az új alkalmazás.

   ![Alkalmazás létrehozása az Azure portálon.][createWebApp]

   A csomag létrehozása:

   a. Válassza ki **[+] új**.

      ![Az App Service-csomag létrehozása.][createASP] 

   b. A **App Service-csomag**, adja meg a csomag nevét.

   c. A **hely**, válasszon egy megfelelő helyet.

   d. A **tarifacsomag**, válassza ki a megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **összes** több, mint az beállítások árképzési nézetre **szabad** és **megosztott**. Miután kiválasztotta a tarifacsomagot, kattintson a **válasszon** gombra.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Az alkalmazások áthelyezése egy másik App Service-csomag

Áthelyezheti egy alkalmazás egy másik App Service-csomag, mindaddig, amíg a forrás terv és a cél terv a _ugyanazt az erőforráscsoportot és földrajzi régió_.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az alkalmazást, amelynek át szeretné helyezni.

2. A menüben keresse meg a **App Service-csomag** szakasz.

3. Válassza ki **módosítás App Service-csomag** megnyitásához a **App Service-csomag** választó.

   ![App Service-csomag választó.][change] 

4. Az a **App Service-csomag** választó, jelölje be egy meglévő tervezi, hogy ezzel az alkalmazással történő áthelyezéséhez.   

> [!IMPORTANT]
> A **kiválasztása az alkalmazásszolgáltatási csomag** lap szűrve van a következő szempontok szerint: 
> - Ugyanabban az erőforráscsoportban található 
> - Ugyanabban a földrajzi régióban található 
> - Az azonos webtárhely szerepel  
> 
> A _webtárhely_ egy logikai szerkezet, amely meghatározza a kiszolgáló erőforrások csoportosítása App Service-ben. Egy földrajzi régiót (például az USA nyugati régiója) számos webspaces tartalmaz ahhoz, hogy az ügyfelek, akik az App Service használata lefoglalni. Közötti webspaces jelenleg nem helyezhető át az App Service-erőforrásokat. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Minden egyes tervnek a saját IP-címek. Például áthelyezése egy helyet a **szabad** a réteg a **szabványos** réteg lehetővé teszi, hogy a szolgáltatások és erőforrások rendelt minden alkalmazás a **szabványos** réteg. Azonban egy magasabb rétegű terv alsó rétegű előfizetésre alkalmazás áthelyezése azt jelenti, hogy már nem rendelkezik bizonyos funkciók. Az alkalmazás, amely nem érhető el a célként megadott tervben szolgáltatást használja, ha hibaüzenet jelenik meg, mely szolgáltatásokat, amely nincs használatban van. 

Ha SSL-tanúsítványokat használ az alkalmazások, például előfordulhat, hogy ez a hibaüzenet lásd:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Ebben az esetben a továbblépés előtt az alkalmazást a cél-csomagra, kell vagy:
- A cél terv tarifacsomagját növelheti **alapvető** vagy újabb verzióját.
- Távolítsa el az alkalmazás összes SSL-kapcsolatot.

## <a name="move-an-app-to-a-different-region"></a>Az alkalmazások áthelyezése egy másik régióban található

A régióban, amelyben az alkalmazás fut. az a régió, az App Service-csomag. Az App Service-csomag régió nem módosíthatja. Az alkalmazás futtatása egy másik régióban szeretné, ha egy alternatív-e az alkalmazás a Klónozás. A Klónozás teszi az alkalmazás másolatát egy új vagy meglévő App Service-csomag bármely régióban.

Található **Klónozott alkalmazás** a a **Fejlesztőeszközök** a menü részét.

> [!IMPORTANT]
> A Klónozás bizonyos korlátozásokkal rendelkezik. Áttekintheti azokat az [Azure App Service-alkalmazást a Klónozás](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Az App Service-csomag vertikális

Méretezést kívánó egy App Service előfizetési csomag csomagazonosítóját tarifacsomag című [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).

Terjessze ki egy alkalmazást a példányok száma, lásd: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Az App Service-csomag törlése

Az App Service-csomag az utolsó alkalmazás törlésekor váratlan díjak elkerülése App Service is törli a terv alapértelmezés szerint. Ehelyett tartsa a terv választja, akkor módosítani kell az a terv **szabad** réteg, ezért még nem alkalmazott.

> [!IMPORTANT]
> App Service-csomagokról, amelyeken nincs hozzájuk társított alkalmazások továbbra is mert lefoglalni a konfigurált Virtuálisgép-példányok mindaddig függő díj terheli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vertikális felskálázás egy alkalmazást az Azure-ban](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
