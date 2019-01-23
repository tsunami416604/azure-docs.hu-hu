---
title: 'Az Azure AD Connect szinkronizálása:  Az AD DS-fiók jelszavának módosítása |} A Microsoft Docs'
description: Ez a témakör a dokumentum ismerteti az Azure AD Connect frissítése után az AD DS-fiók jelszava megváltozott.
services: active-directory
keywords: Az AD DS-fiókot, az Active Directory-fiók, jelszó
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1b487651e938beadcada78eaf51b3f50beb7acf1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470537"
---
# <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
Az AD DS-fiókot az Azure AD Connect a helyszíni Active Directoryval való kommunikációhoz használt felhasználói fiók hivatkozik. Ha megváltoztatja a jelszót az AD DS-fiók, frissítenie kell az Azure AD Connect szinkronizálási szolgáltatás az új jelszóval. Ellenkező esetben a szinkronizálás a helyszíni Active Directoryval már nem szinkronizálhatók megfelelően, és fog történni hibák a következők:

* A Synchronization Service Managert, bármely importálási vagy exportálási művelet a helyszíni AD meghiúsul, és **-start – hitelesítő adatok nélkül** hiba.

* A Windows eseménynaplót, az alkalmazás eseménynaplója tartalmaz hibát **Event ID 6000** és az üzenet **"a"contoso.com"kezelőügynök futtatása sikertelen, mert a hitelesítő adatok érvénytelenek voltak a"**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>A Synchronization Service frissítése az új jelszót az AD DS-fiókot
A Synchronization Service frissítése az új jelszóval:

1. Indítsa el a Synchronization Service Managert (KEZDŐ → szinkronizálási szolgáltatás).
</br>![Sync Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Nyissa meg a **összekötők** fülre.

3. Válassza ki a **AD Connectoron** , amely megfelel a az AD DS-fiókot, amelynek a jelszava megváltozott.

4. A **műveletek**válassza **tulajdonságok**.

5. Az előugró párbeszédpanelen válassza ki a **csatlakozhat az Active Directory-erdő**:

6. Adja meg az új jelszót az AD DS-fiók a **jelszó** szövegmezőbe.

7. Kattintson a **OK** az új jelszó mentéséhez és a felugró párbeszédpanel bezárásához.

8. Indítsa újra az Azure AD Connect szinkronizálási szolgáltatás a Windows szolgáltatásvezérlő. Ez azért szükséges, hogy a régi jelszó minden hivatkozás, a memória-gyorsítótárból törlődik.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
