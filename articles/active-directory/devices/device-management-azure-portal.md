---
title: Eszközkezelés az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használja az Azure portal-eszközök kezeléséhez.
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
ms.openlocfilehash: a4a0037d46db67460d507c6e92ab550f7d9c2fbe
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341418"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Az Azure portal használatával eszközidentitások kezelése

Az eszköz identity management az Azure Active Directoryban (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak.

A cikk tartalma:

- Feltételezi, hogy ismeri a [eszköz Identitáskezelés az Azure Active Directoryban bemutatása](overview.md)
- Információt a az Azure AD Portalon eszközidentitások kezelése

## <a name="manage-device-identities"></a>Eszközidentitások kezelése

Az Azure AD-portálon az eszközidentitások kezelése központi helyet biztosít. Ennek a helynek a bármelyik használatával kérheti egy [közvetlen hivatkozás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) vagy a következő manuális lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) rendszergazdaként.
2. A bal oldali navigációs sávon kattintson **Active Directory**.

   ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/01.png)

3. Az a **kezelés** területén kattintson **eszközök**.

   ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/74.png)

A **eszközök** lap lehetővé teszi, hogy:

- Az eszközök beállításainak konfigurálása
- Eszközök megkeresése
- Eszköz identitása felügyeleti feladatok végrehajtása
- Eszközzel kapcsolatos auditnaplók áttekintéséhez  
  
## <a name="configure-device-settings"></a>Eszközbeállítások konfigurálása

Az az Azure AD Portalon eszközidentitások kezelése, az eszközök kell lennie vagy [regisztrált vagy azzal összekapcsolt](overview.md) az Azure ad-hez. A rendszergazdák finomhangolhatja regisztrálása és az eszközök csatlakoztatása az eszköz beállításait konfigurálja.

![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/22.png)

Az eszköz beállításai lapon konfigurálását teszi lehetővé:

![Az Intune-eszközök kezelése](./media/device-management-azure-portal/21.png)

