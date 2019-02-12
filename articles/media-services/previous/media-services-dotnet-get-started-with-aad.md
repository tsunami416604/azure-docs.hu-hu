---
title: Az Azure AD hitelesítési el az Azure Media Services API .NET-tel |} A Microsoft Docs
description: Ez a témakör bemutatja az Azure Active Directory (Azure AD-) hitelesítés használata a .NET-tel az Azure Media Services (AMS) API eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 3d27407b329168fe6bc78def988cb350d4eeae83
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990308"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Az Azure AD-hitelesítés használata az Azure Media Services API .NET-keretrendszerrel történő eléréséhez

Windowsazure.mediaservices 4.0.0.4 verziótól kezdődően az Azure Media Services támogatja az Azure Active Directory (Azure AD-) alapú hitelesítést. Ez a témakör bemutatja, hogyan eléréséhez az Azure Media Services API a Microsoft .NET-tel az Azure AD-hitelesítés használható.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [hozzon létre egy Azure Media Services-fiók az Azure portal használatával](media-services-portal-create-account.md).
- A legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) csomagot.
- A témakörre való ismerkedés során bizonyulhat [elérése az Azure Media Services API Azure AD-hitelesítés – áttekintés](media-services-use-aad-auth-to-access-ams-api.md). 

Ha az Azure Media Services Azure AD-hitelesítést használ, két módon hitelesítheti:

- **Felhasználói hitelesítés** hitelesíti magát az a személy, aki használja az alkalmazást kezelheti az Azure Media Services-erőforrások. Az interaktív alkalmazás először kéri a felhasználótól a hitelesítő adatokat. Ilyen például, a kódolási feladatok figyelésére, vagy az élő adások online közvetítése jogosult felhasználók által használt felügyeleti konzolalkalmazást. 
- **Egyszerű szolgáltatásnév hitelesítése** szolgáltatás hitelesíti. Alkalmazásokat, amelyek gyakran használják ezt a hitelesítési módszert démonszolgáltatásokat, a középső rétegű services vagy az ütemezett feladatok, például a web apps, a függvényalkalmazások, a logic apps, API-k vagy mikroszolgáltatás-alapú alkalmazások.

>[!IMPORTANT]
>Az Azure Media Services jelenleg támogatja az Azure Access Control Service-hitelesítési modellre. Azonban hozzáférés-vezérlés engedélyezési fog 2018. június 22 elavulttá válik. Azt javasoljuk, hogy telepít át egy Azure Active Directory-hitelesítési modellre minél hamarabb.

## <a name="get-an-azure-ad-access-token"></a>Az Azure AD hozzáférési token beszerzése

Ha csatlakozni szeretne az Azure Media Services API Azure AD-hitelesítés, az ügyfélalkalmazás kell az Azure AD hozzáférési jogkivonat kérése. A Media Services .NET client SDK használatakor az Azure AD hozzáférési jogkivonat beszerzése részleteit számos burkolt be, és Önnek az egyszerűsített a [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) és [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)osztályokat. 

Például, hogy nem kell adja meg az Azure AD-szolgáltatót, a Media Services-erőforrás URI-t vagy a natív Azure AD alkalmazás részleteit. Ezek a jól ismert értékek, amelyek már az Azure AD hozzáférési jogkivonat-szolgáltató osztály alapján. 

Ha nem az Azure Media Service .NET SDK-t használ, azt javasoljuk, hogy használja a [Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md). Használatával lekérjük az értékeket a paraméterek, amelyek az Azure AD Authentication Library szüksége, tekintse meg a [az Azure portal használata az Azure AD-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md).

Lehetősége is van, hogy az alapértelmezett megvalósítása a **AzureAdTokenProvider** a saját implementációjához.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Telepítse és konfigurálja az Azure Media Services .NET SDK

