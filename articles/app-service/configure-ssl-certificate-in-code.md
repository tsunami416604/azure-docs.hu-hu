---
title: SSL-tanúsítvány használata az alkalmazás kódjában – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Ügyféltanúsítványok az azokat igénylő távoli erőforrásokhoz való kapcsolódáshoz.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513691"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Használjon SSL-tanúsítványt az alkalmazás kódjában Azure App Service

Az App Service-alkalmazás kódja ügyfélként működhet, és olyan külső szolgáltatáshoz férhet hozzá, amelyhez tanúsítványalapú hitelesítés szükséges. Ez a útmutató bemutatja, hogyan használhatók a nyilvános vagy privát tanúsítványok az alkalmazás kódjában.

A kódban szereplő tanúsítványok használatának módszere a App Service SSL funkciójának használatát igényli, amelyhez az alkalmazásnak **alapszintű vagy magasabb szintűnek** kell lennie. Azt is megteheti, hogy [felveszi a tanúsítványfájl alkalmazást az alkalmazás-adattárba](#load-certificate-from-file), de nem ajánlott eljárás a privát tanúsítványokhoz.

Ha App Service felügyeli az SSL-tanúsítványokat, megtarthatja a tanúsítványokat és az alkalmazás kódját, és gondoskodhat a bizalmas adatok védelméről is.

## <a name="prerequisites"></a>Előfeltételek

A következő útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Az ujjlenyomat megkeresése

A <a href="https://portal.azure.com" target="_blank">Azure Portal</a>bal oldali menüjében válassza a **app Services** >  **\<app-Name >** elemet.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások**, majd a **titkos kulcsú tanúsítványok (. pfx)** vagy a **nyilvános kulcsú tanúsítványok (. cer)** lehetőséget.

Keresse meg a használni kívánt tanúsítványt, és másolja az ujjlenyomatot.

![A tanúsítvány ujjlenyomatának másolása](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>A tanúsítvány betöltése

Ha egy tanúsítványt szeretne használni az alkalmazás kódjában, adja hozzá az ujjlenyomatát a `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállításához a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Az összes tanúsítvány elérhetővé tételéhez állítsa `*`értékre az értéket.

> [!NOTE]
> Ez a beállítás a legtöbb díjszabási szinten elhelyezi a megadott tanúsítványokat a [jelenlegi Settingsnew](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -tárolóban, de az **elkülönített** szinten (azaz az alkalmazás [app Service Environmentban](environment/intro.md)fut) a tanúsítványokat a [helyi Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) helyezi el. Store.
>

A konfigurált tanúsítványok most már készen állnak a kód használatára.

## <a name="load-the-certificate-in-code"></a>A tanúsítvány betöltése a kódban

Miután a tanúsítvány elérhetővé válik, a Tanúsítvány ujjlenyomata alapján férhet hozzá a C# kódban. A következő kód betölt egy tanúsítványt az ujjlenyomattal `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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
## <a name="load-certificate-from-file"></a>Tanúsítvány betöltése fájlból

Ha be kell töltenie egy tanúsítványfájl-fájlt az alkalmazás könyvtárából, akkor jobb, ha például a [git](deploy-local-git.md)helyett a [FTPS](deploy-ftp.md) használatával tölti fel. A bizalmas adatokat, például egy privát tanúsítványt a forrás-ellenőrzésen kívül kell tartania.

Annak ellenére, hogy a fájlt közvetlenül a .NET-kódban tölti be, a függvénytár továbbra is ellenőrzi, hogy az aktuális felhasználói profil be van-e töltve. Az aktuális felhasználói profil betöltéséhez állítsa be a `WEBSITE_LOAD_USER_PROFILE` alkalmazás beállítását a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>következő parancsával.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

A beállítás beállítása után a következő C# példa egy `mycert.pfx` nevű tanúsítványt tölt be az alkalmazás adattárának `certs` könyvtárából.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá tétele SSL-kötéssel](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 betartatása](configure-ssl-bindings.md#enforce-tls-versions)
* [Gyakori kérdések: App Service tanúsítványok](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
