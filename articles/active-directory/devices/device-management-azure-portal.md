---
title: Eszközök kezelése a Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az eszközöket a Azure Portal használatával.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf3f6455be992502182fb942f0e6db089051ab1a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253154"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Az eszközök identitásának kezelése a Azure Portal használatával

Az Azure Active Directory (Azure AD) eszköz-identitások kezelésével biztosíthatja, hogy a felhasználók hozzáférhessenek az erőforrásokhoz a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről.

A cikk tartalma:

- Feltételezi, hogy ismeri a [Azure Active Directory eszköz-identitások kezelésének bevezetését](overview.md)
- Információt nyújt az eszköz identitásának az Azure AD-portál használatával történő kezeléséről

![A Azure Portal összes eszköz nézete](./media/device-management-azure-portal/all-devices-azure-portal.png)

## <a name="manage-device-identities"></a>Eszközidentitások kezelése

Az Azure AD-portál központi helyet biztosít az eszköz identitásának kezeléséhez. Ezt a helyet [közvetlen hivatkozással](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) vagy a következőkkel érheti el:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory**  >  **eszközöket**.

Az **eszközök** lap a következőket teszi lehetővé:

- Az eszközbeállítások konfigurálása
- Eszközök megkeresése
- Eszközállapot-kezelési feladatok végrehajtása
- Az eszközhöz kapcsolódó naplók áttekintése  
  
## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszköz identitásának Azure AD-portálon való kezeléséhez az eszközöknek [regisztrálva](overview.md) kell lenniük, vagy hozzá kell csatlakozniuk az Azure ad-hez. Rendszergazdaként az eszközbeállítások konfigurálásával részletesen beállíthatja az eszközök regisztrálásának és csatlakoztatásának folyamatát.

Az eszközbeállítások lapon konfigurálhatja az eszköz identitásával kapcsolatos beállításokat:

![Az Azure AD-hez kapcsolódó eszközbeállítások](./media/device-management-azure-portal/device-settings-azure-portal.png)

- A **felhasználók csatlakozhatnak az eszközökhöz az Azure ad** -ben – ezzel a beállítással kiválaszthatja azokat a felhasználókat, akik regisztrálhatják az eszközeiket az Azure ad-hez csatlakoztatott eszközökként. Az alapértelmezett érték az **összes**.

> [!NOTE]
> Előfordulhat, hogy a **felhasználók az Azure ad-be való csatlakoztatáshoz** csak az Azure ad-csatlakozásra vonatkoznak a Windows 10 rendszeren.

- **További helyi rendszergazdák az Azure ad-hez csatlakoztatott eszközökön** : kiválaszthatja azokat a felhasználókat, akik helyi rendszergazdai jogosultságokat kapnak az eszközön. Az itt hozzáadott felhasználók az Azure AD-ben az *eszköz-rendszergazdák* szerepkörbe kerülnek. A globális rendszergazdák az Azure AD-ben és az eszközök tulajdonosai alapértelmezés szerint helyi rendszergazdai jogosultságokat kapnak. Ez a lehetőség egy prémium kiadású funkció, amely olyan termékeken keresztül érhető el, mint a prémium szintű Azure AD vagy a nagyvállalati mobilitási csomag (EMS).
- A **felhasználók regisztrálhatják eszközeiket az Azure ad** -ben – ezt a beállítást úgy kell konfigurálni, hogy engedélyezze a Windows 10 személyes, iOS-, Android-és MacOS-eszközök regisztrálását az Azure ad-ben. Ha a **nincs**lehetőséget választja, az eszközök nem regisztrálhatnak az Azure ad-ben. Az Office 365-hez készült Microsoft Intune vagy mobileszköz-felügyelettel (MDM) való regisztrációhoz regisztráció szükséges. Ha ezen szolgáltatások valamelyikét konfigurálta, akkor az **összes** kiválasztva, a **none** érték nem érhető el.
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
- A **felhasználói** oldal **kezelés** szakaszában lévő **eszközök**

Mindkét lehetőséggel megtekintheti a következőket:

- Lehetővé teszi az eszközök keresését a megjelenítendő név vagy az eszköz azonosítója alapján szűrőként.
- Részletes áttekintést nyújt a regisztrált és csatlakoztatott eszközökről
- Lehetővé teszi az eszközök általános felügyeleti feladatainak elvégzését

