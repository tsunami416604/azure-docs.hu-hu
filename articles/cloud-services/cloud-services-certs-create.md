---
title: Felhőszolgáltatások és felügyeleti tanúsítványok | Microsoft dokumentumok
description: A tanúsítványok létrehozása és használata a Microsoft Azure-ral
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 173f5c698ab44ea269995665bcbc33c726d4f03a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811463"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure Cloud Services – tanúsítványok áttekintése
A tanúsítványokat az Azure-ban felhőszolgáltatásokhoz ([szolgáltatástanúsítványokhoz)](#what-are-service-certificates)és a felügyeleti API-val ([felügyeleti tanúsítványok)](#what-are-management-certificates)való hitelesítésre használják. Ez a témakör általános áttekintést nyújt mindkét tanúsítványtípusról, hogyan [hozhat létre](#create) és helyezhet i üzembe az Azure-ban.

Az Azure-ban használt tanúsítványok x.509 v3-as tanúsítványok, és aláírhatók egy másik megbízható tanúsítvány, vagy lehet önaláírt. Az önaláírt tanúsítványt saját létrehozója írja alá, ezért alapértelmezés szerint nem megbízható. A legtöbb böngésző figyelmen kívül tudja hagyni ezt a problémát. Csak önaláírt tanúsítványokat használjon a felhőszolgáltatások fejlesztése és tesztelése során. 

Az Azure által használt tanúsítványok tartalmazhatnak egy személyes vagy nyilvános kulcsot. A tanúsítványok ujjlenyomata egyértelmű módon biztosítja azok azonosítását. Ez az ujjlenyomat az Azure [konfigurációs fájlban](cloud-services-configure-ssl-certificate-portal.md) található a felhőszolgáltatás által használandó tanúsítvány azonosítására. 

>[!Note]
>Az Azure Cloud Services nem fogadja el az AES256-SHA256 titkosított tanúsítványt.

## <a name="what-are-service-certificates"></a>Mik azok a szolgáltatási tanúsítványok?
A szolgáltatási tanúsítványok a felhőszolgáltatásokhoz kapcsolódnak, és a szolgáltatásból kifelé és befelé irányuló biztonságos kommunikációt teszik lehetővé. Például ha telepített egy webes szerepkört, érdemes megadnia egy tanúsítványt, amely hitelesítheti a elérhető HTTPS-végpontot. A szolgáltatásdefinícióban definiált szolgáltatástanúsítványok automatikusan telepítve vannak a szerepkör egy példányát futtató virtuális gépre. 

A szolgáltatási tanúsítványokat feltöltheti az Azure-ba az Azure Portal használatával, vagy a klasszikus üzembehelyezési modell alkalmazásával is. A szolgáltatási tanúsítványok az adott felhőszolgáltatáshoz vannak társítva. Ezek a szolgáltatás definíciós fájljában vannak hozzárendelve a központi telepítéshez.

A szolgáltatási tanúsítványok a szolgáltatásoktól elkülönítve kezelhetők, és előfordulhat, hogy különböző személyek kezelik őket. Például egy fejlesztő feltölthet egy szolgáltatási csomagot, amely egy olyan tanúsítványra hivatkozik, amelyet egy informatikai vezető korábban feltöltött az Azure-ba. Az IT-menedzser felügyelheti és megújíthatja a tanúsítványt (vagyis módosíthatja a szolgáltatás konfigurációját) anélkül is, hogy új szervizcsomagot kellene feltölteni. Új szolgáltatáscsomag nélküli frissítésre azért van lehetőség, mert a tanúsítvány logikai neve, tárolóneve és helye a szolgáltatásdefiníciós fájlban található, és amíg a tanúsítvány ujjlenyomata meg van adva a szolgáltatás konfigurációs fájljában. A tanúsítvány frissítéséhez ezért elég feltölteni egy új tanúsítványt és megváltoztatni az ujjlenyomat értéket a szolgáltatás konfigurációs fájljában.

>[!Note]
>A [Felhőszolgáltatások – Konfiguráció és kezelés](cloud-services-configuration-and-management-faq.md) című cikk néhány hasznos információt tartalmaz a tanúsítványokról.

## <a name="what-are-management-certificates"></a>Mik azok a felügyeleti tanúsítványok?
A felügyeleti tanúsítványok lehetővé teszik a klasszikus üzembehelyezési modell alkalmazásával történő hitelesítést. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. Ezek nem igazán kapcsolódnak a felhőszolgáltatásokhoz. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok lehetővé teszik, hogy bárki, aki hitelesíti magát vele, kezelje azt az előfizetést, amelyhez társítva van. 
> 
> 

### <a name="limitations"></a>Korlátozások
Előfizetésenként legfeljebb 100 kezelési tanúsítvány lehet. Egy adott szolgáltatásrendszergazda felhasználói azonosítója alatt minden előfizetéshez legfeljebb 100 felügyeleti tanúsítvány vonatkozik. Ha a fiókrendszergazdának a felhasználói azonosítóját már 100 felügyeleti tanúsítvány hozzáadásához használták, és további tanúsítványokra van szükség, hozzáadhat egy társrendszergazdát a további tanúsítványok hozzáadásához. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Új önaláírt tanúsítvány létrehozása
Bármilyen rendelkezésre álló eszközzel létrehozhat önaláírt tanúsítványt, amennyiben azok megfelelnek ezeknek a beállításoknak:

* Egy X.509-es tanúsítvány.
* Személyes kulcsot tartalmaz.
* Kulcscsere (.pfx fájl) esetén készült.
* A tulajdonos nevének meg kell egyeznie a felhőszolgáltatás eléréséhez használt tartománnyal.

    > Nem szerezhet TLS/SSL-tanúsítványt a cloudapp.net (vagy bármely Azure-hoz kapcsolódó) tartományhoz; a tanúsítvány tulajdonosnevének meg kell egyeznie az alkalmazás eléréséhez használt egyéni tartománynévvel. Például **a contoso.net**nem **contoso.cloudapp.net**.

* Legalább 2048 bites titkosítás.
* **Csak szolgáltatás:** Az ügyféloldali tanúsítványnak a *Személyes* tanúsítványtárolóban kell lennie.

A windowsos, a segédprogrammal vagy az `makecert.exe` IIS szolgáltatással kétféleképpen hozhat létre tanúsítványt.

### <a name="makecertexe"></a>Makecert.exe
Ez a segédprogram elavult, és itt már nincs dokumentálva. További információt [ebben az MSDN-cikkben](/windows/desktop/SecCrypto/makecert)talál.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Ha a tanúsítványt tartomány helyett IP-címmel szeretné használni, használja a -DnsName paraméter IP-címét.


Ha ezt a [tanúsítványt a felügyeleti portállal](../azure-api-management-certs.md)szeretné használni, exportálja egy **.cer** fájlba:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Sok oldal van az interneten, amelyek lefedik, hogyan kell ezt csinálni az IIS-szel. [Itt](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) van egy nagy találtam, hogy azt hiszem, jól magyarázza. 

### <a name="linux"></a>Linux
[Ez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a cikk azt ismerteti, hogyan hozhat létre tanúsítványokat az SSH segítségével.

## <a name="next-steps"></a>További lépések
[Töltse fel a szolgáltatási tanúsítványt az Azure Portalra.](cloud-services-configure-ssl-certificate-portal.md)

Töltsön fel egy [felügyeleti API-tanúsítványt](../azure-api-management-certs.md) az Azure Portalra.




