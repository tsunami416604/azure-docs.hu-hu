---
title: Eszközök kezelése a Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az eszközöket a Azure Portal használatával.
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
ms.openlocfilehash: b1b44a7df499dc0b6ce4370bf74ece35c99a4493
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200238"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Az eszközök identitásának kezelése a Azure Portal használatával

Az Azure Active Directory (Azure AD) eszköz-identitások kezelésével biztosíthatja, hogy a felhasználók hozzáférhessenek az erőforrásokhoz a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről.

A cikk tartalma:

- Feltételezi, hogy ismeri a [Azure Active Directory eszköz-identitások kezelésének bevezetését](overview.md)
- Információt nyújt az eszköz identitásának az Azure AD-portál használatával történő kezeléséről

## <a name="manage-device-identities"></a>Eszközidentitások kezelése

Az Azure AD-portál központi helyet biztosít az eszköz identitásának kezeléséhez. Ezt a helyet a [közvetlen hivatkozás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) használatával vagy a manuális lépések követésével érheti el:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
2. A bal oldali navigációs sávon kattintson a **Active Directory**elemre.

   ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/01.png)

3. A **kezelés** szakaszban kattintson az **eszközök**elemre.

   ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/74.png)

Az **eszközök** lap a következőket teszi lehetővé:

- Az eszközbeállítások konfigurálása
- Eszközök megkeresése
- Eszközállapot-kezelési feladatok végrehajtása
- Az eszközhöz kapcsolódó naplók áttekintése  
  
## <a name="configure-device-settings"></a>Eszközbeállítások konfigurálása

Az eszköz identitásának Azure AD-portálon való kezeléséhez az eszközöknek [regisztrálva](overview.md) kell lenniük, vagy hozzá kell csatlakozniuk az Azure ad-hez. Rendszergazdaként az eszközbeállítások konfigurálásával részletesen beállíthatja az eszközök regisztrálásának és csatlakoztatásának folyamatát.

![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/22.png)

Az eszközbeállítások oldalon a következőket konfigurálhatja:

![Intune-eszköz kezelése](./media/device-management-azure-portal/21.png)

- A **felhasználók csatlakozhatnak az eszközökhöz az Azure ad** -ben – ezzel a beállítással kiválaszthatja azokat a felhasználókat, akik regisztrálhatják az eszközeiket az Azure ad-hez csatlakoztatott eszközökként. Az alapértelmezett érték az **összes**.

> [!NOTE]
> Előfordulhat, hogy a **felhasználók az Azure ad-be való csatlakoztatáshoz** csak az Azure ad-csatlakozásra vonatkoznak a Windows 10 rendszeren.

