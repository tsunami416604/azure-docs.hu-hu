---
title: 'Azure AD Connect szinkronizálása: Az ADSync szolgáltatásfiók módosítása | Microsoft dokumentumok'
description: Ez a témakör ismerteti a titkosítási kulcsot, és hogyan lehet elhagyni azt a jelszó módosítása után.
services: active-directory
keywords: Azure AD szinkronizálási szolgáltatásfiók, jelszó
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
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077671ab4e964d7641aa3a0f0b435b39117eb6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65139395"
---
# <a name="changing-the-adsync-service-account-password"></a>Az ADSync szolgáltatásfiók jelszavának módosítása
Ha módosítja az ADSync szolgáltatásfiók jelszavát, a szinkronizálási szolgáltatás nem fog tudni helyesen elindulni, amíg fel nem hagyott a titkosítási kulccsal, és újra nem inicializálta az ADSync szolgáltatásfiók jelszavát. 

Az Azure AD Connect a szinkronizálási szolgáltatások részeként egy titkosítási kulcsot használ az AD DS Connector-fiók és az ADSync szolgáltatásfiók jelszavainak tárolására.  Ezek a fiókok titkosítva vannak, mielőtt az adatbázisban tárolnák őket. 

A használt titkosítási kulcs a [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx)segítségével védett. A DPAPI az **ADSync szolgáltatásfiókkal**védi a titkosítási kulcsot. 

