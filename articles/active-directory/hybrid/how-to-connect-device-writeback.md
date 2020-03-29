---
title: 'Azure AD Connect: Eszközvisszaírás engedélyezése | Microsoft dokumentumok'
description: Ez a dokumentum részletezi, hogyan engedélyezheti az eszköz-visszaírást az Azure AD Connect használatával
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
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109507"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Eszköz-visszaírás engedélyezése
> [!NOTE]
> Az Azure AD Premium előfizetése szükséges az eszköz visszaírásához.
> 
> 

Az alábbi dokumentáció az azure AD Connect eszköz-visszaírási szolgáltatásának engedélyezéséről tartalmaz tájékoztatást. Az eszközvisszaírás a következő esetekben használatos:

* A Windows Hello vállalati verzió engedélyezése [hibrid tanúsítványmegbízhatósági telepítéssel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Feltételes hozzáférés engedélyezése az ADFS (2012 R2 vagy újabb) védett alkalmazások (függő entitás megbízhatóságai) eszközök alapján.

Ez további biztonságot és biztosítékot nyújt arra nézve, hogy az alkalmazásokhoz való hozzáférés csak megbízható eszközök számára biztosított. A feltételes hozzáférésről a [Kockázat kezelése feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md) és a helyszíni feltételes hozzáférés beállítása az Azure Active [Directory-eszközregisztrációval című](../../active-directory/active-directory-device-registration-on-premises-setup.md)témakörben talál.

> [!IMPORTANT]
> <li>Az eszközöknek ugyanabban az erdőben kell lenniük, mint a felhasználóknak. Mivel az eszközöket egyetlen erdőbe kell visszaírni, ez a szolgáltatás jelenleg nem támogatja a több felhasználói erdővel rendelkező központi telepítést.</li>
> <li>A helyszíni Active Directory erdőhöz csak egy eszközregisztrációs konfigurációs objektum adható hozzá. Ez a szolgáltatás nem kompatibilis egy olyan topológiával, ahol a helyszíni Active Directory több Azure AD-könyvtárral van szinkronizálva.</li>

## <a name="part-1-install-azure-ad-connect"></a>1. rész: Az Azure AD Connect telepítése
Telepítse az Azure AD Connectet egyéni vagy expressz beállításokkal. A Microsoft azt javasolja, hogy az eszközvisszaírás engedélyezése előtt az összes sikeresen szinkronizált felhasználóval és csoporttal kezdje.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>2. rész: Eszköz-visszaírás engedélyezése az Azure AD Connectben
1. Futtassa újra a telepítővarázslót. Válassza az **Eszközbeállítások konfigurálása** lehetőséget a További feladatok lapon, majd kattintson a **Tovább**gombra. 

    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Az új Eszközkonfigurálás idáig csak az 1.1.819.0-s és újabb verzióban érhető el.

2. Az eszközbeállítások lapon válassza az **Eszközvisszaírás konfigurálása**lehetőséget. Az **eszközvisszaírás letiltása** nem lesz elérhető, amíg az eszköz visszaírása nincs engedélyezve. Kattintson a **Tovább** gombra a varázsló következő oldalára lépéshez.
    ![Eszközművelet beállítása](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. A visszaíró lapon a megadott tartomány lesz az alapértelmezett Device writeback erdő.
   ![Egyéni telepítés eszköz visszaírási célerdő](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Az eszköztárolólap** a következő két lehetőség valamelyikével készülheti elő az active directoryt:

    a. **Vállalati rendszergazdai hitelesítő adatok megadása:** Ha a vállalati rendszergazdai hitelesítő adatok vannak megadva az erdő, ahol eszközöket kell visszaírni, az Azure AD Connect automatikusan előkészíti az erdőt az eszköz-visszaírás konfigurálása során.

    b. **PowerShell-parancsfájl letöltése:** Az Azure AD Connect automatikusan létrehoz egy PowerShell-parancsfájlt, amely előkészíti az active directoryt az eszköz visszaírására. Abban az esetben, ha a vállalati rendszergazdai hitelesítő adatok nem adhatók meg az Azure AD Connect, javasoljuk, hogy töltse le a PowerShell-parancsfájlt. Adja meg a letöltött PowerShell-parancsfájl **CreateDeviceContainer.psq** az erdő vállalati rendszergazdájának, ahová az eszközöket visszaírják.
    ![Active directory erdő előkészítése](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Az active directory erdő előkészítéséhez a következő műveleteket hajtja végre:
    * Ha még nem léteznek, új tárolókat és objektumokat hoz létre és konfigurál a CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] csoportban.
    * Ha még nem léteznek, új tárolókat és objektumokat hoz létre és konfigurál a CN=RegisteredDevices,[domain-dn] alatt. Ebben a tárolóban eszközobjektumok jönnek létre.
    * Beállítja a szükséges engedélyeket az Azure AD Connector-fiók, az Active Directory eszközeinek kezeléséhez.
    * Csak akkor kell futtatni egy erdőben, akkor is, ha az Azure AD Connect több erdőre van telepítve.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Eszközök szinkronizálásának ellenőrzése az Active Directoryval
Az eszköz visszaírásának most már megfelelően kell működnie. Ne feledje, hogy akár 3 órát is igénybe vehet, amíg az eszközobjektumokat visszaírják az AD-be.  Az eszközök megfelelő szinkronizálásának ellenőrzéséhez tegye a következőket a szinkronizálási szabályok befejezése után:

1. Indítsa el az Active Directory felügyeleti központot.
2. Expand RegisteredDevices, a domain, amely összevont.

   ![Az Active Directory Felügyeleti központ regisztrált eszközei](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Az aktuálisan regisztrált eszközök ott jelennek meg.

   ![Az Active Directory Felügyeleti központ regisztrált eszközök listája](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése
A forgatókönyv engedélyezéséhez részletes utasítások érhetők el [a helyszíni feltételes hozzáférés beállítása az Azure Active Directory-eszközregisztráció használatával](../../active-directory/active-directory-device-registration-on-premises-setup.md)című dokumentumban.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="the-writeback-checkbox-is-still-disabled"></a>A visszaírás jelölőnégyzet továbbra is le van tiltva
Ha az eszközvisszaírás jelölőnégyzete nincs engedélyezve annak ellenére, hogy követte a fenti lépéseket, a következő lépések végigvezetik a rról, hogy a telepítővarázsló mit ellenőriz a jelölőnégyzet engedélyezése előtt.

Először is:

* Annak az erdőnek, ahol az eszközök találhatók, az erdősémát Windows 2012 R2 szintre kell frissíteni, hogy az eszközobjektum és a kapcsolódó attribútumok jelen legyenek.
* Ha a telepítővarázsló már fut, a rendszer nem észleli a módosításokat. Ebben az esetben fejezze be a telepítővarázslót, és futtassa újra.
* Győződjön meg arról, hogy az inicializálási parancsfájlban megadott fiók valóban a megfelelő felhasználó, amelyet az Active Directory-összekötő használ. Ennek ellenőrzéséhez kövesse az alábbi lépéseket:
  * A start menüben nyissa meg a **Szinkronizálási szolgáltatás megnyitását.**
  * Nyissa meg az **Összekötők** lapot.
  * Keresse meg az Active Directory tartományi szolgáltatások típusú összekötőt, és jelölje ki.
  * A **Műveletek csoportban**válassza a **Tulajdonságok lehetőséget.**
  * Nyissa meg **a Csatlakozás az Active Directory erdőhöz (Csatlakozás az Active Directory erdőhöz ) (Csatlakozás az Active Directory erdőhöz ) elemre** Ellenőrizze, hogy a képernyőn megadott tartomány és felhasználónév megegyezik-e a parancsfájlhoz megadott fiókkal.
    ![Összekötő fiók a Szinkronizálási szolgáltatáskezelőben](./media/how-to-connect-device-writeback/connectoraccount.png)

Konfiguráció ellenőrzése az Active Directoryban:

* Ellenőrizze, hogy az eszközregisztrációs szolgáltatás az alábbi helyen található-e (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) konfigurációs névhasználati környezetben.

![DeviceRegistrationService hibaelhárítás a konfigurációs névtérben](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Ellenőrizze, hogy csak egy konfigurációs objektum van-e a konfigurációs névtérben való kereséssel. Ha egynél több van, törölje a másolatot.

![Hibaelhárítás, a duplikált objektumok keresése](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Az Eszközregisztrációs szolgáltatás objektumon győződjön meg arról, hogy az msDS-DeviceLocation attribútum jelen van, és rendelkezik értékkel. Keresse meg ezt a helyet, és győződjön meg arról, hogy az msDS-DeviceContainer objectType azonosítóval van el.

![Hibaelhárítás, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Hibaelhárítás, RegisteredDevices objektumosztály](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ellenőrizze, hogy az Active Directory-összekötő által használt fiók rendelkezik-e az előző lépésben talált Regisztrált eszközök tárolóval kapcsolatos szükséges engedélyekkel. Ez a tároló várt engedélyei:

![Hibaelhárítás, a tárolóra vonatkozó engedélyek ellenőrzése](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ellenőrizze, hogy az Active Directory-fiók rendelkezik-e a CN=Device Registration Configuration,CN=Services,CN=Configuration objektummal kapcsolatos engedélyekkel.

![Hibaelhárítás, engedélyek ellenőrzése az eszközregisztrációs konfigurációhoz](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>További információ
* [Kockázatok kezelése feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)
* [Helyszíni feltételes hozzáférés beállítása az Azure Active Directory-eszközregisztráció használatával](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

