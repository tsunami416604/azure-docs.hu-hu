---
title: Ssl-tanúsítvány használata kódban
description: További információ az ügyféltanúsítványok használatáról a kódban. Hitelesítsen távoli erőforrásokkal egy ügyféltanúsítvánnyal, vagy futtasson velük kriptográfiai feladatokat.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671898"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Ssl-tanúsítvány használata a kódban az Azure App Service-ben

Az alkalmazáskódban hozzáférhet az [App Service-hez hozzáadott nyilvános vagy privát tanúsítványokhoz.](configure-ssl-certificate.md) Az alkalmazáskód ügyfélként működhet, és hozzáférhet egy külső szolgáltatáshoz, amely tanúsítványhitelesítést igényel, vagy kriptográfiai feladatokat kell végrehajtania. Ez az útmutató bemutatja, hogyan használhatja a nyilvános vagy privát tanúsítványokat az alkalmazáskódban.

Ez a megközelítés a tanúsítványok használata a kódban él az SSL-funkció az App Service, amely előírja, hogy az alkalmazás **az alapszintű** vagy annál magasabb szintű. Ha az alkalmazás **ingyenes** vagy **megosztott** szinten van, [a tanúsítványfájlt is felveheti az alkalmazástárházba.](#load-certificate-from-file)

Ha hagyja, hogy az App Service kezelje az SSL-tanúsítványokat, a tanúsítványokat és az alkalmazáskódot külön-külön karbantarthatja, és megvédheti a bizalmas adatokat.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Az ujjlenyomat megkeresése

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások lehetőséget,** majd válassza a **személyes kulcstanúsítványok (.pfx)** vagy **a nyilvános kulcsos tanúsítványok (.cer)** lehetőséget.

Keresse meg a használni kívánt tanúsítványt, és másolja az ujjlenyomatot.

![A tanúsítvány ujjlenyomatának másolása](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>A tanúsítvány hozzáférhetővé tétele

Ha meg szeretne egy tanúsítványt elérni az `WEBSITE_LOAD_CERTIFICATES` alkalmazáskódban, adja hozzá az ujjlenyomatát az alkalmazásbeállításhoz a Következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellben:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Ha az összes tanúsítványt elérhetővé `*`szeretné tenni, állítsa az értéket a értékre.

## <a name="load-certificate-in-windows-apps"></a>Tanúsítvány betöltése windowsos alkalmazásokba

Az `WEBSITE_LOAD_CERTIFICATES` alkalmazásbeállítás elérhetővé teszi a megadott tanúsítványokat a Windows által üzemeltetett alkalmazás számára a Windows tanúsítványtárolóban, és a hely a [tarifacsomagtól](overview-hosting-plans.md)függ:

- **Elkülönített** réteg - a [Helyi számítógép\Saját](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)mappában. 
- Az összes többi szint – az [Aktuális felhasználó\Saját](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)mappában.

A C# kódban a tanúsítvány ujjlenyomatával érhető el. A következő kód betölt egy `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`tanúsítványt az ujjlenyomattal.

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

Java-kód esetén a tanúsítványt a "Windows-MY" tárolóból a Tulajdonos közneve mezőben érheti el (lásd: [Nyilvános kulcs tanúsítvány).](https://en.wikipedia.org/wiki/Public_key_certificate) A következő kód bemutatja, hogyan kell betölteni egy személyes kulcs tanúsítvány:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Azokat a nyelveket, amelyek nem támogatják a Windows tanúsítványtárolót, vagy nem nyújtanak megfelelő támogatást, olvassa el a [Tanúsítvány fájlból betöltése](#load-certificate-from-file)című témakört.

## <a name="load-certificate-in-linux-apps"></a>Tanúsítvány betöltése Linux-alkalmazásokban

Az `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállításai elérhetővé teszik a megadott tanúsítványokat a Linux által üzemeltetett alkalmazások (beleértve az egyéni tárolóalkalmazásokat is) fájlokként. A fájlok a következő könyvtárak alatt találhatók:

- Privát tanúsítványok `/var/ssl/private` `.p12` - (fájlok)
- Nyilvános tanúsítványok `/var/ssl/certs` `.der` - (fájlok)

A tanúsítványfájl nevei a tanúsítvány ujjlenyomatai. A következő C# kód bemutatja, hogyan tölthet be egy nyilvános tanúsítványt egy Linux-alkalmazásban.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha meg szeretné tekinteni, hogyan tölthet be SSL-tanúsítványt node.js, PHP, Python, Java vagy Ruby fájlból, tekintse meg az adott nyelv vagy webes platform dokumentációját.

## <a name="load-certificate-from-file"></a>Tanúsítvány betöltése fájlból

Ha manuálisan kell betöltenie egy manuálisan feltöltött tanúsítványfájlt, akkor jobb, ha például a [Git](deploy-local-git.md)helyett [FTPS-sel](deploy-ftp.md) tölti fel a tanúsítványt. A bizalmas adatokat, például a magántanúsítványokat a forrásellenőrzésen kívülien kell tartania.

> [!NOTE]
> a Windows ASP.NET és ASP.NET Core rendszernek akkor is hozzá kell férnie a tanúsítványtárolóhoz, ha fájlból tölt be tanúsítványt. Ha tanúsítványfájlt szeretne betölteni egy Windows .NET alkalmazásba, töltse be az aktuális felhasználói profilt a következő paranccsal a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>ben:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Ez a megközelítés a tanúsítványok használata a kódban él az SSL-funkció az App Service, amely előírja, hogy az alkalmazás **az alapszintű** vagy annál magasabb szintű.

A következő C# példa betölt egy nyilvános tanúsítványt az alkalmazás relatív elérési útjáról:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Ha meg szeretné tekinteni, hogyan tölthet be SSL-tanúsítványt node.js, PHP, Python, Java vagy Ruby fájlból, tekintse meg az adott nyelv vagy webes platform dokumentációját.

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá tétele SSL-kötéssel](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [GYAKORI KÉRDÉSEK : App Service-tanúsítványok](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
