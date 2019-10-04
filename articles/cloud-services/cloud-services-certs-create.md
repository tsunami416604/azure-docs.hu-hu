---
title: Cloud Services és felügyeleti tanúsítványok | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és használhat tanúsítványokat Microsoft Azure
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3c84c6832856986a45be7d275fb94a6c5fc066f0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359193"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Az Azure Cloud Services tanúsítványok áttekintése
A tanúsítványok az Azure for Cloud Services ([szolgáltatás-tanúsítványok](#what-are-service-certificates)) és a felügyeleti API ([felügyeleti tanúsítványok](#what-are-management-certificates)) használatával történő hitelesítéshez használatosak. Ez a témakör általános áttekintést nyújt mindkét tanúsítvány típusáról, valamint arról, hogyan [hozhatja létre](#create) és helyezheti üzembe őket az Azure-ban.

Az Azure-ban használt tanúsítványok x. 509 v3 tanúsítványok, és egy másik megbízható tanúsítvány alá is vonhatók, vagy önaláírtak. Az önaláírt tanúsítványt a saját létrehozója írja alá, ezért alapértelmezés szerint nem megbízható. A legtöbb böngésző figyelmen kívül hagyhatja ezt a problémát. A felhőalapú szolgáltatások fejlesztése és tesztelése során csak önaláírt tanúsítványokat használhat. 

Az Azure által használt tanúsítványok tartalmazhatnak magán-vagy nyilvános kulcsot. A tanúsítványok ujjlenyomattal rendelkeznek, amely azt jelenti, hogy egyértelmű módon azonosítja őket. Ezt az ujjlenyomatot használja az Azure [konfigurációs fájlban](cloud-services-configure-ssl-certificate-portal.md) annak azonosítására, hogy melyik tanúsítványnak kell használnia a Cloud Service-t. 

>[!Note]
>Az Azure Cloud Services nem fogadja el a AES256-SHA256 titkosított tanúsítványt.

## <a name="what-are-service-certificates"></a>Mik azok a szolgáltatási tanúsítványok?
A szolgáltatási tanúsítványok a Cloud Serviceshez kapcsolódnak, és lehetővé teszik a szolgáltatás felé irányuló biztonságos kommunikációt. Ha például központilag telepített egy webes szerepkört, olyan tanúsítványt kell megadnia, amely képes a kitett HTTPS-végpont hitelesítésére. A szolgáltatás definíciójában definiált szolgáltatási tanúsítványokat a rendszer automatikusan telepíti a szerepkör példányát futtató virtuális gépre. 

A szolgáltatási tanúsítványokat feltöltheti az Azure-ba a Azure Portal vagy a klasszikus üzemi modell használatával. A szolgáltatási tanúsítványok egy adott felhőalapú szolgáltatáshoz vannak társítva. Ezek egy központi telepítéshez vannak rendelve a szolgáltatás definíciós fájljában.

A szolgáltatási tanúsítványok a szolgáltatásaitól függetlenül kezelhetők, és a különböző személyek is kezelhetik. Előfordulhat például, hogy egy fejlesztő feltölt egy olyan tanúsítványt, amely egy IT-kezelő által korábban az Azure-ba feltöltött tanúsítványra hivatkozik. Az informatikai vezető felügyelheti és megújíthatja a tanúsítványt (a szolgáltatás konfigurációjának módosítása) anélkül, hogy új szervizcsomagot kellene feltöltenie. Az új szervizcsomag nélküli frissítés lehetséges, mert a tanúsítvány logikai neve, tárolójának neve és helye a szolgáltatás definíciós fájljában található, és a Tanúsítvány ujjlenyomata meg van adva a szolgáltatás konfigurációs fájljában. A tanúsítvány frissítéséhez csak új tanúsítványt kell feltöltenie, és módosítania kell az ujjlenyomat értékét a szolgáltatás konfigurációs fájljában.

>[!Note]
>A [Cloud Services GYIK – konfigurációs és felügyeleti](cloud-services-configuration-and-management-faq.md) cikk hasznos információkat tartalmaz a tanúsítványokról.

## <a name="what-are-management-certificates"></a>Mik azok a felügyeleti tanúsítványok?
A felügyeleti tanúsítványok lehetővé teszik a klasszikus üzemi modellel történő hitelesítést. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. Ezek nem igazán kapcsolódnak a Cloud Serviceshez. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok lehetővé teszik, hogy bárki hitelesítse őket a velük társított előfizetés kezeléséhez. 
> 
> 

### <a name="limitations"></a>Korlátozások
Az előfizetéshez legfeljebb 100 felügyeleti tanúsítvány adható meg. A szolgáltatás rendszergazdája által megadott felhasználói AZONOSÍTÓhoz tartozó összes előfizetéshez legfeljebb 100 felügyeleti tanúsítvány tartozik. Ha a fiók rendszergazdájának felhasználói azonosítója már használatban van az 100-es felügyeleti tanúsítvány hozzáadásához, és további tanúsítványokra van szükség, hozzáadhat egy társ-rendszergazdát a további tanúsítványok hozzáadásához. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány létrehozása
A rendelkezésre álló bármely eszköz használatával létrehozhat egy önaláírt tanúsítványt, ha ezek a beállítások megfelelnek a következőknek:

* X. 509 tanúsítvány.
* Titkos kulcsot tartalmaz.
* Létrehozva a Key Exchange (. pfx fájl) számára.
* A tulajdonos nevének meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartománnyal.

    > Nem vásárolhat SSL-tanúsítványt a cloudapp.net (vagy bármely Azure-hoz kapcsolódó tartományhoz). a tanúsítvány tulajdonosának nevének meg kell egyeznie az alkalmazás eléréséhez használt egyéni tartománynévvel. Például **contoso.net**, nem **contoso.cloudapp.net**.

* Legalább 2048 bites titkosítás.
* **Csak a szolgáltatás tanúsítványa**: Az ügyféloldali tanúsítványnak a *személyes* tanúsítványtárolóban kell lennie.

A Windowson, a `makecert.exe` segédprogramon vagy az IIS-ben kétféleképpen hozhat létre tanúsítványokat.

### <a name="makecertexe"></a>Makecert.exe
Ez a segédprogram elavult, és már nem dokumentálva van. További információt [ebben az MSDN-cikkben](/windows/desktop/SecCrypto/makecert)talál.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Ha tartomány helyett IP-címmel szeretné használni a tanúsítványt, használja az IP-címet a-DnsName paraméterben.


Ha ezt [a tanúsítványt a felügyeleti portálon](../azure-api-management-certs.md)szeretné használni, exportálja egy **. cer** fájlba:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Az interneten sok oldal található, amely az IIS használatával foglalkozik. [Itt](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) van egy nagyszerű, hogy azt hiszem, jól megmagyarázom. 

### <a name="linux"></a>Linux
[Ez a](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk bemutatja, hogyan hozhat létre a tanúsítványokat SSH-val.

## <a name="next-steps"></a>További lépések
[Töltse fel a szolgáltatási tanúsítványt a Azure Portalba](cloud-services-configure-ssl-certificate-portal.md).

Töltsön fel egy [felügyeleti API-tanúsítványt](../azure-api-management-certs.md) a Azure Portalba.

