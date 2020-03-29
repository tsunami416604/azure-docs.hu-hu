---
title: Azure AD Connect és a felhasználók adatainak védelme | Microsoft dokumentumok
description: Ez a dokumentum ismerteti, hogyan szerezhet gdpr-kompatibilitás az Azure AD Connect.
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
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455784"
---
# <a name="user-privacy-and-azure-ad-connect"></a>A felhasználók adatainak védelme és az Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ez a cikk az Azure AD Connecttel és a felhasználók adatainak védelmével foglalkozik.  Az Azure AD Connect Állapotáról és a felhasználók adatairól az [itt](reference-connect-health-user-privacy.md)található cikkben olvashat.

A felhasználók adatvédelmének javítása az Azure AD Connect telepítéseinél kétféleképpen:

1.  Kérésre kikell nyerni egy személy adatait, és ki kell venni az adatokat az adott személytől a
2.  Győződjön meg arról, hogy 48 órán túl nem őriz meg adatokat.

Az Azure AD Connect csapata a második lehetőséget ajánlja, mivel sokkal könnyebb megvalósítani és karbantartani.

Az Azure AD Connect szinkronizálási kiszolgálója a következő felhasználói adatvédelmi adatokat tárolja:
1.  Az **Azure AD Connect adatbázisban** lévő személy adatai
2.  A **Windows eseménynapló-fájljaiban** lévő adatok, amelyek egy személyre vonatkozó információkat tartalmazhatnak
3.  Az **Azure AD Connect telepítési naplófájljaiban** lévő adatok, amelyek egy személyről

Az Azure AD Connect-ügyfeleknek a következő irányelveket kell használniuk a felhasználói adatok eltávolításakor:
1.  Az Azure AD Connect telepítési naplófájljait tartalmazó mappa tartalmának rendszeres törlése – legalább 48 óránként
2.  Ez a termék eseménynaplókat is létrehozhat.  Ha többet szeretne megtudni az Eseménynaplók naplókról, olvassa el a [dokumentációt itt.](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx)

Egy személy adatait automatikusan eltávolítja az Azure AD Connect adatbázisból, amikor az adott személy adatait eltávolítja a forrásrendszerből, ahonnan származnak. A rendszergazdák nem kötelesek a GDPR-kompatibilis konkrét műveletet végrehajtani.  Azonban azt követeli meg, hogy az Azure AD Connect-adatok legalább kétnaponta szinkronizálva legyenek az adatforrással.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Az Azure AD Connect telepítési naplófájl-tartalmának törlése
Rendszeresen ellenőrizze és törölje a **c:\programdata\aadconnect** mappa tartalmát – kivéve a **PersistedState.Xml** fájlt. Ez a fájl az Azure A Connect előző telepítésének állapotát tartja karban, és a frissítés telepítésekor használatos. Ez a fájl nem tartalmaz adatokat egy személyről, ezért nem szabad törölni.

>[!IMPORTANT]
>Ne törölje a PersistedState.xml fájlt.  Ez a fájl nem tartalmaz felhasználói adatokat, és fenntartja az előző telepítés állapotát.

Áttekintheti és törölheti ezeket a fájlokat a Windows Intézővel, vagy az alábbihoz hasonló parancsfájlt használhat a szükséges műveletek végrehajtásához:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>A parancsfájl ütemezése 48 óránként
A parancsfájl 48 óránként történő futtatásának ütemezése az alábbi lépésekkel ütemezheti.

1.  Mentse a parancsfájlt egy **fájlba, amelynek**kiterjesztése&#46;PS1 kiterjesztéssel, majd nyissa meg a Vezérlőpultot, és kattintson a **Rendszerek és biztonság parancsra.**
    ![Rendszer](./media/reference-connect-user-privacy/gdpr2.png)

2.  A Felügyeleti eszközök fejlécben kattintson a **Feladatok ütemezése gombra.**
    ![Tevékenység](./media/reference-connect-user-privacy/gdpr3.png)
3.  A Feladatütemezőben kattintson a jobb gombbal a **Feladatütemezés könyvtárra,** és kattintson az **Egyszerű feladat létrehozása gombra...**
4.  Írja be az új feladat nevét, majd kattintson a **Tovább gombra.**
5.  Válassza a **Napi** lehetőséget a feladateseményindítóhoz, és kattintson a **Tovább**gombra.
6.  Állítsa az ismétlődést **2 napra,** majd kattintson a **Tovább**gombra.
7.  Válaszd a **Program indítása** műveletként lehetőséget, majd kattintson a **Tovább**gombra.
8.  Írja be a **PowerShell** értéket a Program/parancsfájl mezőjébe, és az **Argumentumok hozzáadása (nem kötelező)** mezőbe írja be a korábban létrehozott parancsfájl teljes elérési útját, majd kattintson a **Tovább**gombra.
9.  A következő képernyőn a létrehozni kívánt tevékenység összegzése látható. Ellenőrizze az értékeket, és kattintson a **Befejezés** gombra a feladat létrehozásához.



## <a name="next-steps"></a>További lépések
* [A Microsoft adatvédelmi irányelveinek áttekintése az Adatvédelmi központban](https://www.microsoft.com/trustcenter)
* [Az Azure AD Connect állapota és a felhasználók adatainak védelme](reference-connect-health-user-privacy.md)
