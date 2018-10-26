---
title: Az Update Management kapcsolatos hibák elhárítása
description: Ismerje meg, az Update Management hibáinak elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f52767058ef69d29465f1274109b6d3ffe58296c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092627"
---
# <a name="troubleshooting-issues-with-update-management"></a>Az Update Management kapcsolatos hibák elhárítása

Ez a cikk ismerteti a megoldásokat, amelyek találkozhat az Update Management használatakor problémák megoldásához.

Van egy ügynök hibaelhárító hibrid feldolgozó ügynök határozza meg a hibát kiváltó problémát. A hibaelhárító kapcsolatos további információkért lásd: [hibaelhárítás frissítési ügynök problémák](update-agent-issues.md). A többi probléma, a részletes információinak megtekintéséhez alábbi lehetséges okokat.

## <a name="general"></a>Általános kérdések

### <a name="components-enabled-not-working"></a>Forgatókönyv: Az "Update Management" megoldás összetevői engedélyezve van, és most a virtuális gép konfigurálása

#### <a name="issue"></a>Probléma

A következő üzenet jelenik a virtuális gépen 15 perc, az előkészítés után továbbra is:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

1. Térjen vissza az Automation-fiók kommunikációja blokkolva van folyamatban.
2. A virtuális gép típusától függenek előfordulhat, hogy a klónozott gép, amelynek nem a Microsoft Monitoring Agent telepítése a Sysprep használatával előkészített kell származnia.

#### <a name="resolution"></a>Megoldás:

