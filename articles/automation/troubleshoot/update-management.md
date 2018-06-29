---
title: Frissítéskezelés a hibák elhárítása
description: További frissítés-kezeléssel kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063989"
---
# <a name="troubleshooting-issues-with-update-management"></a>Frissítéskezelés a problémák elhárítása

Ez a cikk ismerteti, amelyek megoldások frissítéskezelés használata során esetlegesen felmerülő problémák megoldása

## <a name="windows"></a>Windows

Ellenőrizze, hogy a felmerülő problémák közben bevezetni a megoldás a virtuális gépen a **Operations Manager** eseménynaplóban **alkalmazás- és szolgáltatásnaplók** eseményeit a helyi számítógépen Eseményazonosító **4502** és az eseménynapló-üzenet tartalmazó **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Az alábbi szakasz az egyes vonatkozó hibaüzeneteket és egy lehetséges megoldás emeli ki. Más bevezetési problémák megtekintéséhez [megoldás bevezetése hibaelhárítása](onboarding.md).

### <a name="machine-already-registered"></a>Forgatókönyv: Gép már regisztrálva van egy másik fiókkal

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Ok

A számítógép már egy másik munkaterületet, a felügyeletéhez előkészítve.

#### <a name="resolution"></a>Megoldás:

A régi összetevőihez karbantartást végez által a gépen [a hibrid forgatókönyv-csoport törlése](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , és próbálkozzon újra.

### <a name="machine-unable-to-communicate"></a>Forgatókönyv: A gép nem tud kommunikálni a szolgáltatás

#### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek valamelyike kapja:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Ok

A proxy, az átjáró vagy a hálózati kommunikációt blokkoló tűzfal lehet.

#### <a name="resolution"></a>Megoldás:

Tekintse át a hálózati, és ellenőrizze a megfelelő portok és címek használhatók. Lásd: [hálózati vonatkozó követelmények](../automation-hybrid-runbook-worker.md#network-planning), portokat és felügyelete és a hibrid forgatókönyv-feldolgozók által igényelt címek listáját.

### <a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: Nem sikerült létrehozni az önaláírt tanúsítvány

#### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek valamelyike kapja:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Ok

A hibrid forgatókönyv-feldolgozó nem tudta létrehozni az önaláírt tanúsítvány

#### <a name="resolution"></a>Megoldás:

Ellenőrizze a rendszer fióknak van olvasási hozzáférési joga mappa **C:\ProgramData\Microsoft\Crypto\RSA** , és próbálkozzon újra.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Forgatókönyv: A frissítési menetet nem indul el

#### <a name="issue"></a>Probléma

Egy frissítés futtatása sikertelen lesz a Linux-számítógép indításához.

#### <a name="cause"></a>Ok

A Linux hibrid feldolgozó állapota nem megfelelő.

#### <a name="resolution"></a>Megoldás:

Másolatot készít a következő naplófájlt, és megőrizheti a hibaelhárítás céljából:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Forgatókönyv: A frissítési menetet elindul, de hibát észlel

#### <a name="issue"></a>Probléma

Frissítési menet elindul, de a futtatás során hibát észlel.

#### <a name="cause"></a>Ok

Lehetséges okai lehetnek:

* A Package manager állapota nem megfelelő
* Adott csomagok ütközhet az olyan felhőalapú javítását
* Más okok miatt

#### <a name="resolution"></a>Megoldás:

Ha hiba fordul elő sikeresen Linux elindulása után frissítési, tekintse meg a feladat, a fertőzött gép futtató kimenetét. Előfordulhat, hogy a vonatkozó hibaüzeneteket a kutatás és művelet végrehajtása a Csomagkezelő a gép. Frissítéskezelés a lesz kifogástalan, a sikeres központi telepítést a Csomagkezelő igényel.

Bizonyos esetekben csomag frissítések zavarhatja frissítése felügyeleti akadályozza meg, hogy egy frissítés központi telepítés befejeződését. Ha megjelenik, amely, vagy zárja ki ezeket a csomagokat a jövőbeli frissítési menetek, vagy telepítse manuálisan a összekapcsolta magát.

Nem oldható fel egy javítási hiba, ha másolatot készít a következő naplófájlt, és megőrizni **előtt** hibaelhárítási célból elindítja a következő központi telepítéséhez:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>További lépések

Ha nem talál a problémát, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák további támogatásért:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, az Azure támogatási incidens fájl is. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.