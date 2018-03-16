---
title: "Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezést - GDPR megfelelőségi |} Microsoft Docs"
description: "Ez a cikk az Azure Active Directory (Azure AD) zökkenőmentes egyszeri Bejelentkezéssel és GDPR megfelelőségi foglalkozik."
services: active-directory
keywords: "Mi az Azure AD Connect GDPR, szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Az Azure AD zökkenőmentes egyszeri bejelentkezés: GDPR megfelelőségi

## <a name="overview"></a>Áttekintés

A lehetséges, hogy 2018, az európai adatvédelmi törvény, a [általános Data Protection szabályozás (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), esedékes érvénybe léptetéséhez. A GDPR támaszt a vállalatok, állami intézményekhez, nem nyereség és más szervezetekkel, hogy az ajánlat termékek és szolgáltatások vállalatoknál az Európai Unió, vagy ha az adatgyűjtés és -elemzés EU lakosai kötve új szabályokat. A GDPR elhelyezkedő függetlenül érvényes. 

Microsoft-termékek és szolgáltatások elérhetők ma segítséget nyújtanak a GDPR követelményeknek. További információk a Microsoft Privacy Policy házirend [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter).

Az Azure AD zökkenőmentes SSO hoz létre a következő napló típus, amely tartalmazhat EUII:

- Az Azure AD Connect nyomkövetésének naplófájljait.

Zökkenőmentes SSO GDPR megfelelőségének kétféleképpen érhető el:

1.  Kérésre adatok kinyerése a személy, és adatok eltávolítása a telepítések az adott személyt.
2.  Győződjön meg arról, 48 órával adatot nem őrződnek meg.

Javasoljuk a második lehetőség, mert az egyszerűbb bevezetéséhez és karbantartásához. Lásd az alábbi utasításokat az egyes naplóban:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect nyomkövetési naplófájlok törlése

Ellenőrizze a tartalmát **%ProgramData%\AADConnect** mappa és a delete a nyomkövetési napló tartalma (**nyomkövetési -\*.log** fájlok) a mappa telepítése vagy frissítése az Azure AD Connect 48 órán belül vagy zökkenőmentes SSO-konfigurációt, ez a művelet módosítja GDPR által szabályozott adatokat hozhat létre.

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

### <a name="note-about-domain-controller-logs"></a>Tartomány a tartományvezérlő naplók kapcsolatos megjegyzés:

Ha naplózás engedélyezve van, a a termék hoz létre a biztonsági naplók a tartományvezérlők. Naplózási házirendek konfigurálásával kapcsolatos további tudnivalókért olvassa el ezt [cikk](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>További lépések

- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
