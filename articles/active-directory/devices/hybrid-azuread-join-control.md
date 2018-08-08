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
ms.openlocfilehash: 9ffc84009adfca60e9ae6b188b65b15e874e7d9c
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622170"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>A hibrid Azure AD join eszköz vezérlése

Hibrid Azure AD-csatlakozás egy olyan folyamat, automatikusan regisztrálja az Azure AD a helyi tartományhoz csatlakoztatott eszközök. Előfordulhatnak olyan esetek, amikor nem szeretné automatikusan regisztrálja az eszközök. Ez a példa true, ellenőrizze, hogy minden megfelelően működik-e a kezdeti bevezetés során.

Ez a cikk nyújt útmutatást, hogy miként szabályozható az eszközök hibrid Azure AD-csatlakozás. 


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy Ön ismeri a:

-  [Az Eszközfelügyelet az Azure Active Directory bemutatása](../device-management-introduction.md)
 
-  [A hibrid Azure Active Directory-csatlakozás megvalósításának tervezése](hybrid-azuread-join-plan.md)

-  A hibrid Azure Active Directory-csatlakozás konfigurálása [felügyelt tartományok](hybrid-azuread-join-managed-domains.md) vagy [összevont tartományok](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Aktuális Windows-eszközök

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verziója a Windows 10 Évfordulós frissítés (1607-es verzió) vagy újabb. Ajánlott eljárásként frissítse a Windows 10 legújabb verzióját.

Minden windows aktuális eszközök automatikus regisztrációját az Azure AD-eszköz indítása vagy a felhasználói bejelentkezés. Ez a viselkedés, a csoportházirend-objektum (GPO) vagy a System Center Configuration Managerben való szabályozhatja.

A Windows jelenlegi eszközeinek vezérléséhez, kell tennie: 

1.  **Az összes eszközre**: automatikus eszközregisztráció letiltása.
2.  **A kijelölt eszközökhöz**: automatikus eszközregisztráció engedélyezése.

Ha ellenőrizte, hogy minden a várt módon működik, készen áll ismét engedélyezi az összes eszköz regisztrálására.



## <a name="group-policy-object"></a>Csoportházirend-objektum 

Szabályozhatja az eszköz regisztrációs viselkedését az eszközök a következő csoportházirend-objektum a központi telepítésével: **eszközként regisztrálja a tartományhoz csatlakoztatott számítógépeken**

**A csoportházirend-objektum beállítása**:

1.  Nyissa meg **Kiszolgálókezelő**, majd lépjen **eszközök \> csoportházirend-kezelő**.

2.  Nyissa meg a tartomány csomópontjára, amely megfelel a tartományhoz, ahol szeretné az automatikus regisztráció engedélyezése vagy tiltása.

3.  Kattintson a jobb gombbal **csoportházirend-objektumok**, majd válassza ki **új**.

4.  Adjon meg egy nevet (például *hibrid Azure AD-csatlakozás*) a csoportházirend-objektum. 

5.  Kattintson az **OK** gombra.

6.  Kattintson a jobb gombbal az új csoportházirend-Objektumot, és válassza **szerkesztése**.

7.  Lépjen a **számítógép konfigurációja \> házirendek \> felügyeleti sablonok \> Windows-összetevők \> Eszközregisztráció**. 

8.  Kattintson a jobb gombbal **eszközként regisztrálja a tartományhoz csatlakoztatott számítógépeket**, majd válassza ki **szerkesztése**.

    > [!NOTE] 
    > A csoportházirend-sablon a Csoportházirend kezelése konzol korábbi verzióiról át lett nevezve. Ha a konzol korábbi verzióiban használ, lépjen a **számítógép konfigurációja \> házirendek \> felügyeleti sablonok \> Windows-összetevők \> munkahelyi csatlakoztatás \> Automatikus munkahelyi csatlakozás ügyfélszámítógépek**. 

9.  A következő beállítások egyikének kiválasztásával, és kattintson a **alkalmaz**:

    - **Letiltott** – az automatikus eszközregisztráció megelőzése érdekében
    - **Engedélyezett** – az automatikus eszközregisztráció engedélyezése

10. Kattintson az **OK** gombra.

Kapcsolja a csoportházirend-Objektumot egy Ön által választott helyre van szüksége. Például a házirend az összes, a tartományhoz csatlakoztatott jelenlegi eszköz beállítása a szervezetben, kapcsolja a csoportházirend-Objektumot a tartományhoz. Ehhez az ellenőrzött telepítési, állítsa ezt a szabályzatot tartományhoz csatlakoztatott Windows aktuális eszközökre, szervezeti egység vagy egy biztonsági csoport tartozik.

### <a name="configuration-manager-controlled-deployment"></a>A Configuration Manager központi telepítési szabályozza 

Az eszköz regisztrációs viselkedését az aktuális eszközök szabályozhatja úgy konfigurálja a következő ügyfélbeállítással: ** automatikusan regisztrálja az azure Active Directory új Windows 10-tartományhoz csatlakozott eszközökkel **


**Az ügyfél-eszközbeállítást beállítása**:

1.  Nyissa meg **Configuration Manager**, majd lépjen **Cloud Services**.

2.  A **eszközbeállítások**, válasszon egyet a következő értékeket a **automatikusan regisztrálja az azure Active Directory új Windows 10-tartományhoz csatlakozott eszközökkel**:

    - **Nem** – az automatikus eszközregisztráció megelőzése érdekében
    - **Igen** – az automatikus eszközregisztráció engedélyezése


3.  Kattintson az **OK** gombra.
    

Kíván hivatkozni egy Ön által választott helyre ezt az ügyfélbeállítást. Adja meg ezt az ügyfélbeállítást a minden Windows aktuális a szervezetnél található eszközökön, például az ügyfélbeállítás, a tartományhoz kell társítani. Ehhez egy ellenőrzött központi telepítést, az ügyfél-eszközbeállítást Windows-tartományhoz tartozó szervezeti egység vagy egy biztonsági csoport aktuális eszközök konfigurálhatja.

> [!Important]
> Míg a fenti konfigurációs gondoskodik a meglévő tartományhoz csatlakoztatott Windows 10 rendszerű eszközökön, és van egy lehetséges, hogy továbbra is teljes körű hibrid Azure AD-csatlakozás a tényleges csoportházirend alkalmazása a potenciális késéssel megpróbálja újonnan tartományhoz csatlakozó eszközök vagy A Configuration Manager-beállítások az újonnan csatlakoztatott Windows 10 rendszerű eszköz. Ennek elkerülése érdekében javasoljuk, hogy hoz létre egy új sysprep lemezkép (használt példaként az üzembe helyezési módszer) egy eszközről, amely soha nem volt a hibrid Azure AD-hez, és, hogy már rendelkezik a fenti csoportházirend-beállítás alkalmazva vagy a Configuration Manager-ügyfél a beállítás a alkalmazni. Az új rendszerképet is az új számítógépeket, amelyek a szervezet tartományához kell használnia. 

## <a name="control-windows-down-level-devices"></a>Windows régebbi verziójú eszközök

Windows régebbi verziójú eszközök regisztrálásához, töltse le és telepítse a Windows Installer-csomag (.msi) a letöltőközpontból szeretne a [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/en-us/download/details.aspx?id=53554) lapot.

Telepítheti a csomagot a szoftverterjesztési rendszer például System Center Configuration Manager használatával. A csomag a standard szintű beavatkozás nélküli telepítéssel beállítások quiet paraméter támogatja. [A System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) aktuális ág további előnyöket kínál a korábbi verziójú, például a befejezett regisztrációk nyomon követéséhez.

A telepítő létrehoz egy ütemezett feladatot a rendszer a felhasználó környezetében futó. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure AD-felhasználói hitelesítő adatok az Azure AD-hitelesítés után.


Szabályozhatja az eszközök regisztrációját, üzembe kell helyeznie a Windows Installer-csomag csak a Windows régebbi verziójú eszközök egy kiválasztott csoportja számára. Ha ellenőrizte, hogy minden a várt módon működik, készen áll a csomag összes régebbi verziójú eszközök bevezetés.


## <a name="next-steps"></a>További lépések

* [Az Eszközfelügyelet az Azure Active Directory bemutatása](../device-management-introduction.md)



