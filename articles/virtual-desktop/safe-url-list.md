---
title: Windows rendszerű virtuális asztali biztonságos URL-címek listája – Azure
description: A feloldani kívánt URL-címek listája, hogy a Windows rendszerű virtuális asztali környezet a kívánt módon működjön.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9b1bdfc326ff217e68785d823b4af046af3241b7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225179"
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
|prod.warmpath.msftcloudes.com|443|Ügynök forgalma|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Piactér|AzureCloud|
|kms.core.windows.net|1688|A Windows aktiválása|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal támogatás|AzureCloud|

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
>2. Nyissa meg az **eseménynaplót**, majd lépjen a **Windows logs**  >  **Application**  >  **WVD-Agent** elemre, és keresse meg a 3702-es azonosítójú eseményt.
>3. Az 3702-as AZONOSÍTÓJÚ esemény alatt található URL-címek engedélyezési listája. Az 3702-es AZONOSÍTÓJÚ esemény URL-címei régiónként jellemzőek. Az összes olyan régióhoz tartozó URL-címmel meg kell ismételnie a blokkolási folyamatot, amelyre telepíteni szeretné a virtuális gépeket a alkalmazásban.

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
>Az URL-címek megnyitása elengedhetetlen a megbízható ügyfél-élményhez. Az ezekhez az URL-címekhez való hozzáférés letiltása nem támogatott, és a szolgáltatás funkcióit is befolyásolja. Ezek az URL-címek csak az ügyfél helyeinek és erőforrásainak felelnek meg, és nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directoryhoz.