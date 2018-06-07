---
title: Az Azure AD Connect és a felhasználói adatvédelem |} Microsoft Docs
description: Ez a dokumentum ismerteti az beszerzése az Azure AD Connect GDPR előírásainak.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8e3f81a6480e9de55c8f803e2266c4ac6e33c316
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590538"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Felhasználói adatok és az Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connect és a felhasználói adatvédelem foglalkozik.  Az Azure AD Connect Health és felhasználói adatvédelmi információkat lásd: a cikk [Itt](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Javítja a felhasználói adatvédelem az Azure AD Connect telepítése két módon:

1.  Kérésre adatok kinyerése a személy, és távolítsa el adatait az adott személy a létesítmények
2.  Győződjön meg arról, 48 órával adatot nem őrződnek meg.

Az Azure AD Connect csapat azt javasolja, hogy a második lehetőség óta sokkal egyszerűbb, bevezetéséhez és karbantartásához.

Az Azure AD Connect szinkronizálási kiszolgáló a következő adatvédelmi adatokat tároló:
1.  Az a személy adatait a **az Azure AD Connect-adatbázis**
2.  Az adatok a **Windows-Eseménynapló** előfordulhat, hogy egy személy kapcsolatos információkat tartalmazó fájlokat.
3.  Az adatok a **az Azure AD Connect telepítési naplófájlok** , hogy egy személy kapcsolatos tartalmazhat

Azure AD Connect-ügyfél használnia kell az alábbi iránymutatásokat, amikor a felhasználó adatainak eltávolítása:
1.  Az Azure AD Connect telepítési naplófájlok rendszeresen – legalább 48 óránként tartalmazó mappa tartalmának törlése
2.  A termék eseménynaplók is létrehozhat.  Eseménynaplók naplók kapcsolatos további tudnivalókért tekintse meg a [dokumentációját Itt](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Egy személy adatait a rendszer automatikusan törli az Azure AD Connect adatbázis során, hogy a címzett adatai törlődnek a forrásrendszerben, ahol szolgáltatásoktól érkező. A rendszergazda nem adott műveletet megfelelő GDPR lehet szükséges.  Azonban azt igénylik, hogy az Azure AD Connect adatok szinkronizálva van a adatforrás legalább két naponta.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Az Azure AD Connect telepítési naplófájl mappa tartalma törlése
Rendszeresen ellenőrizze, és törli a tartalmát **c:\programdata\aadconnect** mappa – kivéve a **PersistedState.Xml** fájlt. A fájl fenntartja a korábbi telepítés Azure A Connect és használatos, ha egy frissítés telepítésének történik. A fájl nem tartalmaz egy személy kapcsolatos adatokat, és nem törölhető.

>[!IMPORTANT]
>Ne törölje a PersistedState.xml fájlt.  A fájl tartalmazza a felhasználói adatokat, és megőrzi a korábbi telepítés állapotát.

Tekintse át és törli ezeket a fájlokat, a Windows Intézővel, vagy a következő parancsfájl segítségével hajtsa végre a szükséges műveleteket:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>A parancsfájl futtatását 48 óránként ütemezése
A következő lépésekkel ütemezni a parancsfájl futtatásához 48 óránként.

1.  A parancsfájl kiterjesztésű fájlba mentése  **&#46;PS1**, majd nyissa meg a Vezérlőpultot, és kattintson a **rendszerek és biztonsági**.
    ![Rendszer](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  Felügyeleti eszközök fejléc alatt kattintson a **feladatok ütemezése**.
    ![Tevékenység](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  Feladatütemező, kattintson a jobb gombbal **feladat ütemezése könyvtár** , majd kattintson a **alapvető létrehozása feladat...**
4.  Adja meg az új feladat nevét, és kattintson a **következő**.
5.  Válassza ki **napi** a feladat eseményindítóját, és kattintson a **következő**.
6.  Az ismétlődés beállítása **2 nap** kattintson **következő**.
7.  Válassza ki **egy programot elindítani** a művelet, és kattintson a **következő**.
8.  Típus **PowerShell** a Program vagy parancsfájl, és a jelölőnégyzetet **(nem kötelező) argumentumok hozzáadása**, írja be a teljes elérési útját a korábban létrehozott parancsfájlt, majd kattintson a **tovább**.
9.  A következő képernyő fog létrehozni a feladat összegzését jeleníti meg. Ellenőrizze az értékeket, és kattintson a **Befejezés** a feladat létrehozásához.



## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy házirendet, a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
- [Az Azure AD Connect Health és a felhasználói adatok](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
