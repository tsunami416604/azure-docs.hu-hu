---
title: Központi telepítés forrásának konfigurálása az App Services az Azure Stackben |} A Microsoft Docs
description: Hogyan szolgáltatás-rendszergazdák is központi telepítés forrásának konfigurálása (a Git, GitHub, BitBucket, DropBox és onedrive vállalati verzió) az Azure Stack App Service szolgáltatásában
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4945ab0b8da961fc5739388d83f7c645f40e510d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923518"
---
# <a name="configure-deployment-sources"></a>Központi telepítés forrásának konfigurálása
*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*


Az Azure Stack App Service támogatja az igény szerinti üzembe helyezést, több verziókövetési szolgáltatók az. Ez a funkció lehetővé teszi, hogy az alkalmazásfejlesztők forráskódtárházakból helyezheti üzembe közvetlenül a forrásból. Ha felhasználókat szeretne csatlakozni a tárházak az App Service konfigurálása, a felhő üzemeltetője először konfigurálnia kell az Azure Stack App Service-ben és a Forrásvezérlő-szolgáltatónál integrációjával.  

Helyi Git, valamint a következő verziókövetési szolgáltatók támogatottak:

* GitHub
* Bitbucket-alapú
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Az App Service-ben Adminisztráció központi telepítési források megtekintése

1. Jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
2. Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.  ![Az App Service erőforrás-szolgáltató rendszergazda][1]
3. Kattintson a **verziókövetési konfiguráció**.  Itt láthatja a listában konfigurált összes központi telepítési forrás.
    ![Az App Service erőforrás szolgáltató rendszergazdai verziókövetési konfiguráció][2]

## <a name="configure-github"></a>Konfigurálja a Githubon

Ez a feladat végrehajtásához GitHub-fiókkal kell rendelkeznie. Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

1. Jelentkezzen be a GitHub, keresse meg a https://www.github.com/settings/developers kattintson **regisztrálhat új alkalmazásokat**.
    ![GitHub - egy új alkalmazás regisztrálása][3]
2. Adjon meg egy **alkalmazásnév** – például az Azure Stack App Service-ben.
3. Adja meg a **kezdőlap URL-címe**. A kezdőlap URL-címe az Azure Stack portálon címnek kell lennie. Például: https://portal.local.azurestack.external.
4. Adjon meg egy **alkalmazásleírás**.
5. Adja meg a **engedélyezési visszahívási URL-Címének**.  Egy alapértelmezett Azure Stack üzemelő példányban, az URL-cím szerepel az űrlap https://portal.local.azurestack.external/TokenAuthorize, ha le egy másik tartományt helyére írja be a tartomány local.azurestack.external
6. Kattintson a **alkalmazás regisztrálása**.  Most már megjelenik egy oldal termékadatlapot a **ügyfél-azonosító** és **titkos Ügyfélkód** az alkalmazáshoz.
    ![GitHub - befejezett alkalmazás regisztrálása][5]
7.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
8.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
9. Kattintson a **verziókövetési konfiguráció**.
10. Másolja és illessze be a **ügyfél-azonosító** és **titkos Ügyfélkód** be a megfelelő bemeneti állíthatunk be a github használatára.
11. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-bitbucket"></a>Bitbucket-alapú konfigurálása

Ez a feladat végrehajtásához egy BitBucket-fiókkal kell rendelkeznie. Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

1. Jelentkezzen be a BitBucket, és keresse meg a **Integrációk** a fiókban.
    ![BitBucket irányítópult - Integrációk][7]
2. Kattintson a **OAuth** hozzáférés felügyelete alá, és **Hozzáadás fogyasztói**.
    ![Bitbucket-alapú OAuth fogyasztói hozzáadása][8]
3. Adjon meg egy **neve** a fogyasztót, például az Azure Stack App Service-ben.
4. Adjon meg egy **leírás** az alkalmazáshoz.
5. Adja meg a **visszahívási URL-Címének**.  Az alapértelmezett Azure Stack üzembe helyezés a visszahívási URL-címének neve a https://portal.local.azurestack.external/TokenAuthorize, ha le egy másik tartományt helyére írja be a tartomány azurestack.local.  Az URL-címet kell a kis-és nagybetűk kövesse az itt felsorolt BitBucket-integráció sikeres.
6. Adja meg a **URL-cím** – az URL-címet kell az Azure Stack portál URL-CÍMÉT, például https://portal.local.azurestack.external.
7. Válassza ki a **engedélyek** szükséges:
    - **Tárházak**: *olvasása*
    - **Webhookok**: *olvasása és írása*
