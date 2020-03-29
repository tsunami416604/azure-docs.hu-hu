---
title: A leromlott állapot hibaelhárítása az Azure Traffic Managerben
description: A Traffic Manager-profilok hibaelhárítása, ha az állapota csökkent.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938364"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Az Azure Traffic Manager csökkentett teljesítményének hibaelhárítása

Ez a cikk ismerteti, hogyan hárítsa el az Azure Traffic Manager-profil, amely egy leromlott állapotot jelenít meg. Az Azure Traffic Manager leromlott állapotának hibaelhárítása első lépéseként a diagnosztikai naplózás engedélyezése.  További információt a [Diagnosztikai naplók engedélyezése](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) című dokumentumban talál. Ebben a forgatókönyvben vegye figyelembe, hogy konfigurált egy Traffic Manager-profilt, amely a cloudapp.net üzemeltetett szolgáltatások néhány. Ha a Traffic Manager állapota **degradálódott** állapotot jelenít meg, akkor egy vagy több végpont állapota **csökkenhet:**

![degradált végpont állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Ha a Traffic Manager állapota **inaktív** állapotot jelenít meg, akkor mindkét végpont **letiltható:**

![Inaktív forgalomkezelő állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>A Traffic Manager-mintavételek ismertetése

* A Traffic Manager csak akkor tekinti online végpontnak egy végpontot, ha a mintavétel http 200-as választ kap a mintavételi útvonalról. Ha az alkalmazás bármely más HTTP-válaszkódot ad vissza, hozzá kell adnia a válaszkódot a Traffic Manager-profil [Várt állapotkódtartományaihoz.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring)
* A 30x-es átirányítási válasz hibaként lesz kezelve, kivéve, ha ezt érvényes válaszkódként adta meg a Traffic Manager-profil [Várt állapotkódtartományaiban.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) A Traffic Manager nem vizsgálja az átirányítási célt.
* A HTTPs-mintavételek esetén a tanúsítványhibákat a rendszer figyelmen kívül hagyja.
* A mintavételi útvonal tényleges tartalma nem számít, amíg egy 200-as értéket ad vissza. Szondázás egy URL-t, hogy néhány statikus tartalom, mint a "/favicon.ico" egy gyakori technika. Dinamikus tartalom, például az ASP-lapok, nem mindig ad vissza 200, akkor is, ha az alkalmazás kifogástalan.
* Ajánlott eljárás: a mintavételi útvonalat olyan ra, amely elég logikával rendelkezik annak megállapításához, hogy a hely felfelé vagy lefelé van-e. Az előző példában a "/favicon.ico" elérési út beállításával csak azt teszteli, hogy a w3wp.exe válaszol-e. Ez a mintavétel nem jelezheti, hogy a webalkalmazás kifogástalan állapotú. Egy jobb megoldás az lenne, hogy egy utat egy olyan valami, mint a "/Probe.aspx", amely logikával határozza meg a hely állapotát. Használhatja például a processzorkihasználtság teljesítményszámlálóit, vagy mérheti a sikertelen kérelmek számát. Vagy megpróbálhat hozzáférni az adatbázis-erőforrásokhoz vagy a munkamenet-állapothoz, hogy megbizonyosodjon arról, hogy a webalkalmazás működik.
* Ha egy profil összes végpontja levan csökkentve, majd a Traffic Manager az összes végpontot kifogástalanállapotúként kezeli, és a forgalmat az összes végponthoz irányítja. Ez a viselkedés biztosítja, hogy a szondázási mechanizmussal kapcsolatos problémák ne eredményezsék a szolgáltatás teljes kimaradását.

## <a name="troubleshooting"></a>Hibaelhárítás

A mintavételi hiba elhárításához olyan eszközre van szüksége, amely a mintavétel URL-címéről visszaadott HTTP-állapotkódot jeleníti meg. Számos olyan eszköz áll rendelkezésre, amely megmutatja a nyers HTTP-választ.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Az Internet Explorer F12 hibakereső eszközeinek Hálózat lapján megtekintheti a HTTP-válaszokat is.

Ebben a példában szeretnénk látni a választ\/a mi mintavételi URL-cím: http: /watestsdp2008r2.cloudapp.net:80/Probe. A következő PowerShell-példa bemutatja a problémát.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Példa a kimenetre:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Figyelje meg, hogy átirányítási választ kaptunk. Ahogy azt korábban említettük, a 200-tól eltérő statuscode-kód hibanak minősül. A Traffic Manager a végpont állapotát Kapcsolat nélküli állapotra módosítja. A probléma megoldásához ellenőrizze a webhely konfigurációját, hogy a megfelelő StatusCode visszaadható-e a mintavételi útvonalról. Konfigurálja újra a Traffic Manager-mintavételt úgy, hogy egy 200-as értéket visszaadó útvonalra mutasson.

Ha a mintavétel a HTTPS protokollt használja, előfordulhat, hogy le kell tiltania a tanúsítványellenőrzést az SSL/TLS hibák elkerülése érdekében a teszt során. A következő PowerShell-utasítások letiltják az aktuális PowerShell-munkamenet tanúsítvány-érvényesítését:

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

[A Traffic Manager forgalomirányítási módszerei](traffic-manager-routing-methods.md)

[Mi a Traffic Manager](traffic-manager-overview.md)

[Felhőszolgáltatások](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operations on Traffic Manager (A Traffic Managerrel végezhető műveletek) (REST API-referencia)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager parancsmagok][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
