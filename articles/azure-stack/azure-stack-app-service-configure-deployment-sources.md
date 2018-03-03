---
title: "Központi telepítés forrásainak konfigurálása az Azure-veremben alkalmazásszolgáltatások |} Microsoft Docs"
description: "Hogyan szolgáltatás-rendszergazda számára állíthat be (Git, GitHub, BitBucket, DropBox és a OneDrive) központi telepítés forrásból veremben Azure App Service"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 277ed277b14886d386e097e0ce4bef8add5d4ba1
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="configure-deployment-sources"></a>Központi telepítés forrásának konfigurálása
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*


App Service Azure veremben több verziókövetési szolgáltatók igény szerinti telepítését támogatja. Ez a funkció lehetővé teszi, hogy a forrás vezérlő tárolóhelyekkel való közvetlen telepítése alkalmazásfejlesztők. Szeretnék csatlakozni a tárolóhelyekkel App Service konfigurálása, a felhő üzemeltetője először konfigurálnia kell a veremben Azure App Service és a szolgáltatót a verziókövetési közötti integrációt.  

Helyi Git mellett a következő verziókövetési szolgáltatók támogatottak:

* GitHub
* Bitbucket szolgáltatásokhoz
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>App Service felügyeleti nézet telepítési források

1. Jelentkezzen be az Azure verem felügyeleti portál (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
2. Keresse meg a **erőforrás-szolgáltató** válassza ki a **App Service erőforrás szolgáltató Admin**.  ![Az App Service erőforrás-szolgáltató rendszergazda][1]
3. Kattintson a **forrás vezérlőkonfiguráció**.  Itt láthatja a konfigurált központi telepítési források felsorolása.
    ![Az App Service erőforrás szolgáltató Admin forrás vezérlő konfigurálása][2]

## <a name="configure-github"></a>GitHub konfigurálása

A feladat végrehajtásához GitHub fiókkal kell rendelkeznie. Előfordulhat, hogy szeretne egy személyes fiók helyett a szervezet olyan fiókot használjon.

1. Jelentkezzen be a GitHub, keresse meg a https://www.github.com/settings/developers, és kattintson a **egy új alkalmazás regisztrálása**.
    ![GitHub - egy új alkalmazás regisztrálása][3]
2. Adjon meg egy **alkalmazásnév** például - App Service-Azure veremben.
3. Adja meg a **kezdőlap URL-cím**. A kezdőlap URL-CÍMÉT az Azure verem Portal címnek kell lennie. Például https://portal.local.azurestack.external.
4. Adjon meg egy **alkalmazás leírása**.
5. Adja meg a **engedélyezési visszahívási URL-címet**.  Alapértelmezett Azure verem üzembe helyezése esetén az URL-cím van az űrlap https://portal.local.azurestack.external/tokenauthorize, ha le egy másik tartományba helyettesítő local.azurestack.external a tartomány ![GitHub - új alkalmazás feltöltve értékekkel][4]
6. Kattintson a **alkalmazás regisztrálása**.  Most már választhat az oldal lista tartalmazza a **ügyfél-azonosító** és **Ügyfélkulcs** az alkalmazáshoz.
    ![GitHub - regisztrációja befejeződött][5]
7.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure verem felügyeleti portál (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
8.  Keresse meg a **erőforrás-szolgáltató** válassza ki a **App Service erőforrás szolgáltató Admin**.
9. Kattintson a **forrás vezérlőkonfiguráció**.
10. Másolja és illessze be a **ügyfél-azonosító** és **Ügyfélkulcs** be a megfelelő beviteli mezők a github használatára.
11. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-bitbucket"></a>BitBucket konfigurálása

A feladat végrehajtásához BitBucket fiókkal kell rendelkeznie. Előfordulhat, hogy szeretne egy személyes fiók helyett a szervezet olyan fiókot használjon.

1. BitBucket bejelentkezni, és keresse meg a **integrációja** a fiókjában.
    ![BitBucket irányítópult - integrációja][7]
2. Kattintson a **OAuth** hozzáférés felügyelete alatt álló és **Hozzáadás fogyasztói**.
    ![BitBucket OAuth fogyasztói hozzáadása][8]
3. Adjon meg egy **neve** veremben Azure App Service-például a fogyasztó számára.
4. Adjon meg egy **leírás** az alkalmazáshoz.
5. Adja meg a **visszahívási URL-cím**.  Alapértelmezett Azure verem telepítésében a visszahívás URL-cím esetén az űrlap https://portal.local.azurestack.external/TokenAuthorize le egy másik tartományba helyettesítő azurestack.local a tartományt.  Az URL-címet kell a kis-és nagybetűk kövesse az itt felsorolt BitBucket integrációs sikeres.
6. Adja meg a **URL-cím** – az URL-cím az Azure verem portál URL-CÍMÉT, például https://portal.local.azurestack.external kell lennie.
7. Válassza ki a **engedélyek** szükséges:
    - **Adattárak**: *olvasása*
    - **Webhook**: *olvasása és írása*
8. Kattintson a **Save** (Mentés) gombra.  Megjelenik az új alkalmazás, valamint a **kulcs** és **titkos** alatt **OAuth fogyasztók**.
    ![BitBucket alkalmazás listázása][9]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure verem felügyeleti portál (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltató** válassza ki a **App Service erőforrás szolgáltató Admin**.
11. Kattintson a **forrás vezérlőkonfiguráció**.
12. Másolja és illessze be a **kulcs** azokat a **ügyfél-azonosító** beviteli mezőt és **titkos** be a **Ügyfélkulcs** bitbucket szolgáltatásokhoz tartozó beviteli mezőt.
13. Kattintson a **Save** (Mentés) gombra.


## <a name="configure-onedrive"></a>Onedrive vállalati verzió konfigurálásához

A feladat végrehajtásához Microsoft Account csatolva a OneDrive-fiókja kell rendelkeznie.  Előfordulhat, hogy szeretne egy személyes fiók helyett a szervezet olyan fiókot használjon.

> [!NOTE]
> A OneDrive vállalati fiókok jelenleg nem támogatottak.

1. Tallózással keresse meg a https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm, és jelentkezzen be a Microsoft Account.
2. A **alkalmazásaimból**, kattintson a **hozzáadhat egy alkalmazást**.
![A OneDrive-alkalmazások][10]
3. Adjon meg egy **neve** adja meg az új alkalmazás regisztrációja **veremben Azure App Service**, és kattintson a **alkalmazás létrehozása**
4. A következő képernyőn az új alkalmazás tulajdonságok listája. Rekord a **Alkalmazásazonosító**.
![Onedrive vállalati verzió alkalmazás tulajdonságai][11]
5. A **alkalmazás titkos kulcsok**, kattintson a **új jelszó létrehozása**. Jegyezze fel a **jön létre új jelszót**. Ez az alkalmazás titkos kulcs és nincs lekérhető kattintás után **OK** ebben a szakaszban.
6. A **platformok** kattintson **hozzáadása Platform** válassza **webes**.
7. Adja meg a **átirányítási URI**.  Alapértelmezett Azure verem telepítésében, az átirányítási URI-van az űrlap https://portal.local.azurestack.external/tokenauthorize, ha le egy másik tartományba helyettesítő azurestack.local a tartomány ![onedrive vállalati verzió alkalmazás - hozzáadása Webes platform jöhet létre][12]
8. Adja hozzá a **Microsoft Graph engedélyek** - **delegált engedélyekkel**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Onedrive vállalati verzió alkalmazás - grafikon engedélyek][13]
9. Kattintson a **Save** (Mentés) gombra.
10.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure verem felügyeleti portál (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
11.  Keresse meg a **erőforrás-szolgáltató** válassza ki a **App Service erőforrás szolgáltató Admin**.
12. Kattintson a **forrás vezérlőkonfiguráció**.
13. Másolja és illessze be a **Alkalmazásazonosító** azokat a **ügyfél-azonosító** beviteli mezőt és **jelszó** be a **Ügyfélkulcs** beviteli mezőt a onedrive vállalati verzióhoz.
14. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-dropbox"></a>Konfigurálja a dropbox-bA

> [!NOTE]
> A DropBox-fiókot, a feladat végrehajtásához szükséges.  Kezdésként érdemes lehet egy személyes fiók helyett a szervezet olyan fiókot használjon.

1. Tallózással keresse meg a https://www.dropbox.com/developers/apps, és jelentkezzen be a DropBox-fiók használatával.
2. Kattintson a **-alkalmazás létrehozása**.

    ![Dropbox-alkalmazások][14]

3. Válassza ki **DropBox API**.
4. Állítsa a hozzáférési szintet **App mappa**.
5. Adjon meg egy **neve** az alkalmazáshoz.
![Dropbox-alkalmazás regisztrációja][15]
6. Kattintson a **-alkalmazás létrehozása**.  Most már választhat a beállításait az alkalmazás például listázása oldallal **Alkalmazáskulcs** és **alkalmazás titkos kulcs**.
7. Ellenőrizze a **App mappanév** értéke **veremben Azure App Service**.
8. Állítsa be a **OAuth 2 átirányítási URI-** kattintson **Hozzáadás**.  Alapértelmezett Azure verem telepítésében átirányítási URI-t esetén a képernyő https://portal.local.azurestack.external/tokenauthorize le egy másik tartományba helyettesítő azurestack.local a tartományt.
![Dropbox alkalmazás konfigurációja][16]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure verem felügyeleti portál (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltató** válassza ki a **App Service erőforrás szolgáltató Admin**.
11. Kattintson a **forrás vezérlőkonfiguráció**.
12. Másolja és illessze be a **Alkalmazáskulcsot** be a **ügyfél-azonosító** beviteli mezőt és **alkalmazás titkos kulcs** be a **Ügyfélkulcs** beviteli mezőt a dropbox-bA.
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

A felhasználók már a a telepítési források mint például a [folyamatos üzembe helyezés](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [helyi Git-telepítésének](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), és [mappa szinkronizálása a felhőalapú](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
