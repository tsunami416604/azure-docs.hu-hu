---
title: Hibaelhárítási csökkentett teljesítményű az Azure Traffic Manager állapota
description: Traffic Manager-profilok hibaelhárítása, amikor azt mutatja csökkentett teljesítményű az állapota.
services: traffic-manager
documentationcenter: ''
author: chadmath
manager: cshepard
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: e314bac630ce06fbcd62081cc6e9f5e85930d32b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055525"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Csökkentett hibaelhárítása az Azure Traffic Manager

Ez a cikk ismerteti, hogyan háríthatók el az Azure Traffic Manager-profilok csökkentett teljesítményű állapot jelenik. Ebben az esetben fontolja meg, hogy konfigurálta a cloudapp.net üzemeltetett szolgáltatások mutasson a Traffic Manager-profil. Ha a Traffic Manager állapotát jeleníti meg egy **csökkentett teljesítményű** állapotát, majd egy vagy több végpontot állapota lehet **csökkentett teljesítményű**:

![csökkentett teljesítményű végpont állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Ha a Traffic Manager állapotát jeleníti meg egy **inaktív** állapot, akkor mindkét végpontok lehetnek **letiltott**:

![Inaktív a Traffic Manager állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Tudnivalók a Traffic Manager-mintavételek

* A TRAFFIC Manager-végpont ONLINE csak akkor, ha a mintavétel 200-as HTTP-választ kap vissza a mintavétel elérési útjának figyelembe veszi. Semmilyen más nem 200-as értékű választ hiba történik.
* 30 x átirányítási nem sikerül, akkor is, ha az átirányított URL-címet adja vissza a rendszer 200-as.
* A HTTPs-mintavételek menüpontban tanúsítvánnyal kapcsolatos hiba figyelmen kívül hagyja.
* A mintavétel elérési útjának tényleges tartalmat nem számít, mindaddig, amíg egy 200 adja vissza. Tesztelés valamely statikus tartalom, például URL-címe "/ favicon.ico" egy elterjedt megoldás. Dinamikus tartalom, például az ASP-lapok, előfordulhat, hogy nem mindig vissza 200, akkor is, ha az alkalmazás állapota kifogástalan.
* Ajánlott eljárás, hogy a mintavétel elérési útjának beállítása úgy, hogy van elég logikai annak megállapításához, hogy a hely felfelé vagy lefelé. Az előző példában úgy, hogy az elérési út "/ favicon.ico", csak a w3wp.exe tesztelés válaszol-e. Ez a Hálózatfigyelő nem azt jelezheti, hogy a webalkalmazás állapota kifogástalan. Jobb megoldás lehet a adjon meg egy útvonalat a hiba, például "/ Probe.aspx", amelynek logikát meghatározni az a hely. Például használhatja a teljesítményszámlálókat CPU-kihasználtság, vagy a sikertelen kérelmek számát. Vagy sikerült próbál elérni az adatbázis-erőforrások vagy munkamenet-állapot működéséhez győződjön meg arról, hogy a webalkalmazás működik.
* A profilban szereplő összes végpont állapotromlást, ha majd Traffic Manager az összes végpontok állapota kifogástalan és kezeli irányítja a forgalmat az összes végpontokra. Ez a viselkedés garantálja, hogy az ellenőrzési mechanizmus problémákat eredményez a szolgáltatás egy teljes körű leállás.

## <a name="troubleshooting"></a>Hibaelhárítás

A mintavételi hiba elhárításához szüksége van egy eszköz, amely a HTTP-állapotkód: visszatérési bemutatja a mintavétel URL-címről. Nincsenek számos eszközt biztosít, amelyek bemutatják a nyers HTTP-válasz.

* [Fiddler](http://www.telerik.com/fiddler)
* [A curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Emellett használhatja a hálózat lapot az F12 hibakeresés eszközök az Internet Explorer HTTP-válaszok megtekintéséhez.

Ebben a példában szeretnénk megnézni a választ a mintavétel URL-címről: http://watestsdp2008r2.cloudapp.net:80/Probe. A következő PowerShell-példa szemlélteti a problémát.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Példa a kimenetre:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Figyelje meg, hogy az átirányítási válasz érkezett. Ahogy korábban is hangsúlyoztuk, StatusCode bármely más, mint 200 sikertelennek van tekintve. A TRAFFIC Manager végpont állapota Offline változik. A probléma megoldásához ellenőrizze annak érdekében, hogy a mintavétel elérési útját a visszaadható-e a megfelelő StatusCode webhely konfigurációját. Konfigurálja újra a Traffic Manager mintavételezési, amely visszaadja a 200-as elérési útra mutasson.

Ha a mintavétel a HTTPS protokollt használ, szükség lehet tiltsa le a tanúsítvány-ellenőrzés, hogy a teszt során az SSL/TLS-hibák elkerülése érdekében. A következő PowerShell-utasításokat letiltja a tanúsítványok ellenőrzését az aktuális PowerShell-munkamenetben:

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

## <a name="next-steps"></a>További lépések

[Tudnivalók a Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)

[Mi a Traffic Manager](traffic-manager-overview.md)

[Felhőszolgáltatások](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[A Traffic Manager műveletei (REST API-referencia)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Az Azure Traffic Manager parancsmagjai][1]

[1]: https://docs.microsoft.com/powershell/module/azurerm.trafficmanager
