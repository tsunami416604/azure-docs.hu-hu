---
title: Távoli asztali ügyfél-kapcsolatok a Windows virtuális asztal – Azure
description: Kapcsolatos problémák megoldását, virtuális asztali Windows-bérlős környezetben kapcsolatok beállításakor.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c5a67e22c301a2afc73a46a6def9a514426c497f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928047"
---
# <a name="remote-desktop-client-connections"></a>Távoli asztali kapcsolatok

Ez a cikk segítségével Windows virtuális asztali kapcsolatok problémáinak megoldásához.

## <a name="provide-feedback"></a>Visszajelzés küldése

A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai.

## <a name="you-cant-open-a-web-client"></a>A webes ügyfél nem nyitható meg

Ellenőrizze, hogy nincs megnyitva egy másik webhely; internetkapcsolat Ha például [www.Bing.com](https://www.bing.com).

Használat **nslookup** ellenőrizze a DNS képes feloldani a teljes Tartománynevet:

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Próbáljon meg csatlakozni egy másik ügyfél, például a távoli asztali ügyfél Windows 7 vagy Windows 10 és az ellenőrzés tekintse meg, ha a webes ügyfél megnyitható.

### <a name="error-opening-another-site-fails"></a>Hiba: Nyissa meg egy másik hely sikertelen

**OK:** Hálózati problémák és/vagy valamilyen okból kimaradás lép.

**Javítás:** Hálózati ügyfélszolgálatától kérhet.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Hiba: Az nslookup nem oldható fel a neve

**OK:** Hálózati problémák és/vagy valamilyen okból kimaradás lép.

**Javítás:** Forduljon a hálózati támogatás

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Hiba: Nem lehet csatlakoztatni, de más ügyfelek csatlakozhatnak.

**OK:** A böngésző nem megszokott módon működő várt és leállt.

**Javítás:** Kövesse ezeket az utasításokat a böngésző hibaelhárítása.

1. Indítsa újra a böngészőt.
2. Törölje a böngésző cookie-kat. Lásd: [törlése a cookie-fájlokat az Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Törölje a böngésző gyorsítótárát. Lásd: [törölje a böngésző számára a böngésző gyorsítótárát](https://binged.it/2RKyfdU).
4. Nyissa meg a böngészőt privát üzemmódban.

## <a name="web-client-stops-responding-or-disconnects"></a>Webes ügyfél nem válaszol, vagy leválasztása

Próbáljon meg egy másik böngészőben vagy ügyfél használatával.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Hiba: Más böngészőkkel és ügyfelek is megfelelően működni, vagy nem nyílik meg

**OK:** Hálózati és/vagy műveletet a rendszer probléma vagy szünetelés

**Javítás:** Forduljon a támogatási csapat.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webes ügyfél tartja kéri a hitelesítő adatokat

Ha a webes ügyfél tartja kéri a hitelesítő adatokat, kövesse az alábbi utasításokat.

1. Ellenőrizze a webes ügyfél URL-címe.
2. Győződjön meg arról, hogy hitelesítő adatokat a Windows virtuális asztali környezetben, az URL-címhez kötött.
3. Törölje a böngésző cookie-kat. Lásd: [törlése a cookie-fájlokat az Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Törölje a böngésző gyorsítótárát. Lásd: [törölje a böngésző számára a böngésző gyorsítótárát](https://binged.it/2RKyfdU).
5. Nyissa meg a böngészőt privát üzemmódban.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Távoli asztali ügyfél Windows 7 vagy Windows 10-es nem válaszol, vagy nem nyitható meg

A következő PowerShell-parancsmagok használatával sávon kívüli (OOB) ügyfél beállításjegyzékek karbantartása.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navigáljon a **%AppData%\RdClientRadc** , és törölje az összes tartalmat.

Távolítsa el, majd telepítse újra a távoli asztali ügyfél Windows 7 és Windows 10-es.

## <a name="troubleshooting-end-user-connectivity"></a>A végfelhasználói hibaelhárítása

Egyes esetekben a felhasználók a csatorna és a helyi erőforrások elérésére, de továbbra is fennáll, konfigurálás, rendelkezésre állási és teljesítménybeli problémák, amelyek meggátolják távoli erőforrásokhoz való hozzáférés. Ebben az esetben a felhasználó lekéri az üzeneteket hasonló:

![Távoli asztali kapcsolat hibaüzenet.](media/eb76b666808bddb611448dfb621152ce.png)

![Nem lehet csatlakozni az átjáró hibaüzenet.](media/a8fbb9910d4672147335550affe58481.png)

Kövesse az alábbi általános hibaelhárítási utasításokat az ügyfél csatlakozási hibakódok.

1. Erősítse meg a felhasználónevet és az idő, amikor probléma lépett fel.
2. Nyissa meg **PowerShell** , valamint a Windows virtuális asztal bérlőhöz, ahol a hiba történt a következő kapcsolat.
3. Ellenőrizze a kapcsolatot a megfelelő bérlő **Get-RdsTenant.**
4. Ha szükséges, állítsa be a bérlő csoport-környezet **Set-RdsContext – TenantGroupt\<TenantGroup\>**.
5. Használatával **Get-RdsHostPool** és **Get-RdsSessionHost** parancsmagok, győződjön meg arról, hogy hibaelhárítási történik a megfelelő gazdagép készleten.
6. Hajtsa végre az alábbi parancsot a megadott időtartomány típusú kapcsolat összes sikertelen tevékenységek listájának lekérése:

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

7. Használatával a **ActivityId** az előző parancsmag kimenetében, futtassa az alábbi parancsot:

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

8. A parancs az alábbi kimeneti hasonló kimenetet eredményez. Használat **ErrorCodeSymbolic** és **ErrorMessage** hibaelhárítása a hiba okát.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Hiba: O_ADD_USER_TO_GROUP_FAILED / nem sikerült hozzáadni a felhasználói csoporthoz ≤username≥ = Remote Desktop Users =. Indoklás: Win32.ERROR_NO_SUCH_MEMBER

**OK:** Virtuális gép nem csatlakozik a tartományban, ahol felhasználói objektum.

**Javítás:** Adja hozzá a virtuális gép a megfelelő tartományhoz. Lásd: [egy Windows Servert futtató virtuális gép csatlakoztatása felügyelt tartományokhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Hiba: Az nslookup nem oldható fel a neve

**OK:** Hálózati problémák vagy valamilyen okból kimaradás lép.

**Javítás:** Forduljon a hálózati támogatás

### <a name="error-connectionfailedclientprotocolerror"></a>Hiba: ConnectionFailedClientProtocolError

**OK:** Virtuális gépek, hogy a felhasználó próbál csatlakozni amelyek nem tartományhoz csatlakozó-e.

**Javítás:** Csatlakozzon a tartományvezérlő egy gazdagép készlet részét képező összes virtuális gépet.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Felhasználó kapcsolódik, de semmi nem jelenik meg (nincs adatcsatorna)

A felhasználó megkezdheti a távoli asztali ügyfelek, és elvégezheti a hitelesítést, azonban a felhasználó nem látja a webalkalmazás-felderítés hírcsatorna ikonokat.

Győződjön meg arról, hogy a felhasználó a hibáknak felelősként alkalmazáscsoportok Ez a parancssor használatával:

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Győződjön meg arról, hogy a felhasználó jelentkezik be a helyes hitelesítő adataival.

Ha a webes ügyfél használatban van, győződjön meg arról, hogy nincsenek-e problémák gyorsítótárazott hitelesítő adatok.

## <a name="next-steps"></a>További lépések

- Hibaelhárítási Windows virtuális asztal és a kiterjesztés nyomon követi az áttekintést lásd: [hibaelhárítási áttekintése, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- Windows virtuális asztali környezetben egy bérlő és a gazdagép-készlet létrehozása során problémák hibaelhárítása: [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md).
- Egy virtuális gépet (VM) konfigurálása a Windows virtuális asztal során problémák hibaelhárítása: [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A virtuális asztal Windows PowerShell-lel kapcsolatos problémák elhárításához lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Nyissa meg a hibaelhárítás az oktatóanyagot, tekintse meg [oktatóanyag: Resource Manager-sablon üzemelő példányok hibaelhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).