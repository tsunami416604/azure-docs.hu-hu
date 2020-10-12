---
title: Windows rendszerű virtuális asztali biztonságos URL-címek listája – Azure
description: A feloldani kívánt URL-címek listája, hogy a Windows rendszerű virtuális asztali környezet a kívánt módon működjön.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f9f68d3734cd7de83a2ddd376caefa410c619d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291109"
---
# <a name="safe-url-list"></a>Biztonságos URL-címek listája

Fel kell oldania bizonyos URL-címek blokkolását, hogy a Windows rendszerű virtuális asztal üzembe helyezése megfelelően működjön. Ez a cikk felsorolja ezeket az URL-címeket, hogy tudja, melyek biztonságosak.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek hozzáféréssel kell rendelkezniük a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Ügynök-és SXS-verem frissítései|AzureCloud|
|*.core.windows.net|443|Ügynök forgalma|AzureCloud|
|*.servicebus.windows.net|443|Ügynök forgalma|AzureCloud|
|gcs.prod.monitoring.core.windows.net|443|Ügynök forgalma|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Piactér|AzureCloud|
|kms.core.windows.net|1688|A Windows aktiválása|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal támogatás|AzureCloud|
| 169.254.169.254 | 80 | [Azure-példány metaadatainak szolgáltatási végpontja](../virtual-machines/windows/instance-metadata-service.md) | N.A. |
| 168.63.129.16 | 80 | [Munkamenet-gazdagép állapotának figyelése](../virtual-network/security-overview.md#azure-platform-considerations) | N.A. |

>[!IMPORTANT]
>A Windows virtuális asztal mostantól támogatja a FQDN címkét. További információ: [a Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez](../firewall/protect-windows-virtual-desktop.md).
>
>Javasoljuk, hogy az URL-címek helyett a teljes tartománynevet vagy a szolgáltatás címkéit használja a szolgáltatási problémák megelőzése érdekében. A felsorolt URL-címek és címkék csak a Windows rendszerű virtuális asztali helyekhez és erőforrásokhoz tartoznak. Nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directoryhoz.

A következő táblázat felsorolja azokat az opcionális URL-címeket, amelyekhez az Azure-beli virtuális gépek hozzáférhetnek:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*.microsoftonline.com|443|Hitelesítés a Microsoft Online Services szolgáltatásban|Nincs|
|*. events.data.microsoft.com|443|Telemetria szolgáltatás|Nincs|
|www.msftconnecttest.com|443|Észleli, ha az operációs rendszer csatlakozik az internethez|Nincs|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Nincs|
|login.windows.net|443|Bejelentkezés a Microsoft Online Servicesbe, Microsoft 365|Nincs|
|*. sfx.ms|443|OneDrive-ügyfélszoftver frissítései|Nincs|
|*. digicert.com|443|Tanúsítvány visszavonásának ellenőrzése|Nincs|

>[!NOTE]
>A Windows virtuális asztal jelenleg nem tartalmaz olyan IP-címtartományok listáját, amelyekkel engedélyezhető a hálózati forgalom. Jelenleg csak bizonyos URL-címek blokkolását támogatjuk.
>
>A biztonságos Office-hoz kapcsolódó URL-címek listáját, beleértve a szükséges Azure Active Directory kapcsolódó URL-címeket, lásd: [Office 365 URL-címek és IP-címtartományok](/office365/enterprise/urls-and-ip-address-ranges).
>
>A szolgáltatás forgalmát tartalmazó URL-címekhez a helyettesítő karaktert (*) kell használnia. Ha nem kívánja használni az ügynökkel kapcsolatos forgalmat, a következő módon keresheti meg az URL-címeket helyettesítő karakterek nélkül:
>
>1. Regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali gazdaszámítógépen.
>2. Nyissa meg az **eseménynaplót**, majd lépjen a **Windows logs**  >  **Application**  >  **WVD-Agent** elemre, és keresse meg a 3701-es azonosítójú eseményt.
>3. Oldja fel a 3701-es AZONOSÍTÓJÚ esemény alatt megtalált URL-címeket. Az 3701-es AZONOSÍTÓJÚ esemény URL-címei régiónként jellemzőek. Az összes olyan régióhoz tartozó URL-címmel meg kell ismételnie a blokkolási folyamatot, amelyre telepíteni szeretné a virtuális gépeket a alkalmazásban.

## <a name="remote-desktop-clients"></a>Távoli asztali ügyfelek

A használt Távoli asztal ügyfeleknek hozzáféréssel kell rendelkezniük a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Ügyfél (ek)|
|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|Mind|
|*.servicebus.windows.net|443|Hibaelhárítási hibák|Mind|
|go.microsoft.com|443|Microsoft-Fwlinkek|Mind|
|aka.ms|443|Microsoft URL-rövidített|Mind|
|docs.microsoft.com|443|Dokumentáció|Mind|
|privacy.microsoft.com|443|Adatvédelmi nyilatkozat|Mind|
|query.prod.cms.rt.microsoft.com|443|Ügyfelek frissítései|Windows asztali rendszer|

>[!IMPORTANT]
>Az URL-címek megnyitása elengedhetetlen a megbízható ügyfél-élményhez. Az ezekhez az URL-címekhez való hozzáférés letiltása nem támogatott, és a szolgáltatás funkcióit is befolyásolja.
>
>Ezek az URL-címek csak az ügyfél helyei és erőforrásainak felelnek meg. A lista nem tartalmaz URL-címeket más olyan szolgáltatásokhoz, mint például a Azure Active Directory. Azure Active Directory URL-címek az [Office 365 URL-címek és IP-címtartományok](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)alatt találhatók az 56 azonosító alatt.
