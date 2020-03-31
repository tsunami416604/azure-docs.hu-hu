---
title: Fájlintegritás monitorozása az Azure Security Centerben
description: Ismerje meg, hogyan hasonlíthatja össze az alapkonfigurációkat az Azure Security Center fájlintegritás-figyelésével.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664409"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Alapértékek összehasonlítása fájlintegritási monitorozással (FIM)

A fájlintegritás-figyelés (FIM) tájékoztatja Önt, ha az erőforrások bizalmas területein változások történnek, így kivizsgálhatja és kezelheti a jogosulatlan tevékenységeket. A FIM a Windows-fájlokat, a Windows-nyilvántartásokat és a Linux-fájlokat figyeli.

Ez a témakör bemutatja, hogyan engedélyezheti a FIM-et a fájlokon és a nyilvántartásokon. A FIM szolgáltatásról további információt az [Azure Security Center fájlintegritás-figyelése című témakörben talál.](security-center-file-integrity-monitoring.md)

## <a name="why-use-fim"></a>Miért válassza partnerünket, a(z) FIM-et?

Az operációs rendszer, az alkalmazások és a kapcsolódó konfigurációk szabályozzák az erőforrások viselkedését és biztonsági állapotát. Ezért a támadók az erőforrásokat vezérlő fájlokat célozzák meg, hogy megelőzzék az erőforrás operációs rendszerét, és/vagy észlelések nélkül hajtsanak végre tevékenységeket.

Valójában számos jogszabályi megfelelőségi szabvány, mint például a PCI-DSS & Az ISO 17799, fim-ellenőrzések et igényel.  

## <a name="enable-built-in-recursive-registry-checks"></a>Beépített rekurzív beállításjegyzék-ellenőrzések engedélyezése

A FIM rendszerleíró adatbázis-struktúrájának alapértelmezései kényelmes módot biztosítanak a rekurzív változások figyelésére a közös biztonsági területeken.  Az ellenség például konfigurálhat egy parancsfájlt úgy, hogy LOCAL_SYSTEM környezetben hajtson végre egy végrehajtásindításkor vagy leállításkor.  Az ilyen típusú módosítások figyeléséhez engedélyezze a beépített ellenőrzést.  

![Registry](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> A rekurzív ellenőrzések csak az ajánlott biztonsági struktúrákra vonatkoznak, egyéni beállításjegyzék-elérési utakra nem.  

## <a name="adding-a-custom-registry-check"></a>Egyéni beállításjegyzék-ellenőrzés hozzáadása

A FIM alapkonfigurációk az operációs rendszer ismert állapotának és a támogató alkalmazás jellemzőinek azonosításával kezdődnek.  Ebben a példában a Windows Server 2008 és újabb verziók jelszóházirend-konfigurációira összpontosítunk.


|Házirend neve                 | Beállításjegyzék-beállítás|
|---------------------------------------|-------------|
|Tartományvezérlő: Számítógépfiók jelszómódosításának visszautasítása| GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Tartományi tag: A biztonságos csatornák adatainak digitális titkosítása vagy aláírása (mindig)|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Tartományi tag: Az adatok digitális titkosítása (ha lehet)|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Tartományi tag: Az adatok digitális aláírása (ha lehet)|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Tartományi tag: Számítógépfiók jelszómódosításainak tiltása|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Tartományi tag: Számítógépfiók jelszavának maximális kora|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Tartományi tag: Erős (Windows 2000 vagy frissebb rendszerű) munkamenetkulcs megkövetelése|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Hálózati biztonság: NTLM: NTLM-hitelesítés korlátozása ebben a tartományban|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Hálózati biztonság: Az NTLM korlátozása: Kiszolgálókivételek hozzáadása ebből a tartományból|GÉP\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Hálózati biztonság: NTLM korlátozása: NTLM-hitelesítés naplózása ebben a tartományban|GÉP\Rendszer\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Az operációs rendszer különböző verziói által támogatott beállításjegyzék-beállításokról a [Csoportházirend beállításai hivatkozási táblázatban olvashat bővebben.](https://www.microsoft.com/download/confirmation.aspx?id=25250)

*A FIM beállítása a rendszerleíró adatbázis alapkonfigurációinak figyelésére:*

1. A **Windows beállításjegyzék hozzáadása a változáskövetéshez** ablakban a **Windows beállításkulcsszövege** mezőbe írja be a beállításkulcsot.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Fim engedélyezése a rendszerleíró adatbázisban](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>A Windows-fájlok módosításának nyomon követése

1. A Windows fájl hozzáadása a **változáskövetéshez** ablak **Elérési út** szövegmezőjében adja meg a követni kívánt fájlokat tartalmazó mappát. Az alábbi ábrán szereplő példában a **Contoso Web App** a D:\ a **ContosWebApp** mappastruktúrán belül.  
1. Egyéni Windows-fájlbejegyzés létrehozása a beállítási osztály nevének megadásával, a rekurzió engedélyezésével és a felső mappa helyettesítő karakterrel (*) történő megadásával.

    ![FIM engedélyezése fájlban](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Módosítási adatok beolvasása

A fájlintegritás-figyelési adatok az Azure Log Analytics / ConfigurationChange táblakészletben találhatók.  

 1. Állítson be egy időtartományt a módosítások erőforrásonkénti összegzésének beolvasásához.
A következő példában az elmúlt tizennégy nap összes változását lehívtuk a rendszerleíró adatbázis és a fájlok kategóriáiban:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. A rendszerleíró adatbázis változásainak részletei:

    1. **Fájlok** eltávolítása a **where** záradékból, 
    1. Távolítsa el az összegzési sort, és cserélje le egy rendelési záradékra:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

A jelentések archiváláséshez és/vagy Power BI-jelentésekhez irányíthatók csv-be.  

![FIM-adatok](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)