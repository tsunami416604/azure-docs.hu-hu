---
title: Hasonlítsa össze az alapkonfigurációkat a fájlintegritási Monitorozás az Azure Security Centerben |} A Microsoft Docs
description: Ismerje meg, hogyan összehasonlítása alaptervek a fájlintegritási Monitorozás az Azure Security Centerben.
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
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358438"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Hasonlítsa össze az alapkonfigurációk fájl fájlintegritási Monitorozás (FIM) használatával

Fájl fájlintegritási Monitorozás (FIM) figyelmeztet, ha változás történik az erőforrások az érzékeny területekre így vizsgálja meg és oldja meg a jogosulatlan tevékenység. FIM figyeli a Windows-fájlok, beállításjegyzékek Windows és Linux-fájlok.

Ez a témakör ismerteti, hogyan engedélyezheti a fájlok és a beállításjegyzékek FIM. FIM kapcsolatos további információkért lásd: [a fájlintegritási Monitorozás az Azure Security Centerben](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>FIM miért érdemes használni?

Operációs rendszer, alkalmazások és kapcsolódó konfigurációk szabályozhatja az erőforrások viselkedését és biztonsági állapotát. Így a támadók a fájlokat, amelyek vezérlik az erőforrásokat, annak érdekében, hogy az erőforrások operációs rendszer overtake és/vagy a tevékenységek végrehajtása feltűnésmentesen célba.

Sőt számos törvényi megfelelőségi szabványai, például a PCI-DSS & ISO 17799 szükséges FIM ellenőrzések.  

## <a name="enable-built-in-recursive-registry-checks"></a>Beépített rekurzív beállításjegyzék-ellenőrzés engedélyezése

Az FIM beállításjegyzék hive alapértelmezett értéket adja meg a rekurzív közös biztonsági területre végrehajtott módosítások monitorozásához kényelmes módot.  Például egy támadó egy indítási vagy leállítási egy végrehajtási konfigurálásával LOCAL_SYSTEM környezetben végrehajtandó szkript konfigurálhat.  Az ilyen típusú módosítások monitorozásához a beépített-ellenőrzés engedélyezése.  

![Beállításjegyzék](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekurzív ellenőrzések csak ajánlott biztonsági struktúrát és egyéni beállításjegyzék elérési utak, nem vonatkoznak.  

## <a name="adding-a-custom-registry-check"></a>Egy egyéni beállításjegyzék-ellenőrzés hozzáadása

FIM alaptervek azonosítása egy jó állapot, az operációs rendszer jellemzőit és az alkalmazás elindításához.  Ebben a példában a jelszó szabályzat beállításai Windows Server 2008 vagy újabb tárgyaljuk.


|Szabályzat neve                 | Beállításjegyzék-beállítást|
|---------------------------------------|-------------|
|Tartományvezérlő: A Számítógépfiók jelszómódosításának| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Tartományi tag: Digitális titkosítása vagy aláírása (mindig)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Tartományi tag: (Ha lehet) biztonságos csatornák adatainak digitális titkosítása|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Tartományi tag: (Ha ez lehetséges) digitális aláírása|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Tartományi tag: Számítógépfiók jelszómódosításainak tiltása|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Tartományi tag: Számítógépfiók jelszavának maximális élettartama|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Tartományi tag: Erős (Windows 2000 vagy frissebb rendszerű) munkamenetkulcs megkövetelése|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Hálózati biztonság: Az NTLM korlátozása:  Ebben a tartományban az NTLM-hitelesítés|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Hálózati biztonság: Az NTLM korlátozása: Ebben a tartományban kiszolgálókivételek hozzáadása|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Hálózati biztonság: Az NTLM korlátozása: Az NTLM-hitelesítés naplózása ebben a tartományban|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Különböző operációsrendszer-verziók által támogatott beállításjegyzék beállításaival kapcsolatos további tudnivalókért tekintse meg a [csoportházirend-beállítások hivatkozási számolótábla](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*FIM figyelése beállításjegyzék alaptervek konfigurálása:*

1. Az a **Windows-beállításjegyzék felvétele a Change Tracking megoldásba** ablakban, a a **Windows-beállításkulcs** szöveget adja meg a beállításkulcsot.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![A beállításjegyzék FIM engedélyezése](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows-fájlok a változások követését

1. Az a **Windows-fájl felvétele a Change Tracking** ablakban, a a **Enter elérési út** szöveg mezőbe írja be a követni kívánt fájlokat tartalmazó mappát. A példában az alábbi ábrán **Contoso webalkalmazás** a D:\ található meghajtó belül a **ContosWebApp** mappastruktúra.  
1. Hozzon létre egy egyéni Windows fájlt egy nevet a beállítás osztály, rekurzió engedélyezése és a egy helyettesítő karakter (*) utótaggal a legfelső szintű mappa megadása.

    ![A fájl FIM engedélyezése](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Adatváltozások beolvasása

Fájlintegritási monitorozás az Azure Log Analytics belül található adatok / ConfigurationChange tábla állítsa.  

 1. Állítsa be a módosításainak összegzése beolvasni az erőforrás által időtartományát.
A következő példában azt olvassák összes módosítást a beállításjegyzékben és a fájlok a kategóriákban az elmúlt 14 napban:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. A beállításjegyzékbeli változások részleteinek megtekintéséhez:

    1. Távolítsa el **fájlok** származó a **ahol** záradék, 
    1. Távolítsa el az összegzési sort, és cserélje le egy rendelési záradékkal:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Jelentések archiválási és/vagy a Power BI-jelentésekhez channeled csv-fájlba exportálhatók.  

![FIM-adatok](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)