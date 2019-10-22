---
title: Naplók kiépítés a Azure Active Directory portálon (előzetes verzió) | Microsoft Docs
description: A Azure Active Directory-portálon végzett kiépítési tevékenységekről szóló jelentések bemutatása
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "70983498"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók**  -  a[naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
    - **Naplók** kiosztása – rendszertevékenység nyújtása az Azure ad-kiépítési szolgáltatás által kiépített felhasználókkal, csoportokkal és szerepkörökkel kapcsolatban. 

- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](concept-user-at-risk.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a témakör áttekintést nyújt a kiépítési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* Felhasználók a biztonsági rendszergazda, a biztonsági olvasó, a jelentéskészítő, az alkalmazás rendszergazdája és a Felhőbeli alkalmazás rendszergazdai szerepkörei
* Globális rendszergazdák


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Milyen Azure AD-licencre van szükség a kiépítési tevékenységekhez való hozzáféréshez?

A bérlőnek prémium szintű Azure AD licenccel kell rendelkeznie ahhoz, hogy láthassa a teljes kiépítési tevékenység jelentését. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. 

## <a name="provisioning-logs"></a>Üzembehelyezési naplók

A kiépítési naplók a következő kérdésekre adnak választ:

* Milyen csoportokat sikerült létrehozni a ServiceNow-ben?
* Hogyan lettek importálva a szerepkörök a Amazon Web Services?
* Mely felhasználók lettek sikeresen létrehozva a DropBoxban?

A kiépítési naplókat úgy érheti el, ha kijelöli a **kiépítési** naplókat a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **figyelés** szakaszában. Akár két órát is igénybe vehet, hogy egyes kiépítési rekordok megjelenjenek a portálon.

![Naplók kiépítés](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A kiépítési napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az identitás
- A művelet
- A forrásoldali System
- A célként megadott System
- Az állapot
- A dátum


![Alapértelmezett oszlopok](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Oszlop kiválasztása](./media/concept-provisioning-logs/column-chooser.png "Oszlop kiválasztása")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Elérhető oszlopok](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Részletes információk](./media/concept-provisioning-logs/steps.png "Szűrő")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

A jelentett adat leszűkíthető egy olyan szintre, amely az Ön számára működik, a következő alapértelmezett mezők használatával szűrheti a kiépítési adatait. Vegye figyelembe, hogy a szűrőkben szereplő értékek dinamikusan vannak feltöltve a bérlő alapján. Ha például nem rendelkezik létrehozási eseményekkel a bérlőben, a létrehozáshoz nem lesz szűrő lehetőség.

- Identitáskezelés
- Műveletek
- Forrásoldali rendszerek
- Célrendszer
- Állapot
- Dátum


![Szűrő](./media/concept-provisioning-logs/filter.png "Szűrő")

Az **Identity** szűrő segítségével megadhatja a nevet vagy az Ön számára fontos identitást. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. Az objektum neve vagy azonosítója alapján kereshet. Az azonosító forgatókönyv szerint változik. Ha például egy objektumot kiépít az Azure AD-ből a SalesForce-be, a forrás azonosítója az Azure AD-beli felhasználó objektumazonosítóa, míg a TargetID a Salesforce felhasználójának azonosítója. Ha a munkahelyről a Active Directoryre való kiépítés után a forrás azonosítója a munkanap munkavégző alkalmazottjának azonosítója. Vegye figyelembe, hogy a felhasználó neve nem mindig szerepel az Identity oszlopban. Mindig egy azonosító lesz. 

A **forrásrendszer** szűrője lehetővé teszi annak megadását, hogy az identitás honnan legyen kiépítve. Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, akkor a forrásoldali rendszer az Azure AD. 

A célként megadott rendszerszűrő segítségével megadhatja, hogy az identitás hová legyen kiépítve a **szolgáltatásba** . Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, a rendszer ServiceNow. 

Az **állapot** szűrő segítségével a következőket választhatja ki:

- Mind
- Sikeres
- Hiba
- Kimarad

A **művelet** szűrője lehetővé teszi a következő szűrését:

- Létrehozás 
- Frissítés
- Törlés
- Letiltás
- Egyéb

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Amikor kiválaszt egy egyéni időkeretet, beállíthatja a kezdési és a befejezési dátumot.


Az alapértelmezett mezőkön kívül a kiválasztáskor a következő mezőket is megadhatja a szűrőben:

- **Job ID** – a rendszer minden olyan alkalmazáshoz társít egy egyedi azonosítójú feladatot, amelyhez engedélyezte az üzembe helyezést.   

- **Ciklus azonosítója** – egyedi módon azonosítja a létesítési ciklust. Ezt az azonosítót megoszthatja a támogatással, hogy megkeresse azt a ciklust, amelyben ez az esemény történt.

- A kiépítési esemény egyedi azonosítójának **módosítása** . Megoszthatja ezt az azonosítót, hogy támogassa a kiépítési esemény megkeresését.   



  

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kiválaszt egy elemet a kiépítési lista nézetben, az elemről további részleteket talál.
A részletek a következő kategóriák alapján vannak csoportosítva:

- Lépések

- Hibák és javaslatok

- Módosított tulajdonságok

- Összefoglalás


![Szűrő](./media/concept-provisioning-logs/provisioning-tabs.png "Lapok")



### <a name="steps"></a>Lépések

A **lépések** lapon az objektum kiépítéséhez szükséges lépések szerepelnek. Az objektumok kiépítés négy lépésből állhat: 

- Objektum importálása
- Annak megállapítása, hogy az objektum hatókörben van-e
- Objektum egyeztetése a forrás és a cél között
- Objektum kiépítése (művelet elvégzése – ez lehet létrehozás, frissítés, törlés vagy Letiltás)



![Szűrő](./media/concept-provisioning-logs/steps.png "Szűrő")


### <a name="troubleshoot-and-recommendations"></a>Hibák és javaslatok


A **hibakeresés és javaslatok** lap a hibakódot és az okot adja meg. A hiba adatai csak meghibásodás esetén érhetők el. 


### <a name="modified-properties"></a>Módosított tulajdonságok

A **módosított tulajdonságok** a régi értéket és az új értéket jelenítik meg. Olyan esetekben, amikor nincs régi érték, a régi érték oszlop üres. 


### <a name="summary"></a>Összefoglalás

Az **Összefoglalás** lapon áttekintheti, hogy mi történt, és milyen azonosítókat tartalmaz a forrás és a cél rendszer objektumához. 

## <a name="what-you-should-know"></a>Alapismeretek

- A Azure Portal 30 napig tárolja a kiépítési adatgyűjtési jelentést, ha rendelkezik prémium szintű kiadással, és 7 nap van, ha ingyenes kiadással rendelkezik.

- A Change ID attribútum egyedi azonosítóként használható. Ez például a terméktámogatással való interakció esetén hasznos.

- Jelenleg nincs lehetőség az üzembe helyezési adatszolgáltatások letöltésére.

- A log Analytics jelenleg nem támogatott.

- Amikor egy alkalmazás környezetében fér hozzá a kiépítési naplókhoz, nem szűri automatikusan az eseményeket az adott alkalmazáshoz a naplók módon.

## <a name="next-steps"></a>Következő lépések

* [A felhasználó kiépítési állapotának megtekintése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


