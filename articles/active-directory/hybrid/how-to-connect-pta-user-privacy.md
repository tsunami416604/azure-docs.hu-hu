---
title: Felhasználói adatok védelme és az Azure Active Directory átmenő hitelesítés |} A Microsoft Docs
description: Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítés és a GDPR-megfelelőség foglalkozik.
services: active-directory
keywords: Szükséges összetevők az Azure AD Connect az átmenő hitelesítés, GDPR, SSO, Azure AD egyszeri bejelentkezés
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
ms.openlocfilehash: e1f2b717043394634f437caf7dd57411260fd7be
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191095"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Felhasználói adatok védelme és az Azure Active Directory átmenő hitelesítése


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Áttekintés

Az Azure AD átmenő hitelesítés hoz létre a következő log típusát, amely a személyes adatokat tartalmazhatnak:

- Az Azure AD Connect nyomkövetésének naplófájljait.
- Hitelesítési ügynök nyomkövetésének naplófájljait.
- Windows-esemény naplófájlokat.

Javíthatja a felhasználói adatok védelme az átmenő hitelesítés két módon:

1.  Kérésre személy adatokat nyerhet ki, és távolíthatja el az adatokat a telepítések az adott személy.
2.  Győződjön meg arról, adatok nem őrződnek túli 48 óra.

Javasoljuk, hogy a második lehetőség, egyszerűbb bevezetéséhez és karbantartásához. Az alábbiakban az egyes vonatkozó utasításokat:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Az Azure AD Connect a nyomkövetési naplófájlok törlése

Ellenőrizze a tartalmát **%ProgramData%\AADConnect** mappa, és törlése a nyomkövetési napló tartalma (**nyomkövetési -\*.log** fájlok) a mappa telepítése vagy frissítése az Azure AD Connect 48 órán belül vagy az átmenő hitelesítés konfigurációjának, mint ez a művelet módosítása is létrehozhat a GDPR hatálya alá tartozó adatok.

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

Tudjon meg többet az Azure AD Connect általános adatvédelmi rendelet követelményeinek kapcsolódó, lásd: [Ez a cikk](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>A hitelesítési ügynök eseménynaplók törlése

Ez a termék is létrehozhat **Windows eseménynaplók**. További tudnivalókért olvassa el [Ez a cikk](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Az átmenő hitelesítés ügynök naplók megtekintéséhez nyissa meg a **Eseménynapló** alkalmazás a kiszolgáló és az ellenőrzése alatt a **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>A hitelesítési ügynök nyomkövetési naplófájlok törlése

Rendszeresen ellenőrizni kell a tartalmát <strong>%ProgramData%\Microsoft\Azure AD Connect hitelesítési Agent\Trace\</ strong > és ez a mappa tartalmának törlése 48 óránként. 

>[!IMPORTANT]
>Ha a hitelesítési ügynök szolgáltatás fut, nem fogja kattintva törölheti az aktuális fájl a mappában. Állítsa le a szolgáltatást, majd próbálkozzon újra. Felhasználói bejelentkezési hibák elkerülése érdekében már konfigurálni kell az átmenő hitelesítés [magas rendelkezésre állású](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Ellenőrizhetik és törölhetik ezeket a fájlokat a Windows Intézőben, vagy használhatja az alábbi parancsfájlt a szükséges műveletek végrehajtásához:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

A parancsfájl futtatását ütemezni 48 óránként kövesse az alábbi lépéseket:

1.  Mentse a parancsfájlt a fájlt a ". PS1 "kiterjesztéssel.
2.  Nyissa meg **Vezérlőpult** , majd kattintson a **rendszer és biztonság**.
3.  Alatt a **felügyeleti eszközök** szakaszban kattintson a "**feladatokat ütemezhet**".
4.  A **Feladatütemező**, kattintson a jobb gombbal a "**feladat ütemezése könyvtár**"és kattintson a"**létrehozása egyszerű feladat...** ".
5.  Adja meg az új feladat nevét, és kattintson a **tovább**.
6.  Jelölje be "**napi**" az a **feladat eseményindítóját** kattintson **tovább**.
7.  Állítsa az ismétlődési két napra, és kattintson a **tovább**.
8.  Jelölje be "**programot elindítani**" művelet, kattintson **tovább**.
9.  Írja be "**PowerShell**"a Program/szkript mezőben, és be a"**hozzáadása (nem kötelező) argumentumok**", adja meg a teljes elérési útja a korábban létrehozott parancsfájlt, majd kattintson a **következő**.
10. A következő képernyőn a létrehozni kívánt feladat összegzését jeleníti meg. Ellenőrizze az értékeket, és kattintson a **Befejezés** a feladat létrehozásához:
 
### <a name="note-about-domain-controller-logs"></a>Domain controller naplók kapcsolatos megjegyzés:

Ha naplózása engedélyezve van, a termék hozzon létre a biztonsági naplók a tartományvezérlők. Naplózási házirendek konfigurálásával kapcsolatos további tudnivalókért olvassa el ezt [cikk](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy szabályzat a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
- [**Hibaelhárítás** ](tshoot-connect-pass-through-authentication.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