- **Felhasználók eszközöket csatlakoztathatnak az Azure AD** – Ez a beállítás lehetővé teszi a felhasználók, akik regisztrálhatják eszközeiket, [Azure AD join eszközök](overview.md#azure-ad-joined-devices). Az alapértelmezett érték **összes**.

> [!NOTE]
> **Felhasználók eszközöket csatlakoztathatnak az Azure AD** beállítás csak akkor érvényes, az Azure AD join a Windows 10-es.

- **További helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök** – kiválaszthatja, hogy az adott eszközön helyi rendszergazdai jogosultságokkal rendelkező felhasználók. Az itt hozzáadott felhasználók hozzáadódnak a *Device Administrators* az Azure AD-szerepkörhöz. Az Azure AD globális rendszergazdák és eszköztulajdonosok alapértelmezés szerint helyi rendszergazdai jogokat kapnak. Ez a beállítás akkor például az Azure AD prémium vagy nagyvállalati mobilitási csomag (EMS) termékeken keresztül elérhető premium edition képességet.
- **A felhasználók regisztrálhatják eszközeiket az Azure ad-vel** – Ez a beállítás a Windows 10-es személyes, iOS, Android és MacOS rendszerű eszközök kell konfigurálnia kell [regisztrálva](overview.md#azure-ad-registered-devices) az Azure AD. Ha **None**, eszközök regisztrálása az Azure ad-vel nem engedélyezettek. Az Office 365-höz a Microsoft Intune- vagy mobileszköz-felügyeleti (MDM) beléptetési regisztráció szükséges. Ha ezen szolgáltatások bármelyikét konfigurálta **összes** van kiválasztva, és **NONE** nem érhető el. Ha engedélyezi ezt a beállítást, az Intune-ból, majd itt az lehetőség szürkén jelennek meg.
- **Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése** -kiválaszthatja a felhasználóknak kell-e egy második hitelesítési tényezőt ahhoz, hogy [illesztési](overview.md#azure-ad-joined-devices) az eszközt az Azure AD. Az alapértelmezett érték **nem**. Azt javasoljuk, hogy a multi-factor authentication megkövetelése az eszköz regisztrálásához ajánlatos. Mielőtt engedélyezné ezt a szolgáltatást a multi-factor Authentication hitelesítés, biztosítania kell, hogy a felhasználók regisztrálják az eszközeiket, az Azure multi-factor Authentication hitelesítés van konfigurálva. További információkért tekintse meg a cikket [Azure multi-factor Authentication üzembe helyezési](../authentication/howto-mfa-getstarted.md). 

> [!NOTE]
> **Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése** beállítás nem vonatkozik a hibrid Azure AD-csatlakoztatott eszközök.

- **Eszközök maximális számát** – Ez a beállítás lehetővé teszi, hogy válassza ki, hogy a felhasználók még az Azure AD-eszközök maximális számát. Ha egy felhasználó eléri ezt a kvótát, azok vannak nem tud további eszközöket felvenni, amíg egy vagy több olyan meglévő eszközt el lesznek távolítva. Az eszközökre vonatkozó kvóta minden regisztrált eszközök Azure AD-hez vagy az Azure AD ma után kell fizetnie. Az alapértelmezett érték **20**.

> [!NOTE]
> **Eszközök maximális számát** beállítás nem vonatkozik a hibrid Azure AD-csatlakoztatott eszközök.

- **Felhasználók szinkronizálhatják a beállításokat és alkalmazásadatokat különböző eszközökön** – alapértelmezés szerint ez a beállítás **NONE**. Adott felhasználók vagy csoportok vagy az összes kijelölésével a felhasználói beállításokat és alkalmazásadatokat szinkronizálásának a Windows 10-es eszközeiket. További információ a Windows 10 sync működéséről.
Ez a beállítás akkor egy olyan prémium funkció elérhető termékek, például az Azure AD prémium vagy nagyvállalati mobilitási csomag (EMS).

## <a name="locate-devices"></a>Eszközök megkeresése

Keresse meg a regisztrált és a csatlakoztatott eszközök két lehetősége van:

- **Minden eszköz** a a **kezelés** szakaszában a **eszközök** lap  

   ![Minden eszköz](./media/device-management-azure-portal/41.png)

- **Eszközök** a a **kezelés** szakaszában egy **felhasználói** lap

   ![Minden eszköz](./media/device-management-azure-portal/43.png)

Mindkét lehetőség a beszerezheti a nézetre, amely:

- Lehetővé teszi, hogy a megjelenítési név használatával szűrőként eszközöket keres.
- A regisztrált és a csatlakoztatott eszközök részletes áttekintést nyújt
- Lehetővé teszi, hogy az eszköz gyakori felügyeleti feladatok végrehajtása

![Minden eszköz](./media/device-management-azure-portal/51.png)

Egyes iOS-eszközök esetén az eszköz nevét tartalmazó aposztrófok potenciálisan használhatnak másik hasonló aposztrófot. Ezért az ilyen eszközök keresése forrásanyag – Ha nem lát keresési eredmények megfelelő, győződjön meg arról, hogy a keresési karakterlánc tartalmaz-e egyező aposztróf karaktert.

## <a name="device-identity-management-tasks"></a>Eszköz identitáskezelési feladatokat

Egy globális rendszergazdai vagy a felhőbeli eszközrendszergazda a regisztrált vagy a csatlakoztatott eszközöket kezelheti. Az Intune szolgáltatás-rendszergazdák a következőket teheti:

- Frissítési eszközök - példák napi műveletek, például eszközök engedélyezése vagy letiltása
- Törli az eszközöket –, ha az eszközt kivonják a forgalomból, és azt törölni kell az Azure ad-ben

Ebben a szakaszban részletes információ a közös eszközök identitáskezelési feladatokat.

### <a name="manage-an-intune-device"></a>Az Intune-eszközök kezelése

Ha az Intune-rendszergazdák, jelölésű eszközök kezeléséhez **a Microsoft Intune**.

![Az Intune-eszközök kezelése](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Engedélyezi / letiltja az Azure ad-ben eszközt

Engedélyezi / letiltja az eszközt, két lehetősége van:

- A feladatok menü ("...") található a **minden eszköz** lap

   ![Az Intune-eszközök kezelése](./media/device-management-azure-portal/71.png)

- Az eszköztáron a **eszközök** lap

   ![Az Intune-eszközök kezelése](./media/device-management-azure-portal/32.png)

**Megjegyzés:**

- Globális rendszergazdának lennie, vagy a felhőbeli eszközadminisztrátor engedélyezi / letiltja az eszköz Azure AD-ben kell. 
- Eszköz letiltása megakadályozza, hogy egy eszközt az Azure AD, meggátolva az eszköz regisztrációját az Azure ad-ben vannak eszköz CA által védett erőforrások eléréséhez, vagy a WH4B hitelesítő adatok használatával sikeres hitelesítése.

### <a name="delete-an-azure-ad-device"></a>Az Azure AD-eszköz törlése

Eszköz törlése, két lehetősége van:

- A feladatok menü ("...") található a **minden eszköz** lap

   ![Az Intune-eszközök kezelése](./media/device-management-azure-portal/72.png)

- Az eszköztáron a **eszközök** lap

   ![Eszköz törlése](./media/device-management-azure-portal/34.png)

**Megjegyzés:**

- Meg kell lennie egy globális rendszergazdai vagy Intune-rendszergazda az eszköz törlése az Azure AD-ben.
- Eszköz törlése:
   - Megakadályozza, hogy egy eszközt az Azure AD-erőforrások eléréséhez.
   - Eltávolítja az összes csatolt az eszközön, például részletei, Windows-eszközök esetén a BitLocker-kulcsok.  
   - Egy nem helyreállítható tevékenységet képezi le, és nem ajánlott, kivéve, ha szükséges.

Ha egy eszközt egy másik felügyeleti hatóság (például a Microsoft Intune) által felügyelt, ellenőrizze, hogy az eszköz törlése / Azure AD-ben az eszköz törlése előtt elavult.

### <a name="view-or-copy-device-id"></a>Megtekintése és másolása az eszköz azonosítója

Egy Eszközazonosítót segítségével ellenőrizze az eszköz azonosító adatait az eszköz vagy a PowerShell használata a hibaelhárítás során. A másolási beállítás eléréséhez kattintson arra az eszközre.

![Egy Eszközazonosítót megtekintése](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése és másolása

Megtekintheti, és másolja a BitLocker-kulcsok, hogy segítséget nyújtson a felhasználóknak a titkosított meghajtó helyreállítása. Ezek a kulcsok csak titkosított Windows-eszközökhöz elérhető, és a kulcsok az Azure ad-ben tárolt. Ezek a kulcsok másolhatja az eszköz adatait elérésekor.

![BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/36.png)

megtekintéséhez, illetve a BitLocker-kulcs másolása kell tennie az eszköz tulajdonosa, vagy egy felhasználót, hogy a következő szerepkörök rendelve legalább egyike lehet:

- Globális rendszergazda
- Ügyfélszolgálati adminisztrátor
- Biztonsági rendszergazda
- Biztonsági olvasó
- Intune szolgáltatás rendszergazdája

> [!NOTE]
> Hibrid Azure AD-csatlakoztatott Windows 10-eszközökön nincs tulajdonosa. Tehát ha egy eszköz tulajdonosa keres, és nem találta meg, keressen rá szerint az eszköz azonosítójával.

## <a name="audit-logs"></a>Naplók

Eszköz tevékenységek a vizsgálati naplók keresztül érhetők el. Ezek a naplók felhasználók és az eszközregisztrációs szolgáltatás által indított tevékenységek tartalmazzák:

- Eszköz létrehozása és a tulajdonosok hozzáadása vagy az eszköz felhasználója
- Beállítások módosítása
- Eszköz műveletek, például a törlés, vagy egy eszköz frissítése

A naplózási adatokat belépési pontjához van **Auditnaplók** a a **tevékenység** szakaszában a **eszközök** lapot.

![Naplók](./media/device-management-azure-portal/61.png)

Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A dátum és idő az előfordulás
- A tárolók
- A kezdeményező / aktor (ki) a tevékenységek
- A tevékenység (Mit)

![Naplók](./media/device-management-azure-portal/63.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Naplók](./media/device-management-azure-portal/64.png)

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Category
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Target
- Kezdeményező (szereplő)

A szűrők mellett bejegyzéseit konkrét bejegyzések kereshet.

![Naplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>További lépések

[Elavult eszközök kezelése az Azure ad-ben](manage-stale-devices.md)
