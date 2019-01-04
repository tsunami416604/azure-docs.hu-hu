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
ms.openlocfilehash: 8d1e44eae7e87a450ac5f36e621d559fca92ca74
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016154"
---
# <a name="azure-active-directory-device-management-faq"></a>Az Azure Active Directory-Eszközfelügyelet – gyakori kérdések

**KÉRDÉS: Az eszköz nemrég e regisztrálva. Miért nem látom az eszközt a saját felhasználói adatokat az Azure Portalon? Miért kijelölt vagy egy eszköz tulajdonosa n/a hibrid Azure AD-hez csatlakoztatott eszközök esetében? ** 
 **V:** Hibrid Azure AD-hez Windows 10 rendszerű eszközök nem jelennek meg a felhasználói eszközök alapján.
Szeretné használni az összes eszköz megtekintése az Azure Portalon. Emellett használhatja a Powershellt [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmagot.

Az alábbi eszközök csak a felhasználói eszközök vannak felsorolva:

- Az összes személyes eszközök, amelyek nem a hibrid Azure AD-tartományhoz. 
- Az összes, nem Windows 10 és Windows Server 2016-eszközök.
- Az összes nem-Windows-eszközök 

--- 

**KÉRDÉS: Honnan tudhatom meg, hogy az eszköz regisztrációs állapotát, az ügyfél van?**

**VÁLASZ:** Az Azure Portal, az összes eszközre megnyithatja és keresse meg az eszköz használata az eszköz azonosítójával. Ellenőrizze az értéket az illesztési típus oszlop alatt. Egyes esetekben az eszközre sikerült lettek alaphelyzetbe állítása vagy rendszerképét alaphelyzetbe állítják. Ezért elengedhetetlen is ellenőrizheti az eszköz regisztrációs állapota az eszköz túl:

- A Windows 10 és Windows Server 2016 vagy újabb rendszerű eszközök futtassa a dsregcmd.exe/status.
- A régebbi verziójú operációs rendszerekről futtassa a "%programFiles%\Microsoft munkahelyi Join\autoworkplace.exe"

---

**KÉRDÉS: Tekintse meg a felhasználói adatokat az Azure Portalon alatt eszközrekordhoz e, és a állapotát láthatja, mert az eszköz regisztrálva. Vagyok állíthatom be megfelelően a feltételes hozzáférés?**

**VÁLASZ:** Az illesztési állapot, deviceID, kifejezi kell felel meg, amely az Azure ad-ben, és megfelelnek a feltételes hozzáférés bármely értékelés által. További információkért lásd: [megkövetelése eszközök feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés felügyelt](../conditional-access/require-managed-devices.md).

---

**KÉRDÉS: Törölt rendelkezik az Azure Portalon vagy az eszközön található Windows PowerShell, de a helyi állapot használatával arról tájékoztat, hogy továbbra is regisztrálva van?**

**VÁLASZ:** Ez az elvárt működés. Az eszköz nem fog erőforrásokhoz való hozzáférés a felhőben. 

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

**KÉRDÉS: Miért jelennek meg az Azure portal, ismétlődő eszközbevitelek?**

**VÁLASZ:**

-   A Windows 10 és Windows Server 2016-ra ha ismétlődő elhagyása, és csatlakozzon újra ugyanarra az eszközre is ismétlődő bejegyzéseket. 

-   Használta, adja hozzá munkahelyi vagy iskolai fiókkal, adja hozzá munkahelyi vagy iskolai fiókot használó egyes windows-felhasználók lesznek rekordot kell létrehozni egy új eszköz azonos nevű eszköz.

-   A régebbi verziójú Windows operációs rendszerekről, amelyek a helyszíni AD-tartományhoz automatikus regisztráció használatával hoz létre egy új eszköz rekord ugyanazon eszköznév az egyes tartományi felhasználók, akik az eszköz bejelentkezik. 

-   Egy Azure ad-ben csatlakozó számítógépeken, amelyeket már törlik a tartalmát, újratelepítése, újracsatlakozik azonos nevű, azonos nevű eszköz egy másik rekord jelenik meg.

---

**KÉRDÉS: Miért érdemes a felhasználó erőforrásaihoz is hozzáférjenek az eszközről szeretnék letiltotta az Azure Portalon?**

**VÁLASZ:** A visszavonni a alkalmazni egy órát is igénybe vehet.

>[!Note] 
>A regisztrált eszközök esetében javasoljuk, hogy törlődne az eszközön, győződjön meg arról, hogy a felhasználók nem tudnak hozzáférni az erőforrásokhoz. További információkért lásd: [eszközök regisztrálása az Intune-ban felügyeletre](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Az Azure AD Join – gyakori kérdések

**KÉRDÉS: Hogyan do I elhagyása egy Azure AD-csatlakoztatás eszközt helyileg az eszközön?**

**VÁLASZ:** 
- Hibrid Azure AD-csatlakoztatás eszközökhöz ügyeljen arra, hogy az automatikus regisztráció kikapcsolása, így az ütemezett feladat nem regisztrálja újra az eszközt. Ezután nyissa meg a parancssort rendszergazdaként, és írja be `dsregcmd.exe /debug /leave`. Azt is megteheti ezzel a paranccsal futtathatja, a parancsfájl több eszközre történő tömeges elhagyása.

- Az igazi Azure AD-csatlakoztatás eszközök, ellenőrizze, hogy a kapcsolat nélküli helyi rendszergazdai fiók, vagy létrehozhat egy újat, nem kell minden olyan Azure AD felhasználói hitelesítő adatokkal jelentkezhetnek be. Folytassa **beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**. Válassza ki azt a fiókot, majd kattintson a **Disconnect**. Kövesse az utasításokat, és adja meg a helyi rendszergazdai hitelesítő adatokat, amikor a rendszer kéri. Indítsa újra az eszközt a elhagyásra befejezéséhez.

---

**KÉRDÉS: A felhasználók jelentkezhetnek be, hogy törölték, vagy le van tiltva, az Azure ad-ben az Azure AD-hez csatlakoztatott eszközök? ** 
 **V:** Igen. Windows gyorsítótárazta korábban bejelentkezett felhasználók asztali gyorsan hozzáférhessenek a hálózati kapcsolat nélkül is, hogy be tudjon jelentkezni. Egy eszköz törlésekor vagy le van tiltva, az Azure ad-ben, nem ismert, a Windows-eszköz. Így korábban bejelentkezett felhasználók továbbra is elérhető az asztalon, a gyorsítótárazott bejelentkezés. Azonban amíg az eszköz törli vagy le van tiltva, felhasználók nem férhetnek hozzá olyan eszközalapú feltételes hozzáférés által védett erőforrások. 

Felhasználók, akik még nem már bejelentkezett a nem az eszköz elérésére, mivel nem lett a gyorsítótárazott bejelentkezés nem engedélyezett a számukra. 

---

**KÉRDÉS: Letiltott vagy törölt felhasználók jelentkezhetnek be az Azure AD-csatlakoztatott eszközök? ** 
 **V:** Igen, de csak korlátozott ideig. A felhasználó törlésekor vagy le van tiltva, az Azure ad-ben, nem azonnal ismert a Windows-eszközön. Így korábban bejelentkezett felhasználók férhetnek hozzá a gyorsítótárazott bejelentkezés az asztalon. Amint az eszköz tisztában a felhasználói állapot (általában kevesebb mint 4 óra), Windows letiltja a felhasználók hozzáférését az asztalon. Amíg a felhasználó törli vagy le van tiltva, az Azure ad-ben, az összes tokenjeiket visszavonja, így nem tudják elérni erőforrásokat. 

Törölték, vagy letiltott felhasználók, akik korábban még nem bejelentkezett nem eszköz eléréséhez, mivel nem lett a gyorsítótárazott bejelentkezés nem engedélyezett a számukra. 

---

**KÉRDÉS: A felhasználók nem tud keresni, nyomtatók, az Azure AD-csatlakoztatott eszközök. Hogyan lehet engedélyezni, hogy az Azure AD-csatlakoztatott eszközök nyomtatás?**

**VÁLASZ:** Nyomtatók az Azure AD-csatlakoztatott eszközök telepítése, lásd: [hibrid felhőbeli nyomtatás](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Szüksége lesz egy helyszíni Windows Server üzembe helyezéséhez a hibrid felhőbeli nyomtatás. Nyomtatási szolgáltatás felhőalapú jelenleg nem áll rendelkezésre. 

---

**KÉRDÉS: Hogyan létesíthetek kapcsolatot egy távoli Azure ad-hez csatlakoztatott eszközök? ** 
 **V:** A cikkben https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc részleteiről.

---

**KÉRDÉS: Miért látnak a felhasználók ", nem innen érheti el"?**

**VÁLASZ:** Ha konfigurálta a bizonyos feltételes hozzáférési szabályokat szükséges egy adott eszköz állapotát, és az eszköz nem felel meg a feltételeknek, a felhasználók le vannak tiltva, és az üzenet jelenik meg. A feltételes hozzáférési szabályzat szabályok kiértékelése, és ellenőrizze, hogy az eszköz képes a feltételeknek az üzenet elkerülése érdekében.

---

**KÉRDÉS: Miért néhány saját felhasználók do nem get MFA kérni fogja az Azure AD-hez csatlakoztatott eszközök?**

**VÁLASZ:** Ha a felhasználó csatlakozik, vagy egy eszköz regisztrálása az Azure AD szolgáltatással többtényezős hitelesítés, maga az eszköz egy megbízható második tényező, hogy az adott felhasználó válnak. Ezt követően, amikor ugyanazt a felhasználót az eszköz bejelentkezik, és hozzáfér az alkalmazáshoz, Azure ad-ben az eszköz második tényezőként figyelembe veszi tartalmaz, és lehetővé teszi, hogy a felhasználó zökkenőmentesen elérhessék az alkalmazásaikat, további MFA-kérések nélküli. Ez a viselkedés nem alkalmazható a semmilyen más felhasználó jelentkezik be, hogy az eszköz, így minden egyéb felhasználó fér hozzá az eszköz akkor is meg kell adnia az MFA-hitelesítést MFA igénylő alkalmazások elérése előtt.

---

**KÉRDÉS: Miért kapok "helytelen felhasználónév vagy jelszó" üzenetet egy eszközhöz csatlakoztatott csak rendelkezik a az Azure AD?**

**VÁLASZ:** Ez a forgatókönyv gyakori okai a következők:

- A felhasználói hitelesítő adatok már nem érvényesek.

- A számítógép nem tud kommunikálni az Azure Active Directoryban. Ellenőrizze, hogy minden hálózati problémák léptek fel.

- Összevont bejelentkezés az összevonási kiszolgálón engedélyezve van, és elérhető-e a WS-Trust végpontok támogatásához szükséges. 

- Hitelesítési továbbítása engedélyezve van, és a felhasználó rendelkezik egy ideiglenes jelszót, amely a bejelentkezési módosítani kell.

---

**KÉRDÉS: Miért jelenik meg a "Hoppá... hiba történt!" párbeszédpanel jelenik meg hajtsa végre az Azure AD join számítógépre?**

**VÁLASZ:** Ez a beállítás az Intune-nal az Azure Active Directory-regisztrációt eredménye. Győződjön meg arról, hogy hajtsa végre az Azure AD-csatlakozás próbáló felhasználó rendelkezik-e a megfelelő Intune-licenccel. További információkért lásd: [Windows-eszközök kezelésének beállítása](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**KÉRDÉS: Miért volt saját kísérlet az Azure AD join PC feladatátvétel Bár minden hibaadatok nem kaptam?**

**VÁLASZ:** Ennek valószínű oka az, hogy a felhasználó jelentkezett be az eszközt a beépített helyi rendszergazdai fiók használatával. Hozzon létre egy másik helyi fiók használata az Azure Active Directory-csatlakozás a telepítés befejezéséhez előtt. 

---

## <a name="hybrid-azure-ad-join-faq"></a>Hibrid Azure AD-csatlakozás – gyakori kérdések

**KÉRDÉS: Hol találom meg hibaelhárítási információ a hibrid Azure AD join hibák diagnosztizálásához?**

**VÁLASZ:** Hibaelhárítási információkat, lásd:

- [Tartomány automatikus regisztrációnak hibaelhárítási csatlakoztatott számítógépekre az Azure AD – a Windows 10 és Windows Server 2016-ban](troubleshoot-hybrid-join-windows-current.md)

- [Tartomány automatikus regisztrációnak hibaelhárítási csatlakozott az Azure AD-számítógépek Windows alsó szintű ügyfelek számára](troubleshoot-hybrid-join-windows-legacy.md)
 
**KÉRDÉS: Miért látok egy ismétlődő Azure ad-ben a Windows 10-es hibrid Azure ad-ben regisztrált rekord csatlakoztatott eszköz az Azure ad-ben eszközlistán?**

**VÁLASZ:** Amikor a felhasználók fiókjuk hozzá egy tartományba léptetett eszköz alkalmazásokat, azok "Fiók hozzáadása a Windows?" lehetett kérni. "Igen" gombra kattintva a parancssorban vezetne az eszközt regisztrálni kell az Azure ad-vel, és a Megbízhatóság típusa megjelölve az Azure AD-ban regisztrálva. Miután engedélyezte a hibrid Azure AD-csatlakozás a szervezetben, az eszköz is megkapja a hibrid Azure AD-hez. Ennek eredményeképpen lesz két eszköz állapota jelenik meg az ugyanazon az eszközön. Azonban a hibrid Azure AD-csatlakozás az Azure AD-ban regisztrálva átadta a feladatait élvez elsőbbséget. Így hibrid Azure AD-csatlakozás bármely hitelesítési és feltételes hozzáférési kiértékelésnek az eszköz akkor minősül. Így nyugodtan törölheti az Azure ad-ben regisztrált eszköz rekordot az Azure AD portálon. Tekintse át [ebben a szakaszban](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know) a hibrid Azure ad-ben a csatlakozás a cikk segít megérteni, hogyan elkerülése érdekében vagy karbantartása a kettős állapotát a Windows 10-es gépen. 

---

## <a name="azure-ad-register-faq"></a>Az Azure AD-regisztrációs – gyakori kérdések

**KÉRDÉS: Android vagy IOS rendszerű BYOD-eszközöket is regisztrálni?**

**VÁLASZ:** Igen, de csak az Azure eszközregisztrációs szolgáltatással és a hibrid ügyfelek számára. Az eszközregisztrációs szolgáltatással a helyszíni AD FS nem támogatott.

**KÉRDÉS: Hogyan regisztrálhatnak a macOS-eszközön?**

**VÁLASZ:** MacOS-eszköz regisztrálása:

1.  [Megfelelőségi szabályzat létrehozása](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [MacOS-eszközökre vonatkozó feltételes hozzáférési szabályzat definiálása](../active-directory-conditional-access-azure-portal.md) 

**Megjegyzés:**

- A felhasználóknak, amelyek szerepelnek a feltételes hozzáférési szabályzatot kell egy [támogatott Office-verzió macOS-hez készült](../conditional-access/technical-reference.md#client-apps-condition) erőforrások eléréséhez. 

- Az első hozzáférési kísérlet során a rendszer kéri a felhasználókat az eszköz regisztrálása a vállalati portál használatával.

---
