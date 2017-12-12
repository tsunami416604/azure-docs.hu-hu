---
title: "Azure Active Directory eszköz felügyeleti kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Az Azure Active Directory kezelése – gyakori kérdések."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d44bb23b12e3ccd92d0661175873f6255f238b94
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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

**V:** , amelyek automatikus eszközregisztráció tartományhoz csatlakoztatott Windows 10-eszközök nem jelennek meg a felhasználói adatok alapján.
Minden eszköz tekintse meg a PowerShell használatával kell. 

A felhasználói adatok találhatók a következő eszközöket:

- Az összes személyes eszközök, amelyek nem csatlakoztatott vállalati 
- Az összes nem - Windows 10 vagy Windows Server 2016 
- Minden-Windows eszköz 

---

**K: Miért nem látom minden olyan eszközre, az Azure-portálon az Azure Active Directoryban regisztrálva?** 

**V:** jelenleg nincs mód az Azure portálon minden regisztrált eszközök megjelenítéséhez. Használhatja az Azure PowerShell minden eszköz kereséséhez. További részletekért lásd: a [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmag.

--- 

**K: Hogyan állapítható meg, hogy mi az az ügyfél az eszköz regisztrációs állapotát?**

**V:** az eszköz regisztrációja állapotától függ:

- Mi az az eszköz
- Hogyan regisztrálták 
- Minden hozzá kapcsolódó adatokat. 
 

---

**K: Miért van egy eszközt törölt rendelkezik az Azure portálon vagy a Windows PowerShell használatával továbbra is szerepel a regisztrált?**

**V:** Ez az elvárt működés. Az eszköz nem fog erőforrásokhoz való hozzáférés a felhőben. Ha azt szeretné, távolítsa el az eszközt, és regisztrálja újra, kézi művelet szükséges az eszközön kell lennie. 

A Windows 10 és Windows Server 2016-os, amely a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.

2.  Típusa`dsregcmd.exe /debug /leave`

3.  Jelentkezzen ki, és jelentkezzen be újra regisztrálja az eszközt az ütemezett feladat elindítása. 

Az egyéb Windows platformra, amelyek a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.
2.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Kérdés: Miért látom azt ismétlődő eszközbevitelek Azure-portálon?**

**V:**

-   Windows 10 és Windows Server 2016 ha azok elhagyása és újracsatlakozhatnak ugyanarra az eszközre, az ismételt kísérletek előfordulhat ismétlődő bejegyzések. 

-   Használta, adja hozzá a munkahelyi vagy iskolai fiókkal, minden windows-felhasználó hozzáadása munkahelyi vagy iskolai fiókhoz használó fog rekordot kell létrehozni egy új eszköz ilyen nevű eszköz.

-   Egyéb Windows-platformokat, amelyek a helyszíni AD tartományhoz az automatikus regisztráció használatával hoz létre egy új eszközrekordhoz minden tartományi felhasználó, aki bejelentkezik az eszközre való ilyen eszköz nevű. 

-   Törölt, újratelepítése és ugyanazzal a névvel, újra csatlakoztatott AADJ gép eszköz azonos nevű másik rekordként jelennek meg.

---

**K: Miért egy felhasználó erőforrásaihoz is hozzáférjenek az eszközről szeretnék letiltotta az Azure-portálon?**

**V:** is igénybe vehet egy visszavonási alkalmazandó egy órát.

>[!Note] 
>Az elveszett eszközök azt javasoljuk, hogy a felhasználók nem férhetnek hozzá a az eszköz az eszköz adatainak törlésekor. További részletekért lásd: [eszközök regisztrálása az Intune-ban felügyeletre](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**K: Miért látnak a felhasználók "Nem érheti el innen"?**

**V:** Ha konfigurálta az egyes feltételes hozzáférési szabályai szükséges egy adott eszköz állapotát, és az eszköz nem felel meg a feltételeket, a felhasználók le vannak tiltva, és ez az üzenet megjelenik. Értékelje ki a szabályokat, és győződjön meg arról, hogy az eszköz képes a megadott feltételeknek megfelelő szeretne több ilyen üzenetet.

---


**K: I tekintse meg a rekordokat az Azure-portálon a felhasználói adatok alapján, és az ügyfél regisztrált állapota látható. A feltételes hozzáférés használatának megfelelően beállítani a perceké 'M?**

**V:** eszközrekord (deviceID) és állapotát az Azure portálon kell felel meg az ügyfél és bármely értékelési feltételeknek a feltételes hozzáférés. További részletekért lásd: [Ismerkedés az Azure Active Directory Eszközregisztrációs](active-directory-device-registration.md).

---

**K: Miért kapok "felhasználónév vagy jelszó nem megfelelő" üzenet I csak csatlakozott az Azure AD-eszköz?**

**V:** Ez a forgatókönyv gyakori okai a következők:

- A felhasználói hitelesítő adatok nem érvényesek.

- A számítógép nem tud kommunikálni az Azure Active Directoryban. Ellenőrizze, hogy minden hálózati kapcsolati problémáit.

- Az Azure AD Join előfeltételek nem teljesültek. Győződjön meg arról, hogy elvégezte-e a lépései [Extending felhőalapú képességek a Windows 10-eszközökre az Azure Active Directory Join keresztül](active-directory-azureadjoin-overview.md).  

- Összevont bejelentkezések az összevonási kiszolgálón, a WS-Trust aktív végpontja támogatásához szükséges. 

---

**K: Miért tekintse meg a "Oops... hiba történt!" párbeszédpanel jelenik meg a számítógép csatlakozni?**

**V:** Ez az Azure Active Directory-regisztráció az Intune-nal beállításának eredménye. További részletekért lásd: [Windows-eszközök kezelésének beállítása](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**Kérdés: Miért volt sikertelen a saját kísérlet a számítógép bár kaptam bármely hibainformációk?**

**V:** ennek valószínű oka az, hogy a felhasználó jelentkezett be az eszközre, a beépített Rendszergazda fiók használatával. A telepítés befejezéséhez a Azure Active Directory csatlakozási használata előtt hozzon létre egy másik helyi fiókot. 

---

**K: hol található utasításait a telepítés automatikus eszközregisztráció?**

**V:** részletes útmutatásért lásd: [konfigurálása a Windows-tartományhoz csatlakoztatott eszközök automatikus regisztrálása az Azure Active Directoryval](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**K: hol található a hibaelhárítási információkat az automatikus eszközregisztráció?**

**V:** a hibaelhárítással kapcsolatos információkért, lásd:

- [Hibaelhárítás az automatikus regisztráció tartomány csatlakoztatott számítógépeit az Azure AD – a Windows 10 és Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Automatikus regisztráció tartomány hibaelhárítási csatlakoztatott számítógépeit az Azure AD Windows régebbi ügyfelek](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

