---
title: Felhasználói adatok és az Azure Active Directory áteresztő hitelesítés |} Microsoft Docs
description: Ez a cikk az Azure Active Directory (Azure AD) áteresztő hitelesítés és GDPR megfelelőségi foglalkozik.
services: active-directory
keywords: Az Azure AD Connect áteresztő hitelesítés GDPR, szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést.
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 910eb5bdd1b9d4a2a27a27c89812584bb068bec0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Felhasználói adatok és az Azure Active Directory áteresztő hitelesítés


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés

Az Azure AD áteresztő hitelesítés hoz létre a következő napló típusú, amely tartalmazhat EUII:

- Az Azure AD Connect nyomkövetésének naplófájljait.
- Hitelesítési ügynök nyomkövetésének naplófájljait.
- Windows-esemény naplófájlokat.

Felhasználói adatok áteresztő hitelesítés kétféleképpen érhető el:

1.  Kérésre adatok kinyerése a személy, és adatok eltávolítása a telepítések az adott személyt.
2.  Győződjön meg arról, 48 órával adatot nem őrződnek meg.

Javasoljuk a második lehetőség, mert az egyszerűbb bevezetéséhez és karbantartásához. Az alábbiakban az egyes napló utasításokat:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect nyomkövetési naplófájlok törlése

Ellenőrizze a tartalmát **%ProgramData%\AADConnect** mappa és a delete a nyomkövetési napló tartalma (**nyomkövetési -\*.log** fájlok) a mappa telepítése vagy frissítése az Azure AD Connect 48 órán belül vagy konfigurációjához áteresztő hitelesítés, mint ez a művelet GDPR által szabályozott adatokat hozhat létre.

>[!IMPORTANT]
>Ne törölje a **PersistedState.xml** fájl ebben a mappában, ezt a fájlt az Azure AD Connect korábbi telepítés állapotának karbantartásához használt és használatos, ha egy frissítés telepítésének történik. A fájl soha nem fog tartalmaz egy személy kapcsolatos adatokat, és soha nem lesz törölve.

Tekintse át és a Windows Intézőben nyomkövetési naplófájlok törlése vagy a következő PowerShell-parancsfájl segítségével hajtsa végre a szükséges műveleteket:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt a fájlt a ". PS1 "kiterjesztéssel. Futtassa ezt a parancsfájlt, igény szerint.

További részletek kapcsolódó az Azure AD Connect GDPR követelmények, lásd: [Ez a cikk](active-directory-aadconnect-gdpr.md).

### <a name="delete-authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplók törlése

A termék is létrehozhat **Windows-Eseménynapló**. További tudnivalókért olvassa el [Ez a cikk](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Az áteresztő hitelesítés ügynök naplók megtekintéséhez nyissa meg a **Eseménynapló** a kiszolgálón, és ellenőrizze az alkalmazás **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Hitelesítési ügynök nyomkövetési naplófájlok törlése

Rendszeresen ellenőrizni kell a tartalmát **%ProgramData%\Microsoft\Azure AD Connect hitelesítési Agent\Trace\**  , és törölje a mappa tartalmának 48 óránként. 

>[!IMPORTANT]
>Ha a hitelesítési ügynök szolgáltatás fut, nem lesz tudja törölni az aktuális fájl a mappában. Állítsa le a szolgáltatást, majd próbálkozzon újra. Felhasználói bejelentkezési hibák elkerülése érdekében érdemes már konfigurálta az áteresztő hitelesítés [magas rendelkezésre állású](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

Tekintse át és törli ezeket a fájlokat, a Windows Intézővel, vagy a szükséges műveletek elvégzéséhez a következő parancsfájlt használhatja:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

A parancsfájl futtatását ütemezése 48 óránként kövesse az alábbi lépéseket:

1.  Mentse a parancsfájlt a fájlt a ". PS1 "kiterjesztéssel.
2.  Nyissa meg **Vezérlőpult** , majd kattintson a **rendszer és biztonság**.
3.  Az a **felügyeleti eszközök** elemcsoportban kattintson a "**feladatok ütemezése**".
4.  A **Feladatütemező**, kattintson a jobb gombbal a "**feladat ütemezése könyvtár**"és kattintson a"**létrehozása alapvető feladat...** ".
5.  Adja meg az új feladat nevét, és kattintson a **következő**.
6.  Jelölje ki "**napi**" számára a **feladat eseményindítóját** kattintson **következő**.
7.  Az ismétlődés értékűre két napon, majd kattintson a **következő**.
8.  Jelölje ki "**egy programot elindítani**", a művelet, és kattintson **következő**.
9.  Típusa "**PowerShell**"a Program vagy parancsfájl a mezőbe, és a jelölőnégyzetet"**(nem kötelező) argumentumok hozzáadása**", írja be a teljes elérési útját a korábban létrehozott parancsfájlt, majd kattintson a **tovább**.
10. A következő képernyő fog létrehozni a feladat összegzését jeleníti meg. Ellenőrizze az értékeket, és kattintson a **Befejezés** a feladat létrehozásához:
 
### <a name="note-about-domain-controller-logs"></a>Tartomány a tartományvezérlő naplók kapcsolatos megjegyzés:

Ha naplózás engedélyezve van, a a termék hoz létre a biztonsági naplók a tartományvezérlők. Naplózási házirendek konfigurálásával kapcsolatos további tudnivalókért olvassa el ezt [cikk](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy házirendet, a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
