---
title: "Androidhoz készült Azure Authenticator |} Microsoft Docs"
description: "A Microsoft Azure Authenticator alkalmazás segítségével bejelentkezhet munkahelyi erőforrások eléréséhez. Az Azure Authenticator alkalmazás értesítést küld a egy függőben lévő kéttényezős ellenőrzési kérelmekről a mobil eszközére riasztást megjelenítésével."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 60a5cc2ecc550c76ca3cb1f1d4d20070b3e3b035
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-authenticator-for-android"></a>Androidhoz készült Azure Authenticator
A rendszergazda is ajánlott, hogy a Microsoft Azure Authenticator bejelentkezés a munkahelyi erőforrások eléréséhez. Ez az alkalmazás ezen két bejelentkezési beállításokat biztosítja:

* A multi-factor Authentication lehetővé teszi a kétlépéses ellenőrzéshez használttal a munkahelyi vagy iskolai fiókok biztosításában. Bejelentkezés valami, amit (például a jelszót) ismert és megvédeni a fiókját még tovább valamit az informatikai részleg (a biztonsági kulcs az alkalmazásból). Az Azure Authenticator alkalmazás értesítést küld a egy függőben lévő kéttényezős ellenőrzési kérelmekről a mobil eszközére riasztást megjelenítésével. Egyszerűen nézet a kérést az alkalmazást, koppintson a győződjön meg arról, vagy szakítsa meg kell. Alternatív megoldásként is kérni fogja a PIN-kód jelenik meg az alkalmazásban írjon be.
* Munkahelyi fiókja révén Android telefonját vagy táblagépét kapcsolja be a megbízható eszközök és a vállalati alkalmazásokhoz való egyszeri bejelentkezés (SSO). A rendszergazda megkövetelheti, hogy a munkahelyi fiók hozzáadása a vállalati erőforrások eléréséhez. Egyszeri bejelentkezés lehetővé teszi a bejelentkezés után, és automatikusan további bejelentkezések nélkül hozzáférhet a vállalata rendelkezik elérhetővé tett összes alkalmazáshoz jelentkezik be.

## <a name="installing-the-azure-authenticator-app"></a>Az Azure Authenticator alkalmazás telepítése
Az Azure Authenticator alkalmazást a Google Play áruházból telepítheti.
Vegyen fel munkahelyi fiókot Samsung Android-eszköz és a nem Samsung Android eszközről vonatkozó utasításokat némileg eltérőek. Az utasításokat, mind az alábbiakban láthatók.

## <a name="adding-the-work-account-from-samsung-android-device"></a>A munkahelyi fiók felvétele Samsung Android-eszközön
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Az alkalmazás kezdőképernyő keresztül a munkahelyi fiók hozzáadása
Az alábbi utasítások alapján Samsung GS3 és telefonok vagy a 2. és annál újabb rendszerű táblagépek alkalmazhatók.

1. A kezdőképernyőn az alkalmazás fogadja el a végfelhasználói licencszerződést (EULA).
2. A fiók aktiválása képernyőn kattintson a jobb oldali és válassza ki a helyi menü **munkahelyi fiók**.
3. A fiók felvétele képernyőn válassza ki ** munkahelyi fiók **.
4. Aktiválás eszközt rendszergazda képernyőn kattintson a **aktiválás**.
5. Az adatvédelmi szabályzat képernyőn jelölje be a jelölőnégyzetet, és kattintson a **megerősítése**.
6. A munkahelyi csatlakoztatás képernyőn adja meg a felhasználói azonosítóját a szervezetben, és kattintson által biztosított **csatlakozás**.
7. Jelentkezzen be az Azure Authenticator alkalmazást, írja be a szervezeti egy x írására és a jelszót, majd kattintson **bejelentkezés**.
8. A következő képernyőn, a többtényezős hitelesítés (MFA) információit jeleníti meg a kerül biztonsági, és nem kötelező megadni. Ez a képernyő jelenik meg, ha a munkahelyi vagy iskolai kéttényezős hitelesítést igényel a munkahelyi fiók létrehozásához. Útmutatás további ellenőrizzük a fiókját.
9. A munkahelyi csatlakoztatás képernyőn megjelenik az üzenet "**a munkahelyhez való**". Az Azure authenticator alkalmazás megkísérli az eszköz csatlakoztatását a munkahelyhez.
10. A következő képernyőn megjelenik a munkahelyhez csatlakoztatott üzenet.

> [!NOTE]
> Egyetlen munkahelyi fiókkal az eszközön engedélyezettek.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>A munkahelyi fiók hozzáadása a beállítások menüből
Az Azure Authenticator alkalmazás telepítése után is létrehozhat egy munkahelyi fiókot a Android fiókkezelővel.

