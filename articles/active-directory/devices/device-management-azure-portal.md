---
title: Az Eszközfelügyelet az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használja az Azure portal-eszközök kezeléséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: bc29fbd0946f0abfe692e2270d145cd2cb292356
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054096"
---
# <a name="managing-devices-using-the-azure-portal"></a>Az Eszközfelügyelet az Azure portal használatával


Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. 

A cikk tartalma:

- Feltételezi, hogy ismeri a [az Eszközfelügyelet az Azure Active Directory bemutatása](overview.md)

- Az Eszközfelügyelet az Azure portal használatával kapcsolatos információt

## <a name="manage-devices"></a>Eszközök kezelése 

Az Azure portal egy központi helyen kezelheti az eszközöket biztosít. Ennek a helynek a bármelyik használatával kérheti egy [közvetlen hivatkozás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) vagy a következő manuális lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) rendszergazdaként.

2. A bal oldali navigációs sávon kattintson **Active Directory**.

    ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/01.png)

3. Az a **kezelés** területén kattintson **eszközök**.

    ![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/74.png)
 
A **eszközök** lap lehetővé teszi, hogy:

- A felügyeleti beállítások konfigurálása

- Eszközök megkeresése

- Eszköz felügyeleti feladatok végrehajtása

- Tekintse át az eszközfelügyeleti kapcsolódó naplók  
  

## <a name="configure-device-settings"></a>Eszközbeállítások konfigurálása

