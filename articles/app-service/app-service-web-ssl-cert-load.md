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
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475099"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben

Ez az útmutató bemutatja, hogyan nyilvános vagy privát tanúsítványok használata az alkalmazás kódjában. Egy példa a használati eset az, hogy az alkalmazás hozzáfér egy tanúsítványalapú hitelesítést igénylő külső szolgáltatáshoz.

Tanúsítványok használata a kódban, ez a megközelítés lehetővé teszi, hogy az SSL használata az App Service, amelyhez szükséges az alkalmazását az a funkciók **alapszintű** szint vagy újabb. Lehetőségként [a tanúsítványfájl bevonni az adattár](#load-certificate-from-file), de ez nem ajánlott a privát tanúsítványok.

Ha engedélyezi az SSL-tanúsítványok kezelése az App Service, a tanúsítványok és az alkalmazás kódjában fenntartása érdekében külön-külön, és a bizalmas adatok védelme érdekében.

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Privát tanúsítvány feltöltéséhez előtt ellenőrizze, hogy [összes követelményének megfelel](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), azzal a különbséggel, hogy ennek nem kell a kiszolgáló-hitelesítési kell konfigurálni.

Ha készen áll a feltöltendő, futtassa a következő parancsot a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítványt elérhetővé](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

A rendszer támogatja a nyilvános tanúsítványokat az *.cer* formátumban. A nyilvános tanúsítvány feltöltése a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, és keresse meg az alkalmazáshoz.

Kattintson a **SSL-beállítások** > **nyilvános tanúsítványokat (.cer)**  > **nyilvános tanúsítvány feltöltése** az alkalmazás bal oldali navigációs paneljéről.

A **neve**, írja be a tanúsítvány nevét. A **CER-tanúsítványfájl**, válassza ki a CER-fájlt.

Kattintson a **Feltöltés** gombra.

![Nyilvános tanúsítvány feltöltése](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Miután feltöltötte a tanúsítványt, másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítványt elérhetővé](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Az App Service-tanúsítvány importálása

Lásd: [vásárlása és konfigurálása az Azure App Service SSL-tanúsítvány](web-sites-purchase-ssl-web-site.md).

A tanúsítvány importálása után másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítványt elérhetővé](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Tegyen elérhetővé a tanúsítvány

A kód egy feltöltött vagy importált tanúsítványt használ, győződjön meg arról, az ujjlenyomat kódjával elérhető a `WEBSITE_LOAD_CERTIFICATES` Alkalmazásbeállítás, a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Ahhoz, hogy minden tanúsítvány érhető el, állítsa az értékét `*`.

> [!NOTE]
> Ezzel a beállítással a megadott tanúsítvány helyezi el a [aktuális User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) áruház legtöbb tarifacsomagok, de a **elkülönített** szint (pl. alkalmazás fut egy [App Service Environment-környezet](environment/intro.md)), a tanúsítványokat helyezi a [helyi Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) tárolásához.
>

![Alkalmazás-beállítás konfigurálása](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Amikor végzett, kattintson a **Mentés** gombra.

A konfigurált tanúsítványok immár készen áll a kód által használható.

## <a name="load-the-certificate-in-code"></a>A kódban a tanúsítvány betöltése

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
## <a name="load-certificate-from-file"></a>Betöltés tanúsítvány fájlból

Ha szeretne egy tanúsítványfájlt betölteni az alkalmazás könyvtárába, akkor célszerűbb használatával töltse fel [FTPS](deploy-ftp.md) helyett [Git](deploy-local-git.md), például. Bizalmas adatokat – például egy privát tanúsítványt verziókövetés kívül kell tartania.

Annak ellenére, hogy a .NET-kód közvetlenül a tölt be a fájlt, a a tár továbbra is ellenőrzi, ha a jelenlegi felhasználói profil betöltése. A jelenlegi felhasználói profil betöltése, állítsa be a `WEBSITE_LOAD_USER_PROFILE` a következő paranccsal, az Alkalmazásbeállítás a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Ha ez a beállítás, a következő C# példa betölti a tanúsítvány nevezik `mycert.pfx` a a `certs` az alkalmazás könyvtárába.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
