---
title: 'Az Azure AD Connect szinkronizálása:  Az Azure AD Connect szinkronizálási szolgáltatás szolgáltatásfiók módosítása |} A Microsoft Docs'
description: Ez a témakör a dokumentum a titkosítási kulcsot és a jelszó módosítása után feloldandó zárolású ismerteti.
services: active-directory
keywords: Az Azure AD sync szolgáltatásfiók, a jelszót
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d0d537a23e21eeda3b284e7ec706cde2b443e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014077"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának módosítása
Az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának módosítása esetén a szinkronizálási szolgáltatás nem lesz képes kezdő megfelelően mindaddig, amíg a titkosítási kulcs hagyva, és az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának újrainicializálva. 

Az Azure AD Connect, a szinkronizálási szolgáltatások részeként titkosítási kulcs tárolására használja az AD DS és az Azure AD a szolgáltatásfiókok jelszavait.  Ezek a fiókok titkosításának megkövetelése azok tárolódnak az adatbázisban. 

A használt titkosítási kulcs használatával lett biztonságossá téve [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI védi a titkosítási kulcs használatával a **az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának**. 

Ha a fiók jelszavát módosítani kell az eljárásokkal [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs megszakítása](#abandoning-the-azure-ad-connect-sync-encryption-key) ehhez.  Ezekkel az eljárásokkal kell is használható, ha kénytelen volt megszakítani ezt a titkosítási kulcs bármilyen okból van szüksége.

## <a name="issues-that-arise-from-changing-the-password"></a>A jelszó módosítása a felmerülő problémákat
Két dolgot kell végrehajtani, amikor módosítja a szolgáltatásfiók jelszavát.

Először a csoportban a Windows szolgáltatásvezérlő jelszó módosítására.  A probléma elhárításáig nem jelenik meg a következő hibák:


- Indítsa el a Synchronization Service Windows szolgáltatásvezérlő meg, ha a hibaüzenetet kapja "**Windows nem tudta elindítani a Microsoft Azure AD Sync szolgáltatás a helyi számítógépen**". **1069. hiba: A szolgáltatás nem indult el a bejelentkezési hiba miatt.** "
- A Windows eseménynaplót, a rendszer eseménynaplójában található hiba **Event ID 7038** és az üzenet "**az ADSync szolgáltatás nem tudott bejelentkezni, csakúgy, mint a jelenleg konfigurált jelszóval a következő hiba miatt: A felhasználónév vagy jelszó helytelen.** "

Második bizonyos körülmények között, ha frissítik a jelszót, a szinkronizálási szolgáltatás már nem lekérheti a titkosítási kulcs DPAPI-n keresztül. A titkosítási kulcs nélkül a szinkronizálási szolgáltatás nem tudja visszafejteni a jelszavak szinkronizálása és-tárolókról a helyszíni AD és az Azure AD szükséges.
Hibák például jelenik meg:

- A Windows szolgáltatásvezérlőben, ha a szinkronizálási szolgáltatás indításakor, és nem tudja lekérni a titkosítási kulcs, sikertelen, és "<strong>Windows nem tudta elindítani a Microsoft Azure AD Sync a helyi számítógépen. További információkért tekintse át a rendszer-eseménynaplójában. Ha ez egy nem Microsoft-szolgáltatás, a szállítót, és a szolgáltatásspecifikus hibakódhoz-21451857952</strong>. "
- A Windows eseménynaplót, az alkalmazás eseménynaplója tartalmaz hibát **Event ID 6028** és hibaüzenet *"a kiszolgáló titkosítási kulcs nem lehet elérni."*

Győződjön meg arról, hogy nem jelenik meg ezeket a hibákat, kövesse az ismertetett [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs megszakítása](#abandoning-the-azure-ad-connect-sync-encryption-key) a jelszó módosításakor.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs megszakítása
>[!IMPORTANT]
>A következő eljárásokat csak alkalmazni az Azure AD Connect build 1.1.443.0 vagy régebbi.

Az alábbi eljárásokkal kénytelen volt megszakítani ezt a titkosítási kulcs.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Mi a teendő, ha kénytelen volt megszakítani ezt a titkosítási kulcs van szüksége?

Ha kénytelen volt megszakítani ezt a titkosítási kulcs van szüksége, az alábbi eljárásokkal ehhez.

1. [A szinkronizálási szolgáltatás leállítása](#stop-the-synchronization-service)

1. [A meglévő titkosítási kulcs Abandon](#abandon-the-existing-encryption-key)

2. [Adja meg a jelszót az AD DS-fiók](#provide-the-password-of-the-ad-ds-account)

3. [Az Azure AD sync szolgáltatásfiók jelszavának újrainicializálása](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Indítsa el a Synchronization Service](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>A szinkronizálási szolgáltatás leállítása
Először is állítsa le a szolgáltatást a a Windows szolgáltatásvezérlő kezelőjétől.  Győződjön meg arról, hogy a szolgáltatás nem fut, leállítására tett kísérlet közben.  Ha igen, várjon, amíg befejeződik, majd állítsa le.


1. Nyissa meg a Windows szolgáltatásvezérlőhöz (KEZDŐ → szolgáltatások).
2. Válassza ki **a Microsoft Azure AD Sync** , és kattintson a Leállítás gombra.

#### <a name="abandon-the-existing-encryption-key"></a>A meglévő titkosítási kulcs Abandon
A meglévő titkosítási kulcs Abandon, így a új titkosítási kulcs hozható létre:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect kiszolgáló.

2. Indítsa el egy új PowerShell-munkamenetet.

3. Lépjen a mappába: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Futtassa a parancsot: `./miiskmu.exe /a`

![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogram](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Adja meg a jelszót az AD DS-fiók
Ahogy az adatbázisban tárolt meglévő jelszavak már nem lehet visszafejteni, meg kell adnia a szinkronizálási szolgáltatás jelszót az AD DS-fiókot. A szinkronizálási szolgáltatás titkosítja a jelszavakat az új titkosítási kulcs használatával:

1. Indítsa el a Synchronization Service Managert (KEZDŐ → szinkronizálási szolgáltatás).
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Nyissa meg a **összekötők** fülre.
3. Válassza ki a **AD Connectoron** , amely megfelel a helyszíni AD. Ha egynél több AD-összekötő, mindegyikhez ismételje meg a következő lépéseket.
4. A **műveletek**válassza **tulajdonságok**.
5. Az előugró párbeszédpanelen válassza ki a **csatlakozhat az Active Directory-erdő**:
6. Adja meg a jelszót az AD DS-fiók a **jelszó** szövegmezőbe. Ha nem ismeri a jelszavát, be kell állítani egy ismert értékre a lépés végrehajtása előtt.
7. Kattintson a **OK** az új jelszó mentéséhez és a felugró párbeszédpanel bezárásához.
![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogram](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Az Azure AD sync szolgáltatásfiók jelszavának újrainicializálása
A szinkronizálási szolgáltatás közvetlenül az Azure AD-szolgáltatásfiók jelszava nem tud biztosítani. Ehelyett használja a parancsmagot kell **Add-ADSyncAADServiceAccount** , inicializálja újra az Azure AD-szolgáltatásfiók. A parancsmag állítja vissza a fiók jelszavát, és elérhetővé teszi a a szinkronizálási szolgáltatás:

1. Az Azure AD Connect-kiszolgálón indítsa el a egy új PowerShell-munkamenetet.
2. A parancsmag futtatása `Add-ADSyncAADServiceAccount`.
3. Az előugró párbeszédpanelen adja meg az Azure AD globális rendszergazdai hitelesítő adatait az Azure AD-bérlővel.
![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogram](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Ha sikeres, megjelenik a PowerShell-parancssort.

#### <a name="start-the-synchronization-service"></a>Indítsa el a Synchronization Service
Most, hogy a szinkronizálási szolgáltatás hozzáfér a titkosítási kulcsot, és minden, a szükséges jelszavakat, újraindíthatja a szolgáltatást a a Windows szolgáltatásvezérlő:


1. Nyissa meg a Windows szolgáltatásvezérlőhöz (KEZDŐ → szolgáltatások).
2. Válassza ki **a Microsoft Azure AD Sync** , és kattintson az Újraindítás lehetőségre.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
