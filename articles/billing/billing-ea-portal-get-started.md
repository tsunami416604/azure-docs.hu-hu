---
title: Ismerkedés az Azure EA Portallal
description: Ez a cikk azt ismerteti, hogyan használják az Azure nagyvállalati szerződéssel rendelkező ügyfelek az Azure EA Portalt.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 92581cfcace6ec566428c9cb54d205f108399b1f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660664"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Ismerkedés az Azure EA Portallal

Ez a cikk segítséget nyújt a közvetlen és közvetett Azure nagyvállalati szerződéssel rendelkező ügyfelek számára az [Azure EA Portal](https://ea.azure.com) használatának megkezdéséhez, az alábbi alapvető témakörök ismertetésével:

- Az Azure EA Portal felépítése.
- Az Azure EA Portalon használt szerepkörök.
- Az előfizetések létrehozásának alapjai.
- A költségek elemzése az Azure EA Portalon és az Azure Portalon.

Az alábbi videó az Azure EA Portal előkészítő lépéseit mutatja be:

[Az Azure EA Portal előkészítési videója](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Az Azure Portal hierarchiája

Az Azure Portal hierarchiája a következőkből áll:

**Microsoft Azure EA Portal** – Az Azure EA Portal egy online felügyeleti portál, amely segít az Azure EA-szolgáltatások költségeinek kezelésében. Egy Azure EA-hierarchia létrehozására használhatja, beleértve a részlegeket, a fiókokat és az előfizetéseket is. Arra is használhatja, hogy összeegyeztesse az igénybe vett szolgáltatások költségeit, letöltse a használati jelentéseket és megtekintse az árlistákat. Emellett API-kulcsokat is létrehozhat a regisztrációjához.

**Részlegek** – A részlegek létrehozásával segítheti a költségek logikai csoportokra történő szegmentálását, majd megadhat egy költségkeretet vagy kvótát a részlegszinten.

**Fiókok** – A fiókok az Azure EA Portalon található szervezeti egységek, és az előfizetések kezelésére szolgálnak. A fiókokat jelentéskészítésre is használják.

**Előfizetések** – Az előfizetések az Azure EA Portalon található legkisebb egységek. A szolgáltatásadminisztrátorok által kezelt Azure-szolgáltatások tárolóiként szolgálnak.

Az alábbi ábra egyszerű Azure EA-hierarchiákat mutat be.

![Egyszerű Azure EA-hierarchiák ábrája](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure-szolgáltatásoknak a regisztrációban történő felügyeletére négy különböző vállalati rendszergazdai felhasználói szerepkör jogosult:

- Vállalati rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos
- Szolgáltatás-rendszergazda
- Értesítendő fél

A szerepkörök két különböző Microsoft Azure-portálon végzett feladatok végrehajtásához használhatók. Az Azure EA Portal (https://ea.azure.com) a számlázás és a költségek kezelésére szolgál. Az Azure Portal (https://portal.azure.com) az Azure-szolgáltatások kezelésére szolgál.

A felhasználói szerepkörök felhasználói fiókhoz vannak rendelve. A felhasználók hitelességének ellenőrzéséhez minden felhasználónak érvényes munkahelyi, iskolai vagy Microsoft-fiókkal kell rendelkeznie. Győződjön meg arról, hogy mindegyik fiókhoz olyan e-mail-cím tartozik, amely rendszeresen ellenőrizve van. A fiókértesítéseket az e-mail-címekre küldi a rendszer.

A felhasználók beállításakor több munkahelyi, iskolai vagy Microsoft-fiókot is hozzárendelhet a vállalati rendszergazdai szerepkörhöz. Azonban csak egy munkahelyi, iskolai vagy Microsoft-fiókot rendelhet hozzá a fióktulajdonosi szerepkörhöz. Emellett egyetlen munkahelyi, iskolai vagy Microsoft-fiók rendelkezhet a vállalati rendszergazdai és a fióktulajdonosi szerepkörrel egyszerre.

### <a name="enterprise-administrator"></a>Vállalati rendszergazda

A vállalati rendszergazda rendelkezik a legmagasabb szintű hozzáféréssel. A szerepkörrel rendelkező felhasználók képesek:

- A fiókok és fióktulajdonosok kezelésére
- Más vállalati rendszergazdák kezelésére
- Részlegszintű rendszergazdák kezelésére
- Értesítendő felek kezelésére
- A fiókok használati adatainak megtekintésére
- A fiókok nem számlázott díjainak megtekintésére

Több vállalati rendszergazda is lehet egy vállalati regisztrációban. A vállalati rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Mindegyikük örökli a részlegszintű rendszergazdai szerepkört.

### <a name="department-administrator"></a>Részlegszintű rendszergazda

A szerepkörrel rendelkező felhasználók képesek:

- Részlegek létrehozására és kezelésére
- Új fióktulajdonosok létrehozására
- Az általuk kezelt részlegek használati adatainak megtekintésére
- A szükséges engedélyek birtokában a költségek megtekintésére

Több részlegszintű rendszergazdája is lehet az egyes vállalati regisztrációkban.

A részlegszintű rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Ha csak olvasási hozzáférést szeretne biztosítani, módosítson vagy hozzon létre egy új részlegszintű rendszergazdát, és állítsa a csak olvasási jogosultság beállítást **Igen** értékre.

### <a name="account-owner"></a>Fióktulajdonos

A szerepkörrel rendelkező felhasználók képesek:

- Előfizetések létrehozására és kezelésére
- Szolgáltatásadminisztrátorok kezelésére
- Előfizetések használati adatainak megtekintésére

Mindegyik fiókhoz szükség van egyedi munkahelyi, iskolai vagy Microsoft-fiókra. Az Azure EA Portal rendszergazdai szerepköreivel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Szolgáltatás-rendszergazda

A szolgáltatásadminisztrátor jogosult a szolgáltatások kezelésre az Azure Portalon, és a társadminisztrátor szerepkör felhasználókhoz való hozzárendelésére.

### <a name="notification-contact"></a>Értesítendő fél

Az értesítendő fél a regisztrációval kapcsolatos használati értesítéseket kap.

## <a name="activate-your-enrollment"></a>Regisztráció aktiválása

A szolgáltatás aktiválásához a kezdeti vállalati rendszergazda megnyitja az Azure EA Portalt ([https://ea.azure.com](https://ea.azure.com)), és bejelentkezik a meghívó e-mail e-mail-címével.

Ha Ön EA-rendszergazdaként lett beállítva, nem kell megvárnia az aktiválási e-mail megérkezését, hogy bejelentkezhessen az Azure EA Portalra. A [https://ea.azure.com](https://ea.azure.com) címen bejelentkezhet a munkahelyi, iskolai vagy Live ID-jához tartozó e-mail-címével és jelszavával.

Ha több regisztrációval rendelkezik, válasszon ki egyet az aktiváláshoz. Alapértelmezés szerint csak az aktív regisztrációk jelennek meg. A regisztrációs előzmények megtekintéséhez törölje az **Aktiválás** beállítás jelölőnégyzetét az Azure EA Portal jobb felső sarkában.

A Regisztráció alatt az állapot **Aktív**.

![Aktív regisztrációt bemutató példa](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Csak meglévő Azure vállalati rendszergazdák hozhatnak létre más vállalati rendszergazdákat.

### <a name="create-another-enterprise-admin"></a>Másik vállalati rendszergazda létrehozása

- Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com), és lépjen a **Kezelés** > **Regisztráció részletei** területre, majd kattintson az **+ Adminisztrátor hozzáadása** elemre az oldal jobb felső sarkában.

Győződjön meg arról, hogy tudja a felhasználó e-mail-címeit és előnyben részesített hitelesítési módszerét, úgymint munkahelyi, iskolai vagy Microsoft-fiók használatával végzett hitelesítés. Ezekre az információkra szükség van a felhasználó hozzáadásához.

Ha nem Ön a vállalati rendszergazda, forduljon egy vállalati rendszergazdához, és kérje meg, hogy adja hozzá Önt egy regisztrációhoz. Miután hozzá lett adva egy regisztrációhoz, egy aktiválási e-mailt fog kapni.

Ha a vállalati rendszergazda nem tud segíteni Önnek, hozzon létre egy [támogatási kérést az Azure EA Portalon](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Adja meg az alábbi információkat:

- Regisztrációs szám
- Hozzáadandó e-mail-cím és hitelesítési típus (munkahelyi, iskolai vagy Microsoft-fiók)
- E-mailes jóváhagyás egy meglévő vállalati rendszergazdától
  - Ha a meglévő vállalati rendszergazda nem érhető el, forduljon a partneréhez vagy a szoftvertanácsadójához, és kérje meg, hogy a VLSC eszközzel keresztül módosítsa a kapcsolattartási adatokat.

A vállalati rendszergazdai szerepkörrel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Azure EA-részleg létrehozása

A vállalati rendszergazdák és a rendszerszintű rendszergazdák a részlegek segítségével rendszerezik a vállalati Azure-szolgáltatásokat és a használati adatokat, és készítenek róluk jelentéseket részleg és költséghely szerint. A vállalati rendszergazdák képesek:

- Részlegek felvételére vagy eltávolítására
- Fiókok részlegekhez történő hozzárendelésére
- Részlegszintű rendszergazdák létrehozására
- Díjak és költségek megtekintésének engedélyezésére a részlegszintű rendszergazdák számára

A részlegszintű rendszergazdák új fiókokat adhatnak a részlegeikhez. Eltávolíthatnak fiókokat a részlegekből, de a regisztrációból nem.

Részleg hozzáadása:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Részleg** fülre, majd a **+ Részleg hozzáadása** elemre, és adja meg a kért adatokat.
3. A részleg neve az egyetlen kötelezően kitöltendő mező. A névnek legalább három karakterből kell állnia.
4. Amikor végzett, kattintson a **Hozzáadás** gombra.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

A részleg létrehozása után az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és mindegyiket hozzárendelheti egy részleghez. A részlegszintű rendszergazdák képesek:

- Más részlegszintű rendszergazdák létrehozására
- Olyan részlegtulajdonságok megtekintésére és módosítására, mint a név vagy a költségközpont
- Fiók hozzáadására a részlegeikhez
- Fiókok eltávolítására a részlegeikből
- A részlegeik használati adatainak letöltésére
- A részleg havi használati adatainak és díjainak megtekintésére, ha egy vállalati rendszergazda engedélyt adott nekik <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Vállalati rendszergazdaként:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Részleg** fülre, majd az adott részlegre.
3. Kattintson az **+ Adminisztrátor hozzáadása** elemre, és adja meg a kért adatokat.
4. Csak olvasási hozzáféréshez állítsa a **Csak olvasás** beállítást **Igen** értékre, majd kattintson a **Hozzáadás** elemre.

![A részlegszintű rendszergazda hozzáadására szolgáló párbeszédablakot bemutató példa](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Csak olvasási hozzáférés beállítása

A részlegszintű rendszergazdáknak adhat csak olvasási hozzáférést a. Új részlegszintű rendszergazda létrehozásakor:

- Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre.

Meglévő részlegszintű rendszergazda szerkesztéséhez:

1. Válassza ki a részleget, majd kattintson a ceruza szimbólumra a **Részlegszintű rendszergazda** elem mellett, amelyet szerkeszteni kíván.
2. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre. Ezután kattintson a **Mentés** gombra.

A vállalati rendszergazdai szerepkörrel rendelkező felhasználók automatikusan megkapják a részlegszintű rendszergazda engedélyeit.

<sup>1</sup> Ha engedélyt kapott a részleg havi használati adatainak és díjainak megtekintésére, de nem látja őket, forduljon a partneréhez.

## <a name="add-an-account"></a>Fiók hozzáadása

A fiók és az előfizetés szerkezete befolyásolja azok felügyeletét és azt, hogy azok hogyan jelennek meg a számlákon és a jelentéseken. Jellemző szervezeti szerkezet például a vállalati részlegek, a funkció szerinti csapatok és a földrajzi helyszínek alapján történő bontás.

Fiók hozzáadásához:

1. Az Azure EA Portalon kattintson a **Kezelés** elemre a bal oldali navigációs területen.
2. Kattintson a **Fiók** fülre, majd a **Fiók** oldalon kattintson a **+Fiók hozzáadása** elemre.
3. Válasszon ki egy részleget, vagy hagyja hozzárendelés nélkül, majd válassza ki a kívánt hitelesítési típust.
4. Adjon meg egy felhasználóbarát nevet a fiók jelentéskor történő beazonosításához.
5. Adja meg a **Fióktulajdonos e-mail-címét** az új fiókhoz történő hozzárendeléshez.
6. Erősítse meg az e-mail-címet, majd kattintson a **Hozzáadás** elemre.

![A fiókok listáját bemutató példa és a + Fiók hozzáadása lehetőség](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Hozzáadhat egy másik fiókot a **Másik fiók hozzáadása** elemre, vagy a bal oldali eszköztár jobb alsó sarkában lévő **Hozzáadás** gombra kattintva.

A fióktulajdonjog megerősítéséhez:

1. Jelentkezzen be az Azure EA Portalra.
1. Ellenőrizze a fióktulajdonjogot az állapot megtekintésével. Az állapotnak **Függőben** állapotról **Kezdő és záró dátum** állapotra kell átváltania. A Kezdő és záró dátum a felhasználó első bejelentkezésének és a szerződés végének a dátuma.
1. Amikor megjelenik a „Figyelmeztetés” üzenet, a fiók tulajdonosának a **Folytatás** gombra kell kattintania a fiók aktiválásához, amikor először jelentkezik be az Azure EA portálra.


## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

A vállalati rendszergazdák az Azure EA Portal használatával átruházhatják az előfizetői fiókok tulajdonjogát a regisztrációkban. A művelet áthelyezi az összes előfizetést a forrásként megadott felhasználói fiókból egy célként megadott felhasználói fiókba.

Fontos tudnivalók a felhasználói fiókok adatainak átviteléről:

- Az egyik munkahelyi vagy iskolai fiókról másik munkahelyi vagy iskolai fiókra történő átvitel támogatott.
- A Microsoft-fiókról munkahelyi vagy iskolai fiókra történő átvitel támogatott.
- A munkahelyi vagy iskolai fiókról Microsoft-fiókra történő átvitel nem támogatott.
- Az egyik Microsoft-fiókról a másik Microsoft-fiókra történő átvitel támogatott. A célfióknak érvényes Azure Commerce-fióknak kell lennie, hogy érvényes átviteli cél lehessen. Új fiókok esetén új Azure Commerce-fiókot kell létrehoznia az Azure EA Portalra való bejelentkezéskor. Meglévő fiókok esetén először új Azure-előfizetést kell létrehoznia ahhoz, hogy a fiók jogosulttá váljon.
- Az előfizetés-átvitel befejezésekor a Microsoft frissíti a fióktulajdonost.

Szerepköralapú hozzáférés-vezérlés házirendjei:

- Csak az ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés-átvitelek őrzik meg a meglévő Azure szerepköralapú hozzáférés-vezérlési (RBAC) szabályzatokat, a szolgáltatásadminisztrátori szerepkör-hozzárendeléseket és a társadminisztrátori szerepkör-hozzárendeléseket. Más előfizetés-átvitelek az RBAC-szabályzatok és a szolgáltatásadminisztrátori szerepkör-hozzárendelések elvesztését eredményezhetik. A szabályzatok és a rendszergazdai szerepkörök nem vihetők át különböző címtárakba. A szolgáltatásadminisztrátorokból a célfiók tulajdonosa lesz.
- Ha ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés-átvitelt hajt végre, az RBAC-szabályzatok és a meglévő szolgáltatásadminisztrátori és társadminisztrátori szerepkörök megmaradnak.

A fióktulajdonos módosítása előtt:

1. Tekintse meg a **Fiók** lapot, és határozza meg a forrásfiókot. A forrásfióknak aktívnak kell lennie.
2. Azonosítsa be a célfiókot. Aktívnak kell lennie.

A fiók tulajdonjogának átvitele az összes előfizetésre vonatkozóan:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Fiók** fülre, és vigye a mutatót a fiók fölé.
3. Kattintson a fióktulajdonos módosításának szimbólumára a jobb oldalon. A szimbólum egy személyt ábrázol.
4. Válasszon ki egy jogosult fiókot, majd kattintson a **Következő** gombra.
5. Erősítse meg az átvitelt, majd kattintson a **Küldés** gombra.

![A Fióktulajdonos módosítása szimbólumot bemutató kép](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

A fiók tulajdonjogának átvitele egy előfizetésre vonatkozóan:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Fiók** fülre, és vigye a mutatót a fiók fölé.
3. Kattintson az előfizetések átvitelének szimbólumára a jobb oldalon. A szimbólum egy lapot ábrázol.
4. Válasszon ki egy jogosult előfizetést, majd kattintson a **Következő** gombra.
5. Erősítse meg az átvitelt, majd kattintson a **Küldés** gombra.

![Az Előfizetések átvitele szimbólumot bemutató kép](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Az alábbi videó az Azure EA Portal felhasználókezelését mutatja be:

[Videó az Azure EA Portal felhasználókezeléséről](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Előfizetés létrehozása

A fióktulajdonosok megtekinthetik és kezelhetik az előfizetéseket. Az előfizetésekkel hozzáférést adhat a szervezeti csoportoknak a fejlesztési környezetekhez és a projektekhez. Például a teszteléshez, az éles környezethez, a fejlesztéshez és az előkészítéshez. Amikor különböző előfizetéseket hoz létre az egyes alkalmazáskörnyezetekhez, elősegíti a környezetek védelmét. Mindegyik előfizetéshez különböző szolgáltatásadminisztrátori fiókot rendelhet. Az előfizetéseket korlátlan számú szolgáltatáshoz hozzárendelheti. A fiók tulajdonosa hoz létre előfizetéseket, és rendel hozzá egy szolgáltatásadminisztrátori fiókot a fiókjában lévő összes előfizetéshez.

### <a name="add-a-subscription"></a>Előfizetés hozzáadása

Az alábbiak alapján adhat hozzá előfizetést.

Amikor első alkalommal ad előfizetést a fiókjához, el kell fogadnia a MOSA-szerződést és egy díjkonstrukciót. Bár a Nagyvállalati Szerződéssel rendelkező ügyfelekre ezek nem vonatkoznak, az előfizetés létrehozásához szükségesek. A Microsoft Azure Nagyvállalati Szerződés regisztrációs kiegészítése felülírja a fenti elemeket, így az Ön szerződéses jogviszonya nem módosul. Amikor a rendszer kéri, jelölje be a feltételek elfogadására szolgáló jelölőnégyzetet.

Minden új előfizetés a _Microsoft Azure Enterprise_ alapértelmezett névvel jön létre. Módosíthatja az előfizetés nevét, hogy megkülönböztesse a regisztráció többi előfizetésétől, és azért, hogy a vállalati szintű jelentésekben felismerhető legyen.

Előfizetés hozzáadásához:

1. Az Azure EA Portalon jelentkezzen be a fiókjába.
2. Kattintson a **Rendszergazda** fülre, majd az **Előfizetés** elemre az oldal tetején.
2. Ellenőrizze, hogy a fiók tulajdonosaként jelentkezett-e be.
3. Kattintson az **+Előfizetés hozzáadása**, majd a **Vásárlás** elemre.
  Amikor első alkalommal ad előfizetést a fiókjához, meg kell adnia a kapcsolattartási adatait. További előfizetések hozzáadásakor a rendszer hozzáadja a megadott kapcsolattartási adatait.
4. Kattintson az **Előfizetések** elemre, majd válassza ki a létrehozott előfizetést, és kattintson az **Előfizetés adatainak szerkesztése** elemre.
5. Frissítse az **Előfizetés neve** és a **Szolgáltatásadminisztrátor** adatokat, majd jelölje be a jelölőnégyzetet.
  Az előfizetés neve megjelenik a jelentéseken, és a fejlesztési portálon az előfizetéshez rendelt projekt nevét is ez adja.

Akár 24 órába is telhet, amíg az új előfizetések megjelennek az előfizetések listáján. Az előfizetés létrehozása után elvégezheti az alábbiakat:

- [Az előfizetés adatainak szerkesztése](https://account.azure.com/Subscriptions)
- [Az előfizetési szolgáltatások kezelése](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>EA-előfizetés átvitele használatalapú előfizetéssé

Ha egy EA-előfizetést használatalapú fizetésű önálló előfizetéssé szeretné átvinni, új támogatási kérést kell létrehoznia az Azure EA portálon. Támogatási kérés létrehozásához kattintson az **+ Új támogatási kérés** elemre a Súgó és támogatás területen.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Meglévő fiók társítása használatalapú előfizetéssel

Ha már rendelkezik meglévő Microsoft Azure-fiókkal a Microsoft Azure Portalon, akkor adja meg a társított Microsoft-fiókot, munkahelyi vagy iskolai fiókot, hogy a Nagyvállalati Szerződés regisztrációhoz rendelhesse.

### <a name="associate-an-existing-account"></a>Meglévő fiók hozzárendelése

1. Az Enterprise Portalon kattintson a **Kezelés** elemre.
1. Kattintson a **Fiók** fülre.
1. Kattintson a **+Fiók hozzáadása** elemre.
1. Adja meg a meglévő fiókhoz társított Microsoft-fiókot, munkahelyi vagy iskolai fiókot.
1. Erősítse meg a meglévő fiókhoz társított Microsoft-fiókot, munkahelyi vagy iskolai fiókot.
1. Adjon meg egy nevet a fiók jelentéskor történő azonosításához.
1. Kattintson a **Hozzáadás** parancsra.
1. További fiók hozzáadásához válassza a **+ Fiók hozzáadása** lehetőséget, vagy térjen vissza a kezdőlapra a **Rendszergazda** gomb kiválasztásával.
1. Ha a **Fiók** lap megtekintésére kattint, az újonnan hozzáadott fiók **Függőben lévő** állapotban fog megjelenni.

### <a name="confirm-account-ownership"></a>Fiók tulajdonjogának megerősítése

1. Jelentkezzen be a megadott Microsoft-fiókhoz, munkahelyi vagy iskolai fiókhoz rendelt e-mail-fiókba.
1. Nyissa meg az _„Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing”_ (A Microsoft mennyiségi licencelés meghívója fiókja aktiválására a Microsoft Azure Service-ben) című e-mail-értesítést.
1. Kattintson a meghívóban a **Log into the Microsoft Azure Enterprise Portal** (Bejelentkezés a Microsoft Azure Enterprise Portalra) hivatkozásra.
1. Kattintson a **Bejelentkezés** elemre.
1. Adja meg a Microsoft-fiók, munkahelyi vagy iskolai fiók nevét és jelszavát a bejelentkezéshez és annak megerősítéséhez, hogy Ön a fiók tulajdonosa.

### <a name="azure-marketplace"></a>Azure Piactér

Bár a legtöbb előfizetés a használatalapú fizetési környezetből átváltható nagyvállalati Azure-ra, az Azure Marketplace szolgáltatásai nem. Az összes előfizetés és díj egyetlen nézetéhez javasoljuk, hogy adja hozzá az Azure Marketplace-szolgáltatásokat az Enterprise Portalhoz:

1. Kattintson a **Kezelés** elemre a bal oldali navigációs sávon.
1. Kattintson a **Regisztráció** fülre.
1. Tekintse meg a Regisztráció részletei szakaszt.
1. Kattintson az Azure Marketplace mező jobb oldalán lévő ceruza ikonra az engedélyezéshez, majd nyomja le a **Mentés** gombot.

A fiók tulajdonosa így már megvásárolhatja a korábban használatalapú tulajdonú Azure Marketplace-előfizetéseket.

Miután az új Azure Marketplace-előfizetéseket aktiválta a regisztrációjában, mondja le a használatalapú környezetben létrehozott Marketplace-előfizetéseket. Ez a lépés kritikus fontosságú, így a Marketplace-előfizetések nem válnak rossz állapotúvá, amikor a használatalapú fizetési eszköz lejár.

### <a name="msdn"></a>MSDN

Az MSDN-előfizetések automatikusan az MSDN Dev/Test szolgáltatásba alakulnak át, és az EA-ajánlat elveszít minden meglévő pénzügyi kreditet.

### <a name="azure-in-open"></a>Azure in Open

Az Azure in Open licencprogramon keresztül vásárolt Azure-előfizetések nagyvállalati szerződéssel való társításával az el nem használt kreditek elvesznek. A kreditek esetleges elvesztésének elkerülése érdekében javasoljuk, hogy használja el az Azure in Open licencprogramokon keresztül vásárolt Azure-előfizetésekhez tartozó összes kreditet, mielőtt nagyvállalati szerződéshez adná hozzá a fiókját.  

### <a name="accounts-with-support-subscriptions"></a>Támogatási előfizetésekkel rendelkező fiókok

Ha meglévő fiókokat ad hozzá egy támogatási előfizetéssel rendelkező Enterprise Portalhoz (és még nincs nagyvállalati támogatási előfizetése), vegye figyelembe, hogy a MOSA támogatási előfizetés átvitele nem történik meg automatikusan, és a támogatást újra meg kell vásárolnia a nagyvállalati szerződésben. A támogatási lefedettséghez a következő hónap végéig türelmi időszakot nyújtunk, hogy elég ideje legyen a támogatás átrendezésére.

## <a name="view-usage-summary-and-download-reports"></a>Használati adatok összegzésének megtekintése és jelentések letöltése

A vállalati rendszergazdák az Azure EA Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az egyes fiókok részletes használati adatainak megtekintése:

Töltse le a használati adatokról szóló jelentést. Kattintson a **Jelentések** elemre, majd a **Használati adatok letöltése** lapra. A jelentések listáján kattintson a **Letöltés** elemre annál a havi jelentésnél, amelyet le szeretne tölteni.

A jelentés nem tartalmazza a vonatkozó adókat. A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

A használati adatokat összegző jelentések és ábrák megtekintése:

1. Az Azure EA Portal bal oldali navigációs területén kattintson a **Jelentések** elemre, és tekintse meg a **Használati adatok összegzése** lapot.  
  ![Használati adatok összegzésének létrehozása és megtekintése, illetve jelentések letöltése](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Válasszon ki egy pénzügyikeret-időtartamot.
3. Váltson az **M** (Havi) és a **C** (Egyéni) módok között az oldal jobb felső részén, hogy egyéni kezdő- és záró dátummal is megtekinthesse a **használati adatok összegzését**.  
  ![Használati adatok összegzésének létrehozása és megtekintése, illetve jelentések letöltése egyéni nézetben](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon további részletek megtekintéséhez.
5. A grafikon a havi használatot mutatja a használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és a Marketplace-költségek megjelenítésével.
6. A kiválasztott hónapra vonatkozóan a grafikon alatt részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
7. Válthat a díjak **szolgáltatások** és **hierarchia** szerinti megjelenítése között.
8. A részletek megtekintéséhez kibonthatja vagy összecsukhatja az **Azure-szolgáltatások**, a **Külön számlázott díjak** és az **Azure Marketplace** területeket.

Az alábbi videó a használati adatok megtekintésének módját mutatja be:

[Videó az Azure EA Portal használati adatinak megtekintéséről](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják több jelentés CSV-fájlként való letöltéséhez. Ezek például az alábbi jelentések lehetnek:

- Egyenleg és díjak
- Használati adatok
- A Marketplace díjai
- Árlista

A jelentések letöltéséhez:


1. Az Azure EA Portalon kattintson a **Jelentések** elemre.
2. Kattintson a lap tetején található **Használati adatok letöltése** elemre.
3. Válassza a **Letöltés** elemet a havi jelentés mellett.

A használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.

A CSV-fájlokat a Safari böngészőből az Excelbe letöltő felhasználók formázási hibákat tapasztalhatnak. A hibák elkerülése érdekében szövegszerkesztővel nyissa meg a fájlt.

![A Használati adatok letöltése oldalt bemutató példa](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Az alábbi videó a használati adatok letöltésének módját mutatja be:

[Videó az Azure EA Portal használati adatinak megtekintéséről](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. 2016. augusztus 30-tól a kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében .xlsx-ről .csv-re változik.

1. Válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a **megfelelő dátumtartományt**.
1. Válassza ki a **megfelelő fiókokat**.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Válassza a **Frissítés** gombot, amíg a jelentés állapota **Letöltés** értékűre frissül.
1. Jelentés letöltése.

## <a name="ea-term-glossary"></a>EA-kifejezések szószedete

- **Fiók**: Az Azure EA Portal szervezeti egysége, amely az előfizetések felügyeletére, valamint jelentéskészítésre szolgál.
- **Fióktulajdonos**: A Microsoft Azure-ban az előfizetések és a szolgáltatásadminisztrátorok kezeléséhez azonosított személy. Megtekinthetik ezen fiók és a társított előfizetéseinek használati adatait.
- **Módosítási előfizetés**: A regisztrációs módosítás alá eső egy egyéves vagy egybeeső előfizetés.
- **Kötelezettségvállalás**: A Microsoft Azure-szolgáltatásokra kedvezményes kötelezettségvállalási díjszabással fizetett éves pénzügyi összeg kötelezettségvállalása az előre fizetéssel való használathoz.
- **Részlegszintű rendszergazda**: A részlegek kezeléséhez, új fiókok és fióktulajdonosok létrehozásához, az általuk kezelt részlegek használati adatinak megtekintéséhez és engedélyek esetén a költségek megtekintéséhez azonosított személy(ek).
- **Regisztrációs szám**: A Microsoft által a nagyvállalati szerződéshez társított adott regisztráció azonosítására megadott egyedi azonosító.
- **Vállalati rendszergazda**: A Microsoft Azure-on a részlegek és részlegtulajdonosok, valamint a fiókok és fióktulajdonosok kezelésére azonosított személy(ek). Képesek kezelni a vállalati rendszergazdákat, valamint megtekinthetik a használati adatokat, a számlázott mennyiségeket és a nem számlázott díjakat a nagyvállalati regisztrációhoz társított összes fiókra és előfizetésre vonatkozóan.
- **Nagyvállalati szerződés**: Központosított vásárlást használó Microsoft-licencszerződés olyan ügyfelek számára, akik a Microsoft technológiájára alapozva szeretnék szabványosítani teljes szervezetüket, és szabványos Microsoft-szoftverekkel szeretnék fenntartani az informatikai infrastruktúrájukat.
- **Nagyvállalati szerződéses regisztráció**: Regisztráció a nagyvállalati szerződési programba, amely kedvezményes áron nyújt Microsoft-termékeket.
- **Microsoft-fiók**: Webalapú szolgáltatás, amellyel a résztvevő helyek egyetlen hitelesítőadat-készlettel hitelesíthetik a felhasználókat.
- **Microsoft Azure nagyvállalati regisztrációs módosítás (regisztrációs módosítás)** : Egy vállalat által aláírt módosítás, amely a vállalati regisztráció részeként hozzáférést biztosít számukra a Microsoft Azure-hoz.
- **Azure EA Portal**: A vállalati ügyfeleink által a Microsoft Azure-fiókok és a kapcsolódó előfizetések kezeléséhez használt portál.
- **Felhasznált erőforrások mennyisége**: Az egy adott hónapban felhasznált egyes Microsoft Azure-szolgáltatások mennyisége.
- **Szolgáltatásadminisztrátor**: Az Azure EA Portalon az előfizetések és fejlesztési projektek eléréséhez és kezeléséhez azonosított személy.
- **Előfizetés**: Egy Azure EA Portal-előfizetést képvisel, és az ugyanazon szolgáltatásadminisztrátor által kezelt Microsoft Azure-szolgáltatások tárolója.
- **Munkahelyi vagy iskolai fiók**: Olyan szervezeteknek, amelyek felhőbeli összevonással telepítették az Active Directoryt, és amelyek esetében az összes fiók egyetlen bérlőn található.

### <a name="enrollment-statuses"></a>Regisztrációs állapotok:

- **Függőben**: A regisztrációs rendszergazdának be kell jelentkeznie az Azure EA Portalra. Ha bejelentkezett, a regisztráció aktív állapotra vált.
- **Aktív**: A regisztráció aktív, és az Azure EA Portalon fiókok és előfizetések hozhatók létre. A regisztráció a nagyvállalati szerződés záró dátumáig aktív marad.
- **Határozatlan időre meghosszabbítva**: A határozatlan időre meghosszabbítva állapot a nagyvállalati szerződés záró dátuma után jelenik meg. Lehetővé teszi a meghosszabbított időszakot választó, nagyvállalati szerződéssel rendelkező ügyfelek számára, hogy a nagyvállalati szerződésük végén továbbra is határozatlan ideig használják az Azure-t. Mielőtt az EA-regisztráció elérné a nagyvállalati szerződés záró dátumát, a regisztrációs rendszergazdának el kell döntenie, hogy megújítja- a regisztrációt további pénzügyi kötelezettségvállalás hozzáadásával, új regisztrációra vált, a Microsoft Online Előfizetési Programra (MOSP) migrál, vagy megerősíti a regisztrációval társított összes szolgáltatás letiltását.
- **Lejárt**: Az EA-ügyfél nem kérte az időtartam meghosszabbítását, és az EA-regisztráció elérte a nagyvállalati szerződés záró dátumát, a regisztráció le fog járni, és az összes kapcsolódó szolgáltatás le lesz tiltva.
- **Átvitt**: Az Átvitt állapotban jelennek meg azok a regisztrációk, amelyek társított fiókjainak és szolgáltatásainak mindegyikét új regisztrációba vitték át. Vegye figyelembe, hogy a regisztrációk nem kerülnek át automatikusan, ha a megújításkor új regisztrációs szám jön létre. Az előző regisztrációs számnak szerepelnie kell az ügyfél megújítási dokumentumaiban az automatikus átvitel megkönnyítése érdekében.

## <a name="get-started-on-azure-ea-faq"></a>Bevezetés az Azure EA használatába – gyakori kérdések

Ez a dokumentum részletesen ismerteti az ügyfelek által az előkészítési folyamat során feltett gyakori kérdéseket.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Társíthatom a meglévő Azure-fiókomat vállalati regisztrációhoz?

Igen. Fontos megjegyezni, hogy az összes olyan Azure-előfizetés, amely esetén Ön a fióktulajdonos, a nagyvállalati szerződésre lesz átváltva. Ez magában foglalja a havi kreditet (pl. Visual Studio, AzurePass, MPN, BizSpark stb.) használó előfizetéseket, ami azt jelenti, hogy ezzel elveszíti a havi kreditet.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Véletlenül társítottam a meglévő Azure-fiókot a vállalati regisztrációval. Ennek eredményeképpen elveszítettem a havi kreditet. Lehetséges a havi kreditek visszaszerzése?

Ha egyéni Visual Studio-előfizetésének Azure-előnyeit szeretné visszakapni az EA-fióktulajdonosként történt hitelesítés után, amikor is ugyanazokat a bejelentkezési adatokat használta az EA-hez, mint a Visual Studio-előfizetéshez, az alábbiak valamelyikét kell tennie:
1. Törölje ezt a fióktulajdonost az EA Portalon, miután eltávolította vagy áthelyezte a tulajdonában lévő Azure-előfizetéseket, és kérje meg, hogy ismét regisztráljon az egyéni Visual Studio Azure-előnyeire.
 VAGY
1. Törölje a Visual Studio-előfizetőt a felügyeleti webhelyen a VLSC-ben, rendelje hozzá újra az előfizetést, és kérje meg, hogy ezúttal más bejelentkezési adatokat használjon – ezután ismét regisztrálhat az egyéni Visual Studio Azure-előnyeire.

### <a name="what-type-of-subscription-should-i-create"></a>Milyen típusú előfizetést kell létrehoznom?

Az EA Portal két típusú előfizetést kínál a vállalati ügyfelek számára:

- Microsoft Azure Enterprise – a következőkhöz ideális:
  - Minden éles használat
  - A legjobb árak az infrastruktúrakiadások alapján
  - További információ: https://azure.microsoft.com/pricing/enterprise-agreement/
- Enterprise Dev/Test – a következőkhöz ideális:
  - Minden, csapatban zajló fejlesztési/tesztelési számítási feladat
  - A közepestől a nagy méretűig terjedő egyéni fejlesztési/tesztelési számítási feladatok
  - Hozzáférés különleges MSDN-lemezképekhez és kedvezményes szolgáltatási díjakhoz
  - További információ: https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Lehetséges az előfizetés tulajdonjogának átadása egy másik fióknak?

Igen, az előfizetés tulajdonjoga átadható egy másik fióknak. Ha például az „A” fiók három előfizetéssel rendelkezik, a vállalati rendszergazda átvihet egy-egy előfizetést a „B”, a „C” és a „D” fiókba, vagy mindegyiket az „E” fiókba.

Lépjen az EA-portálra, és kattintson a Kezelés > Fiók elemre. Vigye a mutatót a **Fiók** fölé (a jobb szélen), és látni fogja a Tulajdonjogok átadása (portré ikon) és az Előfizetés átadása (lista ikon) lehetőséget.

Ez a lehetőség csak aktív fiókok esetén jelenik meg.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Az előfizetés neve az ajánlat neveként jelenik meg alapértelmezés szerint. Módosítsam az előfizetés nevét a vállalatom számára jelentéssel bíró névre?

A létrehozott előfizetések alapértelmezés szerint a kiválasztott ajánlattípus nevét kapják. Javasoljuk, hogy módosítsa az előfizetés nevét olyan névre, amellyel könnyebben követheti nyomon az előfizetést.

**A név módosítása:**
1. Jelentkezzen be itt: [https://account.windowsazure.com](https://account.windowsazure.com).
1. Kattintson az Előfizetés listára.
1. Válassza az Előfizetés elemet.
1. Kattintson az **Előfizetés kezelése** ikonra.
1. Szerkessze az előfizetés adatait.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Hogyan követhetem nyomon a felmerült költségeket költséghely alapján?

A költség költséghely alapján történő nyomon követéséhez a következő szintek valamelyikén meg kell adnia egy költséghelyet:
- Részleg
- Fiók
- Előfizetés

Ha szükséges, ugyanezzel a költséghellyel követhet nyomon egy adott költséghelyhez kapcsolódó használatot és költségeket.

Ha például egy olyan különleges projekt költségeit szeretné nyomon követni, amelyben több részleg is részt vesz, a költséghelyet célszerű előfizetési szinten használni a használat és a költség nyomon követéséhez.

Költséghely nem definiálható szolgáltatási szinten, és ha szolgáltatási szinten szeretné nyomon követni a használatot, a szolgáltatási szinten elérhető „Címke” funkciót használhatja.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hogyan követhetem nyomon a használatot és a kiadást a szervezet különböző részlegei alapján?

Az EA-regisztrációban tetszőleges számú részleget hozhat létre. A használat megfelelő nyomon követéséhez gondoskodnia kell arról, hogy az előfizetések ne legyenek megosztva a részlegek között.

A részleg és az előfizetés létrehozásának befejezése után a használati jelentésben láthatja azokat az információkat, amelyekkel könnyebben követheti nyomon a használatot, és kezelheti a költségeket/kiadásokat a részleg szintjén.

A használattal kapcsolatos információkat API által biztosított adatokkal és a következő helyen elérhető mintakóddal is elérheti: [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Megadhatom a költségkvótát, és kérhetek riasztásokat, ha közelítek a korlát felé?

Költségkvótát a részleg szintjén állíthat be, és a rendszer automatikusan értesíti Önt, amikor a költségkeret eléri a megadott kvóta 50%-át, 75%-át, 90%-át és 100%-át.

A költségkvóta meghatározásához kattintson arra a részlegre, amelyhez költségkeretet szeretne hozzáadni, majd kattintson a Szerkesztés ikonra. A részletek mentéséhez kattintson a **Mentés** elemre.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Erőforráscsoportokat használtam az RBAC implementálásához és a használat nyomon követéséhez. Hogyan tekinthetem meg a kapcsolódó használati adatokat?

Az olyan információk, mint az „Erőforráscsoportok” és „Címke” (ha használatban van) nyomon követése a szolgáltatás szintjén történik, és az információk a részletes használati adatokat tartalmazó, az Azure EA Portalról [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage) letölthető fájlban (CSV-fájl) érhetők el.

A használattal kapcsolatos információkat API által biztosított adatokkal és a következő helyen elérhető mintakóddal is elérheti: [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Vegye figyelembe, hogy csak olyan erőforrásokra alkalmazhat címkéket, amelyek támogatják a Resource Manager-műveleteket. Ha egy virtuális gépet, virtuális hálózatot vagy tárolót a klasszikus üzemi modellel (például a klasszikus portált használva) hozott létre, akkor nem alkalmazhat címkét az adott erőforrásra. A címkézés támogatásához ismételten üzembe kell helyeznie ezeket az erőforrásokat a Resource Managerrel. Minden más erőforrás támogatja a címkézést.

### <a name="can-i-perform-analyses-using-power-bi"></a>Végezhetek elemzéseket Power BI használatával?

Igen. A Power BI-hoz készült Microsoft Azure Enterprise tartalomcsomaggal könnyen importálhatja és elemezheti a vállalati regisztrációinak Azure-használatát, kiderítheti, melyik részleg, fiók vagy előfizetés idézte elő a legtöbb használatot, és melyik szolgáltatást használta a legtöbbet a vállalata, illetve nyomon követheti a költekezési és használati trendeket.

**Lépjen a Power BI webhelyére:**

 1. Jelentkezzen be egy érvényes munkahelyi vagy iskolai fiókkal.
    - A munkahelyi vagy iskolai fiók lehet ugyanaz vagy egy másik fiók, mint amelyet a regisztrációnak az Azure EA Portallal történő eléréséhez használ.
 1. A szolgáltatások irányítópultján válassza a következőt:
    - Microsoft Azure Enterprise csempe.
    - Kattintson a **Connect** (Csatlakozás) gombra.
 1. Az Azure Enterprise-hoz való kapcsolódás képernyőjén válassza a következőt:
    - Az Azure-környezet URL-címe: [https://ea.azure.com](https://ea.azure.com).
    - Hónapok száma: válasszon 1 és 36 között.
    - Regisztrációs szám: adja meg a regisztrációs számot.
    - Kattintson a **Tovább** gombra.
 1. Adja meg az API-kulcsot a Hitelesítési kulcs mezőben. Az API-kulcsot az Azure EA Portal „Használati adatok letöltése” lapján kérheti le az **API hozzáférési kulcsa** elemre kattintva.
    - Másolja és illessze be a kulcsot a „Fiókkulcs” mezőbe.
    - Az adathalmaz méretétől függően az adatok betöltése a Power BI-ba 5–30 percet vesz igénybe.

A Power BI-jelentések készítése olyan közvetlen, partneri és közvetett vállalati szerződéssel rendelkező ügyfelek számára érhető el, akik meg tudják tekinteni a számlázási adatokat.

## <a name="next-steps"></a>További lépések

- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](billing-ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](billing-ea-portal-troubleshoot.md).
- Az Azure EA előkészítési útmutatóját az [Azure EA előkészítési útmutatóját](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) tartalmazó témakörben tekintheti meg.
