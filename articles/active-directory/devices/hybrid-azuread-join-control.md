---
title: Szabályozhatja a hibrid Azure AD join eszközét |} A Microsoft Docs
description: Ismerje meg, hogyan szabályozhatja a hibrid Azure AD-csatlakozás az eszközök az Azure Active Directoryban.
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
ms.openlocfilehash: a2ae1d3f4166bfaa035902aaa5dc101636a98646
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117537"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Az eszközök hibrid Azure AD-csatlakozásának vezérlése

Hibrid Azure Active Directory (Azure AD) csatlakozás egy olyan folyamat, automatikusan regisztrálja az Azure AD a helyi tartományhoz csatlakoztatott eszközök. Előfordulhatnak olyan esetek, amikor nem szeretné automatikusan regisztrálni kell az eszközök. Ez igaz, például ellenőrizheti, hogy minden megfelelően működik-e a kezdeti bevezetés során.

Ez a cikk útmutatást nyújt a hibrid Azure AD-csatlakozás az eszközök felügyeletét. 


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a:

-  [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)
 
-  [A hibrid Azure Active Directory join megvalósítás megtervezése](hybrid-azuread-join-plan.md)

-  [Konfigurálás hibrid Azure Active Directory-csatlakozás a felügyelt tartományok](hybrid-azuread-join-managed-domains.md) vagy [konfigurálása hibrid Azure Active Directory-csatlakozás összevont tartományok](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Aktuális Windows-eszközök

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verziója a Windows 10 Évfordulós frissítés (1607-es verzió) vagy újabb. Ajánlott eljárásként frissítse a Windows 10 legújabb verzióját.

Minden Windows aktuális eszközök automatikus regisztrációját az Azure AD-eszköz indítása vagy a felhasználói bejelentkezés. Ez a viselkedés a csoportházirend-objektumot (GPO) vagy a System Center Configuration Manager segítségével szabályozhatja.

A Windows jelenlegi eszközeinek vezérléséhez, kell tennie: 


1.  **Az összes eszközre**: Tiltsa le az automatikus regisztrációt.
2.  **A kijelölt eszközökhöz**: Automatikus eszközregisztráció engedélyezése.

Miután ellenőrizte, hogy minden a várt módon működik, készen áll az összes eszköz regisztrálására újra engedélyezni szeretné az.



### <a name="group-policy-object"></a>Csoportházirend-objektum 

Szabályozhatja az eszköz regisztrációs viselkedését az eszközök a következő csoportházirend-objektum a központi telepítésével: **Regisztrálja a tartományhoz csatlakoztatott számítógépeket eszközként**.

A csoportházirend-objektum beállítása:

1.  Nyissa meg **Kiszolgálókezelő**, majd lépjen **eszközök** > **csoportházirend-kezelő**.

2.  Nyissa meg a tartomány csomópontjára, amely megfelel a tartományhoz, ahol szeretné az automatikus regisztráció engedélyezése vagy letiltása.

3.  Kattintson a jobb gombbal **csoportházirend-objektumok**, majd válassza ki **új**.

4.  Adjon meg egy nevet (például **hibrid Azure AD-csatlakozás**) a csoportházirend-objektum számára. 

5.  Kattintson az **OK** gombra.

6.  Kattintson a jobb gombbal az új csoportházirend-Objektumot, és válassza **szerkesztése**.

7.  Lépjen a **számítógép konfigurációja** > **házirendek** > **felügyeleti sablonok** > **Windows Összetevők** > **Eszközregisztráció**. 

8.  Kattintson a jobb gombbal **eszközként regisztrálja a tartományhoz csatlakoztatott számítógépeket**, majd válassza ki **szerkesztése**.

    > [!NOTE] 
    > Ez a csoportházirend-sablon a Csoportházirend kezelése konzol korábbi verzióiról át lett nevezve. Ha a konzol korábbi verzióiban használ, lépjen a **számítógép konfigurációja** > **házirendek** > **felügyeleti sablonok**  >  **Windows-összetevők** > **munkahelyi csatlakoztatás** > **automatikus munkahelyi csatlakozás ügyfélszámítógépek**. 

9.  Az alábbi beállítások közül, majd válassza ki és **alkalmaz**:

    - **Letiltott**: Automatikus eszközregisztráció megakadályozására.
    - **Engedélyezett**: Automatikus eszközregisztráció engedélyezése.

10. Kattintson az **OK** gombra.

Kapcsolja a csoportházirend-Objektumot egy Ön által választott helyre van szüksége. Például a házirend az összes, a tartományhoz csatlakoztatott jelenlegi eszköz beállítása a szervezetben, kapcsolja a csoportházirend-Objektumot a tartományhoz. Ehhez az ellenőrzött telepítési, állítsa ezt a szabályzatot tartományhoz csatlakoztatott Windows aktuális eszközökre, szervezeti egység vagy egy biztonsági csoport tartozik.

### <a name="configuration-manager-controlled-deployment"></a>A Configuration Manager központi telepítési szabályozza 

Az eszköz regisztrációs viselkedését az aktuális eszközök szabályozhatja úgy konfigurálja a következő ügyfélbeállítással: **Automatikusan regisztrálja az Azure Active Directory új Windows 10-tartományhoz csatlakozott eszközökkel**.

Ügyfélbeállítás konfigurálása:

1.  Nyissa meg **Configuration Manager**, majd lépjen **Cloud Services**.

2.  A **eszközbeállítások**, válasszon egyet a következő értékeket a **automatikusan regisztrálja az Azure Active Directory új Windows 10-tartományhoz csatlakozott eszközökkel**:

    - **Nem**: Automatikus eszközregisztráció megakadályozására.
    - **Igen**: Automatikus eszközregisztráció engedélyezése.


3.  Kattintson az **OK** gombra.
    

Kíván hivatkozni egy Ön által választott helyre ezt az ügyfélbeállítást. Adja meg ezt az ügyfélbeállítást a minden Windows aktuális a szervezetnél található eszközökön, például az ügyfélbeállítás, a tartományhoz kell társítani. Ehhez egy ellenőrzött központi telepítést, az ügyfél-eszközbeállítást Windows-tartományhoz tartozó szervezeti egység vagy egy biztonsági csoport aktuális eszközök konfigurálhatja.

> [!Important]
> Noha a fenti konfigurációs gondoskodik a meglévő tartományhoz csatlakoztatott Windows 10-es eszközök, a hibrid Azure AD-csatlakozás befejezéséhez a Csoportházirend alkalmazása a potenciális késedelem miatt előfordulhat, hogy továbbra is próbálja eszközöket, amelyek a rendszer újonnan a tartományhoz, vagy A Configuration Manager-beállítások az eszközökön. 
>
> Ennek elkerülése érdekében azt javasoljuk, hogy létrehozott egy új Sysprep lemezkép (használt példaként az üzembe helyezési módszer). Hozza létre a olyan eszköz, amely lett korábban sosem hibrid Azure AD-hez, és azt, hogy már rendelkezik a csoportházirend beállítást vagy a Configuration Manager ügyfél-eszközbeállítást a alkalmazni. Az új rendszerképet is az új számítógépeket, amelyek a szervezet tartományához kell használnia. 

## <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése

Windows régebbi verziójú eszközök regisztrálásához, töltse le és telepítse a Windows Installer-csomag (.msi) a letöltőközpontból szeretne a [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/download/details.aspx?id=53554) lapot.

A csomag például egy szoftverterjesztési rendszer segítségével telepíthet [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). A csomag a standard szintű beavatkozás nélküli telepítés beállítások quiet paraméter támogatja. A Configuration Manager aktuális ágának előnyöket kínál, hiszen nyomon követheti a befejezett regisztrációk például korábbi verzióihoz képest.

A telepítő létrehoz egy ütemezett feladatot a rendszer a felhasználó környezetében futó. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure AD-felhasználói hitelesítő adatok az Azure AD-hitelesítés után.

Szabályozhatja az eszközök regisztrációját, üzembe kell helyeznie a Windows Installer-csomag csak a Windows régebbi verziójú eszközök egy kiválasztott csoportja számára. Ha ellenőrizte, hogy minden a várt módon működik, készen áll a csomag megkezdik az összes régebbi verziójú eszközök.


## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)



