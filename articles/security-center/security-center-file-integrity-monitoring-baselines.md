---
title: Az alapkonfigurációk összehasonlítása a fájlok integritásának figyelésével Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan hasonlíthatja össze az alapkonfigurációkat a fájlok integritásának figyelésével Azure Security Centerban.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: afc03baa71f17deb0b923f483fde214a86c5e9b4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296471"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Alaptervek összehasonlítása a fájl sértetlenségének figyelésével (FIM)

A fájl integritásának figyelése (FIM) arról tájékoztatja, hogy Mikor változnak az erőforrások érzékeny területei, így megvizsgálhatja és kezelheti a jogosulatlan tevékenységeket. A FIM figyeli a Windows-fájlokat, a Windows-nyilvántartásokat és a Linux-fájlokat.

Ez a témakör bemutatja, hogyan engedélyezheti a FIM-t a fájlokon és a jegyzékeken. További információ a FIM-ről: [a fájlok integritásának figyelése Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Miért érdemes a FIM-t használni?

Az operációs rendszer, az alkalmazások és a társított konfigurációk vezérlik az erőforrások viselkedését és biztonsági állapotát. Ezért a támadók az erőforrásokat vezérlő fájlokat célozzák meg, hogy az erőforrás operációs rendszerének és/vagy végrehajtásának észlelése nélkül is el lehessen végezni a műveleteket.

Valójában számos szabályozási megfelelőségi szabvány, például a PCI-DSS & ISO 17799 megköveteli a FIM-vezérlők megvalósítását.  

## <a name="enable-built-in-recursive-registry-checks"></a>Beépített rekurzív beállításjegyzék-ellenőrzések engedélyezése

A FIM beállításjegyzék-struktúra alapértelmezései kényelmes módot biztosítanak a rekurzív változások figyelésére a közös biztonsági területeken belül.  Előfordulhat például, hogy egy támadó úgy konfigurálhat egy parancsfájlt, hogy LOCAL_SYSTEM környezetben fusson egy végrehajtás indításkor vagy leállításkor történő konfigurálásával.  Az ilyen típusú változások figyeléséhez engedélyezze a beépített ellenőrzést.  

![Beállításjegyzék](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> A rekurzív ellenőrzések csak az ajánlott biztonsági struktúrákra vonatkoznak, és nem az egyéni beállításjegyzékbeli elérési utakra.  

## <a name="adding-a-custom-registry-check"></a>Egyéni beállításjegyzék-ellenőrző hozzáadása

A FIM alapkonfigurációk az operációs rendszer és a támogató alkalmazás ismert állapotának azonosításával kezdődnek.  Ebben a példában a Windows Server 2008-es és újabb verziójának jelszóházirend-konfigurációit fogjuk összpontosítani.


|Házirend neve                 | Beállításjegyzék-beállítás|
|---------------------------------------|-------------|
|Tartományvezérlő: A számítógépfiók jelszavának módosításainak elutasítása| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Tartományi tag: Biztonságos csatorna adatai digitális titkosítása vagy aláírása (mindig)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Tartományi tag: Biztonságos csatorna adatai digitális titkosítása (ha lehetséges)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Tartományi tag: Biztonságos csatornák digitális aláírása (ha lehetséges)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Tartományi tag: A számítógépfiók jelszavának módosításainak letiltása|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Tartományi tag: Számítógépfiók maximális jelszavának kora|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Tartományi tag: Erős (Windows 2000 vagy újabb) munkamenetkulcs megkövetelése|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Hálózati biztonság: NTLM korlátozása:  NTLM-hitelesítés ebben a tartományban|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Hálózati biztonság: NTLM korlátozása: Kiszolgálói kivételek hozzáadása ebben a tartományban|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Hálózati biztonság: NTLM korlátozása: NTLM-hitelesítés naplózása ebben a tartományban|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Ha többet szeretne megtudni a különböző operációsrendszer-verziók által támogatott beállításjegyzék-beállításokról, tekintse meg a [csoportházirend beállítások hivatkozását tartalmazó táblázatot](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*A FIM konfigurálása a beállításjegyzék alapkonfigurációinak figyeléséhez:*

1. A **Windows beállításjegyzék hozzáadása Change Tracking** ablakban a **Windows beállításjegyzék kulcsa** szövegmezőbe írja be a beállításkulcsot.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![A FIM engedélyezése a beállításjegyzékben](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows-fájlok változásainak követése

1. A **Windows-fájl hozzáadása Change Tracking** ablakban az **elérési út megadása** szövegmezőbe írja be azt a mappát, amely tartalmazza a nyomon követni kívánt fájlokat. Az alábbi ábrán látható példában a **contoso** -webalkalmazás a D:\ található. meghajtó a **ContosWebApp** -mappa struktúráján belül.  
1. Hozzon létre egy egyéni Windows-bejegyzést úgy, hogy megadja a beállítási osztály nevét, engedélyezi a rekurziót, és megadta a legfelső mappát helyettesítő karakter (*) utótaggal.

    ![FIM engedélyezése fájlon](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Módosítási adatok beolvasása

A fájlok integritásának figyelési adatok az Azure Log Analytics/konfigurációváltozás tábla készletén belül találhatók.  

 1. Állítsa be az időtartományt, hogy lekérje az erőforrás változásainak összefoglalását.
A következő példában a beállításjegyzék és a fájlok kategóriáinak utolsó tizennégy napján beolvasunk minden változást:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. A beállításjegyzék módosításainak részleteinek megtekintése:

    1. **Fájlok** eltávolítása a **Where** záradékból 
    1. Távolítsa el az összegzési sort, és cserélje le egy megrendelési záradékra:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

A jelentések a CSV-fájlba exportálhatók archiválásra és/vagy csatornára Power BI jelentésbe.  

![FIM-adatbázis](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)