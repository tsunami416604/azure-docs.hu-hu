---
title: Azure Active Directory eszköz felügyeleti kapcsolatos gyakori kérdések |} Microsoft Docs
description: Az Azure Active Directory kezelése – gyakori kérdések.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 60b77f5956cb627905eb955995652098337c4dea
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309861"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory eszköz felügyeleti kapcsolatos gyakori kérdések



**K: hogyan regisztrálhatja macOS eszköz?**

**V:** macOS eszköz regisztrálása:

1.  [Megfelelőségi szabályzat létrehozása](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [MacOS eszközök feltételes hozzáférési házirend meghatározása](active-directory-conditional-access-azure-portal.md) 

**Megjegyzés:**

- A felhasználóknak, amelyek szerepelnek a feltételes hozzáférési házirend egy [Office verziója támogatott macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) erőforrások eléréséhez. 

- Az első hozzáférési kísérlet során a rendszer kéri a felhasználóktól regisztrálja az eszközt a vállalati portál használatával.

---

**K: az eszköz nemrég I regisztrálva. Miért nem látom az eszközt az Azure-portálon a saját felhasználói adatok alapján?**

**V:** csatlakozott az Azure AD hibrid Windows 10-es eszközök nem jelennek meg a felhasználói eszközök alapján.
Kell használnia minden eszközök megtekintése az Azure portálon. PowerShell is használható [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmag.

A felhasználói eszközök csak a következő eszközök találhatók:

- Az összes személyes eszközök, amelyek az Azure AD hibrid nincsenek csatlakoztatva. 
- Az összes nem - Windows 10 vagy Windows Server 2016-os eszközök.
- Minden-Windows eszköz 

--- 

**K: Hogyan állapítható meg, hogy mi az az ügyfél az eszköz regisztrációs állapotát?**

**V:** használhatja az Azure-portálon, nyissa meg az összes eszközre és keresse meg az eszköz-azonosítójával. Ellenőrizze az értéket a illesztési oszlop.

Ha egy regisztrált eszközről helyi eszköz regisztrációs állapotát ellenőrizni kívánja:

- A Windows 10 és Windows Server 2016 vagy újabb rendszerű eszközök futtassa a dsregcmd.exe/status.
- A régebbi verziójú operációs rendszer verziója futtassa a "%programFiles%\Microsoft munkahelyi Join\autoworkplace.exe"

---

**K: rendelkezik törölt az Azure portálon, vagy, hogy továbbra is regisztrálva van az eszközön található használatával a Windows PowerShell, de a helyi állapot szerint?**

**V:** Ez az elvárt működés. Az eszköz nem fog erőforrásokhoz való hozzáférés a felhőben. 

Ha szeretné újra regisztrálni újra, a manuális műveletet kell venni az eszközön kell lennie. 

Törölje a csatlakoztatási állapotnak a Windows 10 és Windows Server 2016-os, amelyek a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.

2.  Típusa `dsregcmd.exe /debug /leave`

3.  Jelentkezzen ki, és jelentkezzen be újra az Azure AD-val regisztrálja az eszközt az ütemezett feladat elindítása. 

A régebbi verziójú Windows operációsrendszer-verziók, amely a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.
2.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---
** K: Hogyan do I elhagyása az Azure AD csatlakoztatott eszközök helyileg az eszközön?
**V:** 
- A hibrid a Azure AD csatlakoztatott eszközök, ügyeljen arra, hogy az automatikus regisztráció kikapcsolása, úgy, hogy az ütemezett feladat újra nem regisztrálja az eszközt. Ezután nyissa meg a parancssort rendszergazdaként, típus `dsregcmd.exe /debug /leave`. Azt is megteheti Ez a parancs futtatható parancsfájlként kilépni a egyszerre több eszközön.

- A tiszta az Azure AD csatlakoztatott eszközök, ellenőrizze, hogy a kapcsolat nélküli helyi rendszergazdai fiók, vagy hozzon létre egy, az, akkor nem fog tudni bármely Azure AD-felhasználó hitelesítő adatokkal jelentkezhetnek be. A következő Ugrás **beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**. Válassza ki azt a fiókot, majd kattintson a **Disconnect**. Kövesse az utasításokat, és adja meg a helyi rendszergazdai hitelesítő adatokat, amikor a rendszer kéri. Indítsa újra az eszközt a elhagyásra befejezéséhez.

---

**Kérdés: Miért látom azt ismétlődő eszközbevitelek Azure-portálon?**

**V:**

-   Windows 10 és Windows Server 2016 elhagyása vagy újracsatlakozhatnak ugyanarra az eszközre tett ismételt kísérlet esetén előfordulhat ismétlődő bejegyzések. 

-   Használta, adja hozzá a munkahelyi vagy iskolai fiókkal, minden windows-felhasználó hozzáadása munkahelyi vagy iskolai fiókhoz használó fog rekordot kell létrehozni egy új eszköz ilyen nevű eszköz.

-   Régebbi verziójú Windows operációsrendszer-verziók, amely a helyszíni AD tartományhoz az automatikus regisztráció használatával létre fog hozni egy új eszközrekordhoz minden tartományi felhasználóhoz, aki bejelentkezik az eszközre az eszköz néven. 

-   Az Azure AD csatlakozó számítógépeken tárolt adatok törléséig, újratelepítése és ugyanazzal a névvel, újra csatlakoztatott eszköz azonos nevű másik rekordként jelennek meg.

---

**K: Miért egy felhasználó erőforrásaihoz is hozzáférjenek az eszközről szeretnék letiltotta az Azure-portálon?**

**V:** is igénybe vehet egy visszavonási alkalmazandó egy órát.

>[!Note] 
>Regisztrált eszközök azt javasoljuk, hogy a felhasználók nem férhetnek hozzá a az erőforrásokat az eszköz adatainak törlésekor. További részletekért lásd: [eszközök regisztrálása az Intune-ban felügyeletre](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**K: Miért látnak a felhasználók "Nem érheti el innen"?**

**V:** Ha konfigurálta az egyes feltételes hozzáférési szabályai szükséges egy adott eszköz állapotát, és az eszköz nem felel meg a feltételeket, a felhasználók le vannak tiltva, és ez az üzenet megjelenik. Értékelje ki a feltételes hozzáférési szabályzat előírásainak, és győződjön meg arról, hogy az eszköz képes a megadott feltételeknek megfelelő szeretne több ilyen üzenetet.

---


**K: I tekintse meg a rekordokat az Azure-portálon a felhasználói adatok alapján, és az állapot látható, az eszköz regisztrálva. A feltételes hozzáférés használatának megfelelően beállítani a perceké 'M?**

**V:** kifejezi deviceID, az eszköz csatlakoztatási állapotnak kell felelnek meg, hogy Azure ad-val, és bármely értékelési feltételeknek a feltételes hozzáférés. További részletekért lásd: [Ismerkedés az Azure Active Directory Eszközregisztrációs](active-directory-device-registration.md).

---

**K: Miért kapok "felhasználónév vagy jelszó nem megfelelő" üzenet I csak csatlakozott az Azure AD-eszköz?**

**V:** Ez a forgatókönyv gyakori okai a következők:

- A felhasználói hitelesítő adatok nem érvényesek.

- A számítógép nem tud kommunikálni az Azure Active Directoryban. Ellenőrizze, hogy minden hálózati kapcsolati problémáit.

- Az Azure AD Join előfeltételek nem teljesültek. Győződjön meg arról, hogy elvégezte-e a lépései [Extending felhőalapú képességek a Windows 10-eszközökre az Azure Active Directory Join keresztül](active-directory-azureadjoin-overview.md).  

- Összevont bejelentkezések az összevonási kiszolgálón, a WS-Trust aktív végpontja támogatásához szükséges. 

- Hitelesítési továbbítása engedélyezve van, és a felhasználó rendelkezik-e egy ideiglenes jelszót, amely a bejelentkezési módosítani kell.

---

**K: Miért látható a "Oops... hiba történt!" párbeszédpanel jelenik meg, hajtsa végre az Azure AD join a számítógép?**

**V:** Ez az Azure Active Directory-regisztráció az Intune-nal beállításának eredménye. Győződjön meg arról, hogy hajtsa végre az Azure AD join próbáló felhasználó rendelkezik-e a megfelelő Intune-licenccel. További részletekért lásd: [Windows-eszközök kezelésének beállítása](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**Kérdés: Miért volt sikertelen a saját kísérlet a számítógép bár kaptam bármely hibainformációk?**

**V:** ennek valószínű oka az, hogy a felhasználó jelentkezett be az eszközre, a beépített helyi rendszergazdai fiók használatával. A telepítés befejezéséhez a Azure Active Directory csatlakozási használata előtt hozzon létre egy másik helyi fiókot. 

---

**K: hol található utasításait a telepítés automatikus eszközregisztráció?**

**V:** részletes útmutatásért lásd: [konfigurálása a Windows-tartományhoz csatlakoztatott eszközök automatikus regisztrálása az Azure Active Directoryval](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**K: hol található a hibaelhárítási információkat az automatikus eszközregisztráció?**

**V:** a hibaelhárítással kapcsolatos információkért, lásd:

- [Hibaelhárítás az automatikus regisztráció tartomány csatlakoztatott számítógépeit az Azure AD – a Windows 10 és Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Automatikus regisztráció tartomány hibaelhárítási csatlakoztatott számítógépeit az Azure AD Windows régebbi ügyfelek](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

