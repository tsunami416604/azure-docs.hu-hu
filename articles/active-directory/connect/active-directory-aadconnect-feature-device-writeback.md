---
title: 'Az Azure AD Connect: Eszközvisszaírás engedélyezése |} Microsoft Docs'
description: Ez a dokumentum részletesen engedélyezése az Azure AD Connect használatával eszközök visszaírásához.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fa8cdaf1a21a59a5bb695e3be90382f1e33823a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590589"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Az Azure AD Connect: Eszközvisszaírás engedélyezése
> [!NOTE]
> Prémium szintű Azure AD-előfizetés szükség az eszközök visszaírásához.
> 
> 

Az alábbi dokumentáció az Azure AD Connect eszköz a visszaírási szolgáltatás hogyan ismertetése. Eszközvisszaíró a következő esetekben használja:

* Feltételes hozzáférési házirend alapján AD FS-eszközök engedélyezése (2012 R2 vagy újabb) védett alkalmazások (függő entitások megbízhatóságához).

Ez biztosítja, további biztonsági és garantálja, hogy az alkalmazások hozzáférés csak a megbízható eszközök számára. További információ a feltételes hozzáférési: [kockázat kezelése feltételes hozzáféréssel rendelkező](../active-directory-conditional-access-azure-portal.md) és [helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Eszközök objektumának a felhasználók ugyanabban az erdőben kell lennie. Eszközök kell írni vissza egyetlen erdő esetén, mert ez a funkció jelenleg nem támogatja a központi telepítés felhasználói több erdővel.</li>
> <li>Csak egy eszköz regisztrációs konfigurációs objektum a helyszíni Active Directory-erdőbe is hozzáadhatók. Ez a funkció nem kompatibilis a topológia, ahol a helyszíni Active Directory több Azure AD-címtártól szinkronizált.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>1. lépés: Telepítse az Azure AD Connect
Az Azure AD Connect egyéni használatával telepítsen, vagy Gyorsbeállítások. A Microsoft azt javasolja, hogy kezdje összes felhasználók és csoportok sikeresen kell szinkronizálnia a engedélyezése előtt.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>2. lépés: Az Azure AD Connect engedélyezése eszközvisszaíró
1. Futtassa újra a telepítési varázslót. Válassza ki **eszközbeállítások konfigurálása** a feladatokat a lapon, majd kattintson **következő**. 

    ![Eszközbeállítások konfigurálása](./media/active-directory-aadconnect-feature-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Adja meg új eszköz a beállításokat a rendszer csak a 1.1.819.0 verzió vagy újabb.

2. Az eszköz beállítások lapon, válassza ki a **eszközvisszaíró konfigurálása**. A beállítás **eszközvisszaíró letiltása** nem lesz elérhető eszközvisszaíró engedélyezéséig. Kattintson a **következő** Ugrás a varázsló következő lapjára.
    ![Kiválasztott eszköz művelet](./media/active-directory-aadconnect-feature-device-writeback/configuredevicewriteback1.png)

3. A visszaírási lapon akkor jelenik meg a megadott tartomány as az alapértelmezett eszköz visszaírási erdő.
   ![Egyéni telepítés eszköz visszaírási célerdő](./media/active-directory-aadconnect-feature-device-writeback/writebackforest.png)

4. **Eszköz tároló** lap biztosít lehetőséget az active directory előkészítése a két rendelkezésre álló lehetőségek egyikének használatával:

    a. **Adja meg a vállalati rendszergazda hitelesítő adatait**: az erdőhöz, ahol az eszközök visszaírása kell kapott a vállalati rendszergazdájának hitelesítő adatait, az Azure AD Connect készítse elő az erdő automatikusan konfigurálása során eszközök visszaírásához.

    b. **Töltse le a PowerShell-parancsfájl**: az Azure AD Connect automatikus-hoz létre egy PowerShell-parancsfájlt, amely előkészítheti az active directory az eszközök visszaírásához. Abban az esetben, ha a vállalati rendszergazdájának hitelesítő adatait az Azure AD Connectben nem adható meg, töltse le a PowerShell parancsfájl javasolt. Adja meg a letöltött PowerShell-parancsfájl **CreateDeviceContainer.psq** a vállalati rendszergazdáknak az erdő, ahol eszközök visszaíródnak a.
    ![Az active Directoryban erdő előkészítése](./media/active-directory-aadconnect-feature-device-writeback/devicecontainercreds.png)
    
    A következő műveleteket az active directory-erdő előkészítése:
    * Ha már nem léteznek, hoz létre és konfigurálja az új tárolók és objektumok CN = Device Registration Configuration, CN = Services, CN = Configuration, [erdő-dn].
    * Ha már nem léteznek, hoz létre és konfigurálja az új tárolók és objektumok CN = RegisteredDevices, [tartomány-dn]. Eszközobjektumok létrehozza a tárolóban található.
    * Az Azure AD-összekötő fiók, az Active Directoryban lévő eszközök felügyeletéhez szükséges engedélyeket állít be.
    * Csak kell futnia egy erdőt, akkor is, ha az Azure AD Connect van telepítve a több erdőt.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ellenőrizze az eszköz szinkronizált Active Directory
Eszközvisszaíró most már megfelelően fog működni. Vegye figyelembe, hogy az eszköz objektumokat írt vissza kell ad akár 3 órát is igénybe vehet.  Győződjön meg arról, hogy az eszközök vannak folyamatban megfelelően szinkronizálva, tegye a következőket a szinkronizálási szabályok befejezése után:

1. Indítsa el az Active Directory felügyeleti központ.
2. Bontsa ki a RegisteredDevices, a tartományban, hogy az összevont van folyamatban.

   ![Az Active Directory felügyeleti központ regisztrált eszközök](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)

3. Aktuális regisztrált eszközök nem jelennek meg.

   ![Az Active Directory felügyeleti központ regisztrált eszközök listája](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése
A forgatókönyv engedélyezésének részletes útmutatás belül érhető el [helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="the-writeback-checkbox-is-still-disabled"></a>A visszaírási jelölőnégyzet továbbra is le van tiltva.
Ha az eszközök visszaírásához. a jelölőnégyzet nincs engedélyezve, annak ellenére, hogy a fenti lépésekkel, a következő lépések végigvezeti Önt Mi a telepítési varázsló ellenőrzi a lista engedélyezése előtt.

Első lépések első:

* Győződjön meg arról, hogy legalább egy erdő Windows Server 2012 R2 rendszerben. Az eszköz objektumtípus jelen kell lennie.
* Ha a telepítővarázsló már fut, majd a módosításokat nem érzékeli. Ebben az esetben befejezheti a telepítővarázsló futtatását, és futtassa újból.
* Ellenőrizze, hogy az inicializálási parancsfájl a megadott fióknak ténylegesen az Active Directory-összekötő által használt megfelelő felhasználó. Ennek az ellenőrzéséhez kövesse az alábbi lépéseket:
  * A start menüből nyissa meg a **szinkronizálási szolgáltatás**.
  * Nyissa meg a **összekötők** fülre.
  * Keresse meg az összekötő típusú Active Directory tartományi szolgáltatásokban, és válassza ki azt.
  * A **műveletek**, jelölje be **tulajdonságok**.
  * Ugrás a **csatlakozni az Active Directory Erdőfelderítési**. Győződjön meg arról, hogy a tartomány és a felhasználó-név megadva a képernyő megfelelés a parancsfájlt a fiókját.
    ![A Szinkronizáló Service Manager-összekötő-fiók](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Ellenőrizze az Active Directory konfigurációját:

* Győződjön meg arról, hogy az Eszközregisztrációs szolgáltatás az alábbi helyen található (CN DeviceRegistrationService, CN = eszköz regisztrációs Services, CN = Device Registration Configuration, CN = Services, CN = = Configuration) konfigurációs névhasználati környezetében.

![A hiba elhárításához DeviceRegistrationService konfigurációs névtér](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Győződjön meg arról csak egy konfigurációs objektumot a konfigurációs névtér keresve. Ha egynél több, törölje az ismétlődő.

![Elhárításával kapcsolatos tudnivalókat a duplikált objektumok keresése](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Az Eszközregisztrációs szolgáltatás az objektumon győződjön meg arról, az attribútum az msDS-DeviceLocation jelen, és értéke. Keresés a helyét, és győződjön meg arról, hogy telepítve a objectType msDS-DeviceContainer együtt.

![A hiba elhárításához msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![A hiba elhárításához RegisteredDevices objektum osztálya](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Ellenőrizze, hogy az Active Directory-összekötő által használt fiók rendelkezik a szükséges engedélyekkel a regisztrált eszközöket tárolójához az előző lépésben található. Ez az a várt engedélyekkel a tárolóra:

![Elhárításával kapcsolatos tudnivalókat tároló engedélyeinek ellenőrzése](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Ellenőrizze, hogy az Active Directory-fiók engedélyekkel rendelkezzen a CN = Device Registration Configuration, CN = Services, CN = konfigurációs objektum.

![Elhárításával kapcsolatos tudnivalókat Eszközregisztráció konfigurációja az engedélyek ellenőrzése](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>További információ
* [Kockázatkezelés feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)
* [A helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

