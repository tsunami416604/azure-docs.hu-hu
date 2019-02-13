---
title: Az Azure AD Connect és a felhasználói adatvédelem |} A Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan juthat hozzá az Azure AD Connect általános adatvédelmi rendelet előírásainak való megfelelést.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cdbd704f589ca4c045f672bbc49fa4460003a0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175846"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Felhasználói adatok védelme és az Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connect és a felhasználói adatvédelem foglalkozik.  Az Azure AD Connect Health és a felhasználói adatvédelmi információkat lásd: a cikk [Itt](reference-connect-health-user-privacy.md).

Felhasználói adatok védelme az Azure AD Connect telepítés két módon javítani:

1.  Kérésre gyűjthet ki adatokat a személy, és távolíthatja el az adatokat a telepítések az adott személy
2.  Győződjön meg arról, adatok nem őrződnek túli 48 óra.

Az Azure AD Connect csapat javasolja a második lehetőség, mivel jóval könnyebb megadni bevezetéséhez és karbantartásához.

Az Azure AD Connect szinkronizálási kiszolgáló a következő felhasználói adatvédelem adatokat tároló:
1.  Az a személy adatait a **az Azure AD Connect-adatbázis**
2.  Az adatok a **Windows Eseménynapló** előfordulhat, hogy egy személy kapcsolatos információkat tartalmazó fájlok
3.  Az adatok a **az Azure AD Connect telepítési naplófájlok** információk személy tartalmazhat

Az Azure AD Connect-ügyfelek az alábbi irányelveket kell használnia, ha a felhasználói adatok eltávolítása:
1.  Az Azure AD Connect telepítési naplófájlok rendszeres időközönként – legalább 48 óránként tartalmazó mappa tartalmának törlése
2.  Ez a termék is létrehozhatnak az eseménynaplókat.  Eseménynaplók naplók kapcsolatos további információkért tekintse meg a [itt található dokumentáció](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Egy személy adatait automatikusan törlődik az Azure AD Connect-adatbázisból, hogy a címzett adatait, ahol származik a forrásrendszerből eltávolításakor. A rendszergazdák nincs megadott művelet szükséges lehet a GDPR-kompatibilis.  Azonban azt igénylik, hogy az Azure AD Connect adatok szinkronizálva van az adatforráshoz legalább két naponta.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Az Azure AD Connect telepítési naplófájl mappa tartalma törlése
Rendszeresen ellenőrizze, és törölje a tartalmát **c:\programdata\aadconnect** mappa – kivéve a **PersistedState.Xml** fájlt. Ez a fájl kezeli az Azure A Connect korábbi telepítésének állapotát, és használatos, ha egy frissítés telepítésének történik. Ez a fájl nem tartalmaz egy személy kapcsolatos adatokat, és nem szabad törölni.

>[!IMPORTANT]
>Ne törölje a PersistedState.xml fájlt.  Ez a fájl nem tartalmaz felhasználói információt, és megőrzi a korábbi telepítés állapotát.

Ellenőrizhetik és törölhetik ezeket a fájlokat a Windows Intézőben, vagy használhatja egy parancsprogram az alábbihoz hasonló a szükséges műveletek végrehajtásához:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Ez a szkript futtatásához 48 óránként ütemezése
A következő lépések használatával ütemezheti a parancsfájl futtatásához, hogy 48 óránként.

1.  A parancsfájl kiterjesztésű fájlba mentése  **&#46;PS1**, majd nyissa meg a Vezérlőpultot, és kattintson a **rendszerek és biztonsági**.
    ![Rendszer](./media/reference-connect-user-privacy/gdpr2.png)

2.  Felügyeleti eszközök fejléc alatt kattintson a **feladatokat ütemezhet**.
    ![Tevékenység](./media/reference-connect-user-privacy/gdpr3.png)
3.  A Feladatütemező, kattintson a jobb gombbal **feladat ütemezése könyvtár** , majd kattintson a **alapszintű létrehozása feladat...**
4.  Adja meg az új feladat nevét, és kattintson a **tovább**.
5.  Válassza ki **napi** esetében a feladat eseményindítóját, majd kattintson a **tovább**.
6.  Az ismétlődés beállítása **2 nap** kattintson **tovább**.
7.  Válassza ki **programot elindítani** művelet, kattintson a **tovább**.
8.  Típus **PowerShell** be a Program/szkript, és a feliratú mezőbe **hozzáadása (nem kötelező) argumentumok**, adja meg a teljes elérési útja a korábban létrehozott parancsfájlt, majd kattintson a **következő**.
9.  A következő képernyőn a létrehozni kívánt feladat összegzését jeleníti meg. Ellenőrizze az értékeket, és kattintson a **Befejezés** a feladat létrehozásához.



## <a name="next-steps"></a>További lépések
* [Tekintse át a Microsoft Privacy szabályzat a biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)
- [Az Azure AD Connect Health és a felhasználói adatok védelme](reference-connect-health-user-privacy.md)
