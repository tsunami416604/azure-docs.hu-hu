---
title: Eszközök kezelése az Azure Portal használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Portalon az eszközök kezelésére.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262242"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Eszközidentitások kezelése az Azure Portal használatával

Az Azure Active Directory (Azure AD) eszközidentitás-kezelésével biztosíthatja, hogy a felhasználók olyan eszközökről férjenek hozzá az erőforrásokhoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak.

A cikk tartalma:

- Feltételezi, hogy ismeri az [azure-identitáskezelés bevezetését](overview.md) az Azure Active Directoryban
- Tájékoztatást nyújt az eszközidentitások Azure AD portálon keresztül történő kezeléséről

## <a name="manage-device-identities"></a>Eszközidentitások kezelése

Az Azure AD portál központi helyet biztosít az eszközidentitások kezeléséhez. Ezt a helyet [közvetlen kapcsolattal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) vagy:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Tallózás az **Azure Active Directory-eszközök** > **Devices**között.

Az **Eszközök** lap lehetővé teszi a következőket:

- Az eszközbeállítások konfigurálása
- Eszközök megkeresése
- Eszközidentitás-kezelési feladatok végrehajtása
- Eszközalapú naplózási naplók áttekintése  
  
## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszközidentitások kezeléséhez az Azure AD portálhasználatával, az eszközök regisztrálni kell, [vagy csatlakozott](overview.md) az Azure AD-hez. Rendszergazdaként az eszközbeállítások konfigurálásával finomíthatja az eszközök regisztrálásának és csatlakoztatásának folyamatát.

Az eszközbeállítások lap lehetővé teszi az eszközidentitásokkal kapcsolatos beállítások konfigurálását:

![Intune-eszköz kezelése](./media/device-management-azure-portal/21.png)

- **A felhasználók csatlakozhatnak az Azure AD-hez** – Ez a beállítás lehetővé teszi, hogy kiválassza azokat a felhasználókat, akik regisztrálhatják eszközeiket Azure AD-hez csatlakozott eszközökként. Az alapértelmezett érték az **Összes**.

> [!NOTE]
> **A felhasználók csatlakozhatnak az Azure AD-beállításhoz,** csak a Windows 10-en az Azure AD-csatlakozásra vonatkozik.

- **További helyi rendszergazdák az Azure AD-hez csatlakozott eszközök** – Kiválaszthatja a felhasználók, amelyek helyi rendszergazdai jogosultságokat kapnak az eszközön. Az itt hozzáadott felhasználók hozzáadódnak az *Azure AD-ben* az Eszközrendszergazdák szerepkörhöz. Az Azure AD globális rendszergazdái és az eszköztulajdonosok alapértelmezés szerint helyi rendszergazdai jogokat kapnak. Ez a beállítás egy prémium kiadású funkció, amely olyan termékeken keresztül érhető el, mint az Azure AD Premium vagy az Enterprise Mobility Suite (EMS).
- **A felhasználók regisztrálhatják eszközeiket az Azure AD-vel** – Ezt a beállítást úgy kell konfigurálnia, hogy a Windows 10 személyes, iOS, Android és macOs-eszközök regisztrálhatók legyenek az Azure AD-ben. Ha a Nincs lehetőséget **választja,** az eszközök nem regisztrálhatnak az Azure AD-vel. Az Office 365-höz a Microsoft Intune-ban vagy a Mobileszköz-kezelésben (MDM) való regisztrációhoz regisztráció szükséges. Ha a szolgáltatások valamelyikét konfigurálta, az **ALL** beállítás be van jelölve, és a **NONE nincs** elérhető.
- **Többtényezős hitelesítés szükséges eszközök csatlakoztatása** – Kiválaszthatja, hogy a felhasználóknak kell-e további hitelesítési tényezőt biztosítaniuk ahhoz, hogy csatlakozzanak az eszközükhöz az Azure AD-hez. Az alapértelmezett érték a **Nem**. Azt javasoljuk, hogy az eszköz regisztrálásakor többtényezős hitelesítést igényeljen. Mielőtt engedélyezne többtényezős hitelesítést ehhez a szolgáltatáshoz, gondoskodnia kell arról, hogy a többtényezős hitelesítés konfigurálva legyen az eszközeiket regisztráló felhasználók számára. A különböző Azure többtényezős hitelesítési szolgáltatásokról az [Azure többtényezős hitelesítésének első lépései](../authentication/concept-mfa-whichversion.md)című témakörben talál további információt. 