>[!TIP]
>
>* Ha megjelenik egy "hibrid Azure AD-hez csatlakoztatott" állapotú eszköz, amely a regisztrált oszlopban a "függőben" állapotban van, az azt jelzi, hogy az eszköz szinkronizálva lett az Azure AD-kapcsolattal, és az ügyféltől való regisztráció befejezésére vár. További információ a [hibrid Azure ad-csatlakozás megvalósításának megtervezéséről](hybrid-azuread-join-plan.md). További információt a cikkben, az [eszközök gyakori kérdéseiben](faq.md)találhat.
>
>* Egyes iOS-eszközök esetében az aposztrófokat tartalmazó eszközök nevei különböző karaktereket használhatnak, amelyek hasonlóak az aposztrófokhoz. Így az ilyen eszközök keresése kicsit trükkös – ha nem látja megfelelően a keresési eredményeket, ügyeljen arra, hogy a keresési karakterlánc a megfelelő aposztróf-karaktert tartalmazza.

## <a name="device-identity-management-tasks"></a>Eszközállapot-kezelési feladatok

Globális rendszergazdaként vagy felhőalapú eszköz-rendszergazdaként felügyelheti a regisztrált vagy csatlakoztatott eszközöket. Az Intune szolgáltatás-rendszergazdák a következőket tehetik:

- Eszközök frissítése – a példák olyan napi műveletek, mint például az eszközök engedélyezése/letiltása
- Eszközök törlése – az eszköz kivonása és az Azure AD-ben való törlése

Ez a szakasz az eszköz általános identitás-kezelési feladataival kapcsolatos információkat tartalmaz.

### <a name="manage-an-intune-device"></a>Intune-eszköz kezelése

Ha Ön Intune-rendszergazda, akkor a **Microsoft Intuneként**megjelölt eszközöket kezelheti. Ha az eszköz nincs regisztrálva Microsoft Intune, a "kezelés" beállítás szürkén jelenik meg.

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

Ha egy eszközt egy másik felügyeleti hatóság felügyel (például Microsoft Intune), győződjön meg róla, hogy az eszköz törölve lett vagy ki lett vonva, mielőtt törölné az eszközt az Azure AD-ben. Tekintse át, hogyan [kezelheti az elavult eszközöket](manage-stale-devices.md) az eszközök törlése előtt.

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

### <a name="device-list-filtering-preview"></a>Eszközök listájának szűrése (előzetes verzió)

Korábban csak tevékenység és engedélyezett állapot alapján szűrheti az eszközök listáját. Ez az előzetes verzió lehetővé teszi az eszközök listájának szűrését az eszköz következő attribútumai alapján:

- Engedélyezett állapot
- Megfelelő állapot
- Csatlakozás típusa (Azure AD-hez csatlakoztatott, hibrid Azure AD-hez csatlakoztatott, Azure AD-regisztráció)
- Tevékenység-időbélyeg
- Operációs rendszer
- Eszköz típusa (nyomtatók, biztonságos virtuális gépek, megosztott eszközök, regisztrált eszközök)

Az előnézet szűrési funkciójának engedélyezése a **minden eszköz** nézetben:

![A szűrési Előnézet funkció engedélyezése](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory**  >  **eszközöket**.
1. Válassza ki a szalagcímet, amely szerint **kipróbálhatja az új eszközök szűrésének újdonságait. Ide kattintva engedélyezheti az előzetes verziót.**

Mostantól lehetősége lesz **szűrők hozzáadására** a **minden eszköz** nézethez.

## <a name="audit-logs"></a>Auditnaplók

Az eszközök tevékenységeit a tevékenység naplóiban érheti el. Ezek a naplók az eszköz regisztrációs szolgáltatása és a felhasználók által aktivált tevékenységeket foglalják magukban:

- Eszközök létrehozása és tulajdonosok/felhasználók hozzáadása az eszközhöz
- Eszközbeállítások módosítása
- Eszközök műveletei, például az eszközök törlése vagy frissítése

A naplózási adathoz való belépési pont az **eszközök** lap **tevékenység** szakaszában található **naplók** .

A napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az előfordulás dátuma és időpontja
- A célok
- Egy tevékenység kezdeményezője/szereplője
- A tevékenység (mi)

![Auditnaplók](./media/device-management-azure-portal/63.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Auditnaplók](./media/device-management-azure-portal/64.png)

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Kategória
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Cél
- Kezdeményező (színész)

A szűrők mellett megkeresheti az adott bejegyzéseket is.

![Auditnaplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>További lépések

[Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
