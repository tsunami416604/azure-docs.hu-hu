---
title: Egy feltöltött SSL-tanúsítvány használható az alkalmazás kódjában, az Azure App Service szolgáltatásban |} Microsoft Docs
description: ''
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
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047699"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Egy SSL-tanúsítvány használható az alkalmazás kódjában, az Azure App Service-ben

Ez az útmutató útmutató bemutatja, hogyan SSL-tanúsítványok feltöltött, illetve az alkalmazás kódjában az App Service alkalmazásba importált egyikét kell használnia. Egy példa a használati eset az, hogy az alkalmazás hozzáfér-e a tanúsítvány alapú hitelesítést igényel egy külső szolgáltatás. 

Ez a megközelítés a kódban SSL-tanúsítványok használatával lehetővé teszi, hogy az SSL használatát az App Service szolgáltatásban kell lennie az alkalmazás funkcióinak **alapvető** réteg vagy újabb. A tanúsítványfájl tartalmazza az alkalmazás könyvtárába, és töltse be közvetlenül helyett (lásd: [alternatív: terhelés tanúsítványt fájlként](#file)). Azonban ez a megoldás nem engedélyezi a titkos kulcsot a tanúsítvány az alkalmazás kódja vagy a fejlesztői elrejtése. Továbbá ha az alkalmazás kódjában egy nyílt forráskódú tárházban, tanúsítványt és a titkos kulcs a tárházban tartása lehetőség nem érhető el.

Ha engedélyezi az SSL-tanúsítványok kezelése az App Service, a tanúsítványok és az alkalmazás kódjában külön karbantartása, és a bizalmas adatok védelme.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató útmutató befejezése:

- [Az App Service-alkalmazás létrehozása](/azure/app-service/)
- [Egy egyéni DNS-nevet a webalkalmazás hozzárendelését](app-service-web-tutorial-custom-domain.md)
- [SSL-tanúsítvány feltöltése](app-service-web-tutorial-custom-ssl.md) vagy [tanúsítványt importálni az App Service](web-sites-purchase-ssl-web-site.md) a webalkalmazáshoz


## <a name="load-your-certificates"></a>A tanúsítványok betöltése

Olyan tanúsítvány, amely fel van töltve, vagy az App Service importálni használatához először elérhetővé teszik az alkalmazás kódjában. Az ebben a `WEBSITE_LOAD_CERTIFICATES` Alkalmazásbeállítás.

Az a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, nyissa meg a weblap alkalmazást.

Kattintson a bal oldali navigációs **SSL-tanúsítványok**.

![A tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Összes a feltöltött és importált SSL-tanúsítvány a webalkalmazás az ujjlenyomatok az itt látható. Másolja át a használni kívánt tanúsítvány ujjlenyomata.

Kattintson a bal oldali navigációs **Alkalmazásbeállítások**.

Hozzáadhat egy alkalmazást nevű beállítása `WEBSITE_LOAD_CERTIFICATES` és állítsa be az értékét a tanúsítvány ujjlenyomatát. Ahhoz, hogy több tanúsítvány elérhető, ujjlenyomat vesszővel elválasztott értékeket használja. Minden tanúsítvány válik elérhetővé, adja meg az értéket `*`. 

![Alkalmazás-beállítás konfigurálása](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Ha befejezte, kattintson a **mentése**.

A konfigurált tanúsítvánnyal készen áll a a kódot kell használja.

## <a name="use-certificate-in-c-code"></a>Tanúsítvány használata a C#-kódban

Ha a tanúsítvány érhető el, érhetők el, C#-kódban a tanúsítvány ujjlenyomatát. Az alábbi kód betölti az ujjlenyomattal rendelkező tanúsítványt `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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
## <a name="alternative-load-certificate-as-a-file"></a>Másik megoldás: a tanúsítványnak-fájlként

Ez a szakasz bemutatja, hogyan, és betölti a tanúsítványfájlt, amely az alkalmazás könyvtárába. 

Az alábbi C# példa betölti a tanúsítvány nevezik `mycert.pfx` a a `certs` könyvtárhoz, az alkalmazás tárház.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

