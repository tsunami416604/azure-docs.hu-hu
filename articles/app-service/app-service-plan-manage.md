---
title: "Az Azure App Service-csomagot kezelése |} Microsoft Docs"
description: "Ismerje meg, hogyan az App Service csomagokban különböző feladatok végrehajtására az App Service-csomag kezeléséhez."
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
ms.openlocfilehash: b2305782fa4d8ca1bc90f239bc1f6ade0ff8fdc3
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Az Azure App Service-csomagot kezelése

Egy [App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) biztosít az erőforrások egy App Service alkalmazást kell futtatni. Ez az útmutató útmutató bemutatja, hogyan kezelheti az App Service-csomag. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha az App Service-környezetek, olvassa el [az App Service-csomag létrehozása az App Service-környezetek](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Létrehozhat egy üres App Service-csomag vagy alkalmazás létrehozásának részeként.

A a [Azure-portálon](https://portal.azure.com), kattintson a **új** > **Web + mobil**, majd válassza ki **webalkalmazás** vagy más App Service alkalmazás típusa.

![Alkalmazás létrehozása az Azure portálon.][createWebApp]

Ezután válassza ki a meglévő App Service-csomagot, vagy hozzon létre egy csomagot az új alkalmazás.

 ![Az App Service-csomag létrehozása.][createASP]

Az App Service-csomag létrehozásához kattintson a **[+] hozzon létre új**, típusa a **App Service-csomag** nevet, és válasszon egy megfelelő **hely**. Kattintson a **tarifacsomag**, majd válassza ki a megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **összes** több, mint az beállítások árképzési nézetre **szabad** és **megosztott**. 

Miután kiválasztotta a tarifacsomagot, kattintson a **válasszon** gombra.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Az alkalmazások áthelyezése egy másik App Service-csomag

Áthelyezheti egy alkalmazás egy másik App Service-csomag mindaddig, amíg a forrás terv és a cél terv a _ugyanazt az erőforráscsoportot és földrajzi régió_.

Az alkalmazások áthelyezése másik terv, keresse meg az alkalmazást, áthelyezni kívánt a [Azure-portálon](https://portal.azure.com).

Az a **menü**, keresse meg a **App Service-csomag** szakasz.

Válassza ki **módosítás App Service-csomag** a folyamat elindításához.

**App Service-csomag módosítása** megnyitja a **App Service-csomag** választó. Válasszon egy meglévő terv ebbe az alkalmazásba történő áthelyezése. A ugyanazt az erőforráscsoportot és régió csak tervek jelennek meg. Ha ugyanazt az erőforráscsoportot és régió imént létrehozott egy App Service-csomag, de az nem jelenik meg a listában, próbálja meg frissíteni a böngésző lapot.

![App Service-csomag választó.][change]

Minden egyes tervnek a saját IP-címek. Például áthelyezése egy helyet a **szabad** a réteg a **szabványos** réteg, lehetővé teszi, hogy a szolgáltatások és erőforrások rendelt minden alkalmazás a **szabványos** réteg. Azonban egy alkalmazást egy magasabb rétegzett tervből áthelyezése egy alacsonyabb rétegzett terv azt jelenti, hogy már nem bizonyos szolgáltatások eléréséhez. Az alkalmazás, amely nem érhető el a célként megadott tervben szolgáltatást használja, ha hibaüzenet jelenik meg, mely szolgáltatásokat, amely nincs használatban van. Például, ha az alkalmazások SSL-tanúsítványokat használ, előfordulhat, hogy a hibaüzenet jelenik: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`ebben az esetben kell a célként megadott terv az árképzési szint növelheti **alapvető** vagy újabb verzióját, vagy el kell távolítania az összes SSL-kapcsolatok az alkalmazás, a továbblépés előtt az alkalmazást a cél-csomagra.

## <a name="move-an-app-to-a-different-region"></a>Az alkalmazások áthelyezése egy másik régióban található

A régióban, amelyben az alkalmazás fut. az a régió, az App Service-csomag. Az App Service-csomag régió nem módosíthatja. Az alkalmazás futtatása egy másik régióban szeretné, ha egy alternatív-e az alkalmazás a Klónozás. A Klónozás teszi az alkalmazás másolatát egy új vagy meglévő App Service-csomag bármely régióban.

Található **Klónozott alkalmazás** a a **Fejlesztőeszközök** a menü részét.

> [!IMPORTANT]
> A Klónozás rendelkezik néhány címen olvashat [Azure App Service-alkalmazást a Klónozás](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Az App Service-csomag vertikális

Méretezést kívánó ah App Service-csomag csomagazonosítóját IP-címek című [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).

Terjessze ki egy alkalmazást a példányok száma, lásd: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Az App Service-csomag törlése

Az App Service-csomag az utolsó alkalmazás törlésekor váratlan díjak elkerülése App Service is törli a terv alapértelmezés szerint. Ha ehelyett megtarthatja a terv, akkor módosítani kell a terv **szabad** , hogy Ön nem get felszámított réteg.

> [!IMPORTANT]
> **App Service-csomagok** , amelyek nem találhatók alkalmazások hozzájuk társított továbbra is függő díj terheli, mivel azok továbbra is a konfigurált Virtuálisgép-példányok lefoglalni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Vertikális felskálázás egy alkalmazást az Azure-ban](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
