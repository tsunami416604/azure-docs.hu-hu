---
title: "Azure AD Connect szinkronizálása: az Azure AD Connect szinkronizálási szolgáltatás fiók módosítása |} Microsoft Docs"
description: "Ez a témakör a dokumentum útmutatást nyújt a titkosítási kulcsot és abandon azt a jelszó módosítása után."
services: active-directory
keywords: "Azure AD sync szolgáltatás-fiók, jelszó"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: bf6234d0810f870909957ee1c1e33c225a4922b9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavának módosítása
Ha módosítja a Azure AD Connect szinkronizálási szolgáltatás fiók jelszavát, a szinkronizálási szolgáltatás nem lesz képes start megfelelően félbehagyná a titkosítási kulcs, és újra lesz inicializálva a Azure AD Connect szinkronizálási szolgáltatás fiók jelszavát. 

Az Azure AD Connect, a szinkronizálási szolgáltatások részeként titkosítási kulcs tárolására használja az Active Directory tartományi szolgáltatások és az Azure AD a szolgáltatásfiókok jelszavait.  Ezeket a fiókokat ahhoz, azok vannak tárolva az adatbázisban titkosított. 

A használt titkosítási kulcs használatával lett biztonságossá téve [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI védi, a titkosítási kulcs használatával a **az Azure AD Connect szinkronizálási szolgáltatás fiók jelszavát**. 

Ha a szolgáltatásfiók jelszavát módosítani kell az eljárásokkal [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs kivonásának](#abandoning-the-azure-ad-connect-sync-encryption-key) ehhez.  Ezekkel az eljárásokkal kell is használható, ha bármilyen okból a titkosítási kulcs abandon kell.

##<a name="issues-that-arise-from-changing-the-password"></a>A jelszó módosításának felmerülő problémák
Két dolgot kell elvégezni, ha módosítja a szolgáltatás fiók jelszavát.

Először módosítani szeretné a jelszó alapján a Windows szolgáltatásvezérlő kezelőjétől.  A probléma megoldásáig jelenik meg a következő hibák:


- Indítsa el a Synchronization Service Windows szolgáltatásvezérlő kísérli meg, ha a hibaüzenet "**Windows nem sikerült elindítani a Microsoft Azure AD Sync szolgáltatás a helyi számítógépen**". **1069. hiba: A szolgáltatás nem indult el bejelentkezési hiba miatt.** "
- A Windows Eseménynapló, a rendszer eseménynaplójában található hiba **Event ID 7038** és az üzenet "**az ADSync szolgáltatás nem tudott bejelentkezni csakúgy, mint a jelenleg konfigurált jelszóval a következő hiba miatt: A felhasználó név vagy a jelszó nem megfelelő.** "

Második meghatározott feltételek mellett frissítése után a jelszót, a szinkronizálási szolgáltatást is már nem beolvasni a titkosítási kulcsot DPAPI keresztül. A titkosítási kulcs nélkül a szinkronizálási szolgáltatás nem tudja visszafejteni a jelszavak szinkronizálása vagy a helyszíni AD és az Azure AD szükséges.
Hibák például jelenik meg:

- A Windows szolgáltatásvezérlő, ha a szinkronizálási szolgáltatás indításakor, és nem tudja lekérni a titkosítási kulcs sikertelen a következő hiba "** a Windows nem sikerült elindítani a Microsoft Azure AD Sync helyi számítógépen. További információkért tekintse át a rendszer-eseménynaplóban találhatók. Ha ez egy nem Microsoft-szolgáltatás, a szállítót, és a szolgáltatásspecifikus hibakódot **-21451857952 x. "
- A Windows eseménynaplóban, az alkalmazások eseménynaplójában keresse meg a hibát tartalmaz **Event ID 6028** és hibaüzenet *"**nem érhető el a kiszolgáló titkosítási kulcsához.* *"*

Győződjön meg arról, hogy nem jelenik meg ezeket a hibákat, kövesse az ismertetett [az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs kivonásának](#abandoning-the-azure-ad-connect-sync-encryption-key) Ha megváltoztatta a jelszavát.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Az Azure AD Connect szinkronizálási szolgáltatás titkosítási kulcs megszakítása
>[!IMPORTANT]
>A következő eljárásokat csak alkalmazni az Azure AD Connect build 1.1.443.0 vagy annál régebbi.

Az alábbi eljárásokkal kénytelen volt megszakítani ezt a titkosítási kulcs.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Mi a teendő, ha szüksége kénytelen volt megszakítani ezt a titkosítási kulcs

Ha a titkosítási kulcs abandon van szüksége, az alábbi eljárásokkal ehhez.

1. [Szakítsa meg a meglévő titkosítási kulcs](#abandon-the-existing-encryption-key)

2. [Adja meg az Active Directory tartományi szolgáltatások fiók jelszavát](#provide-the-password-of-the-ad-ds-account)

3. [Inicializálja újra az Azure AD sync fiók jelszavát](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Indítsa el a Synchronization Service](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Szakítsa meg a meglévő titkosítási kulcs
A meglévő titkosítási kulcs Abandon, így az új titkosítási kulcs is létrehozható:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect-kiszolgáló.

2. Indítson el egy új PowerShell-munkamenetet.

3. Navigáljon a következő mappához:`$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Futtassa a parancsot:`./miiskmu.exe /a`

![Az Azure AD Connect Sync titkosítási kulcs segédprogram](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Adja meg az Active Directory tartományi szolgáltatások fiók jelszavát
A meglévő az adatbázisban tárolt jelszavak már nem lehet visszafejteni, mert meg kell adnia a szinkronizálási szolgáltatás az Active Directory tartományi szolgáltatások fiók jelszavát. A szinkronizálási szolgáltatás titkosítja a jelszavakat az új titkosítási kulcs használatával:

1. Indítsa el a Synchronization Service Managert (KEZDŐ → szinkronizálási szolgáltatás).
</br>![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Lépjen a **összekötők** fülre.
3. Válassza ki a **Címtárösszekötőben** , amely megfelel a helyszíni AD. Ha több AD-összekötő, esetében ismételje meg az alábbi lépéseket.
4. A **műveletek**, jelölje be **tulajdonságok**.
5. Az előugró párbeszédpanelen válassza ki a **csatlakozás az Active Directory-erdő**:
6. Adja meg az AD DS-fiókjához tartozó jelszót a **jelszó** szövegmező. Ha nem ismeri a jelszavát, be kell állítani egy ismert értékre a lépés végrehajtása előtt.
7. Kattintson a **OK** az új jelszó mentse és zárja be az előugró párbeszédpanelen.
![Az Azure AD Connect Sync titkosítási kulcs segédprogram](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Inicializálja újra az Azure AD sync fiók jelszavát
A szinkronizálási szolgáltatás közvetlenül nem adja meg az Azure AD szolgáltatás fiók jelszavát. Ehelyett használja a parancsmagot kell **Add-ADSyncAADServiceAccount** újrainicializálni az Azure AD szolgáltatás-fiókot. A parancsmag a fiók jelszavának alaphelyzetbe állítása, és lehetővé teszi a szinkronizálási szolgáltatás:

1. Új PowerShell-munkamenet el az Azure AD Connect-kiszolgáló.
2. Parancsmag futtatásához `Add-ADSyncAADServiceAccount`.
3. Az előugró párbeszédpanelen adja meg az Azure AD globális rendszergazda hitelesítő adatait az Azure AD-bérlő.
![Az Azure AD Connect Sync titkosítási kulcs segédprogram](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Ha sikeres, megjelenik a PowerShell-parancssort.

#### <a name="start-the-synchronization-service"></a>Indítsa el a Synchronization Service
Most, hogy a szinkronizálási szolgáltatás hozzáfér a titkosítási kulcsot és a szükséges jelszavak, újraindíthatja a szolgáltatást a a Windows szolgáltatásvezérlő:


1. Nyissa meg a Windows szolgáltatásvezérlő kezelőjéhez (KEZDŐ → szolgáltatások).
2. Válassza ki **Microsoft Azure AD Sync** és levő Újraindítás lehetőségre.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