1. Látogasson el, [hálózattervezés](../automation-hybrid-runbook-worker.md#network-planning) további információt arról, hogy mely címeket és portokat engedélyezni kell, az Update Management működjön.
2. Ha egy klónozott lemezképét, a sysprep lemezkép használatával először, és az MMA-ügynök telepítése után az a tény.

## <a name="windows"></a>Windows

Ha problémák merülnek fel a megoldás virtuális gépen való felvétele közben, ellenőrizze a **az Operations Manager** eseménynaplóban **alkalmazás- és szolgáltatásnaplók** események a helyi gépen Eseményazonosító **4502** és esemény üzenetet tartalmazó **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

A következő szakasz az egyes konkrét hibaüzeneteket és a egy lehetséges megoldás emeli ki. Egyéb regisztrációs problémák megtekintéséhez [hibaelhárítása a megoldás bevezetése](onboarding.md).

### <a name="machine-already-registered"></a>Forgatókönyv: Gép már regisztrálva van egy másik fiókkal

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenhet meg:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Ok

A gép már egy másik munkaterület az Update Management előkészítve.

#### <a name="resolution"></a>Megoldás:

Hajtsa végre a régi összetevők tisztítását által a gépen [a hibrid runbook-csoport törlése](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , és próbálkozzon újra.

### <a name="machine-unable-to-communicate"></a>Forgatókönyv: A gép nem tud kommunikálni a szolgáltatás

#### <a name="issue"></a>Probléma

A következő hibaüzeneteket egyikét kapja:

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

Egy proxy, átjáró vagy egy tűzfal blokkolja a hálózati kommunikációt lehet.

#### <a name="resolution"></a>Megoldás:

Tekintse át a hálózati, és győződjön meg, hogy a megfelelő portok és a címek használata engedélyezett. Lásd: [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning), portokat és az Update Management és a hibrid Runbook-feldolgozók által igényelt-címek listáját.

### <a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: Nem sikerült önaláírt tanúsítvány létrehozása

#### <a name="issue"></a>Probléma

A következő hibaüzeneteket egyikét kapja:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nem tudta önaláírt tanúsítvány létrehozása

#### <a name="resolution"></a>Megoldás:

Ellenőrizze a rendszer fiók olvasási hozzáféréssel rendelkezik mappába **C:\ProgramData\Microsoft\Crypto\RSA** , és próbálkozzon újra.

### <a name="nologs"></a>Forgatókönyv: Az Update Management adatokat a Log Analytics a gép nem látható

#### <a name="issue"></a>Probléma

Olyan gépeket azt mutatják be, mint **nincs értékelve** alatt **megfelelőségi**, de a Log Analytics a szívverési adatok jelennek meg a hibrid Runbook-feldolgozó, de nem az Update Management.

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó újra regisztrálni és telepíteni kell.

#### <a name="resolution"></a>Megoldás:

Kövesse a lépéseket [Windows hibrid Runbook-feldolgozó üzembe helyezése](../automation-windows-hrw-install.md) újra kell telepítenie a hibrid feldolgozó.

### <a name="hresult"></a>Forgatókönyv: Gép nincs értékelve, és bemutatja a kivétel HResult mutatja

#### <a name="issue"></a>Probléma

Olyan gépeket azt mutatják be, mint **nincs értékelve** alatt **megfelelőségi**, és a egy alatta kivétel üzenet jelenik meg.

#### <a name="cause"></a>Ok

Windows update nincs megfelelően konfigurálva a gépen.

#### <a name="resolution"></a>Megoldás:

Kattintson duplán a kivételt a kivétel teljes üzenet jelenik meg a vörös színnel jelenik meg. Tekintse át a következő táblázat a lehetséges megoldások vagy műveleteket tartalmazza:

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Keresse meg a megfelelő hibakód [Windows update kód Hibalista](https://support.microsoft.com/help/938205/windows-update-error-code-list) megtalálja a további részleteket a kivétel okát.        |
|`0x8024402C` vagy `0x8024401C`     | Ezek a hibák a hálózati problémák léptek fel. Győződjön meg arról, hogy a gépe képes-e a megfelelő hálózati kapcsolattal a frissítéskezelés. A szakaszban [hálózattervezés](../automation-update-management.md#ports) portokat és a szükséges címek listája.        |
|`0x8024402C`     | Ha egy WSUS-kiszolgálót használ, ellenőrizze, hogy a beállításkulcs-értékeket `WUServer` és `WUStatusServer` a beállításkulcs alatt `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` megfelelő a WSUS-kiszolgálót.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, hogy a Windows Update szolgáltatás (wuauserv) fut-e, és nincs letiltva.        |
|Bármely egyéb általános kivétel     | Keresés a lehetséges megoldásokat az interneten működnek és a helyi IT-támogatással.         |

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Forgatókönyv: A frissítési menetet nem indul el

#### <a name="issue"></a>Probléma

Egy frissítés futtatások Linuxos gépen indítása sikertelen.

#### <a name="cause"></a>Ok

A Linux hibrid feldolgozó állapota nem megfelelő.

#### <a name="resolution"></a>Megoldás:

Készítsen másolatot a következő naplófájl, és megőrizheti azokat hibaelhárítás céljából:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Forgatókönyv: A frissítési menetet elindul, de hibát észlel

#### <a name="issue"></a>Probléma

A frissítési menetet elindul, de a futtatás során hibát észlel.

#### <a name="cause"></a>Ok

Lehetséges okai lehetnek:

* A Package manager állapota nem megfelelő
* Adott csomagok zavarhatják felhőalapú javítása
* Egyéb okok

#### <a name="resolution"></a>Megoldás:

Ha egy frissítés futtatása után sikeresen elindul a linuxon futó hiba fordul elő, ellenőrizze a feladat kimenete a Futtatás a fertőzött gép. Azt tapasztalhatja, hogy hibaüzeneteket a kutatás és a művelet végrehajtása a számítógépe Csomagkezelő. Az Update Management a Csomagkezelőt a sikeres telepítések állapota megfelelő lesz szükséges.

Bizonyos esetekben a csomagfrissítéseket zavarhatja megakadályozza, hogy befejezze a frissítéstelepítések frissítéskezelési. Ha látni, hogy kell ezeket a csomagokat kizárása későbbi frissítési menetek, vagy telepítse manuálisan saját magának.

Ha mégsem sikerül megoldani a javítási problémát, készítsen másolatot a következő naplófájlt, és megőrizheti azokat **előtt** a következő központi telepítési elindítja a hibaelhárítás céljából:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.