> [!NOTE]
> **A Multi-Factor Auth csatlakoztatása az eszközökhez** beállítás olyan eszközökre vonatkozik, amelyek vagy az Azure AD-hez csatlakoztak, vagy az Azure AD regisztrált. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakozó eszközök.

- **Eszközök maximális száma** – Ez a beállítás lehetővé teszi, hogy válassza ki a maximális számú Azure AD-hez csatlakozott vagy az Azure AD regisztrált eszközök, amelyek a felhasználó rendelkezhet az Azure AD-ben. Ha egy felhasználó eléri ezt a kvótát, nem tud további eszközöket hozzáadni, amíg egy vagy több meglévő eszközt el nem távolít. Az alapértelmezett érték **20**.

> [!NOTE]
> **Az eszközök** maximális beállítása az Okra vonatkozik, amelyek vagy az Azure AD-hez csatlakoznak, vagy az Azure AD regisztrált. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakozó eszközök.

- **A felhasználók szinkronizálhatják a beállításokat és az alkalmazásadatokat az eszközök között** – Alapértelmezés szerint ez a beállítás **NINCS**. Adott felhasználók vagy csoportok vagy ALL kiválasztása lehetővé teszi, hogy a felhasználó beállításai és alkalmazásadatai szinkronizálódjanak a Windows 10-es eszközeiken. További információ a szinkronizálás windows 10-es működéséről.
Ez a beállítás olyan prémium szintű képesség, amely olyan termékeken keresztül érhető el, mint az Azure AD Premium vagy az Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Eszközök megkeresése

A regisztrált és az összekapcsolt eszközök megkeresésére két lehetőség közül választhat:

- **Az** **Eszközök** lap **Kezelés** szakaszában található összes eszköz  
- **Eszközök** a **Felhasználó** lap **Kezelés** szakaszában

Mindkét lehetőséggel olyan nézetet érhet el, amely:

- Lehetővé teszi a megjelenítendő név vagy az eszközazonosító szűrőként történő keresését.
- Részletes áttekintést nyújt a regisztrált és összekapcsolt eszközökről
- Lehetővé teszi a gyakori eszközkezelési feladatok elvégzését

![Minden eszköz](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Ha egy eszköz, amely "Hibrid Azure AD csatlakozott" állapotú "Függőben" a REGISZTRÁLT oszlopban, azt jelzi, hogy az eszköz szinkronizálva lett az Azure AD connect, és arra vár, hogy a regisztráció befejezéséhez az ügyféltől. További információ [a hibrid Azure AD-csatlakozási megvalósítás megtervezéséről.](hybrid-azuread-join-plan.md) További információ a cikkben található, [Eszközök gyakran ismételt kérdések](faq.md).
>
>   ![Függőben lévő eszközök](./media/device-management-azure-portal/75.png)
>
>* Egyes iOS-eszközök esetében az aposztrófokat tartalmazó eszköznevek különböző karaktereket használhatnak, amelyek aposztrófnak tűnnek. Tehát az ilyen eszközök keresése egy kicsit trükkös - ha nem látja a keresési eredményeket helyesen, győződjön meg arról, hogy a keresési karakterlánc megfelelő aposztróf karaktert tartalmaz.

## <a name="device-identity-management-tasks"></a>Eszközidentitás-kezelési feladatok

Globális rendszergazdaként vagy felhőbeli eszközadminisztrátorként kezelheti a regisztrált vagy összekapcsolt eszközöket. Az Intune-szolgáltatás rendszergazdái:

- Eszközök frissítése – Ilyenek például a napi műveletek, például az eszközök engedélyezése/letiltása
- Eszközök törlése – Ha egy eszköz kivan vonva, és törölni kell az Azure AD-ben

Ez a szakasz az eszközidentitás-kezelési gyakori feladatokról nyújt tájékoztatást.

### <a name="manage-an-intune-device"></a>Intune-eszköz kezelése

Ha Ön Intune-rendszergazda, kezelheti a **Microsoft Intune**néven megjelölt eszközöket. Ha az eszköz nincs bejelentve a Microsoft Intune-ban, a "Kezelés" lehetőség szürkén jelenik meg.

![Intune-eszköz kezelése](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Azure AD-eszköz engedélyezése/letiltása

Az eszköz engedélyezéséhez / letiltásához két lehetősége van:

- A Feladatok menü ("...") a **Minden eszköz** lapon

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/71.png)

- Az Eszközök lap **eszköztára**

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/32.png)

**Megjegyzések:**