1. Lépjen a beállítások menüből **fiókok** kattintson **fiók hozzáadása**.
2. Hajtsa végre az eljárást, az alkalmazás kezdőképernyő, munkahelyi fiók felvétele a munkahelyi fiókot hozzáadása 3 – 10 lépéseket.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>A munkahelyi fiók hozzáadása egy Samsung Android-eszközön
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Az alkalmazás kezdőképernyő keresztül a munkahelyi fiók hozzáadása
1. A kezdőképernyőn az alkalmazás fogadja el a végfelhasználói licencszerződést (EULA).
2. A fiók aktiválása képernyőn kattintson a jobb oldali és válassza ki a helyi menü **munkahelyi fiók**.
3. A fiókok képernyőn kattintson a **fiók hozzáadása**.
4. Ha a fiókok képernyőt lát, kattintson a **fiók hozzáadása**. Ha egy munkahelyi fiókot már korábban létrehozott, látni fogja a szinkronizálási képernyő megjeleníti a meglévő munkahelyi fiókot. A munkahelyi fiókjával őrizheti meg kell adniuk a kezőképernyőjükhöz vissza nyílra egyszerűen koppintva. Alternatív megoldásként is jelölje ki a fiókot távolítsa el és hozza létre egy új munkahelyi fiókot a a munkahelyi csatlakoztatás képernyő, adja meg a felhasználói azonosítóját, a szervezet biztosítja, és kattintson a Csatlakozás gombra.
5. Jelentkezzen be az Azure Authenticator alkalmazást, adja meg a szervezeti felhasználónévvel és jelszóval, és kattintson a **bejelentkezés**.
6. A következő képernyőn, a többtényezős hitelesítés (MFA) információit jeleníti meg a kerül biztonsági, és nem kötelező megadni. Ez a képernyő jelenik meg, ha a munkahelyi vagy iskolai kéttényezős hitelesítést igényel a munkahelyi fiók létrehozásához. Útmutatás további ellenőrizzük a fiókját.
7. Kattintson a **OK** a következő képernyőn. A tanúsítvány neve nem módosítható.
   az üzenet, "A munkahelyhez való". Az Azure authenticator alkalmazás megkísérli az eszköz csatlakoztatását a munkahelyhez.
   A következő képernyőn megjelenik a munkahelyhez csatlakoztatott üzenet.

> [!NOTE]
> Egyetlen munkahelyi fiókkal az eszközön engedélyezettek.
> 
> 

Az Azure Authenticator alkalmazás telepítése után is létrehozhat egy munkahelyi fiókot a Android fiókkezelővel.

1. Az a **beállítások** menüben keresse meg fiókokat, és kattintson **fiók hozzáadása**.
2. Hajtsa végre az eljárást, munkahelyi fiók felvétele keresztül az alkalmazás otthoni képernyő **, a munkahelyi fiók felvétele 2 – 7 lépéseket.

### <a name="how-to-find-out-which-version-is-installed"></a>Annak ellenőrzése, mely verziója
1. Azt is megtudhatja, az Azure Authenticator alkalmazás melyik verzióját, és a társított szolgáltatás verziói vannak telepítve az eszközön.
2. Az előugró menüből, kattintson **kapcsolatos**.
3. Névjegy képernyőjének azok a szolgáltatások, az alkalmazás és az eszközön telepített verziók.

### <a name="sending-log-files-to-report-issues"></a>Küldő naplófájlokat, és jelentésekkel kapcsolatos problémák
1. Kövesse az útmutatást Microsoft Support incidenst és az Azure Authenticator alkalmazás, egy lekéréséhez és naplófájlok helyét a kiosztott incidens szám elleni küldése az alábbiak szerint:
2. Az előugró menüből, kattintson **naplózás**.
3. Ha Microsoft Support nyitott incidens, jegyezze fel az incidensek számát (szüksége lesz azt egy későbbi lépésben). Ha még nem hozott létre a támogatási incidensek és segítségkéréseit, kövesse az útmutatást, [Microsoft Support](https://support.microsoft.com/en-us/contactus) új incidens megnyitásához.
4. A naplózási képernyőn kattintson a **azonnali küldés**.
5. Válassza ki a használni kívánt e-mail-szolgáltatót.
6. Ha már nyitott incidens Microsoft Support, forduljon a támogatási szakember, a probléma rendelt megtudhatja, hogyan küldhet a naplózási adatokat, és azt társítani, amelyeken az esemény. A támogatási szakember biztosítja azokat az e-mail cím és a tulajdonos sor adatokkal. Ha még nem rendelkezik a támogatási incidensek, kövesse az útmutató a Microsoft Support új incidens megnyitásához.
7. Szerkessze a **való** sor és **tulajdonos** sor a Microsoft Support kapott információkat.
8. Az Azure Authenticator alkalmazást a naplófájl csatol az e-mailt küldünk. Írja le a problémát tapasztal, frissíteni címzettjeinek listája (nem kötelező), és az e-mailek küldése.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>A munkahelyi fiók törlése és a munkahelyi elhagyása
A munkahelyi fiókhoz az alábbiak szerint létrehozott bármikor eltávolítása:

**A munkahelyi fiók törléséhez a beállítások menüből**

1. Válassza ki a fiókkezelő **munkahelyi fiók**.
2. A munkahelyi fiókjával képernyőn a **általános beállítások**, jelölje be **Fiókbeállítások – kilép a munkahelyi hálózathoz**.
3. Válassza ki **hagyja** a a **munkahelyi csatlakoztatás** képernyő.
4. Kattintson a **OK** Ha a "Biztos, hogy kilép a munkahelyi hálózatból" üzenet jelenik meg.
5. Ez biztosítja, hogy már törölt munkahelyi fiókját a munkahelyen.

> [!NOTE]
> Javasoljuk, hogy nem használja a fiók törlése lehetőséget munkahelyi fiók törlése, ez a beállítás nem működnek bizonyos Android korábbi verzióiban.
> 
> 

## <a name="uninstalling-the-app"></a>Az alkalmazás eltávolítása
A Samsung Android-eszközön, eszköz-rendszergazdai jogosultságokkal előtt meg kell szüntetni az alábbiak szerint eltávolítása a 

1. A **beállítások**a **rendszer**, jelölje be **biztonsági**.
2. D**evice felügyeleti**, kattintson a **eszközadminisztrátorok**. Törölje a jelet a **Azure Authenticator** nincs bejelölve.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha megjelenik a **Keystore hiba**, lehetséges, hogy nem rendelkezik a zárolási képernyőn állítsa be PIN-kóddal. A probléma megoldásához távolítsa el az Azure Authenticator alkalmazást, a zárolási képernyő egy PIN-KÓDJUKAT konfigurálják, telepítse újra az alkalmazást.

