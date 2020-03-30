---
title: Azure Active Directory eszközkezelés – gyakori kérdések | Microsoft dokumentumok
description: Az Azure Active Directory eszközkezeléssel kapcsolatos gyakori kérdések.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497516"
---
# <a name="azure-active-directory-device-management-faq"></a>Az Azure Active Directory eszközkezelés – gyakori kérdések

## <a name="general-faq"></a>Általános gyakori kérdések

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>K: Nemrég regisztráltam az eszközt. Miért nem látom az eszközt a felhasználói adataim alatt az Azure Portalon? Vagy miért van az eszköz tulajdonosa n/a-ként megjelölve a hibrid Azure Active Directory (Azure AD) csatlakozott eszközökhez?

**A.** A hibrid Azure AD-hez csatlakozott Windows 10-eszközök nem jelennek meg **a USER-eszközök**alatt.
Használja a **Minden eszköz** nézetet az Azure Portalon. A PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmag is használható.

A **FELHASZNÁLÓI eszközök**alatt csak a következő eszközök jelennek meg:

- Minden olyan személyes eszköz, amely nem hibrid Azure AD-hez csatlakozik. 
- Az összes nem Windows 10 vagy Windows Server 2016 rendszerű eszköz.
- Minden nem Windows rendszerű eszköz. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>K: Honnan tudhatom, hogy mi az ügyfél eszközregisztrációs állapota?

**A.** Az Azure Portalon nyissa meg az **Összes eszköz**lehetőséget. Keresse meg az eszközt az eszközazonosító használatával. Ellenőrizze az illesztési típus oszlop alatti értéket. Előfordulhat, hogy az eszköz alaphelyzetbe áll, vagy újraképezi. Ezért fontos, hogy ellenőrizze az eszköz regisztrációs állapotát is az eszközön:

- Windows 10-es és Windows Server 2016-os vagy újabb rendszerű eszközökön futtassa a futtassa a futtassa a programot. `dsregcmd.exe /status`
- Az operációs rendszer lefelé `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`simítású verzióihoz futtassa a futtassa a futtassa a futtassa a