>[!NOTE] 
>A Media Services .NET SDK használatával az Azure AD-hitelesítés használatához szüksége lesz a legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) csomagot. Ezenkívül vegyen fel egy hivatkozást a **Microsoft.IdentityModel.Clients.ActiveDirectory** sestavení. Ha egy meglévő alkalmazást használ, a **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sestavení. 

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studióban.
2. Használja a [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet-csomag telepítéséhez **Azure Media Services .NET SDK**. 

    Hivatkozások NuGet használatával hozzáadásához tegye a következőket: a **Megoldáskezelőben**, és kattintson a jobb gombbal a projekt nevére, majd válassza ki **NuGet-csomagok kezelése**. Ezután keresse meg **windowsazure.mediaservices** válassza **telepítése**.
    
    – vagy –

    Futtassa a következő parancsot **Package Manager Console** a Visual Studióban.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adjon hozzá **használatával** a forráskódhoz.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Felhasználói hitelesítés használata

Ha csatlakozni szeretne az Azure Media Service API-t a felhasználó-hitelesítési lehetőséget, az ügyfélalkalmazás kell kérése az Azure AD-token a következő paraméterek használatával:  

- Az Azure AD-bérlő végpont. A bérlői kapcsolatos információkat az Azure Portalról kérhető. A jobb felső sarokban a bejelentkezett felhasználó a kurzort.
- Media Services-erőforrás URI-t.
- A Media Services (natív) alkalmazás ügyfél-azonosító. 
- A Media Services (natív) alkalmazás átirányítási URI-t. 

Ezek a paraméterek értékei található **AzureEnvironments.AzureCloudEnvironment**. A **AzureEnvironments.AzureCloudEnvironment** állandó van a .NET SDK-val lekérheti a megfelelő környezetet egy nyilvános Azure-adatközpontok környezetiváltozó-beállításainak a segítő. 

Előre definiált környezeti beállítások eléréséhez a Media Services csak a nyilvános adatközpontokban tartalmazza. A független vagy a kormányzati felhő-régiók, használhatja **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**, vagy **AzureGermanCloudEnvironment** jelölik.

Az alábbi példakód létrehoz egy jogkivonatot:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
A Media Services elleni programozási indításához szeretne létrehozni egy **CloudMediaContext** példányt, amely a kiszolgáló helyi jelöli. A **CloudMediaContext** többek között a feladatok, objektumok, fájlok, hozzáférési házirendek és keresők fontos gyűjtemények mutató hivatkozásokat tartalmaz. 

Is kell átadni a **erőforrás URI-t a Media Services-REST** , a **CloudMediaContext** konstruktor. Beolvasni az erőforrás URI-t REST médiaszolgáltatások, jelentkezzen be az Azure Portalon, válassza ki az Azure Media Services-fiók, jelölje be a **API-hozzáférés**, majd válassza ki **csatlakozhat az Azure Media Services és a felhasználói hitelesítés**. 

Az alábbi példakód létrehoz egy **CloudMediaContext** példány:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Az alábbi példa bemutatja, hogyan hozhat létre az Azure ad-ben és a környezetet:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
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
>Ha a kivételt, amely szerint "a távoli kiszolgáló hibát adott vissza: Jogosulatlan (401-es)"tekintse meg a [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) elérése az Azure Media Services API Azure AD-hitelesítés – áttekintés szakaszában.

## <a name="use-service-principal-authentication"></a>Használja az egyszerű szolgáltatásnév hitelesítése
    
Csatlakozni az Azure Media Services API a szolgáltatást a középső rétegbeli alkalmazás egyszerű lehetőséggel kell (web API-t vagy webes alkalmazás) kér az Azure AD-token, a következő paraméterekkel:  

- Az Azure AD-bérlő végpont. A bérlői kapcsolatos információkat az Azure Portalról kérhető. A jobb felső sarokban a bejelentkezett felhasználó a kurzort.
- Media Services-erőforrás URI-t.
- Az Azure AD-alkalmazás értékeire: a **ügyfél-azonosító** és **titkos Ügyfélkód**.

A tartozó értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód** paramétereket az Azure Portalon tekintheti meg. További információkért lásd: [Ismerkedés az Azure AD-hitelesítés az Azure portal használatával](media-services-portal-get-started-with-aad.md).

Az alábbi példakód létrehoz egy jogkivonat használatával a **AzureAdTokenCredentials** konstruktort, amely a **AzureAdClientSymmetricKey** paramétert: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Azt is megadhatja a **AzureAdTokenCredentials** konstruktort, amely a **AzureAdClientCertificate** paraméterként. 

Hogyan hozhat létre, és a egy tanúsítvány konfigurálása az Azure AD által felhasználható formában kapcsolatos útmutatásért lásd: [tanúsítványok – manuális konfigurációs lépés végrehajtásával démon alkalmazások az Azure ad hitelesítése](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

A Media Services elleni programozási indításához szeretne létrehozni egy **CloudMediaContext** példányt, amely a kiszolgáló helyi jelöli. Is kell átadni a **erőforrás URI-t a Media Services-REST** , a **CloudMediaContext** konstruktor. Beszerezheti a **erőforrás URI-t a Media Services-REST** érték is az Azure Portalról.

Az alábbi példakód létrehoz egy **CloudMediaContext** példány:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Az alábbi példa bemutatja, hogyan hozhat létre az Azure ad-ben és a környezetet:

    namespace AzureADAuthSample
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

Ismerkedés a [fájlokat tölthet fel a fiók](media-services-dotnet-upload-files.md).
