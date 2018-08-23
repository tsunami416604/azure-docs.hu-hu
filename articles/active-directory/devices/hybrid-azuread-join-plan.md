---
title: Hogyan hibrid konfigurálása Azure Active Directoryhoz csatlakoztatott eszközök |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directoryhoz csatlakoztatott eszközökön.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fbc40d0768a7cf1cb83a3e78dd524fac26254bea
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060621"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>A hibrid Azure Active Directory join megvalósítás tervezése

Hasonló módon a felhasználó eszköz neve lesz a védelmét, és az erőforrások védelméhez bármely időben és helyen is használja egy másik identitás. A cél, és az eszközök identitásokat az Azure AD a következő módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Biztosítja az eszközök Azure ad-hez, a felhőalapú és helyszíni erőforrások a felhasználók hatékonyságát egyszeri bejelentkezés (SSO) maximalizálása meg. Egy időben, hozzáférés gondoskodhat a felhőbeli és helyszíni erőforrások [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md).

Ha rendelkezik a helyszíni Active Directory-környezetben, és azt szeretné, a tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-ben, ez elvégezhető a hibrid Azure AD-csatlakoztatott eszközök konfigurálásával. Ez a cikk ismerteti, és a kapcsolódó lépéseket kell végrehajtani egy hibrid Azure AD join a környezetben. 


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a [bemutatása az Eszközfelügyelet az Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A hibrid Azure AD-megvalósítás megtervezése, meg kell ismerkednie az:

|   |   |
|---|---|
|![Jelölőnégyzet][1]|Tekintse át a támogatott eszközök|
|![Jelölőnégyzet][1]|Felülvizsgálat tudnivalók|
|![Jelölőnégyzet][1]|A forgatókönyv kiválasztása|


 


## <a name="review-supported-devices"></a>Tekintse át a támogatott eszközök 

Hibrid Azure AD-csatlakozás egy széles körű tartomány a Windows-eszközöket támogatja. A konfiguráció a Windows korábbi verzióit futtató eszközök esetében további vagy különböző lépéseket igényel, mert a támogatott eszközök két kategóriákba vannak csoportosítva:

**Aktuális Windows-eszközök**

- Windows 10
    
- Windows Server 2016


A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verziója a Windows 10 Évfordulós frissítés (1607-es verzió) vagy újabb. Ajánlott eljárásként frissítse a Windows 10 legújabb verzióját.



 **Windows régebbi verziójú eszközök**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Az első tervezési lépés tekintse át a környezet és alapján eldöntheti, hogy kell-e Windows régebbi verziójú eszközök támogatásához.



## <a name="review-things-you-should-know"></a>Felülvizsgálat tudnivalók

A hibrid Azure AD-csatlakozás nem használható, ha a környezet áll, amelyek egynél több Azure AD-bérlőhöz azonosító adatok szinkronizálva egyetlen erdővel.

Megbízható függő entitásokat a rendszer-előkészítő eszköz (Sysprep), hogy képeket hoz létre, amely nincs beállítva a hibrid Azure AD-csatlakozás a Windows telepítésére.

Ha további virtuális gépek létrehozása egy virtuális gép (VM) pillanatképre, győződjön meg arról, hibrid Azure AD-csatlakozás a virtuális gép pillanatképét, amely nincs konfigurálva használhat.

A Windows régebbi verziójú eszközök regisztrálása nem támogatott eszközök felhasználói profil központi vagy a hitelesítő adatok barangolás konfigurálva. Ha Ön hagyatkoznia a barangoló profilok vagy a beállítások, használja a Windows 10-es.

- A Windows régebbi verziójú eszközök regisztrációja **van** nem összevont környezetben keresztül zökkenőmentes egyszeri bejelentkezést támogatja [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
 
- A Windows régebbi verziójú eszközök regisztrációja **nem** nélkül zökkenőmentes egyszeri bejelentkezést az Azure AD átmenő hitelesítés használata esetén támogatott.

- A Windows régebbi verziójú eszközök regisztrációja **nem** barangoló profilok használó eszközök esetében támogatott. Ha Ön hagyatkoznia a barangoló profilok vagy a beállítások, használja a Windows 10-es.


A regisztráció, a tartományvezérlőn (DC) szerepkört futtató Windows Server nem támogatott.

Ha a szervezet az interneten keresztül egy hitelesített kimenő proxy hozzáférésre van szüksége, győződjön meg arról, hogy a Windows 10-es számítógépek és a kimenő proxy sikeresen hitelesítik. Windows 10-es számítógépek számítógép-környezet használatával történő eszközregisztrációval futtatni, mivel, használatával a számítógép-környezet kimenő proxy-hitelesítés konfigurálása szükséges.


Hibrid Azure AD-csatlakozás egy olyan folyamat, automatikusan regisztrálja az Azure AD a helyi tartományhoz csatlakoztatott eszközök. Előfordulhatnak olyan esetek, amikor nem szeretné automatikusan regisztrálja az eszközök. Ha ez igaz az Ön számára, lásd: [eszközt, a hibrid Azure AD join vezérlése](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>A forgatókönyv kiválasztása

Hibrid Azure AD-csatlakozás a következő esetekben lehet konfigurálni:

- Felügyelt tartományok
- Összevont tartományok  



Ha a környezet rendelkezik felügyelt tartományok, a hibrid Azure AD-csatlakozás támogatja:

- Továbbítása a hitelesítés (ESP) a zökkenőmentes egyszeri bejelentkezési (SSO) 

- Jelszó szinkronizálás (nál) a zökkenőmentes egyszeri bejelentkezési (SSO) tartalmazza. 

1.1.819.0 verzióval kezdve az Azure AD Connect biztosít a hibrid Azure AD-csatlakozás konfigurálása varázsló segítségével. A varázsló lehetővé teszi a jelentősen megkönnyíti a konfigurációs folyamat. További információkért lásd:

- [A hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz](hybrid-azuread-join-federated-domains.md)

- [A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz](hybrid-azuread-join-managed-domains.md)


 Ha a szükséges verzió az Azure AD Connect telepítésével lehetőség nem az Ön számára, lásd: [manuális konfigurálása az eszközregisztrációs](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfigurálás hibrid Azure Active Directory-csatlakozás összevont tartományok](hybrid-azuread-join-federated-domains.md)
> [konfigurálása hibrid Azure Active Directory-csatlakozás a felügyelt tartományok](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
