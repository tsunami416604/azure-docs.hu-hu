---
title: "Az Azure AD Connect: Eszközvisszaírás engedélyezése |} Microsoft Docs"
description: "Ez a dokumentum részletesen engedélyezése az Azure AD Connect használatával eszközök visszaírásához."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 7af8fadca15e07e178f12db27fec2467f43c5d36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Az Azure AD Connect: Eszközvisszaírás engedélyezése
> [!NOTE]
> Prémium szintű Azure AD-előfizetés szükség az eszközök visszaírásához.
> 
> 

Az alábbi dokumentáció az Azure AD Connect eszköz a visszaírási szolgáltatás hogyan ismertetése. Eszközvisszaíró a következő esetekben használja:

* Feltételes hozzáférési házirend alapján AD FS-eszközök engedélyezése (2012 R2 vagy újabb) védett alkalmazások (függő entitások megbízhatóságához).

Ez biztosítja, további biztonsági és garantálja, hogy az alkalmazások hozzáférés csak a megbízható eszközök számára. További információ a feltételes hozzáférési: [kockázat kezelése feltételes hozzáféréssel rendelkező](../active-directory-conditional-access.md) és [helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Eszközök objektumának a felhasználók ugyanabban az erdőben kell lennie. Eszközök kell írni vissza egyetlen erdő esetén, mert ez a funkció jelenleg nem támogatja a központi telepítés felhasználói több erdővel.</li>
> <li>Csak egy eszköz regisztrációs konfigurációs objektum a helyszíni Active Directory-erdőbe is hozzáadhatók. Ez a funkció nem kompatibilis a topológia, ahol a helyszíni Active Directory több Azure AD-címtártól szinkronizált.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>1. lépés: Telepítse az Azure AD Connect
1. Az Azure AD Connect egyéni használatával telepítsen, vagy Gyorsbeállítások. A Microsoft azt javasolja, hogy kezdje összes felhasználók és csoportok sikeresen kell szinkronizálnia a engedélyezése előtt.

## <a name="part-2-prepare-active-directory"></a>2. lépés: Az Active Directory előkészítése
A következő lépésekkel készítse elő az eszközök visszaírásához.

1. A számítógépről, amelyen telepítve van-e az Azure AD Connect indítsa el a Powershellt emelt jogosultsági szinttel.
2. Ha az Active Directory PowerShell-modulja nincs telepítve, telepítse a Távoli kiszolgálófelügyelet eszközei a AD PowerShell modult, és a parancsfájl futtatásához szükséges dsacls.exe tartalmazó.  Futtassa az alábbi parancsot:
  
   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Ha az Azure Active Directory PowerShell-modulja nincs telepítve, majd töltse le és telepítse innen [Active Directory modul Windows Powershellhez készült Azure (64 bites változat)](http://go.microsoft.com/fwlink/p/?linkid=236297). Ez az összetevő a bejelentkezési segéd, amely telepítve van az Azure AD Connect függőség rendelkezik.  
4. Vállalati rendszergazdai hitelesítő adataival a következő parancsokat, és zárja be a PowerShell.
   
   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

Vállalati rendszergazda hitelesítő adataival szükség, mert a konfiguráció névtérhez módosítások szükségesek. A tartományi rendszergazda nem lesz megfelelő engedélyekkel.

![Az eszközvisszaírás PowerShell](./media/active-directory-aadconnect-feature-device-writeback/powershell.png) d


Leírás:

* Ha már nem léteznek, hoz létre és konfigurálja az új tárolók és objektumok CN = Device Registration Configuration, CN = Services, CN = Configuration, [erdő-dn].
* Ha már nem léteznek, hoz létre és konfigurálja az új tárolók és objektumok CN = RegisteredDevices, [tartomány-dn]. Eszközobjektumok létrehozza a tárolóban található.
* Az Azure AD-összekötő fiók, az Active Directoryban lévő eszközök felügyeletéhez szükséges engedélyeket állít be.
* Csak kell futnia egy erdőt, akkor is, ha az Azure AD Connect van telepítve a több erdőt.

Paraméterek:

* Tartománynév: Active Directory-tartomány ahol eszközobjektumok létrejön. Megjegyzés: Minden eszközök adott Active Directory-erdő egyetlen tartományt létrejön.
* AdConnectorAccount: Active Directory-fiókot a címtárban található objektumokhoz kezelése az Azure AD Connect használandó. Ez az AD csatlakozni az Azure AD Connect szinkronizálási szolgáltatás által használt fiók. Ha telepítette a gyorsbeállítások használatával, a fiók MSOL_ előtagként is.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>3. lépés: Az Azure AD Connect engedélyezése eszközvisszaíró
A következő eljárással engedélyezése az Azure AD Connectben.

1. Futtassa újra a telepítési varázslót. Válassza ki **testre szabhatja a szinkronizálási beállítások** a feladatokat a lapon, majd kattintson **következő**.
   ![Egyéni telepítés testre szabhatja a szinkronizálási beállítások](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. A választható szolgáltatások lapon eszközvisszaíró már nem szürkén jelennek meg. Adjon, ha az Azure AD Connect előkészítő lépések nincsenek befejezett eszközvisszaíró Megjegyzés jelenítette ki a választható szolgáltatások lapon. Jelölje be a jelölőnégyzetet, az eszközök visszaírásához, és kattintson a **következő**. Ha a jelölőnégyzet továbbra is le van tiltva, tekintse meg a [hibaelhárítási szakaszában](#the-writeback-checkbox-is-still-disabled).
   ![Egyéni Eszközvisszaíró választható funkciók telepítése](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. A visszaírási lapon akkor jelenik meg a megadott tartomány as az alapértelmezett eszköz visszaírási erdő.
   ![Egyéni telepítés eszköz visszaírási célerdő](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. A varázsló további konfigurációs módosítások nélküli telepítésének befejezéséhez. Ha szükséges, tekintse meg a [az Azure AD Connect egyéni telepítése.](active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése
A forgatókönyv engedélyezésének részletes útmutatás belül érhető el [helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ellenőrizze az eszköz szinkronizált Active Directory
Eszközvisszaíró most már megfelelően fog működni. Vegye figyelembe, hogy az eszköz objektumokat írt vissza kell ad akár 3 órát is igénybe vehet.  Győződjön meg arról, hogy az eszközök vannak folyamatban megfelelően szinkronizálva, tegye a következőket a szinkronizálási szabályok befejezése után:

1. Indítsa el az Active Directory felügyeleti központ.
2. Bontsa ki a RegisteredDevices, a tartományban, hogy az összevont van folyamatban.
   ![Az Active Directory felügyeleti központ regisztrált eszközök](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. Aktuális regisztrált eszközök nem jelennek meg.
   ![Az Active Directory felügyeleti központ regisztrált eszközök listája](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

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
* [Kockázatkezelés feltételes hozzáférés](../active-directory-conditional-access.md)
* [A helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

