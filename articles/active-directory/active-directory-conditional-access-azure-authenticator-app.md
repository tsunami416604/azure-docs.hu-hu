---
title: Androidhoz készült Azure Authenticator |} A Microsoft Docs
description: A Microsoft Azure Authenticator alkalmazás segítségével jelentkezzen be munkahelyi erőforrások eléréséhez. Az Azure Authenticator alkalmazás értesítést küld a kétfaktoros függőben lévő visszaigazolási megjeleníti a riasztást a mobil eszközére.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: fbd728ec8cd2c8c4cd7ca74ecd84fd4d0d41cbd0
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058423"
---
# <a name="azure-authenticator-for-android"></a>Androidhoz készült Azure Authenticator
A rendszergazda előfordulhat, hogy már javasolta, hogy a Microsoft Azure Authenticator használatával jelentkezzen be a munkahelyi erőforrások eléréséhez. Ez az alkalmazás ezen két bejelentkezési beállításokat biztosítja:

* Multi-factor Authentication lehetővé teszi, hogy a kétlépcsős ellenőrzéshez a munkahelyi vagy iskolai fiókok védelme. Bejelentkezés valami tudja (például a jelszó), és a fiók védelme érdekében még valamit rendelkező (a biztonsági kulcs az alkalmazásból). Az Azure Authenticator alkalmazás értesítést küld a kétfaktoros függőben lévő visszaigazolási megjeleníti a riasztást a mobil eszközére. Kell egyszerűen megtekintése az alkalmazást, és koppintson a kérelem ellenőrzése a Mégse gombra. Azt is megteheti kérheti a jelenik meg az alkalmazás PIN-kód megadását.
* Munkahelyi fiók lehetővé teszi, hogy kapcsolja be az Android rendszerű telefon vagy táblagép megbízható eszközként, és adja meg az egyszeri bejelentkezést (SSO) a vállalati alkalmazások. A rendszergazda megkövetelheti, hogy a munkahelyi fiók hozzáadása a vállalati erőforrások eléréséhez. Egyszeri bejelentkezés lehetővé teszi a bejelentkezés után, és automatikusan további bejelentkezések nélkül hozzáférhet a vállalata rendelkezik elérhetővé tett alkalmazások közötti jelentkezik be.

## <a name="installing-the-azure-authenticator-app"></a>Az Azure Authenticator alkalmazás telepítése
Az Azure Authenticator alkalmazást a Google Play Store áruházából telepíthető.
Az utasításokat a munkahelyi fiók hozzáadása Samsung Android-eszköz és a nem Samsung Android eszközről némileg eltérőek. A következő útmutatót: mind az alábbiakban láthatók.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Samsung Android-eszközről a munkahelyi fiók hozzáadása
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Az alkalmazás kezdőképernyőjén keresztül a munkahelyi fiók hozzáadása
Az alábbi utasításokat követve Samsung GS3 és telefonok vagy 2. és annál újabb rendszerű táblagépek vonatkoznak.

1. Az alkalmazás a kezdőképernyőn fogadja el a végfelhasználói licencszerződést (EULA).
2. A fiók aktiválásához képernyőn kattintson a jobb oldalon, és válassza a helyi menü **munkahelyi fiók**.
3. A fiók hozzáadása képernyőn válassza ki ** munkahelyi fiók **.
4. Az aktiválás eszköz rendszergazdai jogosultságait képernyőn kattintson **aktiválás**.
5. Az adatvédelmi szabályzat képernyőn jelölje be a jelölőnégyzetet, és kattintson a **megerősítése**.
6. A munkahelyi csatlakoztatást képernyőn adja meg a felhasználói azonosítóját a szervezethez, és kattintson által biztosított **csatlakozzon**.
7. Jelentkezzen be az Azure Authenticator alkalmazást, adja meg a szervezeti felhasználónévvel és jelszóval, és kattintson a **jelentkezzen be a**.
8. A következő képernyőn, hogy a többtényezős hitelesítés (MFA) információit jeleníti meg a adnak biztonsági, és nem kötelező. Ezen a képernyőn láthatja, ha a munkahelyi vagy iskolai második tényezős hitelesítés szükséges a munkahelyi fiók létrehozását. Útmutatás további ellenőrizni fiókját.
9. A munkahelyi csatlakoztatást képernyőn megjelenik az üzenet "**a munkahelyi csatlakozás**". Az Azure authenticator alkalmazást próbál meg az eszköz a munkahelyi csatlakozás.
10. A következő képernyőn megjelenik a munkahelyhez csatlakoztatott üzenet.

