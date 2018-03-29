---
title: Azure Media Services API a .NET eléréséhez használja az Azure AD hitelesítési |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan Azure Media Services (AMS) API-t .NET eléréséhez Azure Active Directory (Azure AD-) hitelesítés használatára.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: 8817bb0da526769470e96b63b9c80620309757bf
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Azure Media Services API a .NET eléréséhez használja az Azure AD-alapú hitelesítés

Windowsazure.mediaservices 4.0.0.4 verziótól kezdődően Azure Media Services Azure Active Directory (Azure AD) alapuló hitelesítést támogatja. Ez a témakör bemutatja, hogyan használják az Azure AD-alapú hitelesítés Azure Media Services API a Microsoft .NET eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [hozzon létre egy Azure Media Services-fiók az Azure portál használatával](media-services-portal-create-account.md).
- A legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) csomag.
- A témakör ismeretét [eléréséhez Azure Media Services API-t az AAD-hitelesítés áttekintése](media-services-use-aad-auth-to-access-ams-api.md). 

Ha az Azure AD-alapú hitelesítés az Azure Media Services használata esetén az alábbi két módszer egyikével hitelesítheti:

- **Felhasználó hitelesítése** hitelesíti a személy, aki használja az alkalmazás interakciót Azure Media Services-erőforrásokkal. Az interaktív alkalmazás először kell kérni a felhasználót a hitelesítő adatokat. Példa: egy kódolási feladatok figyelése, vagy live streaming jogosult felhasználók által használt felügyeleti konzol alkalmazást. 
- **Szolgáltatás egyszerű hitelesítési** szolgáltatás hitelesíti. Alkalmazásokat, amelyek általában arra használják ezt a hitelesítési módszert démon szolgáltatások, a középső rétegbeli szolgáltatások vagy az ütemezett feladatok, például webalkalmazások, függvény alkalmazások, a logic apps, API-k vagy mikroszolgáltatások futó alkalmazások.

>[!IMPORTANT]
>Az Azure Media Services jelenleg olyan Azure Access Control Service hitelesítési modellt. Azonban a hozzáférés-vezérlés engedélyezési érintetlen a 2018. június 22 elavulttá válik. Azt javasoljuk, hogy telepítse át az Azure Active Directory hitelesítési modell lehető legrövidebb időn belül.

## <a name="get-an-azure-ad-access-token"></a>Az Azure AD hozzáférési jogkivonat beszerzése

Szeretne csatlakozni az Azure Media Services API-t az Azure AD-alapú hitelesítés, az ügyfélalkalmazás kell egy Azure AD hozzáférési jogkivonatot kérni. A Media Services .NET SDK ügyfél használata esetén az Azure AD hozzáférési tokent beszerzésére vonatkozó részleteket számos becsomagolt, és meg az egyszerűsített a [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) és [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)osztályok. 

Például, hogy nem kell adja meg az Azure AD-szolgáltatóként, a Media Services erőforrás URI vagy a natív Azure AD alkalmazás részletei. Ezek a jól ismert értékek, amelyek már az Azure AD hozzáférési jogkivonat-szolgáltató osztály. 

Ha az Azure Media Service .NET SDK-t nem használ, azt javasoljuk, hogy használja a [Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md). Ahhoz, hogy az értékeket a paraméterek, amelyekre szüksége van az Azure AD Authentication Library használandó, lásd: [az Azure AD hitelesítési beállítások eléréséhez használja az Azure-portálon](media-services-portal-get-started-with-aad.md).

Lehetősége is van, hogy az alapértelmezett végrehajtása a **AzureAdTokenProvider** a saját példánnyal.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Telepítse és konfigurálja az Azure Media Services .NET SDK

