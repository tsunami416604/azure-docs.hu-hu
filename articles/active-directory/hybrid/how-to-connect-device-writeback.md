---
title: 'Azure AD Connect: Eszközvisszaírás engedélyezése |} A Microsoft Docs'
description: Ez a dokumentum részletesen bemutatja az Azure AD Connect használatával eszközvisszaírás engedélyezése
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
ms.openlocfilehash: 82ccbe8e57ff35904b7e763e838a81660ab13f88
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412818"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Eszközvisszaírás engedélyezése
> [!NOTE]
> Prémium szintű Azure AD-előfizetés eszközvisszaírás szükség.
> 
> 

A következő dokumentáció arról nyújt tájékoztatást az Azure AD Connect eszköz visszaírási funkciójának engedélyezése. Eszközvisszaírás szolgál a következő esetekben:

* Feltételes hozzáférési házirend alapján AD FS-eszközök engedélyezése (2012 R2 vagy újabb) által védett alkalmazások (függő entitások megbízhatóságainak).

Ez biztosítja a további biztonsági és biztosítható, hogy csak a megbízható eszközökhöz való engedélyezett alkalmazásokhoz való hozzáférést a rendszer. Feltételes hozzáféréssel kapcsolatos további információkért lásd: [kockázat kezelése feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md) és [helyszíni feltételes hozzáférés beállítása az Azure Active Directory Eszközregisztrációs](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Eszközök a felhasználók ugyanabban az erdőben kell működnie. Eszközök kell visszaírása az egyetlen erdővel, mivel ez a funkció jelenleg nem támogatja a több felhasználó erdő telepítésben.</li>
> <li>Csak egy eszköz regisztrációs konfigurációs objektumot a helyi Active Directory-erdőben lehet hozzáadni. Ez a funkció szoftvere nem kompatibilis, ahol a helyszíni Active Directory több Azure AD-címtár szinkronizált topológiákkal.</li>

## <a name="part-1-install-azure-ad-connect"></a>1. rész: Az Azure AD Connect telepítése
Telepítse az Azure AD Connect használatával egyéni vagy Gyorsbeállítások. A Microsoft azt javasolja, a kezdéshez minden felhasználó és csoport szinkronizálása sikeres-eszközvisszaírás engedélyezése előtt.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>2. rész: Az Azure AD Connectben eszközvisszaírás engedélyezése
1. Futtassa újra a telepítővarázslót. Válassza ki **eszközbeállítások konfigurálása** további műveletek az oldalon, és kattintson **tovább**. 

    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Az új konfigurálás eszközbeállítások, csak a 1.1.819.0 verzió vagy újabb.

2. Az eszköz beállítások lapon, válassza ki a **eszközvisszaírás konfigurálása**. Lehetőséget **eszközvisszaírás letiltási** nem lesznek elérhetők addig, amíg az eszközvisszaírás engedélyezve van. Kattintson a **tovább** Ugrás a varázsló következő lapjára.
    ![Kiválasztott eszköz művelet](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. A jelszóvisszaíró oldalon láthatja a megadott tartományhoz, az alapértelmezett eszköz-visszaírási erdő.
   ![Egyéni telepítés eszköz-visszaírási cél erdő](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Eszköztároló** oldal nyújt lehetőséget az active directory előkészítése a két rendelkezésre álló lehetőségek egyikének használatával:

    a. **Adja meg a vállalati rendszergazda hitelesítő adatait**: Ha a vállalati rendszergazdájának hitelesítő adatait az erdőben, ahol az eszközök visszaírása kell vannak megadva, az Azure AD Connect fogja az erdő előkészítésére automatikusan eszközvisszaírás konfigurálása során.

    b. **PowerShell-szkript letöltése**: Az Azure AD Connect automatikusan létrehozza egy eszközvisszaírás készítheti elő az active directory PowerShell-parancsfájlt. Abban az esetben a vállalati rendszergazdájának hitelesítő adatait az Azure AD Connect nem adható meg, javasolt a PowerShell-parancsprogram letöltése. Adja meg a letöltött PowerShell-parancsfájl **CreateDeviceContainer.psq** az erdőben, ahol eszközök visszaíródnak a vállalati rendszergazdájának.
    ![Active directory-erdő előkészítése](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    A következő műveleteket az active directory-erdő előkészítése:
    * Ha azok már nem létezik, hozza létre és konfigurálja az új tárolók és objektumok CN = Device Registration Configuration, CN = Services, CN = Configuration, [erdő-dn].
    * Ha azok már nem létezik, hozza létre és konfigurálja az új tárolók és objektumok CN = RegisteredDevices, [tartomány-dn]. Eszközobjektumok létrejön ebben a tárolóban.
    * Az Azure AD-összekötő fiók, az Active Directoryban lévő eszközök felügyeletéhez szükséges engedélyeket állít be.
    * Csak kell futnia egy erdőt, akkor is, ha az Azure AD Connect telepítése folyamatban van a több erdőt.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ellenőrizze, hogy az Active Directory szinkronizált eszközök
Eszközvisszaírás most kell megfelelően működik. Vegye figyelembe, hogy az eszközobjektumok az ad-hez írt vissza kell akár 3 órát is igénybe vehet.  Győződjön meg arról, hogy az eszközök megfelelően vannak szinkronizálódik, tegye a következőket a szinkronizálási szabályok befejezését követően:

1. Indítsa el az Active Directory felügyeleti központ.
2. Bontsa ki a RegisteredDevices, összevont folyamatban van a tartományon belül.

   ![Az Active Directory felügyeleti központ regisztrált eszközök](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Aktuális regisztrált eszközök nem jelennek meg.

   ![Az Active Directory felügyeleti központ regisztrált eszközök listája](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése
Részletes útmutatás a forgatókönyv engedélyezésének érhető el, [helyszíni feltételes hozzáférés beállítása az Azure Active Directory Eszközregisztrációs](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="the-writeback-checkbox-is-still-disabled"></a>A jelszóvisszaíró jelölőnégyzet továbbra is le van tiltva.
Eszközvisszaírás tartozó jelölőnégyzet nem érhető el, annak ellenére, hogy követte a fenti lépéseket, ha az alábbi lépéseket végigvezeti Mi a telepítővarázsló ellenőrzi a box engedélyezése előtt.

Először a lényeg első:

* Ellenőrizze, hogy legalább egy erdő Windows Server 2012 R2. Az eszköz objektumtípus elérhetőnek kell lennie.
* Ha a telepítővarázsló már fut, majd módosítások észlelése nem történik meg. Ebben az esetben befejezheti a telepítővarázsló, és futtassa újból.
* Ellenőrizze, hogy az inicializálás parancsfájl a megadott fióknak valójában az Active Directory-összekötő által használt megfelelő felhasználó. Ennek az ellenőrzéséhez kövesse az alábbi lépéseket:
  * Nyissa meg a start menüből **szinkronizálási szolgáltatás**.
  * Nyissa meg a **összekötők** fülre.
  * Keresse meg az összekötő típusú Active Directory Domain Servicesben, és válassza ki azt.
  * A **műveletek**válassza **tulajdonságok**.
  * Lépjen a **csatlakozni az Active Directory-erdő**. Győződjön meg arról, hogy a tartomány és a felhasználó nevét megadva az a képernyő egyezést a fiókkal, a parancsfájlt.
    ![A Sync Service Manager-összekötő-fiók](./media/how-to-connect-device-writeback/connectoraccount.png)

Az Active Directory konfigurációjának ellenőrzése:

* Győződjön meg arról, hogy az alábbi helyen található-e az Eszközregisztrációs szolgáltatás (CN DeviceRegistrationService, CN = eszköz regisztrációs Services, CN = Device Registration Configuration, CN = Services, CN = = Configuration) konfigurációs névhasználati környezetében.

![A hiba elhárításához configuration névtér DeviceRegistrationService](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Ellenőrizze, hogy csak egy konfigurációs objektumot a konfigurációs névtér kereséssel van. Ha egynél több, törölje a duplikált.

![Hibaelhárítás, az ismétlődő objektumok keresése](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Az Eszközregisztrációs szolgáltatás objektumon ellenőrizze, hogy, attribútum az msDS-DeviceLocation létezik, és egy értéket. Keresés a helyet, és ellenőrizze, hogy az az objectType msDS-DeviceContainer telepítve-e.

![Troubleshoot, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![A hiba elhárításához RegisteredDevices objektum osztálya](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ellenőrizze, hogy az Active Directory-összekötő által használt fiók rendelkezik a szükséges engedélyekkel a regisztrált eszközöket tárolójához az előző lépésben található. Ez az a tároló a várt engedélyek:

![Hibaelhárítás, ellenőrizze a tároló engedélyeit](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ellenőrizze, hogy az Active Directory-fiók engedélyekkel rendelkezik a CN = Device Registration Configuration, CN = Services, CN = konfigurációs objektumot.

![Hibaelhárítás, Eszközregisztráció konfigurációja az engedélyek ellenőrzése](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>További információk
* [Kockázatkezelés feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)
* [A helyszíni feltételes hozzáférés használata az Azure Active Directory Eszközregisztráció beállítása](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