> [!NOTE]
> Engedélyezett egy munkahelyi fiókkal az eszközön.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>A munkahelyi fiók felvétele a beállítások menü
Az Azure Authenticator alkalmazás telepítése után is létrehozhat egy munkahelyi fiókot az Android-fiókkezelő.

1. Lépjen a beállítások menüben **fiókok** kattintson **fiók hozzáadása**.
2. Kövesse a lépéseket 3 – 10 az eljárás keretében az alkalmazás kezdőképernyő, munkahelyi fiók hozzáadása a munkahelyi fiók hozzáadása.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>A munkahelyi fiók hozzáadása egy Samsung Android-eszközön
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Az alkalmazás kezdőképernyőjén keresztül a munkahelyi fiók hozzáadása
1. Az alkalmazás a kezdőképernyőn fogadja el a végfelhasználói licencszerződést (EULA).
2. A fiók aktiválásához képernyőn kattintson a jobb oldalon, és válassza a helyi menü **munkahelyi fiók**.
3. A fiók képernyőn kattintson a **fiók hozzáadása**.
4. Ha a fiókok képernyőt látja, kattintson a **fiók hozzáadása**. Ha egy munkahelyi fiókot már korábban létrehozott, egy munkahelyi fiók megjelenítése a meglévő szinkronizálási képernyő jelenik meg. A munkahelyi fiók egyszerűen koppintson a vissza nyílra, hogy a kezdőképernyőn őrizheti meg. Azt is megteheti távolítsa el és hozza létre újra egy új munkahelyi fiókot a a munkahelyi csatlakoztatást képernyő, adja meg a felhasználói azonosítót a szervezet biztosítja nekik, és kattintson a csatlakozás a fiókot választhat.
5. Jelentkezzen be az Azure Authenticator alkalmazást, adja meg a szervezeti felhasználónévvel és jelszóval, és kattintson a **jelentkezzen be a**.
6. A következő képernyőn, hogy a többtényezős hitelesítés (MFA) információit jeleníti meg a adnak biztonsági, és nem kötelező. Ezen a képernyőn láthatja, ha a munkahelyi vagy iskolai második tényezős hitelesítés szükséges a munkahelyi fiók létrehozását. Útmutatás további ellenőrizni fiókját.
7. Kattintson a **OK** a következő képernyőn. Ne módosítsa a tanúsítvány nevét.
   az üzenet, "A munkahelyhez való csatlakozás". Az Azure authenticator alkalmazást próbál meg az eszköz a munkahelyi csatlakozás.
   A következő képernyőn megjelenik a munkahelyhez csatlakoztatott üzenet.

> [!NOTE]
> Engedélyezett egy munkahelyi fiókkal az eszközön.
> 
> 

Az Azure Authenticator alkalmazás telepítése után is létrehozhat egy munkahelyi fiókot az Android-fiókkezelő.

1. Az a **beállítások** menüben keresse meg a fiókok, és kattintson **fiók hozzáadása**.
2. Hajtsa végre a 2 – 7. lépéseket az eljárás keretében az alkalmazást az otthoni képernyő **, a munkahelyi fiók hozzáadása a munkahelyi fiók hozzáadása.