Az eszközök kezeléséhez az eszközöket az Azure portal segítségével, kell lennie [regisztrált vagy azzal összekapcsolt](overview.md#getting-devices-under-the-control-of-azure-ad) az Azure ad-hez. A rendszergazdák finomhangolhatja regisztrálása és az eszközök csatlakoztatása az eszköz beállításait konfigurálja. 

![Eszközbeállítások konfigurálása](./media/device-management-azure-portal/22.png)

Az eszköz beállításai lapon konfigurálását teszi lehetővé:

![Az Intune-eszközök kezelése](./media/device-management-azure-portal/21.png)


- **Felhasználók eszközöket csatlakoztathatnak az Azure AD** – Ez a beállítás lehetővé teszi a felhasználók, akik is [eszközöket](overview.md#azure-ad-joined-devices) az Azure ad-hez. Az alapértelmezett érték **összes**. A beállítás csak akkor alkalmazható az Azure AD Join a Windows 10-es.

- **További helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök** – kiválaszthatja, hogy az adott eszközön helyi rendszergazdai jogosultságokkal rendelkező felhasználók. Az itt hozzáadott felhasználók hozzáadódnak a *Device Administrators* az Azure AD-szerepkörhöz. Az Azure AD globális rendszergazdák és eszköztulajdonosok alapértelmezés szerint helyi rendszergazdai jogokat kapnak. Ez a beállítás akkor például az Azure AD prémium vagy nagyvállalati mobilitási csomag (EMS) termékeken keresztül elérhető premium edition képességet. 

- **A felhasználók regisztrálhatják eszközeiket az Azure ad-vel** -be kell állítania ezt a beállítást szeretné lehetővé tenni az eszközök számára [regisztrált](overview.md#azure-ad-registered-devices) az Azure ad-ben. Ha **None**, eszközök regisztrálása, ha azok nem Azure AD-hez vagy a hibrid Azure AD-hez való nem engedélyezett. Az Office 365-höz a Microsoft Intune- vagy mobileszköz-felügyeleti (MDM) beléptetési regisztráció szükséges. Ha ezen szolgáltatások bármelyikét konfigurálta **összes** van kiválasztva, és **NONE** nem érhető el.

- **Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése** -kiválaszthatja a felhasználóknak kell-e egy második hitelesítési tényezőt ahhoz, hogy [illesztési](overview.md#azure-ad-joined-devices) az eszközt az Azure AD. Az alapértelmezett érték **nem**. Azt javasoljuk, hogy a multi-factor authentication megkövetelése az eszköz regisztrálásához ajánlatos. Mielőtt engedélyezné ezt a szolgáltatást a multi-factor Authentication hitelesítés, biztosítania kell, hogy a multi-factor Authentication hitelesítés van konfigurálva a felhasználók regisztrálják az eszközeiket. A másik az Azure multi-factor authentication szolgáltatások további információkért lásd: [az Azure multi-factor authentication – első lépések](../authentication/concept-mfa-whichversion.md). Ez a beállítás nem érinti a hibrid csatlakozás a Windows 10 vagy Windows 7. Ez a tulajdonság csak a Windows 10, iOS és Android rendszerhez készült Windows 10-es és a BYOD eszközregisztráció az Azure AD Join vonatkozik. 

- **Eszközök maximális számát** – Ez a beállítás lehetővé teszi, hogy válassza ki, hogy a felhasználók még az Azure AD-eszközök maximális számát. Ha egy felhasználó eléri ezt a kvótát, azok vannak nem tud további eszközöket felvenni, amíg egy vagy több olyan meglévő eszközt el lesznek távolítva. Az eszköz ajánlat, amely az Azure AD-hez, vagy még ma az Azure AD minden eszköz után kell fizetnie. Az alapértelmezett érték **20**.

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

## <a name="device-management-tasks"></a>Eszközfelügyeleti feladatokat

A rendszergazdák a regisztrált vagy a csatlakoztatott eszközöket kezelheti. Ez a szakasz eszköz felügyeletének általános feladatai kapcsolatos információkat biztosít.


### <a name="manage-an-intune-device"></a>Az Intune-eszközök kezelése

Ha az Intune-rendszergazdák, jelölésű eszközök kezeléséhez **a Microsoft Intune**. A rendszergazda láthatja a további eszközök 

![Az Intune-eszközök kezelése](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Engedélyezi / letiltja az Azure ad-ben eszközt

Engedélyezi / letiltja az eszközt, két lehetősége van:

- A feladatok menü ("...") található a **minden eszköz** lap

    ![Az Intune-eszközök kezelése](./media/device-management-azure-portal/71.png)

- Az eszköztáron a **eszközök** lap

    ![Az Intune-eszközök kezelése](./media/device-management-azure-portal/32.png)


**Megjegyzés:**

- Engedélyezi / letiltja az eszköz Azure AD-ben globális rendszergazdának lennie kell. 
- Eszköz letiltása megakadályozza, hogy egy eszközt az Azure AD-erőforrások eléréséhez. 



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

Ha egy eszközt egy másik felügyeleti hatóság (például a Microsoft Intune) által felügyelt, győződjön meg arról, hogy az eszköz törlése / Azure AD-ben az eszköz törlése előtt elavult.

 


### <a name="view-or-copy-device-id"></a>Megtekintése és másolása az eszköz azonosítója

Egy Eszközazonosítót segítségével ellenőrizze az eszköz azonosító adatait az eszköz vagy a PowerShell használata a hibaelhárítás során. A másolási beállítás eléréséhez kattintson arra az eszközre.

![Egy Eszközazonosítót megtekintése](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése és másolása

Megtekintheti, és másolja a BitLocker-kulcsok, hogy segítséget nyújtson a felhasználóknak a titkosított meghajtó helyreállítása. Ezek a kulcsok csak titkosított Windows-eszközökhöz elérhető, és a kulcsok az Azure ad-ben tárolt. Ezek a kulcsok másolhatja az eszköz adatait elérésekor.
 
![BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/36.png)

megtekintéséhez, illetve a BitLocker-kulcs másolása kell tennie az eszköz tulajdonosa, vagy egy felhasználót, hogy a következő szerepkörök rendelve legalább egyike lehet:

- Globális rendszergazdák
- Ügyfélszolgálati rendszergazdák
- Biztonsági rendszergazda
- Biztonsági olvasó
- Az Intune szolgáltatás-rendszergazdák

> [!NOTE]
> Hibrid Azure AD-csatlakoztatott Windows 10-eszközökön nincs tulajdonosa. Tehát ha egy eszköz tulajdonosa keres, és nem találta meg, keressen rá szerint az eszköz azonosítójával.


## <a name="audit-logs"></a>Naplók


Eszköz tevékenységek a vizsgálati naplók keresztül érhetők el. Ez tartalmazza a felhasználók és az eszközregisztrációs szolgáltatás által indított tevékenységek:

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

- Kategória
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Cél
- Kezdeményező (szereplő)

A szűrők mellett bejegyzéseit konkrét bejegyzések kereshet.

![Naplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>További lépések

* [Az Eszközfelügyelet az Azure Active Directory bemutatása](overview.md)