Ha módosítania kell a szolgáltatásfiók jelszavát, ehhez [használhatja az ADSync szolgáltatásfiók titkosítási kulcsának elhagyása](#abandoning-the-adsync-service-account-encryption-key) című eljárásban található eljárásokat.  Ezeket az eljárásokat akkor is alkalmazni kell, ha bármilyen okból el kell hagynia a titkosítási kulcsot.

## <a name="issues-that-arise-from-changing-the-password"></a>A jelszó módosításából eredő problémák
A szolgáltatásfiók jelszavának módosításakor két dolgot kell elvégezni.

Először meg kell változtatnia a jelszót a Windows Szolgáltatásvezérlő alatt.  Amíg ez a probléma meg nem oldódik, a következő hibák jelennek meg:


- Ha megpróbálja elindítani a Szinkronizálási szolgáltatást a Windows Szolgáltatásvezérlőben, a következő hibaüzenet jelenik meg:**"A Windows nem tudta elindítani a Microsoft Azure AD Sync szolgáltatást a helyi számítógépen".** **1069-es hiba: A szolgáltatás bejelentkezési hiba miatt nem indult el.**"
- A Windows Eseménynapló csoportban a rendszer eseménynaplója a **7038-as azonosítójú és** a következő üzenettel kapcsolatos hibaüzenetet**tartalmazza: "Az ADSync szolgáltatás a következő hiba miatt nem tudott ugyanúgy bejelentkezni, mint az aktuálisan konfigurált jelszóval: A felhasználónév vagy a jelszó helytelen.**"

Másodszor, bizonyos feltételek mellett, ha a jelszó frissül, a szinkronizálási szolgáltatás már nem tudja letölteni a titkosítási kulcsot a DPAPI-n keresztül. A titkosítási kulcs nélkül a szinkronizálási szolgáltatás nem tudja visszafejteni a szinkronizáláshoz szükséges jelszavakat a helyszíni AD és az Azure AD.
Olyan hibák jelennek meg, mint például:

- A Windows Szolgáltatásvezérlés-kezelő csoportban, ha megpróbálja elindítani a szinkronizálási szolgáltatást, és nem tudja beolvasni a titkosítási kulcsot, a következő hibaüzenet telik el: "<strong>A Windows nem tudta elindítani a Microsoft Azure AD szinkronizálást a helyi számítógépen. További információért tekintse át a rendszer eseménynaplóját. Ha ez egy nem microsoftos szolgáltatás, forduljon a szolgáltatóhoz, és olvassa el a szolgáltatásspecifikus hibakódot :21451857952</strong>."
- A Windows Eseménynapló ban az alkalmazás eseménynaplója a **6028-as azonosítójú eseményt** és a következő hibaüzenetet tartalmazza: *"A kiszolgáló titkosítási kulcsa nem érhető el."*

Annak érdekében, hogy ne kapja meg ezeket a hibákat, kövesse [az ADSync szolgáltatásfiók titkosítási kulcsának elvetése](#abandoning-the-adsync-service-account-encryption-key) a jelszó módosításakor című eljárást.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Az ADSync szolgáltatásfiók titkosítási kulcsának elhagyása
>[!IMPORTANT]
>Az alábbi eljárások csak az Azure AD Connect 1.1.443.0-s vagy régebbi buildre vonatkoznak.

A titkosítási kulcs megszüntetéséhez kövesse el az alábbi eljárásokat.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Mi a teendő, ha el kell hagynia a titkosítási kulcsot?

Ha el kell hagynia a titkosítási kulcsot, az alábbi eljárásokkal ezt hajtsa végre.

1. [A szinkronizálási szolgáltatás leállítása](#stop-the-synchronization-service)

1. [A meglévő titkosítási kulcs elhagyása](#abandon-the-existing-encryption-key)

2. [Az AD DS-összekötő fiók jelszavának megadása](#provide-the-password-of-the-ad-ds-connector-account)

3. [Az ADSync szolgáltatásfiók jelszavának újrainicializálása](#reinitialize-the-password-of-the-adsync-service-account)

4. [A szinkronizálási szolgáltatás indítása](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>A szinkronizálási szolgáltatás leállítása
Először leállíthatja a szolgáltatást a Windows szolgáltatásvezérlőben.  Győződjön meg arról, hogy a szolgáltatás nem fut, amikor megpróbálja megállítani.  Ha ez az, várja meg, amíg befejeződik, majd állítsa le.


1. Nyissa meg a Windows szolgáltatásvezérlés-kezelőt (START → Services).
2. Válassza a **Microsoft Azure AD Sync lehetőséget,** és kattintson a Leállítás gombra.

#### <a name="abandon-the-existing-encryption-key"></a>A meglévő titkosítási kulcs elhagyása
Hagyja el a meglévő titkosítási kulcsot, hogy új titkosítási kulcs hozható létre:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect kiszolgálóra.

2. Új PowerShell-munkamenet indítása.

3. Navigálás a mappába:`$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Futtassa a parancsot:`./miiskmu.exe /a`

![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogramja](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Az AD DS-összekötő fiók jelszavának megadása
Mivel az adatbázisban tárolt meglévő jelszavak már nem fejthetők vissza, a szinkronizálási szolgáltatásnak meg kell adnia az AD DS-összekötő fiók jelszavát. A szinkronizálási szolgáltatás titkosítja a jelszavakat az új titkosítási kulcs használatával:

1. Indítsa el a Szinkronizálási szolgáltatáskezelőt (START → Szinkronizálási szolgáltatás).
</br>![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Nyissa meg az **Összekötők** lapot.
3. Válassza ki a helyszíni AD-nek megfelelő **AD-összekötőt.** Ha egynél több AD-összekötővel rendelkezik, ismételje meg a következő lépéseket mindegyiknél.
4. A **Műveletek csoportban**válassza a **Tulajdonságok lehetőséget.**
5. Az előugró párbeszédpanelen válassza a **Csatlakozás az Active Directory erdőhöz lehetőséget:**
6. Írja be az AD DS-fiók jelszavát a **Jelszó** mezőbe. Ha nem ismeri a jelszavát, a lépés végrehajtása előtt be kell állítania egy ismert értéket.
7. Az új jelszó mentéséhez és az előugró párbeszédpanel bezárásához kattintson az **OK** gombra.
![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogramja](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Az ADSync szolgáltatásfiók jelszavának újrainicializálása
Közvetlenül nem adja meg az Azure AD szolgáltatásfiók jelszavát a szinkronizálási szolgáltatásnak. Ehelyett az **Add-ADSyncAADServiceAccount** parancsmag használatával újra kell indítania az Azure AD szolgáltatásfiókot. A parancsmag alaphelyzetbe állítja a fiók jelszavát, és elérhetővé teszi a szinkronizálási szolgáltatás számára:

1. Új PowerShell-munkamenet indítása az Azure AD Connect-kiszolgálón.
2. Futtassa `Add-ADSyncAADServiceAccount`a parancsmagát .
3. Az előugró párbeszédpanelen adja meg az Azure AD globális rendszergazdai hitelesítő adatait az Azure AD-bérlőhöz.
![Az Azure AD Connect szinkronizálási titkosítási kulcs segédprogramja](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Ha sikeres, megjelenik a PowerShell parancssor.

#### <a name="start-the-synchronization-service"></a>A szinkronizálási szolgáltatás indítása
Most, hogy a szinkronizálási szolgáltatás hozzáfér a titkosítási kulcshoz és az összes szükséges jelszóhoz, újraindíthatja a szolgáltatást a Windows szolgáltatásvezérlőben:


1. Nyissa meg a Windows szolgáltatásvezérlés-kezelőt (START → Services).
2. Válassza a **Microsoft Azure AD Sync lehetőséget,** és kattintson az Újraindítás gombra.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
