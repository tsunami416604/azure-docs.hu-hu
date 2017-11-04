---
title: "Hibaelhárítás \"csökkentett teljesítményű\" állapota az Azure Traffic Manager"
description: "Traffic Manager-profilok hibaelhárítása, amikor azt mutatja, mint a \"csökkentett teljesítményű\" állapota."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: kumud
ms.openlocfilehash: b1d00fb84695d2289f37647f55a7c56cf28c8c96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Hibaelhárítás "csökkentett teljesítményű" állapota az Azure Traffic Manager

A cikkből hibaelhárítása az Azure Traffic Manager-profilt, amely egy leromlott állapot jelenik meg. Ebben az esetben fontolja meg, hogy konfigurálta-e az egyes a cloudapp.net üzemeltetett szolgáltatások mutató Traffic Manager-profil. Ha a Traffic Manager állapotát jeleníti meg a **csökkentett teljesítményű** állapotát, majd egy vagy több végpontot állapotának lehet **csökkentett teljesítményű**:

![csökkent végpont állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Ha a Traffic Manager állapotát jeleníti meg egy **inaktív** állapot, akkor mindkét végpontok lehetnek **letiltott**:

![Inaktív Traffic Manager-állapot](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Understanding Traffic Manager-mintavétel

* A TRAFFIC Manager úgy véli, hogy egy végpontot kell ONLINE, csak akkor, ha a mintavételi 200-as HTTP választ kap vissza mintavételi elérési úton található. A rendszer hibát bármely más nem 200 választ.
* 30 x átirányítást nem sikerül, akkor is, ha az átirányított URL-címet adja vissza a 200-as.
* A HTTPs mintavételek menüpontban hitelesítési hibák figyelmen kívül lesznek hagyva.
* A tényleges tartalom a mintavételi elérési út nem számít, mindaddig, amíg egy 200 adja vissza. Probing néhány statikus tartalom, például egy URL-címe "vagy a favicon.ico" egy közös technika. Dinamikus tartalom, például az ASP-lapok, előfordulhat, hogy mindig vissza 200, akkor is, ha az alkalmazás állapota kifogástalan.
* Az ajánlott eljárás is, hogy a mintavétel elérési olyanra, amely rendelkezik elegendő logikai annak megállapításához, hogy a hely felfelé vagy lefelé. Az előző példában, úgy, hogy az elérési út "/ favicon.ico", csak a w3wp.exe tesztelés válaszol. Ez a Hálózatfigyelő is jelzi, hogy a webalkalmazás működik megfelelően. Jobb megoldás lehet a beállítása egy elérési utat a például a "/ Probe.aspx", amely rendelkezik a hely állapotának meghatározására logikát. Például használhatja a CPU-felhasználás kérelemteljesítmény-számlálókat vagy méri a sikertelen kérelmek számát. Vagy, adatbázis-erőforrások, vagy győződjön meg arról, hogy működik-e a webes alkalmazás munkamenet-állapot elérésére tett kísérlet sikertelen.
* Egy profil végpontjai állapotromlást, ha majd Traffic Manager minden végpontok állapota kiváló és kezeli útvonalak forgalom minden végpontokhoz. Ez a viselkedés garantálja, hogy a vizsgálathoz használt mechanizmus problémákat okoz egy teljes leállás a szolgáltatás.

## <a name="troubleshooting"></a>Hibaelhárítás

Mintavételi hiba elhárításához szükség van olyan eszköz, amely a HTTP-állapotkód: visszatérési mutatja a mintavételi URL-címről. Nincsenek a számos elérhető eszköz, amely a nyers HTTP-válasz is láthat.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Az Internet Explorer az F12 hibakeresési eszközök a hálózati lapján használhatja is, a HTTP-válaszok megtekintése.

Ehhez a példához szeretnénk megnézni a mintavételi URL-címhez válasza: http://watestsdp2008r2.cloudapp.net:80/mintavétel. A következő PowerShell-példa szemlélteti a problémát.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Példa a kimenetre:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Figyelje meg, hogy az átirányítási válasz érkezett. Ahogy korábban is hangsúlyoztuk, StatusCode bármely más, mint 200 tekinthető hiba. A TRAFFIC Manager offline a végpont állapota megváltozik. A probléma megoldásához ellenőrizze az webhely konfigurációját, és győződjön meg arról, hogy a megfelelő StatusCode mintavételi elérési útjáról adhatók vissza. Konfigurálja újra a Traffic Manager mintavétel adja vissza egy 200 elérési ponthoz.

Ha a mintavételi a HTTPS protokollt használ, szükség lehet a tanúsítványellenőrzést, a vizsgálat során az SSL/TLS hibák elkerülése érdekében tiltsa le. A következő PowerShell-utasításokat tiltsa le a tanúsítvány érvényesítése a jelenlegi PowerShell-munkamenethez:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Következő lépések

[A Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md)

[Mi az a Traffic Manager](traffic-manager-overview.md)

[Felhőszolgáltatások](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[A Traffic Manager műveletei (REST API-referencia)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-parancsmagok][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
