---
title: Az Azure App Service-üzembehelyezési hitelesítő adatok |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure App Service üzembe helyezési hitelesítő adatok.
services: app-service
documentationcenter: ''
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
ms.openlocfilehash: 79841887ce8413970cb7dff0ca0099d4e0745a68
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259309"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Az Azure App Service üzembe helyezési hitelesítő adatok konfigurálása
[Az Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) hitelesítő adatok kétféle [Git helyi üzemelő példányának](app-service-deploy-local-git.md) és [FTP/S üzembe helyezési](app-service-deploy-ftp.md). Ezek nem azonosak az Azure Active Directory hitelesítő adatként.

* **A felhasználói szintű hitelesítő adatokat**: a teljes Azure-fiók hitelesítő adatait egy készletét. App Service-ben minden olyan alkalmazáshoz, minden előfizetéshez, amely az Azure-fiók rendelkezik hozzáféréssel a üzembe helyezéséhez használható. Ezek a konfigurált alapértelmezett hitelesítő adatok beállítása a **App Services** > **&lt;app_name >** > **üzembe helyezési hitelesítő adatok**. Ez egyben az alapértelmezett csoportot, amely a portálon grafikus felhasználói Felülettel illesztett van (például a **áttekintése** és **tulajdonságok** az alkalmazás [erőforráslapján](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Szerepkör alapú hozzáférés-vezérlés (RBAC) vagy a társadminisztrátori engedélyek használatával Azure-erőforrásokhoz való hozzáférést delegálhat, minden egyes Azure-felhasználó, amely megkapja a hozzáférést egy alkalmazáshoz használhat marketingosztályhoz személyes felhasználói szintű hitelesítő adatokat mindaddig, amíg hozzáférést visszavonták. Ezeket üzembe helyezési hitelesítő adatok nem szabad megosztani a többi Azure-felhasználóval.
    >
    >

* **Az alkalmazásszintű hitelesítő adatok**: hitelesítőadat minden alkalmazáshoz. Csak az alkalmazás üzembe helyezéséhez használható. A hitelesítő adatok minden alkalmazás automatikusan generált alkalmazás létrehozásakor, és megtalálható-e az alkalmazás közzétételi profil. Nem lehet manuálisan konfigurálnia a hitelesítő adatokat, de visszaállíthatja őket az alkalmazás bármikor.

    > [!NOTE]
    > Annak érdekében, hogy valaki ezeket a hitelesítő adatokat keresztül szerepköralapú hozzáférést vezérlő (RBAC), hozzáférést kell tenni őket a közreműködői vagy újabb a webalkalmazásban. Olvasói nem tehet közzé, és ezért nem érhető el ezeket a hitelesítő adatokat.
    >
    >

## <a name="userscope"></a>Állítsa be, és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása

Konfigurálhatja a felhasználói szintű hitelesítő adatok használatát bármely olyan alkalmazásban [erőforráslapján](../azure-resource-manager/resource-group-portal.md#manage-resources). Függetlenül attól hogy mely alkalmazás konfigurálja ezeket a hitelesítő adatokat, vonatkozik minden alkalmazásra és az Azure-fiókjával minden előfizetés esetén. 

A felhasználói szintű hitelesítő adatok megadása:

1. Az a [az Azure portal](https://portal.azure.com), kattintson az App Service-ben >  **&lt;any_app >** > **üzembe helyezési hitelesítő adatok**.

    > [!NOTE]
    > A portálon rendelkeznie kell legalább egy alkalmazást az üzembe helyezési hitelesítő adatok oldal elérése előtt. Azonban a a [Azure CLI-vel](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), konfigurálhatja a felhasználói szintű hitelesítő adatokat, anélkül, hogy egy meglévő alkalmazást.

2. Konfigurálja a felhasználónevet és jelszót, és kattintson a **mentése**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Miután beállította az üzembehelyezési hitelesítő adatokat, annak a *Git* az alkalmazás üzembehelyezési felhasználónév **áttekintése**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

és *FTP* az alkalmazás üzembehelyezési felhasználónév **tulajdonságok**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói szintű üzembehelyezési jelszót. Ha elfelejti a jelszavát, nem lehet beolvasni. Azonban alaphelyzetbe állíthatja a hitelesítő adatait a jelen szakaszban ismertetett lépéseket követve.
>
>  

## <a name="appscope"></a>Első és az alkalmazásszintű hitelesítő adatok alaphelyzetbe állítása
Minden alkalmazás az App Service-ben, az alkalmazásszintű hitelesítő adatok tárolt XML-közzétételi profil.

Az alkalmazásszintű hitelesítő adatok lekérése:

1. Az a [az Azure portal](https://portal.azure.com), kattintson az App Service-ben >  **&lt;any_app >** > **áttekintése**.

2. Kattintson a **... További** > **közzétételi profil letöltése**, és elindítja a letöltési egy. PublishSettings-fájl.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Nyissa meg a. PublishSettings fájlt, és keresse meg a `<publishProfile>` attribútummal rendelkező címke `publishMethod="FTP"`. Kérje le annak `userName` és `password` attribútumok.
Ezek azok az alkalmazásszintű hitelesítő adatok.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    A felhasználói szintű hitelesítő adatokat hasonlóan az FTP-üzembehelyezési felhasználónév is formátumban `<app_name>\<username>`, és a Git telepítési felhasználónév csak rendszer `<username>` anélkül, hogy az előző `<app_name>\`.

Az alkalmazásszintű hitelesítő adatok visszaállítása:

1. Az a [az Azure portal](https://portal.azure.com), kattintson az App Service-ben >  **&lt;any_app >** > **áttekintése**.

2. Kattintson a **... További** > **közzétételi profil alaphelyzetbe állítása**. Kattintson a **Igen** a visszaállítás megerősítéséhez.

    A visszaállítási művelet minden korábban letöltött érvényteleníti. PublishSettings-fájlok.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás üzembe helyezése [helyi Git](app-service-deploy-local-git.md) vagy [FTP/S](app-service-deploy-ftp.md).
