---
title: Ügyfél SSL-tanúsítvány használata az alkalmazás kódjában – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Ügyféltanúsítványok az azokat igénylő távoli erőforrásokhoz való kapcsolódáshoz.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066998"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Használjon SSL-tanúsítványt az alkalmazás kódjában Azure App Service

Ez a útmutató bemutatja, hogyan használhatók a nyilvános vagy privát tanúsítványok az alkalmazás kódjában. Példa a használati esetre, hogy az alkalmazás egy olyan külső szolgáltatáshoz fér hozzá, amelyhez tanúsítványalapú hitelesítés szükséges.

A kódban szereplő tanúsítványok használatának módszere a App Service SSL funkciójának használatát igényli, amelyhez az alkalmazásnak alapszintű vagy magasabb szintűnek kell lennie. Azt is megteheti, hogy felveszi [a tanúsítványfájl alkalmazást az alkalmazás](#load-certificate-from-file)-adattárba, de nem ajánlott eljárás a privát tanúsítványokhoz.

Ha App Service felügyeli az SSL-tanúsítványokat, megtarthatja a tanúsítványokat és az alkalmazás kódját, és gondoskodhat a bizalmas adatok védelméről is.

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Privát tanúsítvány feltöltése előtt győződjön meg arról, hogy [az megfelel az összes követelménynek](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), kivéve, ha a kiszolgáló hitelesítéséhez nem szükséges konfigurálni.

Ha készen áll a feltöltésre, futtassa a következő parancsot a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítvány elérhetővé tétele](#make-the-certificate-accessible)című témakört.

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

A nyilvános tanúsítványokat *. cer* formátumban támogatja a rendszer. Egy nyilvános tanúsítvány feltöltéséhez a <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, majd navigáljon az alkalmazáshoz.

Kattintson **az SSL-beállítások** > **nyilvános tanúsítványok (. cer)**  > **feltöltés nyilvános tanúsítványa** elemre az alkalmazás bal oldali navigációs sávján.

A **név**mezőben adja meg a tanúsítvány nevét. A **CER-tanúsítvány fájljában**válassza ki a CER-fájlt.

Kattintson a **Feltöltés** gombra.

![Nyilvános tanúsítvány feltöltése](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

A tanúsítvány feltöltése után másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítvány elérhetővé tételét](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>App Service tanúsítvány importálása

Lásd: [SSL-tanúsítvány vásárlása és konfigurálása Azure app Servicehoz](web-sites-purchase-ssl-web-site.md).

A tanúsítvány importálása után másolja a tanúsítvány ujjlenyomatát, és tekintse meg [a tanúsítvány elérhetővé tételét](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>A tanúsítvány elérhetővé tétele

Ha feltöltött vagy importált tanúsítványt szeretne használni az alkalmazás kódjában, a következő parancs futtatásával elérhetővé teheti az ujjlenyomatát az `WEBSITE_LOAD_CERTIFICATES` alkalmazás beállításával: <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Az összes tanúsítvány elérhetővé tételéhez állítsa a értéket `*`a következőre:.

> [!NOTE]
> Ez a beállítás a legtöbb díjszabási szinten elhelyezi a megadott tanúsítványokat a [jelenlegi Settingsnew](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -tárolóban, de az **elkülönített** szinten (azaz az alkalmazás [app Service Environmentban](environment/intro.md)fut) a tanúsítványokat a [helyi Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) helyezi el. Store.
>

![Alkalmazás beállításának konfigurálása](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Amikor végzett, kattintson a **Mentés** gombra.

A konfigurált tanúsítványok most már készen állnak a kód használatára.

## <a name="load-the-certificate-in-code"></a>A tanúsítvány betöltése a kódban

Miután a tanúsítvány elérhetővé válik, a Tanúsítvány ujjlenyomata alapján férhet hozzá a C# kódban. A következő kód betölti az ujjlenyomattal `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`rendelkező tanúsítványt.

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

Annak ellenére, hogy a fájlt közvetlenül a .NET-kódban tölti be, a függvénytár továbbra is ellenőrzi, hogy az aktuális felhasználói profil be van-e töltve. Az aktuális felhasználói profil betöltéséhez állítsa be `WEBSITE_LOAD_USER_PROFILE` az alkalmazás beállítását a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>következő parancsával.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Ha ez a beállítás be van állítva, C# az alábbi példa egy nevű `mycert.pfx` tanúsítványt tölt `certs` be az alkalmazás adattárának könyvtárából.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