8. Kattintson a **Save** (Mentés) gombra.  Ekkor megjelenik az új alkalmazás és a **kulcs** és **titkos** alatt **OAuth fogyasztók**.
    ![Bitbucket-alapú alkalmazás listázása][9]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
11. Kattintson a **verziókövetési konfiguráció**.
12. Másolja és illessze be a **kulcs** be a **ügyfél-azonosító** beviteli mezőt és **titkos** be a **titkos Ügyfélkód** beviteli mezőt a BitBucket.
13. Kattintson a **Save** (Mentés) gombra.


## <a name="configure-onedrive"></a>Konfigurálja a onedrive vállalati verzió

Egy Microsoft Account befejezheti a feladatot egy OneDrive-fiókjához társított kell rendelkeznie.  Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

> [!NOTE]
> OneDrive vállalati fiókok esetében jelenleg nem támogatottak.

1. Keresse meg a https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm , és jelentkezzen be Microsoft-Account.
2. A **alkalmazásaimat**, kattintson a **alkalmazás hozzáadása**.
![Onedrive vállalati alkalmazások][10]
3. Adjon meg egy **neve** adja meg az új alkalmazás regisztrálása **futó Azure Stack App Service-**, és kattintson a **alkalmazás létrehozása**
4. A következő képernyőn az új alkalmazás tulajdonságai szerepelnek. Rekord a **Alkalmazásazonosító**.
![Onedrive vállalati verzió alkalmazás tulajdonságai][11]
5. A **titkos Alkalmazáskulcsok**, kattintson a **új jelszó készítése**. Jegyezze fel a **új jelszó készült**. Ez az alkalmazás titkos kulcs és nem lekérdezhető kattintás után **OK** ezen a ponton.
6. Alatt **platformok** kattintson **hozzáadása Platform** válassza **webes**.
7. Adja meg a **átirányítási URI**.  Az alapértelmezett Azure Stack üzembe helyezés az átirányítási URI formájában van https://portal.local.azurestack.external/TokenAuthorize, ha le egy másik tartományt helyére írja be a tartomány azurestack.local ![OneDrive alkalmazás – Webplatform hozzáadása][12]
8. Adja hozzá a **Microsoft Graph-engedélyek** - **delegált engedélyek**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Onedrive vállalati verzió alkalmazás – Graph-engedélyek][13]
9. Kattintson a **Save** (Mentés) gombra.
10.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
11.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
12. Kattintson a **verziókövetési konfiguráció**.
13. Másolja és illessze be a **Alkalmazásazonosító** be a **ügyfél-azonosító** beviteli mezőt és **jelszó** be a **titkos Ügyfélkód** beviteli mezőt a onedrive vállalati verzióhoz.
14. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-dropbox"></a>DropBox konfigurálása

> [!NOTE]
> Szüksége lesz egy DropBox-fiókra, a feladat végrehajtásához.  Előfordulhat, hogy használni kívánt fiók egy személyes fiók helyett a szervezet számára.

1. Keresse meg a https://www.dropbox.com/developers/apps , és jelentkezzen be a DropBox-fiókjában.
2. Kattintson a **alkalmazás létrehozása**.

    ![Dropbox-alkalmazások][14]

3. Válassza ki **DropBox API**.
4. Állítsa a hozzáférés szintet **alkalmazás mappája**.
5. Adjon meg egy **neve** az alkalmazáshoz.
![Dropbox alkalmazás regisztrálása][15]
6. Kattintson a **alkalmazás létrehozása**.  Most már megjelenik a beállítások az alkalmazás többek között az ajánlati oldala **Alkalmazáskulcs** és **titkos Alkalmazáskulcs**.
7. Ellenőrizze a **alkalmazás mappanevet** értékre van állítva **App Service-ben az Azure Stacken**.
8. Állítsa be a **OAuth 2 átirányítási URI-t** kattintson **Hozzáadás**.  Az alapértelmezett Azure Stack üzembe helyezés az átirányítási URI formájában van https://portal.local.azurestack.external/TokenAuthorize, ha le egy másik tartományt helyére írja be a tartomány azurestack.local.
![Dropbox-alkalmazás konfigurációja][16]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
11. Kattintson a **verziókövetési konfiguráció**.
12. Másolja és illessze be a **Alkalmazáskulcsot** be a **ügyfél-azonosító** beviteli mezőt és **titkos Alkalmazáskulcs** be a **titkos Ügyfélkód** beviteli mező, a DropBox.
13. Kattintson a **Save** (Mentés) gombra.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>További lépések

A felhasználók mostantól használhatják a központi telepítés forrásának többek között a [folyamatos üzembe helyezés](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [Git helyi üzemelő példányának](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), és [mappa szinkronizálása a felhő](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
