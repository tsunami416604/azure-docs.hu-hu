---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc8d3525ab7cdbdf298ecbbc686ced16fa7bc77c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060797"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

Az [Azure Portalon](https://portal.azure.com) az Azure Active Directory (Azure AD) jelentéskészítési funkciójával minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure Active Directory jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ez a témakör áttekintést nyújt a bejelentkezési tevékenységekről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* Felhasználók a biztonsági rendszergazdai, biztonsági olvasó, a jelentés olvasói szerepköre
* A globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?
* A bérlőjének prémium szintű Azure AD-licenccel kell rendelkeznie az összes bejelentkezési tevékenység jelentésének megtekintéséhez


## <a name="sign-in-activities"></a>Bejelentkezési tevékenységek

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Az első belépési pontja, amely minden bejelentkezési tevékenység adataihoz, **bejelentkezések** tevékenység szakaszában **Azure Active Directory**.


![Bejelentkezési tevékenység](./media/concept-sign-ins/61.png "Sign-in activity")


A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Bejelentkezési tevékenység](./media/concept-sign-ins/01.png "Sign-in activity")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/concept-sign-ins/19.png "Sign-in activity")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Bejelentkezési tevékenység](./media/concept-sign-ins/02.png "Sign-in activity")

A listanézet egyik elemére kattintva megtekintheti az összes elérhető részletét vízszintes nézetben.

![Bejelentkezési tevékenység](./media/concept-sign-ins/03.png "Sign-in activity")

> [!NOTE]
> Ügyfeleink most elháríthatja a feltételes hozzáférési szabályzatokat az összes bejelentkezési jelentéseken keresztül. Kattintson a a **feltételes hozzáférési** fülre a bejelentkezési rekord, az ügyfelek tekintheti át, a feltételes hozzáférési állapotát és adatait, a bejelentkezés és az eredmény az egyes szabályzatok alkalmazott házirendek bemutatása.
> További információkért lásd: a [– gyakran ismételt kérdések az összes bejelentkezés hitelesítésszolgáltató adatainak](reports-faq.md#conditional-access).

![Bejelentkezési tevékenység](./media/concept-sign-ins/ConditionalAccess.png "Sign-in activity")


## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi alapértelmezett mezőkkel szűrheti a bejelentkezési adatokat:

- Felhasználó
- Alkalmazás
- Bejelentkezési állapot
- A kockázatészlelés állapota
- Dátum

![Bejelentkezési tevékenység](./media/concept-sign-ins/04.png "Sign-in activity")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba

A **Kockázat észlelve** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Igen
- Nem

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Ha további mezőket ad hozzá a bejelentkezési nézethez, a rendszer automatikusan hozzáadja a mezőket a szűrőlistához. Például az **Ügyfélalkalmazás** mező listához való hozzáadásával kap egy további szűrőlehetőséget, amellyel a következő szűrőket állíthatja be:

- Böngésző      
- Exchange ActiveSync (támogatott)               
- Exchange ActiveSync (nem támogatott)
- Más ügyfelek               
    - IMAP
    - MAPI
    - Régebbi Office-ügyfelek
    - POP
    - SMTP


![Bejelentkezési tevékenység](./media/concept-sign-ins/12.png "Sign-in activity")


## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Letöltheti a bejelentkezési tevékenységek adatait, ha az Azure Portalon kívül szeretné használni őket. A **Letöltés** gombra kattintva létrejön egy CSV-fájl a legutóbbi 5 ezer rekordból.  A letöltés gomb mellett az Azure Portalon létrehozhat egy szkriptet is az adatok letöltésére.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

Ha nagyobb rugalmasságra van szüksége, használhat szkriptet. Kattintson a **parancsfájl** létrehoz egy PowerShell-parancsfájlt, amely tartalmazza az összes beállított szűrőket. Töltse le és futtassa ezt a szkriptet **módban rendszergazdának** a CSV-fájl létrehozásához. 

### <a name="running-the-script-on-a-windows-10-machine"></a>A Windows 10-es gépen a szkript futtatása

Ha azt szeretné, futtassa a parancsfájlt egy **Windows 10-es** gépen kell néhány további lépést végeznie. 

1. Telepítse a [AzureRM-modul](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. A modul importálásához nyissa meg egy PowerShell-parancssort, és a következő parancs futtatásával **Import-Module AzureRM**.
3. Futtatás **Set-ExecutionPolicy unrestricted** válassza **Igen, az összeset**. 
4. Most futtathatja a letöltött PowerShell-parancsfájl rendszergazdai módban, a CSV-fájl létrehozásához.

A technikai megvalósítás mellett a letölthető rekordok számát az [Azure Active Directory jelentésmegőrzési szabályzata](reference-reports-data-retention.md) is korlátozza.  


## <a name="sign-in-activities-shortcuts"></a>Bejelentkezési tevékenységek parancsikonjai

Azure Active Directory mellett az Azure portal nyújt jelentkezik be a további belépési pontokra tevékenységek adatokat:

- Az identitásbiztonság védelmének áttekintése
- Felhasználók
- Csoportok
- Vállalati alkalmazások


### <a name="users-sign-ins-activities"></a>Felhasználók bejelentkezési tevékenységei

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?

Az adatok megtekintését a felhasználók bejelentkezési grafikonjával kezdheti, amely **Az identitásbiztonság védelmének áttekintése** oldalon található. A felhasználók bejelentkezési grafikonja az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/06.png "Sign-in activity")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
- Felhasználó
- Felhasználónév
- Alkalmazásazonosító
- Alkalmazás
- Ügyfél
- Hely
- IP-cím
- Dátum
- Az MFA megadása kötelező
- Bejelentkezési állapot

 
A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/concept-sign-ins/08.png "Sign-in activity")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

![Bejelentkezési tevékenység](./media/concept-sign-ins/10.png "Sign-in activity")

A 3 legnépszerűbb alkalmazásba való bejelentkezések heti összesítő grafikonja egy adott időszak során. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/47.png "Sign-in activity")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Bejelentkezési tevékenység](./media/concept-sign-ins/11.png "Sign-in activity")



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a bejelentkezési tevékenységek hibakódjairól, lásd: [Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjai az Azure Active Directory portálon](reference-sign-ins-error-codes.md).

