---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök konfigurálása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
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
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: e273568a04ec2a3758684025acf8034b8e788627
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871338"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>A hibrid Azure Active Directory join megvalósítás tervezése

A felhasználókhoz hasonlóan az eszközök is olyan identitásokká válnak, amelyeket mindig, mindenhol védelem alatt tartunk, illetve felhasználunk az erőforrásaink védelméhez. Ezt úgy tehetjük meg, ha az eszközök identitásait bevonjuk az Azure AD-be, a következő módszerek egyikével:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Ugyanakkor a felhőbeli és a helyszíni erőforrások hozzáférése külön [feltételes hozzáférésekkel](../active-directory-conditional-access-azure-portal.md) is biztosítható.

Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Ez a cikk ismerteti, és a kapcsolódó lépéseket kell végrehajtani egy hibrid Azure AD join a környezetben. 


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a [bemutatása az Eszközfelügyelet az Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A hibrid Azure AD-megvalósítás megtervezése, meg kell ismerkednie az:

|   |   |
|---|---|
|![Jelölőnégyzet][1]|Tekintse át a támogatott eszközök|
|![Jelölőnégyzet][1]|Felülvizsgálat tudnivalók|
|![Jelölőnégyzet][1]|Tekintse át a hibrid Azure AD join eszköz vezérlése|
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

Hibrid Azure AD-csatlakozás régebbi verziójú Windows-eszközök:

- **Van** nem összevont környezetben keresztül támogatott [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Nem** nélkül zökkenőmentes egyszeri bejelentkezést az Azure AD átmenő hitelesítés használata esetén támogatott.

- **Nem** hitelesítő adatokat a központi vagy a barangoló felhasználói profil használata vagy a virtuális asztali infrastruktúra (VDI) használata esetén támogatott.


A regisztráció, a tartományvezérlőn (DC) szerepkört futtató Windows Server nem támogatott.

Ha a szervezet egy hitelesített kimenő proxy használatát írja elő az internethez való csatlakozáshoz, gondoskodni kell arról, hogy a Windows 10 rendszerű számítógépek sikeres hitelesítést tudjanak végezni a kimenő proxyval. Mivel a Windows 10-es számítógépek az eszközregisztrációt gépi kontextusban futtatják, a kimenő proxy hitelesítését is gépi kontextus használatával kell konfigurálni.


Hibrid Azure AD-csatlakozás egy olyan folyamat, automatikusan regisztrálja az Azure AD a helyi tartományhoz csatlakoztatott eszközök. Előfordulhatnak olyan esetek, amikor nem szeretné automatikusan regisztrálja az eszközök. Ha ez igaz az Ön számára, lásd: [eszközt, a hibrid Azure AD join vezérlése](hybrid-azuread-join-control.md).


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Tekintse át a hibrid Azure AD join eszköz vezérlése

Hibrid Azure AD-csatlakozás egy olyan folyamat, automatikusan regisztrálja az Azure AD a helyi tartományhoz csatlakoztatott eszközök. Előfordulhatnak olyan esetek, amikor nem szeretné automatikusan regisztrálja az eszközök. Ez a példa true, ellenőrizze, hogy minden megfelelően működik-e a kezdeti bevezetés során.

További információkért lásd: [eszközt, a hibrid Azure AD join szabályozása](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>A forgatókönyv kiválasztása

Hibrid Azure AD-csatlakozás a következő esetekben lehet konfigurálni:

- Felügyelt tartományok
- Összevont tartományok  



Ha a környezet rendelkezik felügyelt tartományok, a hibrid Azure AD-csatlakozás támogatja:

- Hitelesítés (ESP) keresztül adja át

- Jelszókivonat-szinkronizálás (nál)

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. További információkért lásd:

- [A hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz](hybrid-azuread-join-federated-domains.md)


- [A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz](hybrid-azuread-join-managed-domains.md)


 Ha a szükséges verzió az Azure AD Connect telepítésével lehetőség nem az Ön számára, lásd: [manuális konfigurálása az eszközregisztrációs](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfigurálás hibrid Azure Active Directory-csatlakozás összevont tartományok](hybrid-azuread-join-federated-domains.md)
> [konfigurálása hibrid Azure Active Directory-csatlakozás a felügyelt tartományok](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
