---
title: Az Azure Active Directory-Eszközfelügyelet – gyakori kérdések |} A Microsoft Docs
description: Azure Active Directory-Eszközfelügyelet – gyakori kérdések.
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
ms.openlocfilehash: e6d223b386aea3ca41931c600d16c16b53765f26
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414923"
---
# <a name="azure-active-directory-device-management-faq"></a>Az Azure Active Directory-Eszközfelügyelet – gyakori kérdések

**K: Android vagy IOS rendszerű BYOD-eszközök regisztrálása?**

**V:** Igen, de csak az Azure eszközregisztrációs szolgáltatással és a hibrid ügyfelek számára. Az eszközregisztrációs szolgáltatással a helyszíni AD FS nem támogatott.

**K: hogyan regisztrálhatnak a macOS-eszközön?**

**V:** macOS-eszköz regisztrálása:

1.  [Megfelelőségi szabályzat létrehozása](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [MacOS-eszközökre vonatkozó feltételes hozzáférési szabályzat definiálása](../active-directory-conditional-access-azure-portal.md) 

**Megjegyzés:**

- A felhasználóknak, amelyek szerepelnek a feltételes hozzáférési szabályzatot kell egy [támogatott Office-verzió macOS-hez készült](../active-directory-conditional-access-technical-reference.md#client-apps-condition) erőforrások eléréséhez. 

- Az első hozzáférési kísérlet során a rendszer kéri a felhasználókat az eszköz regisztrálása a vállalati portál használatával.

---

**Az eszköz nemrég kérdés regisztrálva. Miért nem látom az eszközt a saját felhasználói adatokat az Azure Portalon?**

**V:** , amelyek hibrid Azure AD-hez Windows 10-eszközök nem jelennek meg a felhasználói eszközök alapján.
Szeretné használni az összes eszköz megtekintése az Azure Portalon. Emellett használhatja a Powershellt [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmagot.

Az alábbi eszközök csak a felhasználói eszközök vannak felsorolva:

- Az összes személyes eszközök, amelyek nem a hibrid Azure AD-tartományhoz. 
- Az összes, nem Windows 10 és Windows Server 2016-eszközök.
- Az összes nem-Windows-eszközök 

--- 

**K: Hogyan tudhatom meg, hogy az eszköz regisztrációs állapotát, az ügyfél van?**

**V:** az Azure Portal, az összes eszközre megnyithatja és keresse meg az eszköz használata az eszköz azonosítójával. Ellenőrizze az értéket az illesztési típus oszlop alatt.

Ha azt szeretné, egy regisztrált eszközön a helyi eszköz regisztrációs állapotának ellenőrzése:

- A Windows 10 és Windows Server 2016 vagy újabb rendszerű eszközök futtassa a dsregcmd.exe/status.
- A régebbi verziójú operációs rendszerekről futtassa a "%programFiles%\Microsoft munkahelyi Join\autoworkplace.exe"

---

**Kérdés törölték az Azure Portalon, illetve, hogy továbbra is regisztrálva van az eszközön található használatával a Windows PowerShell, de a helyi állapot szerint?**

**V:** Ez az elvárt. Az eszköz nem fog erőforrásokhoz való hozzáférés a felhőben. 

Ha szeretné újra regisztrálja újra, manuális művelet végrehajtását az eszközön kell lennie. 

Törölje a csatlakoztatási állapotnak a Windows 10 és Windows Server 2016-ban, amelyek a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.

2.  Típusa `dsregcmd.exe /debug /leave`

3.  Jelentkezzen ki, és jelentkezzen be az ütemezett feladatot, amely regisztrálja az eszközt az Azure AD-újra elindítani. 

A régebbi verziójú Windows operációs rendszerekről, amelyek a helyszíni AD-tartományhoz:

1.  Nyissa meg a parancssort rendszergazdaként.
2.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Gépelje be: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---
**K: hogyan do I elhagyása egy Azure AD-csatlakoztatás eszközt helyileg az eszközön?**

**VÁLASZ:** 
- Hibrid Azure AD-csatlakoztatás eszközökhöz ügyeljen arra, hogy az automatikus regisztráció kikapcsolása, így az ütemezett feladat nem regisztrálja újra az eszközt. Ezután nyissa meg a parancssort rendszergazdaként, és írja be `dsregcmd.exe /debug /leave`. Azt is megteheti ezzel a paranccsal futtathatja, a parancsfájl több eszközre történő tömeges elhagyása.

- Az igazi Azure AD-csatlakoztatás eszközök, ellenőrizze, hogy a kapcsolat nélküli helyi rendszergazdai fiók, vagy létrehozhat egy újat, nem kell minden olyan Azure AD felhasználói hitelesítő adatokkal jelentkezhetnek be. Folytassa **beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**. Válassza ki azt a fiókot, majd kattintson a **Disconnect**. Kövesse az utasításokat, és adja meg a helyi rendszergazdai hitelesítő adatokat, amikor a rendszer kéri. Indítsa újra az eszközt a elhagyásra befejezéséhez.

---

**K: a felhasználók nem tud keresni, nyomtatók, az Azure AD-csatlakoztatott eszközök. Hogyan lehet engedélyezni, hogy az Azure AD-csatlakoztatott eszközök nyomtatás?**

**V:** nyomtatók az Azure AD-csatlakoztatott eszközök telepítése, lásd: [hibrid felhőbeli nyomtatás](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Szüksége lesz egy helyszíni Windows Server üzembe helyezéséhez a hibrid felhőbeli nyomtatás. Nyomtatási szolgáltatás felhőalapú jelenleg nem áll rendelkezésre. 

---

**K: hogyan létesíthetek kapcsolatot egy távoli Azure ad-hez csatlakoztatott eszközök? ** 
 **V:** a cikkben https://docs.microsoft.com/en-us/windows/client-management/connect-to-remote-aadj-pc részleteiről.

---

**K: Miért tekintse meg az Azure portal, ismétlődő eszközbevitelek?**

**VÁLASZ:**

-   A Windows 10 és Windows Server 2016-ra ha ismétlődő elhagyása, és csatlakozzon újra ugyanarra az eszközre is ismétlődő bejegyzéseket. 

-   Használta, adja hozzá munkahelyi vagy iskolai fiókkal, adja hozzá munkahelyi vagy iskolai fiókot használó egyes windows-felhasználók lesznek rekordot kell létrehozni egy új eszköz azonos nevű eszköz.

-   A régebbi verziójú Windows operációs rendszerekről, amelyek a helyszíni AD-tartományhoz automatikus regisztráció használatával hoz létre egy új eszköz rekord ugyanazon eszköznév az egyes tartományi felhasználók, akik az eszköz bejelentkezik. 

-   Egy Azure ad-ben csatlakozó számítógépeken, amelyeket már törlik a tartalmát, újratelepítése, újracsatlakozik azonos nevű, azonos nevű eszköz egy másik rekord jelenik meg.

---

**K: Mi a felhasználó erőforrásaihoz is hozzáférjenek az eszközről szeretnék letiltotta az Azure Portalon?**

**V:** is igénybe vehet egy visszavonni a alkalmazni egy órát.

>[!Note] 
>A regisztrált eszközök esetében javasoljuk, hogy törlődne az eszközön, győződjön meg arról, hogy a felhasználók nem tudnak hozzáférni az erőforrásokhoz. További információkért lásd: [eszközök regisztrálása az Intune-ban felügyeletre](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**K: Miért látnak a felhasználók ", nem innen érheti el"?**

**V:** konfigurált egyes feltételes hozzáférési szabályokat szükséges egy adott eszköz állapotát, és az eszköz nem felel meg a feltételeket, ha a felhasználók le vannak tiltva, és az üzenet jelenik meg. A feltételes hozzáférési szabályzat szabályok kiértékelése, és gondoskodjon arról, hogy az eszköz képes a feltételeknek az üzenet elkerülése érdekében.

---

**Kérdés: a felhasználók némelyike nem nem get MFA utasításokat az Azure AD-hez csatlakoztatott eszközök?**

**V:** felhasználó csatlakozik, vagy egy eszköz regisztrálása az Azure AD szolgáltatással többtényezős hitelesítés, ha maga az eszköz egy megbízható második tényező, hogy az adott felhasználó válnak. Ezt követően, amikor ugyanazt a felhasználót az eszköz bejelentkezik, és hozzáfér az alkalmazáshoz, Azure ad-ben az eszköz második tényezőként figyelembe veszi tartalmaz, és lehetővé teszi, hogy a felhasználó zökkenőmentesen elérhessék az alkalmazásaikat, további MFA-kérések nélküli. Ez a viselkedés nem alkalmazható a semmilyen más felhasználó jelentkezik be, hogy az eszköz, így minden egyéb felhasználó fér hozzá az eszköz akkor is meg kell adnia az MFA-hitelesítést MFA igénylő alkalmazások elérése előtt.

---

**Tekintse meg a felhasználói adatokat az Azure Portalon alatt eszközrekordhoz kérdés, és a állapotát láthatja, mert az eszköz regisztrálva. Igénybe szeretnék megfelelően beállítani a feltételes hozzáférés használatával?**

**V:** az illesztési állapot, deviceID, kifejezi kell felel meg, amely az Azure ad-ben, és megfelelnek a feltételes hozzáférés bármely értékelés által. További információkért lásd: [megkövetelése eszközök feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés felügyelt](../active-directory-conditional-access-policy-connected-applications.md).

---

**K: Miért kapok "helytelen felhasználónév vagy jelszó" üzenetet egy eszközhöz csatlakoztatott csak rendelkezik a az Azure AD?**

**V:** ebben a forgatókönyvben a leggyakoribb okai a következők:

- A felhasználói hitelesítő adatok már nem érvényesek.

- A számítógép nem tud kommunikálni az Azure Active Directoryban. Ellenőrizze, hogy minden hálózati problémák léptek fel.

- Összevont bejelentkezés az összevonási kiszolgálón, a WS-Trust aktív végpontja támogatásához szükséges. 

- Hitelesítési továbbítása engedélyezve van, és a felhasználó rendelkezik egy ideiglenes jelszót, amely a bejelentkezési módosítani kell.

---

**K: Miért jelenik meg a "Hoppá... hiba történt!" párbeszédpanel jelenik meg hajtsa végre az Azure AD join számítógépre?**

**V:** Ez a beállítás az Intune-nal az Azure Active Directory-regisztrációt eredménye. Győződjön meg arról, hogy hajtsa végre az Azure AD-csatlakozás próbáló felhasználó rendelkezik-e a megfelelő Intune-licenccel. További információkért lásd: [Windows-eszközök kezelésének beállítása](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**K: Miért volt sikertelen a saját kísérlet rendszerű Bár minden hibaadatok nem kaptam?**

**V:** ennek valószínű oka az, hogy a felhasználó jelentkezett be az eszközt a beépített helyi rendszergazdai fiók használatával. Mielőtt használhatná az Azure Active Directory-csatlakozás a telepítés befejezéséhez hozzon létre egy másik helyi fiókot. 


---

**K: Hol találom meg hibaelhárítási információk az automatikus eszközregisztráció?**

**V:** hibaelhárítási információkat, lásd:

- [Automatikus regisztráció tartomány hibaelhárítási csatlakoztatott számítógépekre az Azure AD – a Windows 10 és Windows Server 2016-ban](troubleshoot-hybrid-join-windows-current.md)

- [Tartomány automatikus regisztráció hibaelhárítása csatlakozott az Azure AD-számítógépek Windows alsó szintű ügyfelek számára](troubleshoot-hybrid-join-windows-legacy.md)
 

---