- **További helyi rendszergazdák az Azure ad-hez csatlakoztatott eszközökön** : kiválaszthatja azokat a felhasználókat, akik helyi rendszergazdai jogosultságokat kapnak az eszközön. Az itt hozzáadott felhasználók az Azure AD-ben az *eszköz-rendszergazdák* szerepkörbe kerülnek. A globális rendszergazdák az Azure AD-ben és az eszközök tulajdonosai alapértelmezés szerint helyi rendszergazdai jogosultságokat kapnak. Ez a lehetőség egy prémium kiadású funkció, amely olyan termékeken keresztül érhető el, mint a prémium szintű Azure AD vagy a nagyvállalati mobilitási csomag (EMS).
- A **felhasználók regisztrálhatják eszközeiket az Azure ad** -ben – ezt a beállítást úgy kell konfigurálni, hogy engedélyezze a Windows 10 személyes, iOS-, Android-és macOs-eszközök regisztrálását az Azure ad-ben. Ha a **nincs**lehetőséget választja, az eszközök nem regisztrálhatnak az Azure ad-ben. Az Office 365-hez készült Microsoft Intune vagy mobileszköz-felügyelettel (MDM) való regisztrációhoz regisztráció szükséges. Ha ezen szolgáltatások valamelyikét konfigurálta, akkor az **összes** kiválasztva, a **none** érték nem érhető el.
- **Többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához** – megadhatja, hogy a felhasználóknak további hitelesítési tényezőt kell-e biztosítaniuk az eszközük Azure ad-hez való csatlakoztatásához. Az alapértelmezett érték a **nem**. Az eszközök regisztrálásakor javasoljuk a többtényezős hitelesítés megkövetelését. Mielőtt engedélyezi a többtényezős hitelesítést a szolgáltatáshoz, meg kell győződnie arról, hogy a többtényezős hitelesítés konfigurálva van az eszközeit regisztráló felhasználók számára. A különböző Azure multi-Factor Authentication-szolgáltatásokkal kapcsolatos további információkért lásd: [Az Azure multi-Factor Authentication első lépései](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> A **többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához** beállítás az Azure ad-hez csatlakoztatott vagy az Azure ad által regisztrált eszközökre vonatkozik. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakoztatott eszközökre.

- **Eszközök maximális száma** – ez a beállítás lehetővé teszi, hogy kiválassza az Azure ad-hez csatlakoztatott vagy az Azure ad-ban regisztrált eszközök maximális számát, amelyet a felhasználók az Azure ad-ben használhatnak. Ha a felhasználó eléri ezt a kvótát, nem tud további eszközöket hozzáadni, amíg a meglévő eszközök közül egy vagy több el nem távolítva. Az alapértelmezett érték **20**.

> [!NOTE]
> **Az eszközök maximális száma** beállítás az Azure ad-hez csatlakoztatott vagy az Azure ad-ban regisztrált eszközökre vonatkozik. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakoztatott eszközökre.

- A **felhasználók a beállításokat és az alkalmazásadatok közötti szinkronizálást is megadhatják** – alapértelmezés szerint ez a beállítás a **none**értékre van állítva. Adott felhasználók vagy csoportok kiválasztása esetén a felhasználó beállításai és az alkalmazásadatok szinkronizálása a Windows 10-es eszközökön keresztül végezhető el. További információ a szinkronizálás működéséről a Windows 10 rendszerben.
Ez a lehetőség olyan prémium szintű funkció, amely a prémium szintű Azure AD vagy a nagyvállalati mobilitási csomag (EMS) segítségével érhető el.

## <a name="locate-devices"></a>Eszközök megkeresése

A regisztrált és a csatlakoztatott eszközök keresésére két lehetőség áll rendelkezésre:

- Az **eszközök** lap **kezelés** szakaszában található **összes eszköz**  

   ![Minden eszköz](./media/device-management-azure-portal/41.png)

- A **felhasználói** oldal **kezelés** szakaszában lévő **eszközök**

   ![Minden eszköz](./media/device-management-azure-portal/43.png)

Mindkét lehetőséggel megtekintheti a következőket:

- Lehetővé teszi az eszközök keresését a megjelenítendő név vagy az eszköz azonosítója alapján szűrőként.
- Részletes áttekintést nyújt a regisztrált és csatlakoztatott eszközökről
- Lehetővé teszi az eszközök általános felügyeleti feladatainak elvégzését

![Minden eszköz](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Ha megjelenik egy "hibrid Azure AD-hez csatlakoztatott" állapotú eszköz, amely a regisztrált oszlopban a "függőben" állapotban van, az azt jelzi, hogy az eszköz szinkronizálva lett az Azure AD-kapcsolattal, és az ügyféltől való regisztráció befejezésére vár. További információ a [hibrid Azure ad-csatlakozás megvalósításának megtervezéséről](hybrid-azuread-join-plan.md). További információt a cikkben, az [eszközök gyakori kérdéseiben](faq.md)találhat.
>
>   ![Függőben lévő eszközök](./media/device-management-azure-portal/75.png)
>
>* Egyes iOS-eszközök esetében az aposztrófokat tartalmazó eszközök nevei különböző karaktereket használhatnak, amelyek hasonlóak az aposztrófokhoz. Így az ilyen eszközök keresése kicsit trükkös – ha nem látja megfelelően a keresési eredményeket, ügyeljen arra, hogy a keresési karakterlánc a megfelelő aposztróf-karaktert tartalmazza.

## <a name="device-identity-management-tasks"></a>Eszközállapot-kezelési feladatok

Globális rendszergazdaként vagy felhőalapú eszköz-rendszergazdaként felügyelheti a regisztrált vagy csatlakoztatott eszközöket. Az Intune szolgáltatás-rendszergazdák a következőket tehetik:

- Eszközök frissítése – a példák olyan napi műveletek, mint például az eszközök engedélyezése/letiltása
- Eszközök törlése – ha egy eszköz ki van vonva, és az Azure AD-ben kell törölni

Ez a szakasz az eszköz általános identitás-kezelési feladataival kapcsolatos információkat tartalmaz.

### <a name="manage-an-intune-device"></a>Intune-eszköz kezelése

Ha Ön Intune-rendszergazda, akkor a **Microsoft Intuneként**megjelölt eszközöket kezelheti. Ha az eszköz nincs regisztrálva Microsoft Intune a "kezelés" beállítás szürkén jelenik meg.

![Intune-eszköz kezelése](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Azure AD-eszköz engedélyezése/letiltása

Az eszközök engedélyezéséhez vagy letiltásához két lehetőség közül választhat:

- A feladatok menü ("...") a **minden eszköz** lapon

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/71.png)

- Az **eszközök** lapon lévő eszköztár

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/32.png)

**Megjegyzéseket tartalmazó**

- Az eszközök engedélyezéséhez vagy letiltásához globális rendszergazdai vagy felhőalapú eszköz-rendszergazdának kell lennie az Azure AD-ben. 
- Az eszköz letiltásával megakadályozható, hogy az eszköz sikeresen hitelesítse az Azure AD-t, így megakadályozva, hogy az eszköz hozzáférjen az eszköz HITELESÍTÉSSZOLGÁLTATÓja által védett Azure AD-erőforrásokhoz vagy a WH4B hitelesítő adataihoz.
- Ha letiltja az eszközt, az az elsődleges frissítési tokent (PRT) és a frissítési tokeneket (RT) is visszavonja az eszközön.

### <a name="delete-an-azure-ad-device"></a>Azure AD-eszköz törlése

Egy eszköz törléséhez két lehetőség közül választhat:

- A feladatok menü ("...") a **minden eszköz** lapon

   ![Intune-eszköz kezelése](./media/device-management-azure-portal/72.png)

- Az **eszközök** lapon lévő eszköztár

   ![Eszköz törlése](./media/device-management-azure-portal/34.png)

**Megjegyzéseket tartalmazó**

- Egy eszköz törléséhez globális rendszergazdának vagy Intune-rendszergazdának kell lennie az Azure AD-ben.
- Eszköz törlése:
   - Megakadályozza, hogy az eszköz hozzáférjen az Azure AD-erőforrásokhoz.
   - Eltávolítja az eszközhöz csatolt összes adatot, például a BitLocker-kulcsokat a Windows-eszközökhöz.  
   - Nem helyreállítható tevékenységet jelöl, és nem ajánlott, ha szükséges.

Ha egy eszközt egy másik felügyeleti hatóság felügyel (például Microsoft Intune), győződjön meg róla, hogy az eszköz törölve lett vagy ki lett vonva, mielőtt törölné az eszközt az Azure AD-ben. Tekintse át, hogyan [kezelheti az elavult eszközöket](device-management-azure-portal.md) az eszközök törlése előtt.

### <a name="view-or-copy-device-id"></a>Eszköz AZONOSÍTÓjának megtekintése vagy másolása

Az eszköz AZONOSÍTÓjának használatával ellenőrizheti az eszköz AZONOSÍTÓjának adatait, vagy a PowerShell használatával végezheti el a hibaelhárítást. A másolási lehetőség eléréséhez kattintson az eszközre.

![Eszköz AZONOSÍTÓjának megtekintése](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése vagy másolása

Megtekintheti és átmásolhatja a BitLocker-kulcsokat, hogy a felhasználók helyreállítsák a titkosított meghajtókat. Ezek a kulcsok csak a titkosított Windows-eszközökön érhetők el, és a kulcsaikat az Azure AD-ben tárolják. Ezeket a kulcsokat az eszköz adataihoz való hozzáféréskor is másolhatja.

![BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/36.png)

A BitLocker-kulcsok megtekintéséhez vagy másolásához az eszköz tulajdonosának kell lennie, vagy egy olyan felhasználónak, amely legalább az alábbi szerepkörök valamelyikével rendelkezik:

- Felhőalapú eszköz rendszergazdája
- Globális rendszergazda
- Segélyszolgálat rendszergazdája
- Intune szolgáltatás rendszergazdája
- Biztonsági rendszergazda
- Biztonsági olvasó

> [!NOTE]
> A hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökhöz nem tartozik tulajdonos. Így ha az eszközt tulajdonos szerint keresi, és nem találta meg, akkor keresse meg az eszköz AZONOSÍTÓját.

## <a name="audit-logs"></a>Naplók

Az eszközök tevékenységeit a tevékenység naplóiban érheti el. Ezek a naplók az eszköz regisztrációs szolgáltatása és a felhasználók által aktivált tevékenységeket foglalják magukban:

- Eszközök létrehozása és tulajdonosok/felhasználók hozzáadása az eszközhöz
- Eszközbeállítások módosítása
- Eszközök műveletei, például az eszközök törlése vagy frissítése

A naplózási adathoz való belépési pont az **eszközök** lap **tevékenység** szakaszában található **naplók** .

![Naplók](./media/device-management-azure-portal/61.png)

Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- Az előfordulás dátuma és időpontja
- A célok
- Egy tevékenység kezdeményezője/szereplője
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
- Kezdeményező (színész)

A szűrők mellett megkeresheti az adott bejegyzéseket is.

![Naplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Következő lépések

[Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
