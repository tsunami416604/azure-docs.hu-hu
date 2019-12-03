---
title: SSL-tanúsítvány használata a kódban
description: Ismerje meg, hogyan használhatja az Ügyféltanúsítványok használatát a kódban. Hitelesítse magát a távoli erőforrásokkal egy ügyféltanúsítvány használatával, vagy futtasson titkosítási feladatokat.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671898"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>SSL-tanúsítvány használata a kódban Azure App Service

Az alkalmazás kódjában elérheti a [app Servicehoz hozzáadott nyilvános vagy privát tanúsítványokat](configure-ssl-certificate.md). Az alkalmazás kódja ügyfélként működhet, és olyan külső szolgáltatáshoz férhet hozzá, amelyhez tanúsítványalapú hitelesítés szükséges, vagy szükség lehet titkosítási feladatok végrehajtására. Ez a útmutató bemutatja, hogyan használhatók a nyilvános vagy privát tanúsítványok az alkalmazás kódjában.

A kódban szereplő tanúsítványok használatának módszere a App Service SSL funkciójának használatát igényli, amelyhez az alkalmazásnak **alapszintű vagy magasabb szintűnek** kell lennie. Ha az alkalmazás **ingyenes** vagy **megosztott** szinten van, akkor az alkalmazás adattárában is megadhatja [a tanúsítványfájl-fájlt](#load-certificate-from-file).

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

## <a name="make-the-certificate-accessible"></a>A tanúsítvány elérhetővé tétele

Ha az alkalmazás kódjában szeretne hozzáférni egy tanúsítványhoz, adja hozzá az ujjlenyomatát a `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállításához a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Az összes tanúsítvány elérhetővé tételéhez állítsa `*`értékre az értéket.

## <a name="load-certificate-in-windows-apps"></a>Tanúsítvány betöltése Windows-alkalmazásokban

A `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállítása lehetővé teszi, hogy a megadott tanúsítványok elérhetők legyenek a Windows által üzemeltetett alkalmazás számára a Windows tanúsítványtárolóban, és a hely az [árképzési szinttől](overview-hosting-plans.md)függ:

- **Elkülönített** réteg – [helyi Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Minden egyéb réteg – az [aktuális Settingsnew](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

A C# kódban a Tanúsítvány ujjlenyomata alapján férhet hozzá a tanúsítványhoz. A következő kód betölt egy tanúsítványt az ujjlenyomattal `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

A Java-kódban a tulajdonos köznapi név mezővel férhet hozzá a tanúsítványhoz a "Windows-MY" áruházból (lásd: [nyilvános kulcsú tanúsítvány](https://en.wikipedia.org/wiki/Public_key_certificate)). A következő kód bemutatja, hogyan tölthető be egy titkos kulcsú tanúsítvány:

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

Azokon a nyelveken, amelyeken nem támogatott vagy nem nyújt elegendő támogatást a Windows tanúsítványtárolóhoz, lásd: [tanúsítvány betöltése fájlból](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Tanúsítvány betöltése Linux-alkalmazásokban

A `WEBSITE_LOAD_CERTIFICATES` Alkalmazásbeállítások lehetővé teszik, hogy a megadott tanúsítványok elérhetők legyenek a Linux által üzemeltetett alkalmazások számára (beleértve az egyéni tároló alkalmazásait) fájlként. A fájlok a következő könyvtárakban találhatók:

- Privát tanúsítványok – `/var/ssl/private` (`.p12` fájlok)
- Nyilvános tanúsítványok – `/var/ssl/certs` (`.der` fájlok)

A tanúsítványfájl neve a tanúsítvány ujjlenyomatai megfelelnek. A következő C# kód bemutatja, hogyan tölthető be egy nyilvános tanúsítvány egy Linux-alkalmazásban.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Az SSL-tanúsítványoknak a Node. js, a PHP, a Python, a Java vagy a Ruby fájlból való betöltéséről a megfelelő nyelvi vagy webes platform dokumentációjában talál további információt.

## <a name="load-certificate-from-file"></a>Tanúsítvány betöltése fájlból

Ha manuálisan feltöltött tanúsítványfájl betöltését szeretné elvégezni, akkor jobb, ha például a [git](deploy-local-git.md)helyett a [FTPS](deploy-ftp.md) használatával tölti fel a tanúsítványt. A bizalmas adatokat, például egy privát tanúsítványt a forrás-ellenőrzésen kívül kell tartania.

> [!NOTE]
> A Windows ASP.NET és ASP.NET Core akkor is el kell érnie a tanúsítványtárolót, ha fájlból tölt be tanúsítványt. Egy Windows .NET-alkalmazásban lévő tanúsítványfájl betöltéséhez töltse be az aktuális felhasználói profilt a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>következő parancsával:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> A kódban szereplő tanúsítványok használatának módszere a App Service SSL funkciójának használatát igényli, amelyhez az alkalmazásnak **alapszintű vagy magasabb szintűnek** kell lennie.

Az alábbi C# példa egy nyilvános tanúsítványt tölt be egy relatív elérési útról az alkalmazásban:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Az SSL-tanúsítványoknak a Node. js, a PHP, a Python, a Java vagy a Ruby fájlból való betöltéséről a megfelelő nyelvi vagy webes platform dokumentációjában talál további információt.

## <a name="more-resources"></a>További források

* [Egyéni DNS-név biztonságossá tétele SSL-kötéssel](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 betartatása](configure-ssl-bindings.md#enforce-tls-versions)
* [Gyakori kérdések: App Service tanúsítványok](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
