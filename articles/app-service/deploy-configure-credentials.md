---
title: Az Azure App Service – az üzembe helyezési hitelesítő adatok konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure App Service üzembe helyezési hitelesítő adatok.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d89197fad8354b0bae41ab67b9bb1dfac0a179eb
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820297"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Az Azure App Service üzembe helyezési hitelesítő adatok konfigurálása
[Az Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) hitelesítő adatok kétféle [Git helyi üzemelő példányának](deploy-local-git.md) és [FTP/S üzembe helyezési](deploy-ftp.md). Ezek nem azonosak az Azure Active Directory hitelesítő adatként.

* **A felhasználói szintű hitelesítő adatokat**: a teljes Azure-fiók hitelesítő adatait egy készletét. App Service-ben minden olyan alkalmazáshoz, minden előfizetéshez, amely az Azure-fiók rendelkezik hozzáféréssel a üzembe helyezéséhez használható. Az alapértelmezett készlet, amely a portálon grafikus felhasználói Felülettel van illesztett (például a **áttekintése** és **tulajdonságok** az alkalmazás [erőforráslapján](../azure-resource-manager/manage-resources-portal.md#manage-resources)). Amikor egy felhasználó hozzáférést kap alkalmazás szerepköralapú hozzáférés-vezérlés (RBAC) vagy társfelügyeletű engedélyek, hogy a felhasználó használhatja azt saját felhasználói szintű hitelesítő adatokat mindaddig, amíg a hozzáférést visszavonták. Ne ossza meg ezeket a hitelesítő adatokat más Azure-felhasználóval.

* **Az alkalmazásszintű hitelesítő adatok**: hitelesítőadat minden alkalmazáshoz. Csak az alkalmazás üzembe helyezéséhez használható. A hitelesítő adatokat az egyes alkalmazások automatikusan generált alkalmazás létrehozásakor. Manuálisan nem konfigurálható, de bármikor visszaállíthatja. Adható hozzáférés (RBAC) az alkalmazásszintű hitelesítő adatokat a felhasználók, a felhasználónak kell lennie közreműködői vagy újabb, az alkalmazást. Olvasói nem tehet közzé, és nem fér hozzá ezeket a hitelesítő adatokat.

## <a name="userscope"></a>Állítsa be, és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása

Konfigurálhatja a felhasználói szintű hitelesítő adatok használatát bármely olyan alkalmazásban [erőforráslapján](../azure-resource-manager/manage-resources-portal.md#manage-resources). Függetlenül attól hogy mely alkalmazás konfigurálja ezeket a hitelesítő adatokat, vonatkozik minden alkalmazásra és az Azure-fiókjával minden előfizetés esetén. 

A felhasználói szintű hitelesítő adatok megadása:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben kattintson a **App Services** > **&lt;any_app >** > **üzembe helyezés Center** > **üzembe helyezési hitelesítő adatok**.

    A portálon rendelkeznie kell legalább egy alkalmazást az üzembe helyezési hitelesítő adatok oldal elérése előtt. Azonban a a [Azure CLI-vel](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), konfigurálhatja a felhasználói szintű hitelesítő adatokat, anélkül, hogy egy meglévő alkalmazást.

2. Kattintson a **felhasználói hitelesítő adatok**, konfigurálja a felhasználónevet és jelszót, és kattintson **fájlmentési hitelesítő adatok**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Miután beállította az üzembehelyezési hitelesítő adatokat, annak a *Git* az alkalmazás üzembehelyezési felhasználónév **áttekintése**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

és *FTP* az alkalmazás üzembehelyezési felhasználónév **tulajdonságok**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói szintű üzembehelyezési jelszót. Ha elfelejti a jelszavát, a jelen szakaszban ismertetett lépéseket követve alaphelyzetbe állíthatja a hitelesítő adatait.
>
>  

## <a name="appscope"></a>Első és az alkalmazásszintű hitelesítő adatok alaphelyzetbe állítása
Az alkalmazásszintű hitelesítő adatok lekérése:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben kattintson a **App Services** > **&lt;any_app >** > **üzembe helyezés Center** > **üzembe helyezési hitelesítő adatok**.

2. Kattintson a **alkalmazás hitelesítő adatok**, és kattintson a **másolási** hivatkozás másolása a felhasználónév vagy jelszó.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Az alkalmazásszintű hitelesítő adatok alaphelyzetbe állításához kattintson **hitelesítő adatok alaphelyzetbe állítása** azonos párbeszédpanelen.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás üzembe helyezése [helyi Git](deploy-local-git.md) vagy [FTP/S](deploy-ftp.md).
