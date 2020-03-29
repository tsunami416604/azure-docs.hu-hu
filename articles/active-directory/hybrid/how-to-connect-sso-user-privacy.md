---
title: A felhasználók adatainak védelme és az Azure AD zökkenőmentes egyszeri bejelentkezése | Microsoft dokumentumok
description: Ez a cikk az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezéssel és GDPR-megfelelőséggel foglalkozik.
services: active-directory
keywords: mi az Azure AD Connect, GDPR, az Azure AD, egyszeri bejelentkezés, egyszeri bejelentkezés szükséges összetevői
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242105"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Felhasználói adatok védelme és az Azure AD közvetlen egyszeri bejelentkezése

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés


Az Azure AD seamless sso a következő naplótípust hozza létre, amely személyes adatokat tartalmazhat: 

- Az Azure AD Connect nyomkövetési naplófájljai.

A zökkenőmentes egyszeri bejelentkezés felhasználói adatvédelmének javítása kétféleképpen:

1.  Kérésre kikell nyerni egy személy adatait, és el kell távolítani az adatokat az adott személytől a létesítményekből.
2.  Győződjön meg arról, hogy 48 órán túl nem őriz meg adatokat.

Javasoljuk, hogy a második lehetőség, mivel könnyebb végrehajtani és fenntartani. Lásd az egyes naplótípusokra vonatkozó alábbi utasításokat:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect nyomkövetési naplófájljainak törlése

Ellenőrizze a **%ProgramData%\AADConnect** mappa tartalmát, és törölje a mappa nyomkövetési naplótartalmát (**trace-\*.log** files) az Azure AD Connect telepítését vagy frissítését vagy a zökkenőmentes egyszeri bejelentkezés konfigurációjának módosítását követő 48 órán belül, mivel ez a művelet a GDPR hatálya alá tartozó adatokat hozhat létre.

>[!IMPORTANT]
>Ne törölje a **PersistedState.xml** fájlt ebben a mappában, mivel ez a fájl az Azure AD Connect előző telepítésének állapotának fenntartására szolgál, és a frissítés telepítésekor használatos. Ez a fájl soha nem tartalmaz semmilyen adatot egy személy, és soha nem kell törölni.

Áttekintheti és törölheti ezeket a nyomkövetési naplófájlokat a Windows Intézővel, vagy a következő PowerShell-parancsfájl segítségével hajthatja végre a szükséges műveleteket:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt egy fájlba a ". PS1" kiterjesztés. Szükség szerint futtassa ezt a parancsfájlt.

Ha többet szeretne megtudni a kapcsolódó Azure AD Connect GDPR-követelményekről, olvassa el [ezt a cikket.](reference-connect-user-privacy.md)

### <a name="note-about-domain-controller-logs"></a>Megjegyzés a tartományvezérlői naplókról

Ha a naplózás engedélyezve van, a termék biztonsági naplókat hozhat létre a tartományvezérlők számára. A naplózási házirendek konfigurálásáról a cikkben olvashat [bővebben.](https://technet.microsoft.com/library/dd277403.aspx)

## <a name="next-steps"></a>További lépések

* [A Microsoft adatvédelmi irányelveinek áttekintése az Adatvédelmi központban](https://www.microsoft.com/trustcenter)
  - [**Hibaelhárítás**](tshoot-connect-sso.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - A bejelentés új funkció kérelmeket.