---
title: "Cloud Services és a felügyeleti tanúsítványok |} Microsoft Docs"
description: "Megtudhatja, hogyan történő létrehozásáról és használatáról a tanúsítványokat a Microsoft Azure"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 689977491e6df37e48536f59234bf4ddba6a1575
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure Cloud Services tanúsítványok áttekintése
Rendszer tanúsítványokat használ az Azure felhőszolgáltatások ([tanúsítványok szolgáltatás](#what-are-service-certificates)) és a felügyeleti API hitelesítéséhez ([felügyeleti tanúsítványok](#what-are-management-certificates) a klasszikus Azure portál használata esetén, és nem a nem klasszikus Azure portálon). Ez a témakör hogyan mindkét tanúsítványtípusok általános áttekintést nyújt a [létrehozása](#create) és [telepítése](#deploy) őket az Azure-bA.

Az Azure-ban használt tanúsítványok x.509 v3 alapján létrehozott tanúsítványok, és egy másik megbízható tanúsítvány aláírását, vagy önaláírt el. Önaláírt tanúsítvány aláírásával rendelkezik saját létrehozó, ezért az nem megbízható alapértelmezés szerint. A legtöbb böngésző figyelmen kívül hagyhatja ezt a problémát. Csak akkor ajánlott önaláírt tanúsítványokat, amikor a fejlesztés és tesztelés felhőszolgáltatásban. 

Azure által használt tanúsítványok is tartalmazhat, egy saját vagy nyilvános kulcsot. Tanúsítványának rendelkeznie kell egy ujjlenyomat, amely lehetővé teszi egy egyértelmű módon azonosításához. Ezzel az ujjlenyomattal használatban van az Azure [konfigurációs fájl](cloud-services-configure-ssl-certificate.md) azonosításához amely tanúsítvány egy felhőalapú szolgáltatás kell használni. 

## <a name="what-are-service-certificates"></a>Mik azok a szolgáltatási tanúsítványok?
Szolgáltatási tanúsítványok a felhőalapú szolgáltatások és és a szolgáltatás közötti biztonságos kommunikáció engedélyezése vannak csatolva. Például ha a webes szerepkör, akkor kíván megadni olyan tanúsítvány, amely képes hitelesíteni az elérhetőségi HTTPS-végpontnak. Szolgáltatási tanúsítványok, a szolgáltatás definíciós meghatározott a rendszer automatikusan telepíti a virtuális gépet, hogy fut a szerepkör példánya. 

Szolgáltatási tanúsítványok feltöltheti a klasszikus Azure portálra vagy a klasszikus Azure portál használatával, vagy a klasszikus üzembe helyezési modell használatával. Szolgáltatási tanúsítványokhoz társított egyes adott felhőalapú szolgáltatás. A központi telepítés a szolgáltatásdefiníciós fájlban vannak rendelve.

Szolgáltatási tanúsítványok kezelheti külön-külön a szolgáltatások, és előfordulhat, hogy különböző osztályai kell kezelnie. Egy fejlesztő például előfordulhat, hogy feltöltése a szolgáltatáscsomagot, amelyre hivatkozik egy tanúsítványt, amely az informatikai manager korábban már fel van töltve az Azure-bA. Az informatikai Managerrel kezelhetők és anélkül, hogy egy új service-csomag feltöltése (a szolgáltatás konfigurációjának módosítása) tanúsítvány megújításához. Nélkül egy új service-csomag frissítése már lehetséges, mert a logikai név, a tároló nevét és a tanúsítványt a szolgáltatásdefiníciós fájlban, és amíg a tanúsítvány ujjlenyomatát a szolgáltatás konfigurációs fájlban megadott. A tanúsítvány frissítésére, csak akkor kell töltsön fel új tanúsítványt, és módosítsa az ujjlenyomat értéket, a szolgáltatás konfigurációs fájljában.

>[!Note]
>A [Cloud Services – gyakori kérdések](cloud-services-faq.md) cikk rendelkezik néhány hasznos információ a tanúsítványok.

## <a name="what-are-management-certificates"></a>Mik azok a felügyeleti tanúsítványok?
Felügyeleti tanúsítványok lehetővé teszik a klasszikus üzembe helyezési modellben a hitelesítést. Számos programok telepítése és eszközök (például a Visual Studio vagy az Azure SDK-val) és a különböző Azure-szolgáltatások telepítési automatizálására használható ezeket a tanúsítványokat. Ezek nem valóban kapcsolódnak a felhőalapú szolgáltatások. 

> [!WARNING]
> légy óvatos! Ezek a típusok, a tanúsítványok bárki hitelesíti magát azokat kezelheti az előfizetést, amelyekhez tartoznak. 
> 
> 

### <a name="limitations"></a>Korlátozások
A 100 felügyeleti tanúsítványok előfizetésenként korlátozva van. Szerepel továbbá egy legfeljebb 100 felügyeleti tanúsítványok előfizetéseket alapján egy adott szolgáltatás-rendszergazda felhasználói azonosítóját. Ha a felhasználói Azonosítóját a fiókadminisztrátor már használatban van 100 felügyeleti tanúsítványok hozzáadása, és nincs szükség további tanúsítványok, a további tanúsítványok hozzáadása egy közös rendszergazdát adhat hozzá. 

100-nál több tanúsítványok hozzáadása, előtt tekintse meg, ha újra felhasználhatja egy meglévő tanúsítvány. A tanúsítvány felügyeleti folyamat esetleg felesleges összetettsége társrendszergazdák használatával hozzáadja.

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Hozzon létre egy új önaláírt tanúsítványt
Minden elérhető, amíg azok igazodik ezeket a beállításokat, hozzon létre egy önaláírt tanúsítványt eszközt is használhatja:

* Egy X.509 tanúsítvány.
* A titkos kulcsot tartalmaz.
* Létre kulcscsere (.pfx fájlt).
* Meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartomány.

    > Nem olvasható be a cloudapp.net az SSL-tanúsítvány (vagy bármely Azure-hoz kapcsolódó) tartomány; a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynév, az alkalmazás eléréséhez használt. Például **contoso.net**, nem **contoso.cloudapp.net**.

* Legalább 2048 bites titkosítást.
* **Csak szolgáltatási tanúsítvány**: ügyféloldali tanúsítványt kell lennie a *személyes* tanúsítványtárolójába.

Windows, a tanúsítvány létrehozása a két egyszerű módja van a `makecert.exe` segédprogram, vagy az IIS.

### <a name="makecertexe"></a>MakeCert.exe
A segédprogram elavult, és már nem itt dokumentált. További információkért lásd: [MSDN-cikkben](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Ha azt szeretné, a tanúsítványt a tartomány helyett IP-címet használja, használja az IP-cím - DnsName paraméterében.


Ha ezt szeretné [tanúsítvány a kezelési portállal](../azure-api-management-certs.md), úgy, hogy exportálása egy **.cer** fájlt:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Az Internet Information Services (IIS)
Nincsenek számos lapot az interneten tér ki az ehhez az IIS-kiszolgálón. [Itt](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) találtam ismerteti azt is gondolja kiváló egy. 

### <a name="linux"></a>Linux
[Ez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk ismerteti az SSH olyan tanúsítványokat hoznak létre.

## <a name="next-steps"></a>Következő lépések
[A szolgáltatás-tanúsítvány feltöltése a klasszikus Azure portálra](cloud-services-configure-ssl-certificate.md) (vagy a [Azure-portálon](cloud-services-configure-ssl-certificate-portal.md)).

Töltse fel a [felügyeleti API tanúsítvány](../azure-api-management-certs.md) a klasszikus Azure portálra. Az Azure-portál nem felügyeleti tanúsítványokat használnak a hitelesítéshez.

