---
title: Felhasználói adatok védelme és az Azure AD közvetlen egyszeri bejelentkezés |} A Microsoft Docs
description: Ez a cikk az Azure Active Directory (Azure AD) közvetlen egyszeri bejelentkezés és a GDPR-megfelelőség foglalkozik.
services: active-directory
keywords: Mi az Azure AD Connect, az általános adatvédelmi rendelet, SSO, Azure AD szükséges összetevői egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 50c97ce7a492c934e15634622d86bf587ffb3fb7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915724"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Felhasználói adatok védelme és az Azure AD közvetlen egyszeri bejelentkezés

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés


Az Azure AD közvetlen egyszeri bejelentkezés hoz létre a következő log típusát, amely a személyes adatokat tartalmazhatnak: 

- Az Azure AD Connect nyomkövetésének naplófájljait.

Felhasználói adatok védelme növelése a közvetlen egyszeri bejelentkezés két módon:

1.  Kérésre személy adatokat nyerhet ki, és távolíthatja el az adatokat a telepítések az adott személy.
2.  Győződjön meg arról, adatok nem őrződnek túli 48 óra.

Javasoljuk, hogy a második lehetőség, egyszerűbb bevezetéséhez és karbantartásához. Lásd az alábbi utasítások minden egyes naplótípusnál:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect a nyomkövetési naplófájlok törlése

Ellenőrizze a tartalmát **%ProgramData%\AADConnect** mappa, és törlése a nyomkövetési napló tartalma (**nyomkövetési -\*.log** fájlok) a mappa telepítése vagy frissítése az Azure AD Connect 48 órán belül vagy, ez a művelet a zökkenőmentes egyszeri bejelentkezési konfigurációjának módosítása is létrehozhat a GDPR hatálya alá tartozó adatok.

>[!IMPORTANT]
>Ne törölje a **PersistedState.xml** fájl ebben a mappában, ezt a fájlt az Azure AD Connect a korábbi telepítés állapotának karbantartásához használt és használatos, amikor a frissítési történik. A fájl minden olyan személy adatait soha nem fog tartalmazni, és soha nem törölhető.

Tekintse át és a Windows Explorer használatával nyomkövetési naplófájlok törlése, vagy használhatja a következő PowerShell-parancsfájlt a szükséges műveletek végrehajtásához:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt a fájlt a ". PS1 "kiterjesztéssel. Futtassa ezt a szkriptet, szükség szerint.

Tudjon meg többet az Azure AD Connect általános adatvédelmi rendelet követelményeinek kapcsolódó, lásd: [Ez a cikk](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Domain controller naplók kapcsolatos megjegyzés:

Ha naplózása engedélyezve van, a termék hozzon létre a biztonsági naplók a tartományvezérlők. Naplózási házirendek konfigurálásával kapcsolatos további tudnivalókért olvassa el ezt [cikk](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy szabályzat a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkcióra vonatkozó javaslata tárolásához.
