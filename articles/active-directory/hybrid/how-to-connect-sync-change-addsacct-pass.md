---
title: 'Azure AD Connect szinkronizálás: Az AD DS-fiók jelszavának módosítása | Microsoft dokumentumok'
description: Ez a témakör ismerteti, hogyan frissítheti az Azure AD Connect az AD DS-fiók jelszavának módosítása után.
services: active-directory
keywords: Active Directory Tartományi szolgáltatások fiókja, Active Directory-fiók, jelszó
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
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241596"
---
# <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
Az Active Directory tartományi szolgáltatások kal kapcsolatos fiók az Azure AD Connect által a helyszíni Active Directoryval való kommunikációhoz használt felhasználói fiókra hivatkozik. Ha módosítja az AD DS-fiók jelszavát, frissítenie kell az Azure AD Connect szinkronizálási szolgáltatást az új jelszóval. Ellenkező esetben a szinkronizálás már nem tud megfelelően szinkronizálni a helyszíni Active Directoryval, és a következő hibáklépnek fel:

* A Szinkronizálási szolgáltatáskezelőben a helyszíni AD-vel rendelkező importálási vagy exportálási műveletek **sikertelenek lesznek a nem induló hitelesítő adatokkal** kapcsolatos hibával.

* A Windows Eseménynapló csoportban az alkalmazás eseménynaplója a **6000-es azonosítójú** hibát tartalmazza, és **a "Contoso.com" felügyeleti ügynök nem futtatható, mert a hitelesítő adatok érvénytelenek voltak.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>A szinkronizálási szolgáltatás frissítése az AD DS-fiók új jelszavával
A szinkronizálási szolgáltatás frissítése az új jelszóval:

1. Indítsa el a Szinkronizálási szolgáltatáskezelőt (START → Szinkronizálási szolgáltatás).
</br>![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Nyissa meg az **Összekötők** lapot.

3. Válassza ki azt az **AD-összekötőt,** amely megfelel annak az AD DS-fióknak, amelynek jelszavát módosították.

4. A **Műveletek csoportban**válassza a **Tulajdonságok lehetőséget.**

5. Az előugró párbeszédpanelen válassza a **Csatlakozás az Active Directory erdőhöz lehetőséget:**

6. Írja be az AD DS-fiók új jelszavát a **Jelszó** mezőbe.

7. Az új jelszó mentéséhez és az előugró párbeszédpanel bezárásához kattintson az **OK** gombra.

8. Indítsa újra az Azure AD Connect szinkronizálási szolgáltatást a Windows Szolgáltatásvezérlő kezelője alatt. Ez biztosítja, hogy a régi jelszóra mutató hivatkozások törlődjenek a memória-gyorsítótárból.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
