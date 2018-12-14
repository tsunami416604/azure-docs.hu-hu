---
title: Alkalmazáskód – az Azure App Service használata ügyfél SSL-tanúsítvány |} A Microsoft Docs
description: Ismerje meg az ügyféltanúsítványok használata a távoli erőforrásokhoz, amelyek azt igénylik.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342017"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben

Ez az útmutató bemutatja, hogyan feltöltött vagy az alkalmazáskódban az App Service alkalmazásba importált SSL-tanúsítványok egyikével. Egy példa a használati eset az, hogy az alkalmazás hozzáfér egy tanúsítványalapú hitelesítést igénylő külső szolgáltatáshoz. 

SSL-tanúsítványok használata a kódban, ez a megközelítés lehetővé teszi, hogy az SSL használata az App Service, amelyhez szükséges az alkalmazását az a funkciók **alapszintű** szint vagy újabb. A tanúsítványfájl felvenni az alkalmazás könyvtárába, majd betölti azokat közvetlenül helyett (lásd: [alternatív: load tanúsítványt fájlként](#file)). Azonban ez a megoldás nem teszi lehetővé az alkalmazáskód vagy a fejlesztő a tanúsítvány a titkos kulcs elrejtése. Továbbá ha az alkalmazás kódja egy nyílt forráskódú tárházban, a tárházban található titkos kulccsal rendelkező tanúsítvány tartja lehetőség nem.

Ha engedélyezi az SSL-tanúsítványok kezelése az App Service, a tanúsítványok és az alkalmazás kódjában fenntartása érdekében külön-külön, és a bizalmas adatok védelme érdekében.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Le kell képeznie egy egyéni DNS-nevet a webalkalmazásra.](app-service-web-tutorial-custom-domain.md)
- [SSL-tanúsítvány feltöltése](app-service-web-tutorial-custom-ssl.md) vagy [egy App Service-tanúsítvány importálása](web-sites-purchase-ssl-web-site.md) a webalkalmazáshoz


## <a name="load-your-certificates"></a>A tanúsítványok betöltése

Feltöltött vagy importált App Service-tanúsítványt szeretne használni, először elérhetővé teszik az alkalmazás kódjában. Az ebben a `WEBSITE_LOAD_CERTIFICATES` alkalmazásbeállítást.

Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, a webalkalmazás lapjának megnyitásához.

A bal oldali navigációs sávján kattintson **SSL-tanúsítványok**.

![Feltöltött tanúsítvány](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Összes a feltöltött és importált SSL-tanúsítvány a webalkalmazás az ujjlenyomatok az itt látható. Másolja ki a használni kívánt tanúsítvány ujjlenyomatát.

A bal oldali navigációs sávján kattintson **Alkalmazásbeállítások**.

Adjon hozzá egy alkalmazás nevű beállítása `WEBSITE_LOAD_CERTIFICATES` és az értékét állítsa a tanúsítvány ujjlenyomatát. Több tanúsítvány elérhetővé, ujjlenyomat vesszővel elválasztott értékeket használja. Ahhoz, hogy minden tanúsítvány érhető el, állítsa az értékét `*`. Hajtsa végre a megfelelő vegye figyelembe, hogy ez fogja helyezni a tanúsítványt a `CurrentUser\My` tárolásához.

![Alkalmazás-beállítás konfigurálása](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Amikor végzett, kattintson a **Mentés** gombra.

A konfigurált tanúsítvánnyal már készen áll a kód által használható.

## <a name="use-certificate-in-c-code"></a>Tanúsítvány használata a C#-kódban

A tanúsítványt nem érhető el, miután érhetők el, a C#-kódban a tanúsítvány ujjlenyomatát. Az alábbi kód betölti az ujjlenyomattal rendelkező tanúsítvány `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Alternatív: betölteni a tanúsítványt fájlként

Ez a szakasz bemutatja, hogyan és a egy tanúsítványfájlt, amely az alkalmazás könyvtárába betölteni. 

Az alábbi C#-példa egy nevű tanúsítványt betölti `mycert.pfx` származó a `certs` az alkalmazás könyvtárába.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

