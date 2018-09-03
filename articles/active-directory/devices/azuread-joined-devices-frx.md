---
title: Új Windows 10-es eszköz csatlakoztatása az Azure AD-hez első futtatáskor | Microsoft Docs
description: Ez a cikk ismerteti, hogy a felhasználók hogyan állíthatják be az Azure AD-csatlakozást az első futtatás során.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: eaf0b3e3b607145598660dbb64cadd5a277360cb
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046322"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Oktatóanyag: Új Windows 10-es eszköz csatlakoztatása az Azure AD-hez első futtatáskor

Az Azure Active Directory (Azure AD) eszközfelügyeletével biztosítható, hogy a felhasználók csak olyan eszközökről férjenek hozzá a vállalat erőforrásaihoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. További információ: [Az Azure Active Directory eszközkezelésének alapjai](overview.md).

Windows 10 rendszeren az új eszközöket az első indítás (FRX) során csatlakoztatni lehet az Azure AD-hez.  
Ez lehetővé teszi bontatlan eszközök átadását az alkalmazottaknak vagy a tanulóknak.

Ha egy eszközön a Windows 10 Professional vagy a Windows 10 Enterprise rendszer van telepítve, az FRX élmény alapértelmezés szerint a vállalati eszközök beállítási folyamatát indítja el.

A Windows *kezdőélménye* nem támogatja a helyszíni Azure Active Directory- (AD-) tartományokhoz való csatlakozást. Ha azt tervezi, hogy a számítógépet csatlakoztatja egy AD-tartományhoz, a beállítás során válassza a **Windows helyi fiókkal való beállítására szolgáló** lehetőséget. Ezután a számítógép beállításai között csatlakozhat a tartományhoz.
 
Ebben az oktatóanyagban megismerheti, hogyan csatlakoztathat egy eszközt az Azure AD-hez az FRX során:
 > [!div class="checklist"]
> * Előfeltételek
> * Eszköz csatlakoztatása
> * Ellenőrzés

## <a name="prerequisites"></a>Előfeltételek

Egy Windows 10-es eszköz csatlakoztatásához úgy kell konfigurálni az eszközregisztrációs szolgáltatást, hogy engedélyezze az eszközök regisztrálását. Az eszközök csatlakoztatásának engedélyezése mellett az is feltétel, hogy az engedélyezett maximumnál kevesebb eszköz legyen regisztrálva az Azure AD-bérlőn. További információt az [eszközbeállítások konfigurálásáról szóló részben](device-management-azure-portal.md#configure-device-settings) talál.

Ezenfelül ha a bérlő egy összevont bérlő, akkor az identitásszolgáltatónak támogatnia KELL a WS-Fed és a WS-Trust felhasználónév/jelszó végpontokat. Ez 1.3-as vagy 2005-ös verziójú lehet. A protokoll támogatása szükséges az eszköz Azure AD-hez való csatlakoztatásához és az eszközre jelszóval történő bejelentkezéshez is.

## <a name="joining-a-device"></a>Eszköz csatlakoztatása

**Windows 10-es eszköz csatlakoztatása az Azure AD-hez az FRX során:**


1. Amikor bekapcsolja az új eszközt, és elindul a beállítás folyamata, a **Felkészülés** üzenetnek kell megjelennie. Kövesse az utasításokat az eszköz beállításához.

2. Először is adja meg a területi és nyelvi beállításokat. Azután fogadja el a Microsoft szoftverlicenc-szerződését.
 
    ![Régió testreszabása](./media/azuread-joined-devices-frx/01.png)

3. Válassza ki a hálózatot, amelyen keresztül csatlakozni fog az internethez.

4. Válassza az **Ez az eszköz a szervezethez tartozik** lehetőséget. 

    ![A számítógép tulajdonosát megjelenítő képernyő](./media/azuread-joined-devices-frx/02.png)

5. Adja meg a vállalat által megadott hitelesítő adatokat, majd kattintson a **Bejelentkezés** gombra.

    ![Bejelentkezés képernyő](./media/azuread-joined-devices-frx/03.png)

6. Az eszköz megkeresi a megfelelő bérlőt az Azure AD-ben. Ha az eszköz egy összevont tartományhoz tartozik, akkor a rendszer átirányítja a helyszíni biztonsági jegykiadó szolgáltatás (STS) kiszolgálójához, például az Active Directory összevonási szolgáltatásokhoz (AD FS).

7. Ha egy nem összevont tartományhoz tartozik, akkor a hitelesítő adatokat közvetlenül az Azure AD-oldalon lehet megadni. 

8. A rendszer többtényezős hitelesítést kér. 
 
9. Az Azure AD ellenőrzi, hogy szükséges-e regisztráció a mobileszköz-felügyeleti megoldásban.

10. A Windows regisztrálja az eszközt a szervezet Azure AD-beli címtárában, és ha szükséges, regisztrálja a mobileszköz-felügyeletben.

11. Ha Ön:
    - Felügyelt felhasználó, a Windows az asztalra lép tovább az automatikus bejelentkezési folyamaton keresztül.

    - Összevont felhasználó, a Windows átirányítja a bejelentkezési képernyőre, amely a hitelesítő adatainak megadását kéri.

## <a name="verification"></a>Ellenőrzés

Annak ellenőrzéséhez, hogy a Windows-eszköz csatlakozik-e az Azure AD-hez, használja a **Hozzáférés munkahelyi vagy iskolai rendszerhez** párbeszédpanelt. Ez jelzi, hogy az eszköz csatlakozik-e az Azure AD-címtárhoz.

![Hozzáférés munkahelyi vagy iskolai rendszerhez](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>További lépések

- További információ: [Az Azure Active Directory eszközkezelésének alapjai](overview.md).

- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure AD Portalon](device-management-azure-portal.md).
