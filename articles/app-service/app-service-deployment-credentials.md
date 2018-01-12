---
title: "Az Azure App Service telepítési hitelesítő adatok |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure App Service üzembe helyezési hitelesítő adatokat."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Telepítési hitelesítő adatok beállítása az Azure App Service
[Az Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) támogatja a hitelesítő adatok kétféle [helyi Git-telepítésének](app-service-deploy-local-git.md) és [FTP/S telepítési](app-service-deploy-ftp.md). Ezek olyan nem ugyanaz, mint az Azure Active Directorybeli hitelesítő adatokat.

* **Felhasználói szintű hitelesítő adatokat**: a teljes Azure-fiók hitelesítő adatait egy készletét. Az App Service a telepíteni kívánt alkalmazást, minden előfizetést, az Azure-fiókra van hozzáférése a használható. Ezek az alapértelmezett hitelesítő adatok beállítása, amelyet megadtak a **alkalmazásszolgáltatások** > **&lt;alkalmazás_neve >** > **üzembe helyezési hitelesítő adatok**. Ez egyben az alapértelmezett, amely a grafikus felhasználói Felülettel portálon illesztett van (például a **áttekintése** és **tulajdonságok** az alkalmazás [erőforrás oldala](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Azure-erőforrások szerepköralapú hozzáférés vezérlés (RBAC) vagy társadminisztrátornak engedélyek hozzáférés delegálásához, ha minden Azure felhasználói, amely fogad egy alkalmazás hozzáférési jogának képes személyes felhasználói szintű hitelesítő adatokat használhatna, amíg hozzáférését visszavonja. A központi telepítési hitelesítő adatokat nem lehet megosztva Azure másokkal.
    >
    >

* **Alkalmazási szintű hitelesítő adatokat**: hitelesítő adatok az egyes alkalmazásokhoz egy készletét. Csak az alkalmazás telepítéséhez használható. A hitelesítő adatokat az egyes alkalmazások a alkalmazás létrehozásakor automatikusan létrejön, és az alkalmazás megtalálható közzétételi profil. Nem lehet manuálisan konfigurálnia a hitelesítő adatokat, de visszaállíthatja őket egy olyan alkalmazáshoz bármikor.

    > [!NOTE]
    > Annak érdekében, hogy illetéktelen személyek számára hozzáférést a hitelesítő adatokat keresztül szerepköralapú hozzáférés vezérlés (RBAC), meg kell győződnie őket közreműködő vagy magasabb a webalkalmazásban. Olvasók nem engedélyezett a közzététel, és ezért nem tudja elérni ezeket a hitelesítő adatokat.
    >
    >

## <a name="userscope"></a>Állítsa be, és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása

Konfigurálhatja a felhasználói szintű hitelesítő adatokat a bármely alkalmazás [erőforrás oldala](../azure-resource-manager/resource-group-portal.md#manage-resources). Minden alkalmazás és az Azure-fiókjával a előfizetéseket függetlenül melyik alkalmazás konfigurálja ezeket a hitelesítő adatokat, vonatkozik. 

A felhasználói szintű hitelesítő adatok konfigurálása:

1. Az a [Azure-portálon](https://portal.azure.com), kattintson az App Service >  **&lt;any_app >** > **üzembe helyezési hitelesítő adatok**.

    > [!NOTE]
    > A portálon rendelkeznie kell legalább egy alkalmazást a központi telepítési hitelesítő adatok lap elérése előtt. Azonban a a [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), konfigurálhatja a felhasználói szintű hitelesítő adatok nélkül egy meglévő alkalmazást.

2. Konfigurálja a felhasználónevet és jelszót, és kattintson **mentése**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Az üzembe helyezési hitelesítő adatok megadása után található a *Git* az alkalmazás központi telepítési felhasználónév **áttekintése**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

és *FTP* az alkalmazás központi telepítési felhasználónév **tulajdonságok**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nem jeleníti meg a felhasználói szintű telepítési jelszavát. Ha elfelejti a jelszavát, nem lehet beolvasni. Azonban, alaphelyzetbe állíthatja a hitelesítő adatok ebben a szakaszban ismertetett lépések szerint.
>
>  

## <a name="appscope"></a>Első és az alkalmazási szintű hitelesítő adatok alaphelyzetbe állítása
Minden alkalmazás az App Service-ben, az alkalmazási szintű tárolják a hitelesítő adatokat az XML-közzétételi profil.

Az alkalmazási szintű hitelesítő adatainak lekéréséhez:

1. Az a [Azure-portálon](https://portal.azure.com), kattintson az App Service >  **&lt;any_app >** > **áttekintése**.

2. Kattintson a **... További** > **Get közzétételi profil**, és elindítja a letöltési egy. PublishSettings-fájl.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Nyissa meg a. PublishSettings fájlt, és keresse a `<publishProfile>` attribútummal rendelkező címke `publishMethod="FTP"`. Ezt követően az beszerzése a `userName` és `password` attribútumok.
Ezek azok az alkalmazási szintű hitelesítő adatokat.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    A felhasználói szintű hitelesítő adatokat hasonló, az FTP telepítési felhasználónév az formátumban `<app_name>\<username>`, és a Git telepítési felhasználónév az imént `<username>` anélkül, hogy az előző `<app_name>\`.

Az alkalmazási szintű hitelesítő adatok alaphelyzetbe állítása:

1. Az a [Azure-portálon](https://portal.azure.com), kattintson az App Service >  **&lt;any_app >** > **áttekintése**.

2. Kattintson a **... További** > **a közzétételi profil alaphelyzetbe állítása**. Kattintson a **Igen** az alaphelyzetbe állítás megerősítéséhez.

    A reset művelet érvényteleníti a korábban letöltött. PublishSettings-fájlok.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan telepítse az alkalmazást a rendszer ezen hitelesítő adatok segítségével [helyi Git](app-service-deploy-local-git.md) vagy [FTP/S](app-service-deploy-ftp.md).
