---
title: Az Azure Media Services API-hoz való hozzáféréshez azure AD-hitelesítés használata . Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja az Azure Active Directory (Azure AD) hitelesítést az Azure Media Services (AMS) API eléréséhez.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b53fca292630ef988ee1357ea50adc4d7b7e9be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162879"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Az Azure Media Services API-jának elérése a .NET használatával Azure AD-hitelesítéssel

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A windowsazure.mediaservices 4.0.0.4-es rendszertől kezdve az Azure Media Services támogatja az Azure Active Directoryn (Azure AD) alapuló hitelesítést. Ez a témakör bemutatja, hogyan használhatja az Azure AD-hitelesítést az Azure Media Services API eléréséhez a Microsoft .NET használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek az [Azure ingyenes próbaverziója.](https://azure.microsoft.com/pricing/free-trial/) 
- Egy Media Services-fiók. További információ: [Azure Media Services-fiók létrehozása az Azure Portalon.](media-services-portal-create-account.md)
- A legújabb [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) csomag.
- Az Azure Media Services API elérése az [Azure AD-hitelesítéssel című témakör ismertetése](media-services-use-aad-auth-to-access-ams-api.md). 

Ha Azure AD-hitelesítést használ az Azure Media Services szolgáltatással, kétféleképpen hitelesítheti magát:

- **A felhasználói hitelesítés** hitelesíti azt a személyt, aki az alkalmazást használja az Azure Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználónak a hitelesítő adatokat. Egy példa egy felügyeleti konzol alkalmazás, amelyet a jogosult felhasználók a kódolási feladatok vagy az élő közvetítés figyelésére használják. 
- **Az egyszerű szolgáltatás hitelesítése** hitelesíti a szolgáltatást. A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak, például webalkalmazásokat, függvényalkalmazásokat, logikai alkalmazásokat, API-kat vagy mikroszolgáltatásokat.

>[!IMPORTANT]
>Az Azure Media Service jelenleg támogatja az Azure Access Control Service hitelesítési modelljét. 2018. június 22-én azonban a hozzáférés-vezérlési engedély elavult. Azt javasoljuk, hogy mihamarabb térjen át egy Azure Active Directory-hitelesítési modellre.

## <a name="get-an-azure-ad-access-token"></a>Azure AD hozzáférési token beszerzése

Az Azure Media Services API-hoz az Azure AD-hitelesítéssel való csatlakozáshoz az ügyfélalkalmazásnak szüksége van egy Azure AD-hozzáférési jogkivonat kérésére. A Media Services .NET ügyfél SDK használatakor az Azure AD-hozzáférési jogkivonat beszerzésével kapcsolatos számos részlet et az [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) és az [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) osztályokban csomagolják és egyszerűsítik. 

Például nem kell megadnia az Azure AD-jogosultság, a Media Services erőforrás URI-ját vagy a natív Azure AD-alkalmazás adatait. Ezek a jól ismert értékek, amelyek már konfigurálva vannak az Azure AD hozzáférési jogkivonat-szolgáltató osztály. 

Ha nem használja az Azure Media Service .NET SDK szolgáltatást, javasoljuk, hogy használja az [Azure AD hitelesítési könyvtárat.](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) Az Azure AD-hitelesítési könyvtárral használandó paraméterek értékeinek beolvassa az [Azure AD-hitelesítési beállítások eléréséhez az Azure AD-hitelesítési beállítások használata című témakört.](media-services-portal-get-started-with-aad.md)

Azt is lehetősége van lecserélni az **AzureAdTokenProvider** alapértelmezett implementációját a saját implementációjára.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Az Azure Media Services .NET SDK telepítése és konfigurálása

>[!NOTE] 
>Az Azure AD-hitelesítés a Media Services .NET SDK használatával a legújabb [NuGet-csomaggal](https://www.nuget.org/packages/windowsazure.mediaservices) kell rendelkeznie. Adjon hozzá hivatkozást a **Microsoft.IdentityModel.Clients.ActiveDirectory** szerelvényhez is. Ha meglévő alkalmazást használ, adja meg a **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** kódösszeállítást. 

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studióban.
2. Az **Azure Media Services .NET SDK**telepítéséhez használja a [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet csomagot. 

    Ha hivatkozásokat szeretne hozzáadni a NuGet használatával, tegye a következő lépéseket: a **Megoldáskezelőben**kattintson a jobb gombbal a projekt nevére, majd válassza **a NuGet-csomagok kezelése parancsot.** Ezután keresse meg a **windowsazure.mediaservices elemet,** és válassza **a Telepítés lehetőséget.**
    
    – vagy –

    Futtassa a következő parancsot a Visual Studio **Package Manager konzolján.**

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adja hozzá **a forráskódhoz** való használatot.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Felhasználói hitelesítés használata

Az Azure Media Service API-hoz a felhasználói hitelesítési beállítással való csatlakozáshoz az ügyfélalkalmazásnak a következő paraméterek használatával kell Azure AD-jogkivonatot igényelnie:  

- Azure AD-bérlői végpont. A bérlői adatok az Azure Portalon lehívhatók. Vigye az egérmutatót a jobb felső sarokban lévő bejelentkezett felhasználó fölé.
- Media Services erőforrás-URI.
- Media Services (natív) alkalmazásügyfél-azonosító. 
- Media Services (natív) alkalmazás átirányítási URI. 

Ezeknek a paramétereknek az értékei az **AzureEnvironments.AzureCloudEnvironment**környezetben találhatók. Az **AzureEnvironments.AzureCloudEnvironment** állandó a .NET SDK segítője a megfelelő környezeti változóbeállítások beszerezéséhez egy nyilvános Azure Data Centerhez. 

Előre definiált környezeti beállításokat tartalmaz a Media Services csak a nyilvános adatközpontokban való eléréséhez. Szuverén vagy kormányzati felhőrégiók esetén használhatja **az AzureChinaCloudEnvironment,** **Az AzureUsGovernmentEnvironment**vagy az **AzureGermanCloudEnvironment** szolgáltatást.

A következő kódpélda létrehoz egy jogkivonatot:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
A Media Services en történő programozás elindításához létre kell hoznia egy **CloudMediaContext** példányt, amely a kiszolgáló környezetét képviseli. A **CloudMediaContext** hivatkozásokat tartalmaz a fontos gyűjtemények, beleértve a feladatok, eszközök, fájlok, hozzáférési szabályzatok és lokátorok. 

A **Media REST-szolgáltatások erőforrás URI-ját** is át kell adnia a **CloudMediaContext** konstruktornak. A Media REST-szolgáltatások erőforrásURI-jának lekérnie, jelentkezzen be az Azure Portalra, válassza ki az Azure Media Services-fiókot, válassza az **API-hozzáférés**lehetőséget, majd válassza a Csatlakozás az **Azure Media Serviceshez felhasználói hitelesítéssel**lehetőséget. 

A következő kódpélda létrehoz egy **CloudMediaContext** példányt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

A következő példa bemutatja, hogyan hozhat létre az Azure AD-jogkivonatot és a környezetet:

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
>Ha egy kivételt kap, amely azt mondja: "A távoli kiszolgáló hibát adott vissza: (401) Jogosulatlan," tekintse meg az Access [control](media-services-use-aad-auth-to-access-ams-api.md#access-control) szakasz elérése Az Azure Media Services API-azure AD-hitelesítés áttekintést.

## <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatáshitelesítés használata
    
Az Azure Media Services API-hoz való csatlakozáshoz az egyszerű szolgáltatásbeállítással a középső rétegbeli alkalmazásnak (webes API vagy webalkalmazás) a következő paraméterekkel kell azure-beli AD-jogkivonatot igényelnie:  

- Azure AD-bérlői végpont. A bérlői adatok az Azure Portalon lehívhatók. Vigye az egérmutatót a jobb felső sarokban lévő bejelentkezett felhasználó fölé.
- Media Services erőforrás-URI.
- Az Azure AD-alkalmazások értékei: az **ügyfélazonosító** és **az ügyféltitkos.**

Az **ügyfélazonosító** és az **ügyfél titkos** paramétereinek értékei az Azure Portalon találhatók. További információ: [Az Azure AD-hitelesítés az Azure Portal használatával](media-services-portal-get-started-with-aad.md)című témakörben talál.

A következő kódpélda létrehoz egy jogkivonatot az **AzureAdTokenCredentials** konstruktor használatával, amely **az AzureAdClientSymmetricKey-t** paraméterként veszi fel: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Megadhatja az **AzureAdTokenCredentials** konstruktorát, amely **az AzureAdClientCertificate paraméterként** veszi. 

Az Azure AD által használható űrlapon a tanúsítványok létrehozásáról és konfigurálásáról az [Azure AD hitelesítése tanúsítványokkal rendelkező démonalkalmazásokban – manuális konfigurációs lépések](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md)című témakörben olvashat.

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

A Media Services en történő programozás elindításához létre kell hoznia egy **CloudMediaContext** példányt, amely a kiszolgáló környezetét képviseli. A **Media REST-szolgáltatások erőforrás URI-ját** is át kell adnia a **CloudMediaContext** konstruktornak. A Media **REST-szolgáltatások erőforrás-URI-értékét** az Azure Portalon is lejuthat.

A következő kódpélda létrehoz egy **CloudMediaContext** példányt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
A következő példa bemutatja, hogyan hozhat létre az Azure AD-jogkivonatot és a környezetet:

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

Kezdje el feltölteni a [fájlokat a fiókjába.](media-services-dotnet-upload-files.md)