>[!NOTE] 
>A Media Services .NET SDK-val az Azure AD-alapú hitelesítés használatához meg kell rendelkeznie a legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) csomag. Továbbá adja hozzá egy hivatkozást a **Microsoft.IdentityModel.Clients.ActiveDirectory** szerelvény. Ha egy meglévő alkalmazást használ, a **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** szerelvény. 

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studio.
2. Használja a [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet-csomag telepítése **Azure Media Services .NET SDK**. 

    NuGet segítségével hivatkozások hozzáadásához tegye a következőket: a **Megoldáskezelőben**, kattintson a jobb gombbal a projekt nevére, majd válassza ki **kezelése NuGet-csomagok**. Ezután keresse meg **windowsazure.mediaservices** válassza **telepítése**.
    
    – vagy –

    A következő parancsot **Csomagkezelő konzol** a Visual Studióban.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adja hozzá **használatával** számára a forráskódot.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Hitelesítés használatára

Szeretne csatlakozni az Azure Media Service API a felhasználói hitelesítést választja, az ügyfélalkalmazás van szüksége az Azure AD-tokent kérése a következő paraméterek használatával:  

- Azure AD-bérlő végpont. A bérlői adatait az Azure portálról lehet beolvasni. A jobb felső sarkában a bejelentkezett felhasználó mutasson.
- A Media Services erőforrás URI azonosítója.
- A Media Services (natív) alkalmazás ügyfél-azonosító. 
- Media Services (natív) alkalmazás átirányítási URI-címe. 

Ezek a paraméterek értékeit található **AzureEnvironments.AzureCloudEnvironment**. A **AzureEnvironments.AzureCloudEnvironment** állandó van a .NET SDK segíthetnek a megfelelő környezet egy nyilvános Azure-adatközpont környezetiváltozó-beállításainak segítő. 

Csak a nyilvános adatközpontokban Media Services eléréséhez előre megadott környezeti beállításokat tartalmaz. A állami vagy kormányzati felhőalapú területeket, használhat **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, vagy **AzureGermanCloudEnvironment** kulcsattribútumokkal.

Az alábbi példakód létrehoz egy tokent:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Media Services elleni programozási elindításához kell létrehoznia egy **CloudMediaContext** példányt jelenti. a kiszolgáló a környezetben. A **CloudMediaContext** többek között a feladatok, eszközök, fájlok, hozzáférési házirendek és keresők fontos gyűjtemények mutató hivatkozásokat tartalmaz. 

Is kell átadni a **erőforrás URI azonosítója a Media Services-REST** számára a **CloudMediaContext** konstruktor. Válassza ki ahhoz, hogy az erőforrás URI azonosítója, a többi médiaszolgáltatások, jelentkezzen be az Azure-portálon, válassza ki az Azure Media Services-fiók, **API-hozzáférés**, majd válassza ki **csatlakozás az Azure Media Services és a felhasználói hitelesítés**. 

Az alábbi példakód létrehoz egy **CloudMediaContext** példány:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

A következő példa bemutatja, hogyan hozzon létre az Azure AD és a környezetben:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Ha egy kivételt, amely szerint "a távoli kiszolgáló hibát adott vissza: (401) nem engedélyezett," tekintse meg a [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) Azure Media Services API elérése az Azure AD hitelesítési – Áttekintés szakasza.

## <a name="use-service-principal-authentication"></a>Szolgáltatás egyszerű hitelesítés használata
    
Csatlakozás az Azure Media Services API a szolgáltatás egyszerű a beállítást, a középső rétegbeli alkalmazás (webes API-t vagy a webes alkalmazás) kell kérések az Azure AD-tokent a következő paraméterekkel:  

- Azure AD-bérlő végpont. A bérlői adatait az Azure portálról lehet beolvasni. A jobb felső sarkában a bejelentkezett felhasználó mutasson.
- A Media Services erőforrás URI azonosítója.
- Az Azure AD alkalmazás értékeket: a **ügyfél-azonosító** és **ügyfélkulcs**.

Az értékek a **ügyfél-azonosító** és **ügyfélkulcs** paraméterek az Azure portálon található. További információkért lásd: [Ismerkedés az Azure AD-alapú hitelesítés az Azure portál használatával](media-services-portal-get-started-with-aad.md).

Az alábbi példakód egy token használatával hozza létre a **AzureAdTokenCredentials** konstruktort **AzureAdClientSymmetricKey** paramétert: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Azt is megadhatja a **AzureAdTokenCredentials** konstruktort **AzureAdClientCertificate** paraméterként. 

Hozzon létre és tanúsítvány konfigurálása az Azure AD által felhasználható formában utasításokért lásd: [hitelesítéséhez az Azure AD-démon alkalmazásokban tanúsítványokkal - kézi konfigurálás lépéseit](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Media Services elleni programozási elindításához kell létrehoznia egy **CloudMediaContext** példányt jelenti. a kiszolgáló a környezetben. Is kell átadni a **erőforrás URI azonosítója a Media Services-REST** számára a **CloudMediaContext** konstruktor. Beszerezheti a **erőforrás URI azonosítója a Media Services-REST** érték is az Azure portálról.

Az alábbi példakód létrehoz egy **CloudMediaContext** példány:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
A következő példa bemutatja, hogyan hozzon létre az Azure AD és a környezetben:

    namespace AADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlok feltöltése a fiókjához](media-services-dotnet-upload-files.md).