- Az eszköz engedélyezéséhez/ letiltásához globális rendszergazdának vagy felhőalapú eszközrendszergazdának kell lennie az Azure AD-ben. 
- Az eszköz letiltása megakadályozza, hogy egy eszköz sikeresen hitelesítse az Azure AD-vel, így megakadályozza, hogy az eszköz hozzáférjen az Azure AD-erőforrásokhoz, amelyeket az eszköz hitelesítésszolgáltatója vagy a WH4B hitelesítő adatai használ.
- Az eszköz letiltása az elsődleges frissítési jogkivonatot (PRT) és az eszközön lévő frissítési jogkivonatokat (RT) is visszavonja.

### <a name="delete-an-azure-ad-device"></a>Azure AD-eszköz törlése

Eszköz törléséhez két lehetősége van:

- A Feladatok menü ("...") a **Minden eszköz** lapon

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/72.png)

- Az Eszközök lap **eszköztára**

   ![Eszköz törlése](./media/device-management-azure-portal/34.png)

**Megjegyzések:**

- Az eszköz törléséhez globális rendszergazdának vagy Intune-rendszergazdának kell lennie az Azure AD-ben.
- Eszköz törlése:
   - Megakadályozza, hogy egy eszköz hozzáférjen az Azure AD-erőforrásokhoz.
   - Eltávolítja az eszközhöz csatlakoztatott összes részletet, például a Windows-eszközök BitLocker-kulcsait.  
   - Nem helyreállítható tevékenységet jelöl, és csak akkor ajánlott, ha szükséges.

Ha egy eszközt egy másik felügyeleti hatóság (például a Microsoft Intune) kezel, győződjön meg arról, hogy az eszközt törölték / kivonták, mielőtt törölte az eszközt az Azure AD-ben. Tekintse át, hogyan [kezelheti az elavult eszközöket](device-management-azure-portal.md) az eszközök törlése előtt.

### <a name="view-or-copy-device-id"></a>Eszközazonosító megtekintése vagy másolása

Az eszközazonosító segítségével ellenőrizheti az eszközazonosító adatait az eszközön, vagy a PowerShell használatával a hibaelhárítás során. A másolási lehetőség eléréséhez kattintson az eszközre.

![Eszközazonosító megtekintése](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése vagy másolása

A BitLocker-kulcsok at megtekintheti és másolhatja, hogy a felhasználók helyreállítsák titkosított meghajtójukat. Ezek a kulcsok csak olyan Windows-eszközökön érhetők el, amelyek titkosítottak, és kulcsaikat az Azure AD-ben tárolják. Ezeket a kulcsokat az eszköz részleteinek elérésekor másolhatja.

![BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/36.png)

A BitLocker-kulcsok megtekintéséhez vagy másolásához az eszköz tulajdonosának, vagy olyan felhasználónak kell lennie, akinek legalább az alábbi szerepkörök valamelyike van hozzárendelve:

- Felhőeszköz-rendszergazda
- Globális rendszergazda
- Ügyfélszolgálati rendszergazda
- Intune szolgáltatás rendszergazdája
- Biztonsági rendszergazda
- Biztonsági olvasó

> [!NOTE]
> Hibrid Azure AD csatlakozott Windows 10-eszközök nem rendelkeznek tulajdonossal. Tehát, ha tulajdonosszerint keres egy eszközt, és nem találta meg, keressen az eszköz azonosítója szerint.

## <a name="audit-logs"></a>Naplók

Az eszköztevékenységek a tevékenységnaplókon keresztül érhetők el. Ezek a naplók tartalmazzák az eszközregisztrációs szolgáltatás és a felhasználók által aktivált tevékenységeket:

- Eszköz létrehozása és hozzáadása tulajdonosok / felhasználók a készüléken
- Az eszköz beállítások módosítása
- Eszközműveletek, például eszköz törlése vagy frissítése

A naplózási adatok belépési pontja a **naplózási naplók** az Eszközök lap **Tevékenység** **szakaszában.**

A napló alapértelmezett listanézete a következőket mutatja:

- Az előfordulás dátuma és időpontja
- A célok
- Egy tevékenység kezdeményezője / színésze (aki)
- A tevékenység (mi)

![Naplók](./media/device-management-azure-portal/63.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/device-management-azure-portal/64.png)

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Kategória
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Cél
- Kezdeményezte (aktor)

A szűrőkön kívül konkrét bejegyzéseket is kereshet.

![Naplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>További lépések

[Az elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