**A.** A hibaelhárításról az alábbi cikkekben talál:
- [A dsregcmd paranccsal használható eszközök hibaelhárítása](troubleshoot-device-dsregcmd.md)
- [Hibrid Azure Active Directory csatlakozása Windows 10 és Windows Server 2016-eszközök hibáiról](troubleshoot-hybrid-join-windows-current.md)
- [Hibrid Azure Active Directory-csatlakozás oka az alulról nem vett eszközökre](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>K: Az eszközrekord az Azure-portál felhasználói adatai alatt jelenik meg. És látom, hogy az állam regisztrálva van az eszközön. Helyesen van beállítva a feltételes hozzáférés használata?

**A.** Az eszközillesztési állapot, az **eszközazonosító**által megjelenített, meg kell egyeznie az azure-beli AD állapotának, és meg kell felelnie a feltételes hozzáférés kiértékelési feltételeinek. További információ: [Felügyelt eszközök megkövetelése a felhőalapú alkalmazások hoz való hozzáféréshez feltételes hozzáféréssel.](../conditional-access/require-managed-devices.md)

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>K: Miért jelennek meg a felhasználóim a következő hibaüzenetben: "Az Ön szervezete törölte az eszközt" vagy "A szervezet letiltotta az eszközt" a Windows 10-es eszközeiken?

**A.** Az Azure AD-hez csatlakozott vagy regisztrált Windows 10-es eszközökön a felhasználók [elsődleges frissítési jogkivonatot (PRT)](concept-primary-refresh-token.md) kapnak, amely lehetővé teszi az egyszeri bejelentkezést. A PRT érvényessége magától az eszköz érvényességén alapul. A felhasználók ezt az üzenetet, ha az eszköz vagy törölt vagy le van tiltva az Azure AD-ben anélkül, hogy a művelet et az eszközről is. Egy eszköz törölhető vagy letiltható az Azure AD-ben az alábbi forgatókönyvek egyikében: 

- A felhasználó letiltja az eszközt a Saját alkalmazások portálon. 
- Egy rendszergazda (vagy felhasználó) törli vagy letiltja az eszközt az Azure Portalon vagy a PowerShell használatával
- Csak hibrid Azure AD-hez csatlakozott: A rendszergazda eltávolítja az eszközöket, amelyek nem rendelkeznek a szinkronhatókörrel, így az eszközök törlődnek az Azure AD-ből
- Az Azure AD frissítése az 1.4.xx.x verzióhoz csatlakozik. [Az Azure AD Connect 1.4.xx.x és az eszközök eltűnésének ismertetése.](/azure/active-directory/hybrid/reference-connect-device-disappearance)


Lásd alább, hogyan lehet ezeket a műveleteket orvosolni.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>K: Letiltottam vagy töröltem az eszközömet az Azure Portalon vagy a Windows PowerShell használatával. De a helyi állam a készüléken azt mondja, hogy még mindig regisztrálva van. Mit tegyek?

**A.** Ez a művelet szándékos. Ebben az esetben az eszköz nem fér hozzá a felhőben lévő erőforrásokhoz. A rendszergazdák ezt a műveletet elavult, elveszett vagy ellopott eszközök esetén hajthatják végre a jogosulatlan hozzáférés megakadályozása érdekében. Ha ezt a műveletet nem szándékosan hajtották végre, újra engedélyeznie kell vagy újra regisztrálnia kell az eszközt az alábbiakszerint.

- Ha az eszköz le van tiltva az Azure AD-ben, a megfelelő jogosultságokkal rendelkező rendszergazda engedélyezheti azt az Azure AD portálról  
  > [!NOTE]
  > Ha az Azure AD Connect használatával szinkronizálja az eszközöket, a hibrid Azure AD-hez csatlakozó eszközök automatikusan újra engedélyezve lesznek a következő szinkronizálási ciklusban. Így ha le kell tiltania egy hibrid Azure AD-hez csatlakozott eszközt, le kell tiltania azt a helyszíni AD-ből

 - Ha az eszköz törlődik az Azure AD-ben, újra regisztrálnia kell az eszközt. Az újbóli regisztráláshoz manuális műveletet kell végrehajtania az eszközön. Az eszköz állapotának megfelelő újbóli regisztrálási utasítások alább találhatók. 

      A hibrid Azure AD-hez való csatlakozáshoz a Windows 10 és a Windows Server 2016/2019-es eszközök az alábbi lépésekkel:

      1. Nyissa meg a parancssort rendszergazdaként.
      1. Írja be a `dsregcmd.exe /debug /leave` (igen) kifejezést.
      1. Jelentkezzen ki, és jelentkezzen be az ütemezett feladat elindításához, amely újra regisztrálja az eszközt az Azure AD-vel. 

      A hibrid Azure AD-hez csatlakozott, lefelé érő Windows operációs rendszer verzióihoz tegye a következő lépéseket:

      1. Nyissa meg a parancssort rendszergazdaként.
      1. Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` (igen) kifejezést.
      1. Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` (igen) kifejezést.

      Az Azure AD-hez csatlakozó Windows 10-eszközök esetén tegye a következő lépéseket:

      1. A parancssor megnyitása rendszergazdaként
      1. Írja `dsregcmd /forcerecovery` be (Megjegyzés: A művelet végrehajtásához rendszergazdának kell lennie).
      1. Kattintson a "Bejelentkezés" gombra a megnyíló párbeszédpanelen, és folytassa a bejelentkezési folyamatot.
      1. Jelentkezzen ki, és jelentkezzen be vissza az eszközre a helyreállítás befejezéséhez.

      Az Azure AD regisztrált Windows 10-es eszközök esetén tegye a következő lépéseket:

      1. Nyissa meg a **Beállítások** > **fiókok** > **elérése a munka vagy az iskola**lehetőséget. 
      1. Jelölje ki a fiókot, és válassza a **Kapcsolat bontása**lehetőséget.
      1. Kattintson a "+ Connect" gombra, és regisztrálja újra az eszközt a bejelentkezési folyamaton keresztül.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>K: Miért jelennek meg ismétlődő eszközbejegyzések az Azure Portalon?

**V:**

- Windows 10 és Windows Server 2016 esetén az ugyanazon eszközhöz való ismételt csatlakozásra irányuló és újracsatlakozási próbálkozások ismétlődő bejegyzéseket okozhatnak. 
- Minden Olyan Windows-felhasználó, aki **a Munkahelyi vagy iskolai fiók hozzáadása szolgáltatását** használja, új eszközrekordot hoz létre ugyanazzal az eszköznévvel.
- A helyszíni Azure Directory-tartományhoz csatlakozó, lefelé irányuló Windows operációs rendszer verzióihoz az automatikus regisztráció új eszközrekordot hoz létre ugyanazzal az eszköznévvel minden olyan tartományfelhasználó számára, aki bejelentkezik az eszközre. 
- Egy Azure AD-hez csatlakozott gép, amely törölte, újratelepítette, és újra csatlakozott az azonos nevű jelenik meg, mint egy másik rekord ugyanazzal az eszköznévvel.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>K: Támogatja a Windows 10-es eszközregisztráció az Azure AD-ben a TPM-eket FIPS módban?

**A.** A Windows 10-es eszközregisztráció csak a FIPS-kompatibilis TPM 2.0 esetén támogatott, a TPM 1.2 esetében nem. Ha az eszközök FIPS-kompatibilis TPM 1.2-es, le kell tiltania őket, mielőtt az Azure AD-csatlakozás vagy a hibrid Azure AD-csatlakozás. A Microsoft nem biztosít eszközöket a TPM-ek FIPS-módjának letiltására, mivel az a TPM gyártójától függ. Támogatásért forduljon a hardverogyártóhoz. 

---

**K: Miért férhet hozzá egy felhasználó továbbra is az Azure Portalon letiltott eszköz erőforrásaihoz?**

**A.** Az Azure AD-eszköz letiltottként való megjelölésétől egy visszavonás alkalmazása akár egy órát is igénybe vesz.

>[!NOTE] 
>A regisztrált eszközök esetében azt javasoljuk, hogy törölje az eszközt, hogy a felhasználók ne férhessenek hozzá az erőforrásokhoz. További információ: [Mi az eszközregisztráció?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>K: Miért vannak "Függőben" megjelölve az eszközök az Azure Portal regisztrált oszlopában?

**A**: A Függőben azt jelzi, hogy az eszköz nincs regisztrálva. Ez az állapot azt jelzi, hogy egy eszköz szinkronizálása az Azure AD-csatlakozás sal egy helyszíni AD, és készen áll az eszköz regisztrációjára. Ezek az eszközök a JOIN TYPE beállítása "Hibrid Azure AD csatlakozott". További információ a [hibrid Azure Active Directory-csatlakozási megvalósítás megtervezéséről.](hybrid-azuread-join-plan.md)

>[!NOTE]
>Az eszköz a regisztrált állapotról a "Függőben" állapotra is változhat
>* Ha egy eszköz először törlődik az Azure AD-ből, és újra szinkronizálódik egy helyszíni AD-ből.
>* Ha egy eszközt eltávolítanak az Azure AD Connect szinkronizálási hatóköréből, és újra hozzáadnak.
>
>Mindkét esetben újra regisztrálnia kell az eszközt manuálisan az egyes eszközökön. Ha ellenőrizni szeretné, hogy az eszköz korábban regisztrálva volt-e, [a dsregcmd paranccsal elháríthatja az eszközöket.](troubleshoot-device-dsregcmd.md)

---
## <a name="azure-ad-join-faq"></a>Azure AD-csatlakozás – gyakori kérdések

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>K: Hogyan oldhatom el az Azure AD-hez csatlakozott eszközt helyileg az eszközön?

**A.** A tiszta Azure AD-hez csatlakozó eszközök, győződjön meg arról, hogy egy offline helyi rendszergazdai fiókkal rendelkezik, vagy hozzon létre egyet. Egyetlen Azure AD-felhasználói hitelesítő adatokkal nem jelentkezhet be. Ezután nyissa meg a **Beállítások** > **fiókok** > **elérése munkahelyi vagy iskolai**lehetőséget. Válassza ki a fiókját, és válassza a **Kapcsolat bontása**lehetőséget. Kövesse az utasításokat, és adja meg a helyi rendszergazda hitelesítő adatait, amikor a rendszer kéri. Indítsa újra az eszközt az egyesítési folyamat befejezéséhez.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>K: A felhasználók bejelentkezhetnek az Azure AD-hez csatlakozott eszközök, amelyek törlődnek vagy le vannak tiltva az Azure AD-ben?

**V:** Igen. A Windows gyorsítótárazott felhasználónév- és jelszófunkcióval rendelkezik, amely lehetővé teszi a korábban bejelentkezett felhasználók számára, hogy hálózati kapcsolat nélkül is gyorsan hozzáférjenek az asztalhoz. 

Ha egy eszközt törölnek vagy letiltanak az Azure AD-ben, a Windows-eszköz nem ismeri. Így azok a felhasználók, akik korábban bejelentkeztek, továbbra is hozzáférnek az asztalhoz a gyorsítótárazott felhasználónévvel és jelszóval. De az eszköz törlése vagy letiltása, a felhasználók nem férhetnek hozzá az eszközalapú feltételes hozzáférés által védett erőforrásokhoz. 

Azok a felhasználók, akik korábban nem jelentkezett be, nem férhetnek hozzá az eszközhöz. Nincs engedélyezve a gyorsítótárazott felhasználónév és jelszó. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>K: Bejelentkezhet egy letiltott vagy törölt felhasználó egy Azure AD-hez csatlakozó eszközre

**A.** Igen, de csak korlátozott ideig. Ha egy felhasználó törlődik vagy le van tiltva az Azure AD-ben, a Windows-eszköz nem ismeri azonnal. Így azok a felhasználók, akik korábban bejelentkeztek, hozzáférhetnek az asztalhoz a gyorsítótárazott felhasználónévvel és jelszóval. 

Az eszköz általában négy órán belül ismeri a felhasználói állapotot. Ezután a Windows blokkolja a felhasználók hozzáférését az asztalhoz. A felhasználó törlődik vagy le van tiltva az Azure AD-ben, az összes jogkivonatot visszavonják. Így nem férnek hozzá semmilyen erőforráshoz. 

Azok a törölt vagy letiltott felhasználók, akik korábban nem jelentkezhöttbe, nem férhetnek hozzá az eszközhöz. Nincs engedélyezve a gyorsítótárazott felhasználónév és jelszó. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>K: Miért vannak a felhasználók problémái az Azure AD-hez csatlakozott eszközöken az upn módosítása után?

**A.** Az UPN-módosítások jelenleg nem teljes mértékben támogatottak az Azure AD-hez csatlakozó eszközökön. Így az Azure AD-vel való hitelesítésük sikertelen lesz az upn-módosítások után. Ennek eredményeképpen a felhasználók sso és feltételes hozzáférés problémákat az eszközökön. A probléma megoldásához ekkor a felhasználóknak be kell jelentkezniük a Windows rendszerbe az "Egyéb felhasználó" csempén keresztül az új upn segítségével. Jelenleg dolgozunk a probléma megoldásán. A Windows Hello vállalati verzióval bejelentkező felhasználók azonban nem szembesülnek ezzel a problémával. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>K: A felhasználók nem tudnak keresni nyomtatók az Azure AD-hez csatlakozott eszközök. Hogyan engedélyezhetem a nyomtatást ezekről az eszközökről?

**A.** Nyomtatók üzembe helyezéséről az Azure AD-hez csatlakozó eszközökhöz, olvassa [el a Windows Server hibrid felhőalapú nyomtatás ának telepítése előhitelesítéssel című témakört.](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) A hibrid felhőalapú nyomtatás üzembe helyezéséhez helyszíni Windows Server-kiszolgálóra van szükség. Jelenleg a felhőalapú nyomtatási szolgáltatás nem érhető el. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>K: Hogyan csatlakozhatok egy távoli Azure AD-hez csatlakozott eszközhöz?

**A.** Lásd: [Csatlakozás távoli Azure Active Directoryhoz csatlakozó számítógéphez](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>K: Miért látják a *felhasználóim, hogy innen nem tudsz odajutni?*

**A.** Konfigurált bizonyos feltételes hozzáférési szabályokat egy adott eszközállapot megkövetelésére? Ha az eszköz nem felel meg a feltételeknek, a felhasználók le vannak tiltva, és látják ezt az üzenetet. Értékelje ki a feltételes hozzáférési házirend szabályait. Győződjön meg arról, hogy az eszköz megfelel az üzenet elkerüléséhez megadott feltételeknek.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>K: Miért nem kap néhány felhasználóm azure-beli többtényezős hitelesítési kéréseket az Azure AD-hez csatlakozott eszközökön?

**A.** A felhasználó csatlakozhat, vagy regisztráljon egy eszközt az Azure AD használatával többtényezős hitelesítés. Ezután maga az eszköz az adott felhasználó megbízható második tényezőjének válik. Amikor ugyanaz a felhasználó bejelentkezik az eszközre, és hozzáfér egy alkalmazáshoz, az Azure AD az eszközt második tényezőnek tekinti. Lehetővé teszi, hogy a felhasználó zökkenőmentesen férjen hozzá az alkalmazásokhoz további többtényezős hitelesítési kérések nélkül. 

Ez a viselkedés:

- Az Azure AD-hez csatlakozott és az Azure AD regisztrált eszközökre alkalmazható– de a hibrid Azure AD-hez csatlakozott eszközökre nem.
- Nem alkalmazható más olyan felhasználókra, akik bejelentkeznek az adott eszközre. Így minden más felhasználó, aki hozzáfér az eszközhöz, többtényezős hitelesítési kihívást kap. Ezután hozzáférhetnek a többtényezős hitelesítést igénylő alkalmazásokhoz.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>K: Miért kapok *egy felhasználónevet vagy jelszót helytelen* üzenet egy eszközhöz, amelyhez most csatlakoztam az Azure AD-hez?

**A.** Ennek a forgatókönyvnek a gyakori okai a következők:

- A felhasználói hitelesítő adatok már nem érvényesek.
- A számítógép nem tud kommunikálni az Azure Active Directoryval. Ellenőrizze, hogy vannak-e hálózati kapcsolati problémák.
- Összevont bejelentkezések megkövetelik az összevonási kiszolgáló, hogy támogassa a WS-Trust végpontok, amelyek engedélyezve vannak és elérhető. 
- Engedélyezte az átmenő hitelesítést. Így az ideiglenes jelszót módosítani kell, amikor bejelentkezik.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>K: Miért látom a *Hoppá ... hiba történt!* párbeszédablak, amikor megpróbálom az Azure AD csatlakozni a számítógéphez?

**A.** Ez a hiba akkor fordul elő, ha az Azure Active Directory regisztráció az Intune-nal. Győződjön meg arról, hogy a felhasználó, aki megpróbálja az Azure AD-csatlakozást, a megfelelő Intune-licenc hozzárendelése. További információt a [Windows-eszközök regisztrációjának beállítása című témakörben talál.](/intune/windows-enroll)  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>K: Miért nem sikerült az Azure AD-hez való csatlakozásra tett kísérletem, bár nem kaptam hibainformációt?

**A.** Ennek valószínű oka az, hogy a helyi beépített rendszergazdai fiókkal jelentkezett be az eszközre. Hozzon létre egy másik helyi fiókot, mielőtt az Azure Active Directory-csatlakozással befejezheti a telepítést. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>K:Milyen MS-Organization-P2P-Access tanúsítványok találhatók a Windows 10-es eszközeinken?

**A.** Az MS-Organization-P2P-Access tanúsítványokat az Azure AD mindkét, az Azure AD-hez csatlakozott és a hibrid Azure AD-hez csatlakozott eszközök számára. Ezek a tanúsítványok az ugyanazon a bérlőn lévő eszközök közötti megbízhatóság engedélyezésére szolgálnak a távoli asztali forgatókönyvek számára. Az egyik tanúsítványt az eszköz nek, a másikat pedig a felhasználónak adják ki. Az eszköztanúsítvány egy `Local Computer\Personal\Certificates` napig van jelen, és egy napig érvényes. Ez a tanúsítvány megújul (új tanúsítvány kiállításával), ha az eszköz még aktív az Azure AD-ben. A felhasználói tanúsítvány `Current User\Personal\Certificates` jelen van, és ez a tanúsítvány is érvényes egy napig, de igény szerint adják ki, amikor a felhasználó megpróbál egy távoli asztali munkamenet egy másik Azure AD-hez csatlakozott eszköz. A lejáratkor nem újul meg. Mindkét tanúsítványa a programban található MS-Organization-P2P-Access `Local Computer\AAD Token Issuer\Certificates`tanúsítvánnyal van kiadva. Ezt a tanúsítványt az Azure AD az eszköz regisztrációja során ad ki. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>K:Miért látom az MS-Organization-P2P-Access által kiállított több lejárt tanúsítványt a Windows 10-es eszközeinken? Hogyan törölhetem őket?

**A.** A Windows 10 1709-es és alacsonyabb verziójában problémát állapítottak meg, ahol a lejárt MS-Organization-P2P-Access tanúsítványok kriptográfiai problémák miatt továbbra is fennálltak a számítógéptárolóban. Ha olyan VPN-ügyfeleket (például Cisco AnyConnectet) használ, amelyek nem tudják kezelni a lejárt tanúsítványok nagy számát, problémákmerülhetnek fel a hálózati kapcsolattal kapcsolatban, ha olyan VPN-ügyfeleket (például Cisco AnyConnectet) használ. A Windows 10 1803-as kiadásában kijavították ezt a problémát az ilyen lejárt MS-Organization-P2P-Access tanúsítványok automatikus törléséhez. Ezt a problémát úgy oldhatja meg, hogy frissíti eszközeit a Windows 10 1803 rendszerre. Ha nem tudja frissíteni, ezeket a tanúsítványokat kedvezőtlen hatás nélkül törölheti.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hibrid Azure AD-csatlakozás – gyakori kérdések

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>K: Hogyan lehet leválasztva egy hibrid Azure AD-hez csatlakozott eszköz helyileg az eszközön?

**A.** Hibrid Azure AD-hez csatlakozott eszközök esetén győződjön meg arról, hogy kikapcsolja az automatikus regisztrációt. Ezután az ütemezett feladat nem regisztrálja újra az eszközt. Ezután nyisson meg egy parancssort rendszergazdaként, és írja be a entert. `dsregcmd.exe /debug /leave` Vagy futtassa ezt a parancsot parancsfájlként több eszközön, hogy tömegesen ne csatlakozzon.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>K: Hol találhatok hibaelhárítási információkat a hibrid Azure AD-csatlakozási hibák diagnosztizálására?

**A.** A hibaelhárításról az alábbi cikkekben talál:

- [Hibrid Azure Active Directory csatlakozása Windows 10 és Windows Server 2016-eszközök hibáiról](troubleshoot-hybrid-join-windows-current.md)
- [Hibrid Azure Active Directory-csatlakozás oka az alulról nem vett eszközökre](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>K: Miért jelenik meg egy duplikált Azure AD regisztrált rekord a Windows 10 hibrid Azure AD-hez csatlakozott eszköz az Azure AD-eszközök listájában?

**A.** Amikor a felhasználók hozzáadják a fiókjukat egy tartományhoz csatlakozó eszközön lévő alkalmazásokhoz, előfordulhat, hogy a rendszer a **Fiók hozzáadása a Windowshoz** lehetőséget kéri? Ha a parancssorba az **Igen** értéket adják meg, az eszköz regisztrálja magát az Azure AD-vel. A megbízhatósági típus azure AD regisztráltként van megjelölve. Miután engedélyezte a hibrid Azure AD-csatlakozást a szervezetben, az eszköz hibrid Azure AD-t is csatlakozik. Ezután két eszközállapot jelenik meg ugyanahhoz az eszközhöz. 

A hibrid Azure AD-csatlakozás elsőbbséget élvez az Azure AD regisztrált állapotával szemben. Így az eszköz hibrid Azure AD-hez csatlakozik minden hitelesítési és feltételes hozzáférés kiértékelése. Biztonságosan törölheti az Azure AD regisztrált eszközrekordot az Azure AD portálról. Ismerje meg, hogyan [kerülheti el vagy tisztítsa meg ezt a kettős állapotot a Windows 10 gépen.](hybrid-azuread-join-plan.md#review-things-you-should-know) 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>K: Miért vannak a felhasználók problémái a Windows 10 hibrid Azure AD-hez csatlakozott eszközök módosítása után az upn?

**A.** Jelenleg az UPN-módosítások nem teljes mértékben támogatottak a hibrid Azure AD-hez csatlakozó eszközökkel. Míg a felhasználók bejelentkezhetnek az eszközre, és hozzáférhetnek a helyszíni alkalmazásokhoz, az Azure AD-vel való hitelesítés sikertelen lesz egy upn-módosítás után. Ennek eredményeképpen a felhasználók sso és feltételes hozzáférés problémákat az eszközökön. Ebben az időben le kell választania az eszközt az Azure AD-ből (a "dsregcmd /leave" futtassa emelt szintű jogosultságokkal), és újra csatlakoznia kell (automatikusan történik) a probléma megoldásához. Jelenleg dolgozunk a probléma megoldásán. A Windows Hello vállalati verzióval bejelentkező felhasználók azonban nem szembesülnek ezzel a problémával. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>K: A Windows 10 hibrid Azure AD-hez csatlakozott eszközök megkövetelik a tartományvezérlő látásmódját a felhőalapú erőforrások eléréséhez?

**A.** Nem, kivéve, ha a felhasználó jelszavát módosítja. Miután a Windows 10 hibrid Azure AD-csatlakozás befejeződött, és a felhasználó legalább egyszer bejelentkezett, az eszköz nem igényel rálátással a tartományvezérlőhöz a felhőbeli erőforrások eléréséhez. A Windows 10 bárhonnan beszerezheti az Azure AD-alkalmazásokba az internetkapcsolattal rendelkező egyszeri bejelentkezést, kivéve, ha a jelszó módosítása történik. Azok a felhasználók, akik a Windows Hello for Business szolgáltatással jelentkeznek be, még a jelszó módosítása után is egyszeri bejelentkezést kapnak az Azure AD-alkalmazásokba, még akkor is, ha nem rendelkeznek a tartományvezérlőjükkel. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>K: Mi történik, ha egy felhasználó megváltoztatja a jelszavát, és megpróbál bejelentkezni a Windows 10 hibrid Azure AD-hez csatlakozott eszközére a vállalati hálózaton kívül?

**A.** Ha egy jelszót módosítanak a vállalati hálózaton kívül (például az Azure AD SSPR használatával), akkor a felhasználó bejelentkezésaz új jelszóval sikertelen lesz. A hibrid Azure AD-hez csatlakozó eszközök esetében a helyszíni Active Directory az elsődleges hatóság. Ha egy eszköz nem látja a tartományvezérlőt, nem tudja érvényesíteni az új jelszót. Ezért a felhasználónak kapcsolatot kell létesítenie a tartományvezérlővel (akár VPN-en keresztül, akár a vállalati hálózatban), mielőtt új jelszavával bejelentkezne az eszközre. Ellenkező esetben csak a régi jelszavukkal tudnak bejelentkezni a Windows gyorsítótárazott bejelentkezési funkciója miatt. Azonban a régi jelszót érvényteleníti az Azure AD a jogkivonat-kérelmek során, és így megakadályozza az egyszeri bejelentkezést, és nem sikerül az eszközalapú feltételes hozzáférési szabályzatok. Ez a probléma nem jelentkezik, ha a Windows Hello vállalati verziót használja. 

---

## <a name="azure-ad-register-faq"></a>Azure AD regiszter – gyakori kérdések

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>K: Hogyan távolíthatok el egy Azure AD regisztrált állapotot egy eszköz helyileg?

**V:** 
- Windows 10 Azure AD regisztrált eszközök esetén nyissa meg a **Beállítások** > **fiókok** > **hozzáférési munkahelyét vagy az iskolát.** Válassza ki a fiókját, és válassza a **Kapcsolat bontása**lehetőséget. Az eszközregisztráció felhasználói profilonként történik a Windows 10-ben.
- IOS és Android esetén használhatja a Microsoft Authenticator alkalmazás **beállításai** > **eszközregisztrációt,** és válassza **az Eszköz regisztrálása visszavonása**lehetőséget.
- MacOS esetén a Microsoft Intune Vállalati portál alkalmazással megszüntetheti az eszköz regisztrációját a felügyeletből, és eltávolíthatja a regisztrációkat. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>K: Hogyan tudom letiltani a felhasználókat abban, hogy további munkahelyi fiókokat adjanak hozzá (az Azure AD regisztrált) a vállalati Windows 10-es eszközeimen?

**A.** Engedélyezze a következő beállításjegyzéket, hogy a felhasználók megakadályozzák, hogy további munkahelyi fiókokat adjanak hozzá a vállalati tartományhoz csatlakozott, az Azure AD-hez csatlakozott, vagy a hibrid Azure AD csatlakozott a Windows 10-hez. Ez a szabályzat is használható, hogy letiltsa a tartományhoz csatlakozó gépek véletlenül az Azure AD regisztrálva ugyanazzal a felhasználói fiókkal. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>K: Regisztrálhatok Android- vagy iOS BYOD-eszközöket?

**A.** Igen, de csak az Azure-eszköz regisztrációs szolgáltatás és a hibrid ügyfelek számára. Ez nem támogatott a helyszíni eszköz regisztrációs szolgáltatás az Active Directory összevonási szolgáltatások (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>K: Hogyan regisztrálhatok egy macOS-eszközt?

**A.** Tegye a következő lépéseket:

1.    [Megfelelőségi házirend létrehozása](/intune/compliance-policy-create-mac-os)
1.    [Feltételes hozzáférési házirend definiálása macOS-eszközökhöz](../active-directory-conditional-access-azure-portal.md) 

**Megjegyzések:**

- A feltételes hozzáférésre vonatkozó házirendben szereplő felhasználóknak szükségük van [a macOS-hez készült Office támogatott verziójára](../conditional-access/concept-conditional-access-conditions.md) az erőforrások eléréséhez. 
- Az első hozzáférési kísérlet során a rendszer kéri a felhasználókat, hogy a vállalati portálon keresztül regisztrálják az eszközt.

---
## <a name="next-steps"></a>További lépések

- További információ az [Azure AD-ben regisztrált eszközökről](concept-azure-ad-register.md)
- További információ az [Azure AD-hez csatlakozó eszközökről](concept-azure-ad-join.md)
- További információ a [hibrid Azure AD-hez csatlakozó eszközökről](concept-azure-ad-join-hybrid.md)
