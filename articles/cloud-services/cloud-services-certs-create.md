---
title: Cloud Services és felügyeleti tanúsítványok |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és a tanúsítványok használata a Microsoft Azure
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 5c16f06d0cc031cd9b51a3c6cf0beb149a19aeb4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001439"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure Cloud Services – tanúsítványok áttekintése
Tanúsítványokat használ az Azure cloud Services ([szolgáltatási tanúsítványok](#what-are-service-certificates)) és a felügyeleti API-hoz való hitelesítéshez használt ([felügyeleti tanúsítványok](#what-are-management-certificates)). Ez a témakör hogyan mindkét tanúsítványtípusok általános áttekintést nyújt a [létrehozása](#create) és [üzembe helyezése](#deploy) őket az Azure-bA.

Az Azure-ban használt tanúsítványok mind x.509 v3 tanúsítványokkal és a egy másik, megbízható tanúsítvánnyal írhatók alá, vagy önaláírt is lehet. Önaláírt tanúsítvány aláírásával rendelkezik a saját létrehozója, ezért azt nem megbízható alapértelmezés szerint. A legtöbb böngésző figyelmen kívül hagyhatja ezt a problémát. Ha a fejlesztés és tesztelés a cloud services önaláírt tanúsítványokat csak használja. 

Az Azure által használt tanúsítványok is tartalmazhat, egy privát vagy nyilvános kulccsal. Tanúsítványának rendelkeznie kell egy ujjlenyomatot, amely lehetővé teszi egy egyértelmű módon azonosítani őket. Ezzel az ujjlenyomattal szolgál az Azure-ban [konfigurációs fájl](cloud-services-configure-ssl-certificate-portal.md) azonosítani, hogy melyik tanúsítványt egy felhőalapú szolgáltatás használjon. 

## <a name="what-are-service-certificates"></a>Mik azok a service-tanúsítványok?
Szolgáltatási tanúsítványok cloud services és a biztonságos kommunikációhoz, és a szolgáltatásból is csatlakozik. Például ha telepítette a webes szerepkör, érdemes megadni egy tanúsítványt, amely hitelesíteni tudja a közzétett HTTPS-végpontokat. Szolgáltatási tanúsítványok, a szolgáltatás definíciós meghatározott a rendszer automatikusan telepíti a virtuális gép, amelyen fut a szerepkör példányának. 

Szolgáltatási tanúsítványok tölthet fel az Azure-bA vagy az Azure portal használatával, vagy a klasszikus üzemi modell használatával. Szolgáltatási tanúsítványok társítva egy adott felhőalapú szolgáltatás. Központi telepítés a szolgáltatásdefiníciós fájlban vannak rendelve.

Szolgáltatási tanúsítványok az szolgáltatásokból származó külön-külön kezelhetők, és különböző osztályai felügyelheti. Például egy fejlesztői szolgáltatáscsomagot, amelyre hivatkozik egy tanúsítványt, amely az IT-menedzser korábban már fel van töltve az Azure-bA tölthet fel. Az IT-menedzser kezelheti és megújítása a tanúsítvány (a szolgáltatás konfigurációjának módosítása) anélkül, hogy az új szolgáltatási csomag feltöltése. Anélkül, hogy egy új service-csomag frissítése akkor lehetséges, mert a logikai név, tároló neve és a tanúsítvány helyét a szolgáltatásdefiníciós fájlban, és amíg a tanúsítvány ujjlenyomatát a konfigurációs fájlban megadott. Ha frissíteni szeretné a tanúsítványt, csak akkor kell töltsön fel egy új tanúsítványt, és módosítsa az ujjlenyomat értéket a konfigurációs fájlban.

>[!Note]
>A [Cloud Services – gyakori kérdések](cloud-services-faq.md) cikk tartalmaz néhány hasznos információ a tanúsítványokról.

## <a name="what-are-management-certificates"></a>Mik azok a felügyeleti tanúsítványok?
Felügyeleti tanúsítványok lehetővé teszik a klasszikus üzemi modellben a hitelesítéshez. Számos programok telepítése és eszközök (például a Visual Studio vagy az Azure SDK-t) konfigurálása és a különböző Azure-szolgáltatások üzembe helyezésének automatizálása ezek a tanúsítványok használatával. Ezek nem igazán kapcsolódnak felhőszolgáltatásokhoz. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok engedélyezése bárki, aki az őket a velük társított előfizetés kezelése céljából végzi a hitelesítést. 
> 
> 

### <a name="limitations"></a>Korlátozások
Előfizetésenként 100 felügyeleti tanúsítványok korlátozva van. Emellett van egy legfeljebb 100 felügyeleti tanúsítványok minden előfizetése egy adott szolgáltatás-rendszergazda felhasználói azonosítóját. Ha a fiók rendszergazdája a felhasználói azonosító már használatban van 100 felügyeleti tanúsítványok hozzáadása, és nincs szükség további tanúsítványok, a további tanúsítványok hozzáadása egy társ-rendszergazdaként adhat hozzá. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Hozzon létre egy új önaláírt tanúsítványt
Elérhető mindaddig, amíg azok betartja ezeket a beállításokat, hozzon létre egy önaláírt tanúsítványt minden olyan eszközt is használhatja:

* Egy X.509 tanúsítvány.
* A titkos kulcsot tartalmaz.
* Kulcscsere (.pfx-fájl) hoz létre.
* Tulajdonos nevének egyeznie kell a felhőalapú szolgáltatás eléréséhez használt tartományt.

    > Egy SSL-tanúsítványt a cloudapp.net nem szerez be (vagy bármely Azure-hoz kapcsolódó) tartomány; a tanúsítvány tulajdonosnevének egyeznie kell az alkalmazás eléréséhez használt tartománynév. Ha például **contoso.net**, nem **contoso.cloudapp.net**.

* Legalább 2048 bites titkosítást.
* **Csak szolgáltatási tanúsítvány**: ügyféloldali tanúsítványt kell lennie a *személyes* tanúsítványtárolójába.

Nincsenek a Windows, a tanúsítvány létrehozása a két egyszerű módon a `makecert.exe` segédprogramot, vagy az IIS.

### <a name="makecertexe"></a>Makecert.exe
A segédprogram elavult, és már nem dokumentált itt. További információkért lásd: [MSDN-cikkben](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Ha szeretné használni a tanúsítványt a tartomány helyett IP-címet, IP-címet használja a - DnsName paraméterben.


Ha ezt szeretné [tanúsítványt a kezelési portállal](../azure-api-management-certs.md), exportálása, hogy egy **.cer** fájlt:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Az Internet Information Services (IIS)
Nincsenek ehhez az IIS-t mind az interneten sok oldal. [Itt](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) nagyszerű csak azt ismerteti, hogy jól gondolja találtam. 

### <a name="linux"></a>Linux
[Ez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk ismerteti, hogyan hozhat létre tanúsítványokat az ssh-val.

## <a name="next-steps"></a>További lépések
[A service-tanúsítvány feltöltése az Azure Portalra](cloud-services-configure-ssl-certificate-portal.md).

Töltse fel a [felügyeleti API-tanúsítványok](../azure-api-management-certs.md) az Azure Portalra.

