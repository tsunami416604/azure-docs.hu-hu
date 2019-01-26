---
title: Az Azure Active Directory-Eszközfelügyelet – gyakori kérdések |} A Microsoft Docs
description: Azure Active Directory-Eszközfelügyelet – gyakori kérdések.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2010
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 26133dbfe6b8e11128393957c377c1827e0d2a6e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076502"
---
# <a name="azure-active-directory-device-management-faq"></a>Az Azure Active Directory-Eszközfelügyelet – gyakori kérdések

**K: Az eszköz nemrég e regisztrálva. Miért nem látom az eszközt a saját felhasználói adatokat az Azure Portalon? Vagy miért van az eszköz tulajdonosa megjelölve, a hibrid Azure Active Directory (Azure AD) N/A-hez csatlakoztatott eszközök?**

**VÁLASZ:** Hibrid Azure AD-hez Windows 10 rendszerű eszközök nem jelennek meg a **felhasználói eszközök**.
Használja a **minden eszköz** megtekintése az Azure Portalon. A PowerShell is használható [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmagot.

Csak az alábbi eszközök vannak felsorolva **felhasználói eszközök**:

- Hibrid Azure AD nem az összes személyes eszközök tartományhoz. 
- Minden Windows 10 vagy Windows Server 2016-eszközök.
- Az összes nem-Windows-eszközök. 

--- 

**K: Honnan tudhatom meg, hogy az eszköz regisztrációs állapotát, az ügyfél van?**

**VÁLASZ:** Az Azure Portalon lépjen a **minden eszköz**. Keresse meg az eszköz segítségével, hogy az eszköz azonosítójával. Ellenőrizze az értéket az illesztési típus oszlop alatt. Egyes esetekben az eszköz alaphelyzetbe állítása lehet, vagy rendszerképét alaphelyzetbe állítják. Ezért elengedhetetlen is ellenőrizheti az eszköz regisztrációs állapotát az eszközön:

- Futtassa a Windows 10 és Windows Server 2016 vagy újabb rendszerű eszközök, `dsregcmd.exe /status`.
- A régebbi verziójú operációs rendszerekről, futtassa `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**K: A felhasználói adatokat az Azure Portalon alatt eszközrekordhoz látható. És az állapot jelenik meg, az eszköz regisztrálva. Vagyok állíthatom be megfelelően a feltételes hozzáférés?**

**VÁLASZ:** Az illesztési állapot, által megjelenített **deviceID**, kell az állapot egyező az Azure ad-ben, és megfelelnek a feltételes hozzáférés bármely értékelés által. További információkért lásd: [megkövetelése eszközök feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés felügyelt](../conditional-access/require-managed-devices.md).

---

**K: Az eszköz az Azure Portalon vagy a Windows PowerShell-lel törlődött. De az eszközön a helyi állapot szerint továbbra is regisztrálva lesz.**

**VÁLASZ:** Ezt a műveletet a rendszer kialakításából fakad. Az eszköz nem rendelkezik erőforrásokhoz való hozzáférés a felhőben. 

Ha azt szeretné, az újbóli regisztrációt, a manuális műveletet az eszközön kell végeznie. 

A Windows 10 és Windows Server 2016-ban a helyszíni Active Directory tartományhoz való csatlakozás állapot törléséhez tegye a következőket:

1.  Nyissa meg a parancssort rendszergazdaként.

2.  Írja be a `dsregcmd.exe /debug /leave` (igen) kifejezést.

3.  Jelentkezzen ki, és jelentkezzen be az ütemezett feladatot, amely regisztrálja újra az eszközt az Azure AD-trigger. 

A régebbi verziójú Windows operációs rendszerekről, amelyek a helyszíni Active Directory-tartományhoz csatlakozik, hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a parancssort rendszergazdaként.
2.  Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` (igen) kifejezést.
3.  Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` (igen) kifejezést.

---

**K: Miért jelennek meg az Azure Portal, ismétlődő eszközbevitelek?**

**VÁLASZ:**

-   A Windows 10 és Windows Server 2016 elhagyása, és csatlakozzon újra ugyanarra az eszközre való ismételt próbálkozás okozhat ismétlődő bejegyzéseket. 

-   Összes Windows felhasználónak **adja hozzá munkahelyi vagy iskolai fiókkal** új eszközrekordot hoz létre az azonos nevű eszköz.

-   A régebbi verziójú Windows operációs rendszerekről, amelyek a helyszíni Azure Directory-tartományhoz csatlakoztatott automatikus regisztráció az egyes tartományi felhasználók, akik az eszköz bejelentkezik ilyen eszköz nevű új eszközrekordot hoz létre. 

-   Az Azure ad-ben csatlakozó számítógépeken, törlése, telepíteni és ugyanazzal a névvel újracsatlakozik megjelenik-e az azonos nevű eszköz egy másik rekord formájában.

---

**K: Miért felhasználó erőforrásaihoz is hozzáférjenek az e tiltva, az Azure Portalon eszközökről?**

**VÁLASZ:** Vesz egy visszavonni a alkalmazni egy órát.

>[!NOTE] 
>A regisztrált eszközök esetében azt javasoljuk, hogy az eszköz számára, győződjön meg arról, hogy a felhasználók nem férhetnek hozzá az erőforrásokat minden adatának. További információkért lásd: [Mi az eszközök regisztrálása?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Az Azure AD join – gyakori kérdések

**K: Hogyan do I elhagyása egy az Azure AD-csatlakoztatott eszközön helyileg az eszközön?**

**VÁLASZ:** 
- A hibrid Azure AD a csatlakoztatott eszközökre, ellenőrizze, hogy kapcsolja ki az automatikus regisztráció. Ezután az ütemezett feladat nem regisztrálja újra az eszközt. Ezután nyisson meg egy parancssort rendszergazdaként, és adja meg `dsregcmd.exe /debug /leave`. Vagy futtassa a következő parancsot, a parancsfájl több eszközön való tömeges kilépése.

- Igazi Azure AD-hez csatlakoztatott eszközök, győződjön meg arról, offline helyi rendszergazdai fiókkal rendelkezik, vagy hozzon létre egyet. Minden olyan Azure AD-felhasználói hitelesítő adatok nem tud bejelentkezni. Folytassa **beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**. Válassza ki a fiókját, és válassza ki **Disconnect**. Kövesse az utasításokat, és adja meg a helyi rendszergazdai hitelesítő adatokat, amikor a rendszer kéri. Indítsa újra az eszközt a elhagyásra folyamat befejezéséhez.

---

**K: A felhasználók bejelentkezhetnek a törölt vagy le van tiltva, az Azure ad-ben az Azure AD-hez csatlakoztatott eszközök?**

**VÁLASZ:** Igen. Windows Gyorsítótárazott felhasználónév és jelszó képesség, amely lehetővé teszi a felhasználók, akik korábban jelentkezett be az asztal gyorsan elérni a hálózati kapcsolat nélkül is rendelkezik. 

Egy eszköz törlésekor vagy le van tiltva, az Azure ad-ben, nem ismert, a Windows-eszköz. Így továbbra is a felhasználók, akik korábban már bejelentkezett az asztalon, a gyorsítótárazott felhasználónévvel és jelszóval eléréséhez. Azonban amíg az eszköz törli vagy le van tiltva, a felhasználók nem férhetnek hozzá a eszközalapú feltételes hozzáférés által védett erőforrásokat. 

Felhasználók, akik nem jelentkezzen be a korábban az eszköz nem lehet hozzáférni. Nincs nem gyorsítótárazott felhasználónevet és jelszót a számukra engedélyezett. 

---

**K: Letiltott vagy törölt felhasználók jelentkezhetnek be az Azure AD-csatlakoztatott eszközök?**

**VÁLASZ:** Igen, de csak korlátozott ideig. A felhasználó törlésekor vagy le van tiltva, az Azure ad-ben, azonnal nem ismert, a Windows-eszköz. Így a felhasználók, akik korábban már bejelentkezett hozzáférhet az asztalon, a gyorsítótárazott felhasználónévvel és jelszóval. 

Az eszköz általában a felhasználói állapot tudomást négy órán belül. Majd a Windows blokkolja ezen felhasználók hozzáférését az asztalon. Amíg a felhasználó törli vagy le van tiltva, az Azure ad-ben, az összes tokenjeiket visszavonódnak. Így nem tudják elérni erőforrásokat. 

Egy eszköz nem jelentkezzen be a korábban törölt vagy letiltott felhasználók férhetnek hozzá. Nincs nem gyorsítótárazott felhasználónevet és jelszót a számukra engedélyezett. 

---

**K: Miért érdemes a felhasználók rendelkeznek problémák az Azure AD-csatlakoztatott eszközök Felhasználónevük módosítása után?**

**VÁLASZ:** Jelenleg UPN módosítások nem teljesen támogatottak az Azure AD-csatlakoztatott eszközökön. Így az Azure AD-hitelesítés sikertelen lesz, az egyszerű felhasználónév módosítása után. Ennek eredményeképpen felhasználóinak egyszeri Bejelentkezést és feltételes hozzáférési problémák az eszközeiken. Jelenleg a felhasználóknak kell bejelentkezni a Windows a probléma megoldásához új Felhasználónevük használatával "Más felhasználó" csempe keresztül. Jelenleg dolgozunk a probléma megoldásához. Bejelentkezés Windows Hello for Business felhasználók azonban nem ütköznek a probléma. 

---

**K: A felhasználók nem tud keresni, nyomtatók, az Azure AD-hez csatlakoztatott eszközökről. Hogyan lehet engedélyezni, hogy azokat az eszközöket a nyomtatás?**

**VÁLASZ:** Nyomtatók telepítése az Azure ad-hez csatlakoztatott eszközök, lásd: [üzembe helyezése a Windows Server hibrid Felhőbeli nyomtatás előtti hitelesítés](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Egy helyszíni Windows Server üzembe helyezéséhez a hibrid felhőbeli nyomtatás van szüksége. Felhőalapú nyomtatási szolgáltatás jelenleg nem érhető el. 

---

**K: Hogyan létesíthetek kapcsolatot egy távoli Azure ad-hez csatlakoztatott eszközök?**

**VÁLASZ:** Lásd: [csatlakozhat az Azure Active Directoryhoz csatlakoztatott távoli Számítógépekhez](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**K: Miért érdemes a felhasználók látnak *nem érheti el itt*?**

**VÁLASZ:** Adott konfigurálása bizonyos feltételes hozzáférési szabályokat szükséges egy adott eszköz állapota? Ha az eszköz nem teljesíti a feltételeket, felhasználók le vannak tiltva, és megjelenik az üzenet. Értékelje ki a feltételes hozzáférési szabályzat előírásainak. Győződjön meg arról, hogy az eszköz megfelel a feltételeknek, az üzenet elkerülése érdekében.

---

**K: Miért nem a felhasználói egy része jelenik meg az Azure multi-factor Authentication utasításokat az Azure AD-csatlakoztatott eszközökön?**

**VÁLASZ:** Előfordulhat, hogy a felhasználó csatlakozzon, vagy eszköz regisztrálása az Azure ad-vel a multi-factor Authentication szolgáltatás használatával. Maga az eszköz válik, hogy a felhasználó egy megbízható második tényező. Ha ugyanaz a felhasználó az eszköz bejelentkezik, és hozzáfér az alkalmazáshoz, az Azure AD az eszköz második tényezőként figyelembe veszi. Lehetővé teszi, hogy a felhasználó zökkenőmentes az alkalmazások további multi-factor Authentication-kérések nélküli eléréséhez. 

Ez a viselkedés nem vonatkozik semmilyen más felhasználó, aki az adott eszközre bejelentkezik. Így minden egyéb eszköz hozzáféréssel rendelkező felhasználókat a multi-factor Authentication szolgáltatás kihívást beolvasása. Utána hozzáférhet a multi-factor Authentication hitelesítést igénylő alkalmazásokhoz.

---

**K: Miért kapok egy *helytelen felhasználónév vagy jelszó* üzenet az eszköz az Azure AD közvetlenül csatlakoztatott?**

**VÁLASZ:** Ez a forgatókönyv gyakori okai a következők:

- A felhasználói hitelesítő adatok már nem érvényesek.

- A számítógép nem tud kommunikálni az Azure Active Directoryval. Ellenőrizze, hogy minden hálózati problémák léptek fel.

- Összevont bejelentkezések támogatásához a WS-Trust végpontok, amelyek engedélyezve vannak és elérhetők az összevonási kiszolgálóra van szükség. 

- Engedélyezte az átmenő hitelesítést. Ezért az ideiglenes jelszót kell módosítani, amikor bejelentkezik.

---

**K: Miért látok a *Hoppá... hiba történt!* párbeszédpanel jelenik meg az Azure AD join számítógépre?**

**VÁLASZ:** Ez a hiba akkor fordul elő, ha beállította az Azure Active Directory-regisztráció az Intune-nal. Győződjön meg arról, hogy a felhasználó, aki az Azure AD-csatlakozás próbál rendelkezik-e a megfelelő Intune-licenccel. További információkért lásd: [Windows-eszközök regisztrációjának beállítása](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**K: Miért volt saját kísérlet az Azure AD join a PC-s sikertelenek lesznek, bár minden hibaadatok nem kaptam?**

**VÁLASZ:** Ennek valószínű oka az, hogy jelentkezett be az eszközön a beépített helyi rendszergazdai fiók használatával. Hozzon létre egy másik helyi fiókot, a beállítás befejezéséhez az Azure Active Directory join használata előtt. 

---

**Válaszok: Mik azok az MS-szervezet-P2P-hozzáférési tanúsítvány található, a Windows 10 rendszerű eszközökön?**

**VÁLASZ:** Az MS-szervezet-P2P-hozzáférési engedélyeket adtak ki az Azure AD egyaránt, az Azure AD-hez, és a hibrid Azure AD-hez csatlakoztatott eszközök. Ezek a tanúsítványok segítségével engedélyezze a távoli asztali esetekre ugyanabban a bérlőben eszközök közötti megbízhatósági kapcsolat. Egy tanúsítványt az eszközre, és a egy másik kiadni a felhasználó. Az eszköz tanúsítványa megtalálható `Local Computer\Personal\Certificates` és a egy napig érvényes. Ez a tanúsítvány megújításának (egy új tanúsítvány kiállításával) Ha az eszköz még aktív, az Azure ad-ben. A felhasználói tanúsítvány jelen `Current User\Personal\Certificates` , és ezt a tanúsítványt is érvényes egy nap, de azt ki igény szerinti, ha egy felhasználó megpróbál egy távoli asztali munkamenetet egy másik Azure ad-ben csatlakoztatott eszközre. Ez nem újítja meg lejártakor. Mindkét ezeket a tanúsítványokat kibocsátott használatával a MS-szervezet-P2P-hozzáférési tanúsítvány megtalálható az `Local Computer\AAD Token Issuer\Certificates`. A tanúsítványt az Azure AD-eszközök regisztrációja során. 

---

**Q:Why lásd a Windows 10 rendszerű eszközökön az MS-szervezet-P2P-hozzáférés által kiadott több lejárt tanúsítványok? Hogyan törölhetek őket?**

**VÁLASZ:** Hiba történt a Windows 10 1709-es és alacsonyabb, ahol az MS-szervezet-P2P-hozzáférés lejárt tanúsítványokat továbbra is létezik a számítógép tárolja a titkosítási problémák miatt azonosított. A felhasználók sikerült által tapasztalt problémák a hálózati kapcsolatot, ha bármely VPN-ügyfelek (pl. Cisco AnyConnect), amely nem tudja kezelni a nagy számú lejárt tanúsítványokat használ. A probléma javítását a Windows 10 1803 kiadás automatikusan törli az összes ilyen lejárt MS-szervezet-P2P-hozzáférés tanúsítványokat. A probléma megoldható az eszközök frissítése a Windows 10 1803. Ha Ön nem lehet frissíteni, törölheti ezek a tanúsítványok semmilyen negatív hatással.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Hibrid Azure AD joinnal kapcsolatos gyakori kérdések

**K: Hol találom meg hibaelhárítási információ a hibrid Azure AD join hibák diagnosztizálását?**

**VÁLASZ:** Hibaelhárítási információkat, tanulmányozza a következő cikkeket:

- [Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott Windows 10 és Windows Server 2016-eszközök](troubleshoot-hybrid-join-windows-current.md)

- [Hibaelhárítás az Azure Active Directory hibrid csatlakoztatott régebbi verziójú eszközök](troubleshoot-hybrid-join-windows-legacy.md)
 
**K: Miért látok egy ismétlődő Azure ad-ben a Windows 10-es hibrid Azure ad-ben regisztrált rekord csatlakoztatott eszköz az Azure ad-ben eszközlistán?**

**VÁLASZ:** Amikor a felhasználók hozzá egy tartományhoz csatlakoztatott eszközön lévő alkalmazások fiókjukat, akkor előfordulhat, hogy meg kell adnia **fiók hozzáadása a Windows?** Ha belép **Igen** a parancssorban, az eszköz regisztrálása az Azure AD. A megbízhatósági típust az Azure AD-ban regisztrálva van megjelölve. Miután engedélyezte a hibrid Azure AD-csatlakozás a szervezetben, az eszköz is lekéri a hibrid Azure AD-hez. Ezután két eszköz állapota jelenik meg az ugyanazon az eszközön. 

Hibrid Azure AD-csatlakozás az Azure ad-ben regisztrált átadta a feladatait élvez elsőbbséget. Így az eszköz számít a hibrid Azure AD-hez bármely hitelesítési és feltételes hozzáférési kiértékelésnek. Az Azure ad-ben regisztrált eszközrekordot nyugodtan törölheti az Azure AD portálon. Ismerje meg, hogyan [elkerüléséhez vagy a Windows 10-es gépen a kettős állapot tisztítása](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

**K: Miért érdemes a felhasználók rendelkeznek problémák a Windows 10-es hibrid Azure AD-csatlakoztatott eszközök Felhasználónevük módosítása után?**

**VÁLASZ:** Jelenleg UPN módosítások nem teljesen támogatottak a hibrid Azure AD-csatlakoztatott eszközök. Felhasználók jelentkezzen be az eszköz és a helyszíni alkalmazások elérését, amíg az Azure AD-hitelesítés nem sikerül, egy UPN módosítását követően. Ennek eredményeképpen felhasználóinak egyszeri Bejelentkezést és feltételes hozzáférési problémák az eszközeiken. Jelenleg az eszköz az Azure ad-ből ("dsregcmd /leave" futtassa megemelt jogosultságokkal) elhagyása kell újra belépni az (automatikusan megtörténik), és a probléma megoldásához. Jelenleg dolgozunk a probléma megoldásához. Bejelentkezés Windows Hello for Business felhasználók azonban nem ütköznek a probléma. 

---


## <a name="azure-ad-register-faq"></a>Az Azure AD regisztrálása – gyakori kérdések

**K: Android vagy IOS rendszerű BYOD-eszközöket is regisztrálni?**

**VÁLASZ:** Igen, de csak az Azure eszközregisztrációs szolgáltatással és a hibrid ügyfelek számára. Nem támogatott az a helyszíni Active Directory összevonási szolgáltatások (AD FS) az eszközregisztrációs szolgáltatással.

**K: Hogyan regisztrálhatnak a macOS-eszközön?**

**VÁLASZ:** Az alábbi lépéseket:

1.  [Megfelelőségi szabályzat létrehozása](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [MacOS-eszközökre vonatkozó feltételes hozzáférési szabályzat definiálása](../active-directory-conditional-access-azure-portal.md) 

**Megjegyzés:**

- A feltételes hozzáférési házirendben a felhasználókat kell egy [támogatott Office-verzió macOS-hez készült](../conditional-access/technical-reference.md#client-apps-condition) erőforrások eléréséhez. 

- Az első hozzáférési próbálkozzon során a rendszer kéri a felhasználók regisztrálja az eszközt a vállalati portál használatával.

