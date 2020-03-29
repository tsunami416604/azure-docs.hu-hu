---
title: A felhasználók adatvédelme és az Azure Active Directory átmenő hitelesítése | Microsoft dokumentumok
description: Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítéssel és a GDPR-megfelelőséggel foglalkozik.
services: active-directory
keywords: Azure AD Connect áthárítású hitelesítés, GDPR, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931007"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Felhasználói adatok védelme és az Azure Active Directory átmenő hitelesítése


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés

Az Azure AD áteresztő hitelesítésa a következő naplótípust hozza létre, amely személyes adatokat tartalmazhat:

- Az Azure AD Connect nyomkövetési naplófájljai.
- Hitelesítési ügynök nyomkövetési naplófájljai.
- Windows eseménynaplófájlok.

A felhasználók adatvédelmének javítása az átmenő hitelesítéshez két módon:

1.  Kérésre kikell nyerni egy személy adatait, és el kell távolítani az adatokat az adott személytől a létesítményekből.
2.  Győződjön meg arról, hogy 48 órán túl nem őriz meg adatokat.

Javasoljuk, hogy a második lehetőség, mivel könnyebb végrehajtani és fenntartani. Az egyes naplótípusokra vonatkozó utasítások az alábbiakban találhatók:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect nyomkövetési naplófájljainak törlése

Ellenőrizze a **%ProgramData%\AADConnect** mappa tartalmát, és törölje a mappa nyomkövetési naplótartalmát (**trace-\*.log** files) az Azure AD Connect telepítését vagy frissítését vagy az átmenő hitelesítéskonfiguráció módosítását követő 48 órán belül, mivel ez a művelet a GDPR hatálya alá tartozó adatokat hozhat létre.

>[!IMPORTANT]
>Ne törölje a **PersistedState.xml** fájlt ebben a mappában, mivel ez a fájl az Azure AD Connect előző telepítésének állapotának fenntartására szolgál, és a frissítés telepítésekor használatos. Ez a fájl soha nem tartalmaz semmilyen adatot egy személy, és soha nem kell törölni.

Áttekintheti és törölheti ezeket a nyomkövetési naplófájlokat a Windows Intézővel, vagy a következő PowerShell-parancsfájl segítségével hajthatja végre a szükséges műveleteket:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Mentse a parancsfájlt egy fájlba a ". PS1" kiterjesztés. Szükség szerint futtassa ezt a parancsfájlt.

Ha többet szeretne megtudni a kapcsolódó Azure AD Connect GDPR-követelményekről, olvassa el [ezt a cikket.](reference-connect-user-privacy.md)

### <a name="delete-authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplóinak törlése

Ez a termék **Windows eseménynaplókat**is létrehozhat. Ha többet szeretne megtudni, kérjük, olvassa el [ezt a cikket](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Az átmenő hitelesítési ügynökhöz kapcsolódó naplók megtekintéséhez nyissa meg az **Eseménynapló** alkalmazást a kiszolgálón, és ellenőrizze az **Alkalmazás- és szolgáltatásnaplók\Microsoft\AzureAdConnect\AuthenticationAgent\Admin mappában.**

### <a name="delete-authentication-agent-trace-log-files"></a>Hitelesítési ügynök nyomkövetési naplófájljainak törlése

Rendszeresen ellenőrizze a **%ProgramData%\Microsoft\Azure AD Connect authentication agent\Trace** tartalmát, és 48 óránként törölje a mappa tartalmát. 

>[!IMPORTANT]
>Ha a Hitelesítési ügynök szolgáltatás fut, nem tudja törölni a mappában lévő aktuális naplófájlt. Állítsa le a szolgáltatást, mielőtt újra próbálkozna. A felhasználói bejelentkezési hibák elkerülése érdekében már konfigurálta az átmenő hitelesítést a [magas rendelkezésre állás érdekében.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

Áttekintheti és törölheti ezeket a fájlokat a Windows Intézővel, vagy a következő parancsfájl segítségével hajthatja végre a szükséges műveleteket:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Ha 48 óránként szeretné futtatni ezt a parancsfájlt, kövesse az alábbi lépéseket:

1.  Mentse a parancsfájlt egy fájlba a ". PS1" kiterjesztés.
2.  Nyissa meg **a Vezérlőpultot,** és kattintson a **Rendszer és biztonság gombra.**
3.  A **Felügyeleti eszközök** fejlécben kattintson a **"Feladatok ütemezése"** elemre.
4.  A **Feladatütemezőben**kattintson a jobb gombbal a **"Feladatütemezés könyvtára"** elemre, és kattintson az **"Alapfeladat létrehozása...**" parancsra.
5.  Írja be az új feladat nevét, majd kattintson a **Tovább gombra.**
6.  A **Feladateseményindítóhoz** válassza a **"Napi"** lehetőséget, majd kattintson a **Tovább**gombra.
7.  Állítsa az ismétlődést két napra, majd kattintson a **Tovább**gombra.
8.  Válaszd a **"Program indítása"** lehetőséget műveletként, majd kattintson a **Tovább**gombra.
9.  Írja be a "**PowerShell**" kifejezést a Program/parancsfájl mezőjébe, és az "**Arguments (nem kötelező)**" mezőbe írja be a korábban létrehozott parancsfájl teljes elérési útját, majd kattintson a **Tovább**gombra.
10. A következő képernyőn a létrehozni kívánt tevékenység összegzése látható. Ellenőrizze az értékeket, és kattintson a **Befejezés** gombra a feladat létrehozásához:
 
### <a name="note-about-domain-controller-logs"></a>Megjegyzés a tartományvezérlői naplókról

Ha a naplózás engedélyezve van, a termék biztonsági naplókat hozhat létre a tartományvezérlők számára. A naplózási házirendek konfigurálásáról a cikkben olvashat [bővebben.](https://technet.microsoft.com/library/dd277403.aspx)

## <a name="next-steps"></a>További lépések
* [A Microsoft adatvédelmi irányelveinek áttekintése az Adatvédelmi központban](https://www.microsoft.com/trustcenter)
* [**Hibaelhárítás**](tshoot-connect-pass-through-authentication.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