### <a name="how-to-find-out-which-version-is-installed"></a>Hogyan tudhatja meg, hogy melyik verziója van telepítve
1. Az Azure Authenticator alkalmazás melyik verzióját talál, és a társított szolgáltatás verziója van telepítve az eszközön.
2. Az előugró menüben kattintson **kapcsolatos**.
3. Névjegy képernyőjének jeleníti meg a szolgáltatások, az alkalmazás és az eszközére telepített verziója.

### <a name="sending-log-files-to-report-issues"></a>Naplófájlok küldeni a problémákat
1. Az útmutatást kövesse a Microsoft Support a jelentés az Azure Authenticator alkalmazás az incidens, szerezzen be egy és ellen a hozzárendelt incidensszám naplófájlok küldése a következő:
2. Az előugró menüben kattintson **naplózás**.
3. Ha a Microsoft Support nyitott incidenshez, jegyezze meg a incidensszám használatával (, szüksége lesz rá az egy későbbi lépésben). Ha még nem hozott létre egy támogatási eseményt és segítségkéréseit, kövesse az útmutatóban talál: [Support](https://support.microsoft.com/contactus) új incidens megnyitásához.
4. A naplózás képernyőn kattintson a **azonnali küldés**.
5. Válassza ki a használni kívánt e-mail-szolgáltatónál.
6. Ha már nyitott incidenshez Support, forduljon a támogatási szakember, a probléma megoldásához rendelt megtudhatja, hogyan küldhet a naplóadatok és az incidens társított. A támogatási szakértőhöz biztosít Önnek az e-mail cím és a tulajdonos sorra vonatkozóan. Ha Ön még nem rendelkezik egy támogatási eseményt, kövesse az útmutató a Microsoft Support új incidens megnyitásához.
7. Szerkesztése a **való** sor és **tulajdonos** a Support kapott információkat tartalmazó sort.
8. Az Azure Authenticator alkalmazás csatol az e-mailt küld a naplófájlt. A tapasztalt probléma leírását, címzettlista (nem kötelező) frissítse és az e-mailt.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>A munkahelyi fiók törlése folyamatban van, és hagyja, a munkahelyen
Eltávolíthatja a következőképpen létrehozott bármikor munkahelyi fiókot:

**Törli a munkahelyi fiókot a beállítások menü**

1. Válassza ki a fiókkezelő **munkahelyi fiók**.
2. A munkahelyi fiók képernyőn a **általános beállítások**, jelölje be **fiókbeállításokat – hagyja üresen a munkahelyi hálózathoz**.
3. Válassza ki **hagyja** a a **munkahelyi csatlakoztatás** képernyő.
4. Kattintson a **OK** mikor "Biztosan, hogy a munkahelyi rendszer" üzenet jelenik meg.
5. Ez biztosítja, hogy a munkahelye törölte a munkahelyi fiókjával.

> [!NOTE]
> Javasoljuk, hogy nem használja a fiók eltávolítása lehetőséget egy munkahelyi fiókot törölni, mivel ez a beállítás nem működik az Android néhány korábbi verziójában.
> 
> 

## <a name="uninstalling-the-app"></a>Az alkalmazás eltávolítása
A Samsung Android-eszközön eszközt rendszergazdai jogosultságokkal előtt meg kell szüntetni a következő eltávolítása a 

1. A **beállítások**alatt **rendszer**válassza **biztonsági**.
2. D**köz felügyeleti**, kattintson a **eszközadminisztrátorok**. Győződjön meg arról, hogy a jelölőnégyzetet a **Azure Authenticator** törlődik.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha megjelenik a **Keystore hiba**, ennek oka az lehet, nem kell a zárolási képernyőn set be PIN-kóddal. A probléma megoldásához, távolítsa el az Azure Authenticator alkalmazást, konfigurálhatja a zárolási képernyő PIN-kód, és telepítse újra az alkalmazást.

