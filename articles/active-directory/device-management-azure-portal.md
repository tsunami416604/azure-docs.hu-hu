---
title: "Az Azure portál használatával eszközök kezelése |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure-portálon eszközök kezelésére."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1e0d40b996e181a606d16d26633f890b9169ecbb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="managing-devices-using-the-azure-portal"></a>Az Azure portál használatával eszközök kezelése


Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. 

Ez a témakör:

- Feltételezi, hogy Ön ismeri a [eszköz kezelése az Azure Active Directoryban bemutatása](device-management-introduction.md)

- Az Azure portál használata az eszközök felügyeletével kapcsolatos információt

## <a name="manage-devices"></a>Eszközök kezelése 

Az Azure-portálon az eszközök kezeléséhez központi helyet biztosít. Ennek a helynek a vagy használatával beszerezheti a [közvetlen hivatkozást](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) vagy a következő manuális lépéseket:

1. Rang be a [Azure-portálon](https://portal.azure.com) rendszergazdaként.

2. Kattintson a bal oldali navigációs sávja **Active Directory**.

    ![Eszközök beállításainak konfigurálása](./media/device-management-azure-portal/01.png)

3. Az a **kezelése** kattintson **eszközök**.

    ![Eszközök beállításainak konfigurálása](./media/device-management-azure-portal/11.png)
 
A **eszközök** oldalon:

- Az eszköz kezelési beállításainak konfigurálása

- Keresse meg az eszközök

- Eszköz felügyeleti feladatok végrehajtása

- Tekintse át az eszköz-kezeléssel kapcsolatos naplók  
  

## <a name="configure-device-settings"></a>Eszközök beállításainak konfigurálása

Az Azure portál használatával eszközeinek kezelésére, az eszközök kell lennie vagy [regisztrálva, vagy csatlakoztatott](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) az Azure ad Szolgáltatásba. A rendszergazdák úgy finomhangolhatja, regisztrálása, illetve az eszközök csatlakoztatása az eszköz beállítások konfigurálásával folyamatán. 

![Eszközök beállításainak konfigurálása](./media/device-management-azure-portal/22.png)

Az eszköz beállításai oldal konfigurálását teszi lehetővé:

![Az Intune-ban eszközök kezelése](./media/device-management-azure-portal/21.png)


- **Felhasználók eszközök regisztrálása az Azure AD** – Ez a beállítás lehetővé teszi a felhasználók számára is [eszközök regisztrálása](device-management-introduction.md#azure-ad-joined-devices) az Azure ad Szolgáltatásba. Az alapértelmezett érték **összes**.

- **További Azure AD a helyi rendszergazdák csatlakoztatott eszközök** -kiválaszthatja, hogy az adott eszközön helyi rendszergazdai jogosultságokkal rendelkező felhasználók. Hozzá felhasználót adnak hozzá a *Eszközadminisztrátorok* szerepkört az Azure ad-ben. Az Azure AD globális rendszergazdák és eszköztulajdonosok alapértelmezés szerint kapnak helyi rendszergazdai jogosultságokkal. Ez a beállítás akkor premium edition-funkció termékek, például az Azure AD Premium vagy a nagyvállalati mobilitási csomag (EMS) keresztül érhető el. 

- **Felhasználók előfordulhat, hogy regisztrálják az eszközeiket az Azure AD** -konfigurálnia kell ennek a beállításnak az eszközök akkor is lehet [regisztrált](device-management-introduction.md#azure-ad-registered-devices) az Azure ad-val. Ha **nincs**, eszközök regisztrálása, amelyek nincsenek tartományhoz az Azure AD vagy az Azure AD-tartományhoz hibrid nem engedélyezettek. Az Office 365 mobileszköz-felügyeleti (MDM) vagy a Microsoft Intune beléptetési regisztráció szükséges. Ha konfigurálta a szolgáltatások valamelyikét **minden** van kiválasztva és **NONE** nem érhető el.

- **Csatlakoztathatják az eszközeiket a többtényezős hitelesítés megkövetelése** -dönthet úgy, hogy felhasználóknak meg kell adnia egy második hitelesítési tényezővel való [illesztési](device-management-introduction.md#azure-ad-joined-devices) az eszközeiket az Azure ad Szolgáltatásba. Az alapértelmezett érték **nem**. Azt javasoljuk, hogy többtényezős hitelesítés megkövetelése eszközök regisztrálásakor. Mielőtt engedélyezné ezt a szolgáltatást a többtényezős hitelesítést, győződjön meg róla, hogy a többtényezős hitelesítés beállítása a felhasználók regisztrálják az eszközeiket. A különböző Azure multi-factor authentication szolgáltatások további információkért lásd: [Ismerkedés az Azure multi-factor Authentication hitelesítés](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Eszközök maximális számát** – Ez a beállítás engedélyezi, hogy válassza ki, amelyeken az Azure ad-ben a felhasználói eszközök maximális számát. Ha a felhasználó eléri ezt a kvótát, amelyek nem tudják további eszközöket felvenni, amíg egy vagy több olyan meglévő eszközt a rendszer eltávolítja. Az eszköz ajánlat csatlakozott az Azure AD vagy az Azure AD ma regisztrált összes eszköz akkor számít. Az alapértelmezett érték **20**.

- **Felhasználók is szinkronizálásának beállítások és alkalmazások adatainak eszközök** -alapértelmezés szerint ez a beállítás értéke **NONE**. Egyes felhasználók vagy csoportok, vagy az összes kijelölésével a felhasználói beállítások és alkalmazások adatainak a Windows 10-eszközök szinkronizálásának. További tudnivalókért a Windows 10-es sync működéséről.
Ez a beállítás akkor a termékek, például az Azure AD Premium vagy a nagyvállalati mobilitási csomag (EMS) keresztül érhető el prémium funkció.
 




## <a name="locate-devices"></a>Keresse meg az eszközök

Keresse meg a regisztrált és a csatlakoztatott eszközök a két lehetőség közül választhat:

- **Minden eszköz** a a **kezelése** szakasza a **eszközök** lap  

    ![Minden eszköz](./media/device-management-azure-portal/41.png)


- **Eszközök** a a **kezelése** szakasza egy **felhasználói** lap
 
    ![Minden eszköz](./media/device-management-azure-portal/43.png)



Mindkét lehetőség érhet nézetet, amely:


- Lehetővé teszi, hogy a megjelenített név használata szűrő eszközöket keres.

- Lehetővé teszi a regisztrált és a csatlakoztatott eszközök részletes áttekintése

- Lehetővé teszi az eszköz általános felügyeleti feladatai
   

![Minden eszköz](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Eszközfelügyeleti feladatokat

A rendszergazdák a regisztrált vagy csatlakoztatott eszközöket kezelheti. Ez a szakasz a közös eszköz felügyeleti feladatokkal kapcsolatos információt nyújt.


### <a name="manage-an-intune-device"></a>Az Intune-ban eszközök kezelése

Ha egy Intune-rendszergazda, jelölésű-eszközök kezeléséhez **a Microsoft Intune**. A rendszergazda további eszköz látható 

![Az Intune-ban eszközök kezelése](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Engedélyezi / letiltja az Azure AD-eszközök

Engedélyezi / letiltja az eszköz, két lehetőség közül választhat:

- A feladatok menüjének ("...") a **minden eszköz** lap

    ![Az Intune-ban eszközök kezelése](./media/device-management-azure-portal/71.png)

- Az eszköztáron a **eszközök** lap

    ![Az Intune-ban eszközök kezelése](./media/device-management-azure-portal/32.png)


**Megjegyzés:**

- Kell lennie egy globális rendszergazda Azure ad-engedélyezése / letiltása eszköz. 
- Ha letilt egy eszközt megakadályozza, hogy az eszköz az Azure AD-erőforrások hozzáférését. 



### <a name="delete-an-azure-ad-device"></a>Egy Azure AD-eszköz törlése

Eszköz törlése, két lehetőség közül választhat:

- A feladatok menüjének ("...") a **minden eszköz** lap

    ![Az Intune-ban eszközök kezelése](./media/device-management-azure-portal/72.png)

- Az eszköztáron a **eszközök** lap

    ![Eszköz törlése](./media/device-management-azure-portal/34.png)


**Megjegyzés:**

- Az eszköz törlése az Azure AD globális rendszergazdának lennie kell.  

- Egy eszköz törlése:
 
    - Megakadályozza, hogy egy eszközt az Azure AD-erőforrások eléréséhez. 

    - Eltávolítja az összes csatlakoztatott eszközön, például részletei, a BitLocker-kulcsok Windows-eszközök.  

    - Nem állíthatók helyre tevékenységet jelent, és nem ajánlott, kivéve, ha szükséges.

Ha egy eszköz felügyelete egy másik felügyeleti hatóság (például a Microsoft Intune), győződjön meg arról, hogy az eszközön tárolt adatok törléséig / már nincs az Azure ad-ben az eszköz törlése előtt.

 


### <a name="view-or-copy-device-id"></a>Megtekintése vagy Eszközazonosító másolása

Egy eszköz azonosítója segítségével ellenőrizze az eszköz azonosító adatait az eszköz vagy a PowerShell használatával a hibaelhárítás során. A másolási lehetőséget szeretne használni, kattintson az eszközre.

![Eszközazonosítót megtekintése](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése és másolása

Ha Ön rendszergazda, megtekintheti, és másolja a BitLocker-kulcsok számára, hogy a titkosított meghajtó helyreállítani segítségével. Ezek a kulcsok csak titkosított Windows-eszközökhöz elérhető, és a kulcsaikat az Azure ad-ben tárolt. Ezek a kulcsok másolhatja az eszköz részleteit való hozzáféréskor.
 
![A BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Naplók


Eszköz tevékenységek a tevékenységi naplóit keresztül érhetők el. Ez magában foglalja a felhasználók és az eszközregisztrációs szolgáltatás által indított tevékenységek:

- Eszköz létrehozása és hozzáadása a tulajdonosok vagy az eszköz felhasználója

- Eszköz beállításai

- Eszköz műveletek, például a törlés vagy egy eszköz frissítése
 
A belépési pont a naplózási adatok **naplók** a a **tevékenység** szakasza a **eszközök** lap.

![Naplók](./media/device-management-azure-portal/61.png)


Az auditnapló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- a dátum és idő a előfordulás

- a cél

- a kezdeményező / szereplő (ki) tevékenység

- a tevékenység (Mit)

![Naplók](./media/device-management-azure-portal/63.png)

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.
 
![Naplók](./media/device-management-azure-portal/64.png)


A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Kategória
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Cél
- (Aktor) által kezdeményezett

A szűrők mellett kereshet adott bejegyzéseket.

![Naplók](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Következő lépések

* [Kezelés az Azure Active Directoryban bemutatása](device-management-introduction.md)



