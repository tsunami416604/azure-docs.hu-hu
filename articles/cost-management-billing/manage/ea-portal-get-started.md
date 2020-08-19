---
title: Ismerkedés az Azure Enterprise portállal
description: Ez a cikk azt ismerteti, hogyan használják az Azure Nagyvállalati Szerződéssel (Azure EA) rendelkező ügyfelek az Azure Enterprise portált.
author: bandersmsft
ms.author: banders
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: a4e5b1164bb0a983504d068cc94ba35de9f5a66a
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244262"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Ismerkedés az Azure Enterprise portállal

Ez a cikk segítséget nyújt a közvetlen és közvetett Azure Nagyvállalati Szerződéssel (Azure EA) rendelkező ügyfeleknek az [Azure Enterprise portál](https://ea.azure.com) használatához. Alapvető információk:

- Az Azure Enterprise portál felépítése.
- Az Azure Enterprise portálon használt szerepkörök.
- Előfizetés létrehozása.
- A költségek elemzése az Azure Enterprise portálon és az Azure Portalon.

Tekintse meg ezt a videót az Azure Enterprise portál előkészítő lépéseinek megtekintéséhez:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Az Azure Enterprise portál hierarchiája

Az Azure Enterprise portál hierarchiája az alábbiakat tartalmazza:

- Az **Azure Enterprise portál** egy online felügyeleti portál, amely segít az Azure EA-szolgáltatások költségeinek kezelésében. A következőket teheti:

  - Hozzon létre egy Azure EA-hierarchiát részlegekkel, fiókokkal és előfizetésekkel.
  - Összeegyeztetheti az igénybe vett szolgáltatások költségeit, letöltheti a használati jelentéseket és megtekintheti az árlistákat.
  - Létrehozhat API-kulcsokat a regisztrációjához.

- A **részlegek** segítenek a költségek logikai csoportosítás szerinti szegmentálásában. A részlegek lehetővé teszik, hogy megadjon egy költségkeretet vagy kvótát a részlegszinten.

- A **fiókok** az Azure Enterprise portálon található szervezeti egységek. A fiókok segítségével kezelheti az előfizetéseket és hozzáférhet a jelentésekhez.

- Az **előfizetések** az Azure Enterprise portálon található legkisebb egységek. A szolgáltatásadminisztrátorok által kezelt Azure-szolgáltatások tárolóiként szolgálnak.

Az alábbi ábra egyszerű Azure EA-hierarchiákat mutat be.

![Egyszerű Azure EA-hierarchiák ábrája](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az alábbi rendszergazdai felhasználói szerepkörök a vállalati regisztráció részei:

- Vállalati rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos
- Szolgáltatás-rendszergazda
- Értesítendő fél

A szerepkörök két különböző portálon végzett feladatok végrehajtásához használhatók. Az [Azure Enterprise portált](https://ea.azure.com) használhatja a számlázás és a költségek kezeléséhez, az [Azure Portalt](https://portal.azure.com) pedig az Azure-szolgáltatások kezeléséhez.

A felhasználói szerepkörök felhasználói fiókhoz vannak rendelve. A felhasználók hitelességének ellenőrzéséhez minden felhasználónak érvényes munkahelyi, iskolai vagy Microsoft-fiókkal kell rendelkeznie. Győződjön meg arról, hogy mindegyik fiókhoz olyan e-mail-cím tartozik, amely rendszeresen ellenőrizve van. A fiókértesítéseket az e-mail-címekre küldi a rendszer.

A felhasználók beállításakor több fiókot is hozzárendelhet a vállalati rendszergazdai szerepkörhöz. Azonban csak egy fióktulajdonos engedélyezett. Emellett egyszerre egyetlen fiók rendelkezhet a vállalati rendszergazdai és a fióktulajdonosi szerepkörrel.

### <a name="enterprise-administrator"></a>Vállalati rendszergazda

Az ilyen szerepkörrel felruházott felhasználók rendelkeznek a legmagasabb szintű hozzáféréssel. Ezek a következők:

- A fiókok és fióktulajdonosok kezelése.
- Más vállalati rendszergazdák kezelése.
- Részlegszintű rendszergazdák kezelése.
- Értesítendő felek kezelése.
- A fiókok használati adatainak megtekintése.
- A fiókok nem számlázott díjainak megtekintése.

Több vállalati rendszergazda is lehet egy vállalati regisztrációban. A vállalati rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Mindegyikük örökli a részlegszintű rendszergazdai szerepkört.

### <a name="department-administrator"></a>Részlegszintű rendszergazda

A szerepkörrel rendelkező felhasználók képesek:

- Részlegek létrehozására és kezelésére.
- Új fióktulajdonosok létrehozására.
- Az általuk kezelt részlegek használati adatainak megtekintésére.
- A szükséges engedélyek birtokában a költségek megtekintésére.

Több részlegszintű rendszergazdája is lehet az egyes vállalati regisztrációkban.

A részlegszintű rendszergazdákhoz rendelhet csak olvasási hozzáférést is, amikor részlegszintű rendszergazdát módosít vagy hoz létre. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre.

### <a name="account-owner"></a>Fióktulajdonos

A szerepkörrel rendelkező felhasználók képesek:

- Előfizetések létrehozására és kezelésére.
- Szolgáltatásadminisztrátorok kezelésére.
- Előfizetések használati adatainak megtekintésére.

Mindegyik fiókhoz szükség van egyedi munkahelyi, iskolai vagy Microsoft-fiókra. Az Azure Enterprise portál rendszergazdai szerepköreivel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md).

### <a name="service-administrator"></a>Szolgáltatás-rendszergazda

A szolgáltatásadminisztrátor jogosult a szolgáltatások kezelésre az Azure Portalon, és a társadminisztrátori szerepkör felhasználókhoz való hozzárendelésére.

### <a name="notification-contact"></a>Értesítendő fél

Az értesítendő fél a regisztrációval kapcsolatos használati értesítéseket kap.

## <a name="activate-your-enrollment"></a>Regisztráció aktiválása

A szolgáltatás aktiválásához a kezdeti vállalati rendszergazda megnyitja az [Azure Enterprise portált](https://ea.azure.com), és bejelentkezik a meghívási e-mail e-mail-címével.

Ha Ön vállalati rendszergazdaként lett beállítva, nem kell megvárnia az aktiválási e-mail megérkezését. Az [Azure Enterprise portálon](https://ea.azure.com) bejelentkezhet a munkahelyi, iskolai vagy Microsoft-fiókjához tartozó e-mail-címével és jelszavával.

Ha több regisztrációval rendelkezik, válasszon ki egyet az aktiváláshoz. Alapértelmezés szerint csak az aktív regisztrációk jelennek meg. A regisztrációs előzmények megtekintéséhez törölje az **Aktív** beállítás jelölőnégyzetét az Azure Enterprise portál jobb felső sarkában.

A **Regisztráció** alatt az állapot **Aktív**.

![Aktív regisztrációt bemutató példa](./media/ea-portal-get-started/ea-enrollment-status.png)

Csak meglévő Azure vállalati rendszergazdák hozhatnak létre más vállalati rendszergazdákat.

### <a name="create-another-enterprise-administrator"></a>Másik vállalati rendszergazda létrehozása

Másik vállalati rendszergazda hozzáadása:

1. Jelentkezzen be az [Azure Enterprise portálra](https://ea.azure.com).
1. Lépjen a **Kezelés** > **Regisztráció részletei** menüpontra.
1. A lap jobb felső részén kattintson az **+Adminisztrátor hozzáadása** elemre.

Győződjön meg arról, hogy tudja a felhasználó e-mail-címét és előnyben részesített hitelesítési módszerét (például munkahelyi, iskolai vagy Microsoft-fiók használatával végzett hitelesítés).

Ha nem Ön a vállalati rendszergazda, forduljon egy vállalati rendszergazdához, és kérje meg, hogy adja hozzá Önt egy regisztrációhoz. Miután hozzá lett adva egy regisztrációhoz, egy aktiválási e-mailt fog kapni.

Ha a vállalati rendszergazda nem tud segíteni Önnek, hozzon létre egy [támogatási kérést az Azure Enterprise portálon](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Adja meg az alábbi információkat:

- Regisztrációs szám
- Hozzáadandó e-mail-cím és hitelesítési típus (munkahelyi, iskolai vagy Microsoft-fiók)
- E-mailes jóváhagyás egy meglévő vállalati rendszergazdától
  - Ha a meglévő vállalati rendszergazda nem érhető el, forduljon a partneréhez vagy a szoftvertanácsadójához, és kérje meg, hogy a Mennyiségilicenc-szolgáltatási központ (VLSC) eszközön keresztül módosítsa a kapcsolattartási adatokat.

A vállalati rendszergazdai szerepkörrel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Azure Enterprise-részleg létrehozása

A vállalati rendszergazdák és a rendszerszintű rendszergazdák a részlegek segítségével rendszerezik a vállalati Azure-szolgáltatásokat és a használati adatokat, és készítenek róluk jelentéseket részleg és költséghely szerint. A vállalati rendszergazdák képesek:

- Részlegek felvételére vagy eltávolítására.
- Fiókok részlegekhez történő hozzárendelésére.
- Részlegszintű rendszergazdák létrehozására.
- Díjak és költségek megtekintésének engedélyezésére a részlegszintű rendszergazdák esetén.

A részlegszintű rendszergazdák új fiókokat adhatnak a részlegeikhez. Eltávolíthatnak fiókokat a részlegekből, de a regisztrációból nem.

Részleg hozzáadása:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A bal oldali panelen válassza a **Kezelés** lehetőséget.
1. Válassza a **Részleg** fület, majd a **+ Részleg hozzáadása** elemet.
1. Adja meg az információkat.
   A részleg neve az egyetlen kötelezően kitöltendő mező. A névnek legalább három karakterből kell állnia.
1. Amikor végzett, válassza a **Hozzáadás** elemet.

## <a name="add-a-department-administrator"></a>Részlegszintű rendszergazda hozzáadása

A részleg létrehozása után a vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és mindegyiket hozzárendelheti egy részleghez. A részlegszintű rendszergazdák a következő műveleteket hajthatják végre a részlegeiken:

- Más részlegszintű rendszergazdák létrehozására
- Olyan részlegtulajdonságok megtekintése és módosítása, mint a név vagy a költségközpont
- Fiókok hozzáadása
- Fiókok eltávolítása
- Használati adatok letöltése
- Havi használati adatok és költségek megtekintése <sup>1</sup>

> <sup>1</sup>A vállalati rendszergazdának kell megadnia ezeket az engedélyeket. Ha engedélyt kapott a részleg havi használati adatainak és díjainak megtekintésére, de nem látja őket, forduljon a partneréhez.

### <a name="to-add-a-department-administrator"></a>Részlegszintű rendszergazda hozzáadása

Vállalati rendszergazdaként:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A bal oldali panelen válassza a **Kezelés** lehetőséget.
1. Válassza a **Részleg** fület, majd válassza ki az adott részleget.
1. Válassza az **+ Adminisztrátor hozzáadása** elemet, és adja meg a kért adatokat.
1. Csak olvasási hozzáféréshez állítsa a **Csak olvasás** beállítást **Igen** értékre, majd válassza a **Hozzáadás** elemet.

![A részlegszintű rendszergazda hozzáadására szolgáló párbeszédpanelt bemutató példa](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Csak olvasási hozzáférés beállítása

A részlegszintű rendszergazdáknak adhat csak olvasási hozzáférést a.

- Ha létrehoz egy új részlegszintű rendszergazdát, állítsa a csak olvasási jogosultság beállítást **Igen** értékre.

- Meglévő részlegszintű rendszergazda szerkesztéséhez:
   1. Válassza ki a részleget, majd válassza a ceruza szimbólumot azon **Részlegszintű rendszergazda** elem mellett, amelyet szerkeszteni kíván.
   1. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre, majd válassza a **Mentés** elemet.

A vállalati rendszergazdák automatikusan megkapják a részlegszintű rendszergazda engedélyeit.

## <a name="add-an-account"></a>Fiók hozzáadása

A fiókok és az előfizetések szerkezete befolyásolja azok felügyeletét és azt, hogy azok hogyan jelennek meg a számlákon és a jelentéseken. Jellemző szervezeti szerkezet például a vállalati részlegek, a funkció szerinti csapatok és a földrajzi helyszínek alapján történő bontás.

Fiók hozzáadásához:

1. Az Azure Enterprise portálon válassza a **Kezelés** elemet a bal oldali navigációs területen.
1. Válassza a **Fiók** fület. A **Fiók** lapon válassza a **+ Fiók hozzáadása** elemet.
1. Válasszon ki egy részleget, vagy hagyja hozzárendelés nélkül, majd válassza ki a kívánt hitelesítési típust.
1. Adjon meg egy felhasználóbarát nevet a fiók jelentéskészítéskor történő beazonosításához.
1. Adja meg a **Fióktulajdonos e-mail-címét** az új fiókhoz történő hozzárendeléshez.
1. Erősítse meg az e-mail-címet, majd válassza a **Hozzáadás** elemet.

![A fiókok listáját bemutató példa és a + Fiók hozzáadása lehetőség](./media/ea-portal-get-started/create-ea-add-an-account.png)

Hozzáadhat egy másik fiókot a **Másik fiók hozzáadása** elemre, vagy a bal oldali eszköztár jobb alsó sarkában lévő **Hozzáadás** gombra kattintva.

A fióktulajdonjog megerősítéséhez:

1. Jelentkezzen be az Azure Enterprise portálra.
1. Állapot megtekintése.

   Az állapotnak **Függőben** állapotról **Kezdő és záró dátum** állapotra kell átváltania. A Kezdő és záró dátum a felhasználó első bejelentkezésének és a szerződés végének a dátuma.
1. Amikor megjelenik a **Figyelmeztetés** üzenet, a fiók tulajdonosának a **Folytatás** gombot kell kiválasztania a fiók aktiválásához, amikor először jelentkezik be az Azure Enterprise portálra.

## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

A vállalati rendszergazdák az Azure Enterprise portál használatával átruházhatják az előfizetői fiókok tulajdonjogát a regisztrációkban. A művelet áthelyezi az összes előfizetést a forrásként megadott felhasználói fiókból egy célként megadott felhasználói fiókba.

Fiókok átvitelekor vegye figyelembe ezeket a fontos információkat:

- Az átviteleket végrehajthatja:
  - Az egyik munkahelyi vagy iskolai fiókról egy másik munkahelyi vagy iskolai fiókra.
  - Egy Microsoft-fiókról egy munkahelyi vagy iskolai fiókra.
  - Az egyik Microsoft-fiókról egy másik Microsoft-fiókra.

    A célfióknak érvényes Azure Commerce-fióknak kell lennie, hogy érvényes átviteli cél lehessen. Új fiókok esetén új Azure Commerce-fiókot kell létrehoznia az Azure Enterprise portálra való bejelentkezéskor. Meglévő fiókok esetén először új Azure-előfizetést kell létrehoznia ahhoz, hogy a fiók jogosulttá váljon.

- Nem végezhet átvitelt munkahelyi vagy iskolai fiókról Microsoft-fiókra.

- Az előfizetés-átvitel befejezésekor a Microsoft frissíti a fióktulajdonost.

A szerepköralapú hozzáférés-vezérlési (RBAC-) szabályok megismerése:

- Ha ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés-átvitelt hajt végre, az RBAC-szabályzatok és a meglévő szolgáltatásadminisztrátori és társadminisztrátori szerepkörök megmaradnak.
- Más előfizetés-átvitelek az RBAC-szabályzatok és a szerepkör-hozzárendelések elvesztését eredményezhetik.
- A szabályzatok és a rendszergazdai szerepkörök nem vihetők át különböző címtárakba. A szolgáltatásadminisztrátorokból a célfiók tulajdonosa lesz.

A fióktulajdonos módosítása előtt:

1. Az Azure Enterprise portálon tekintse meg a **Fiók** lapot, és határozza meg a forrásfiókot. A forrásfióknak aktívnak kell lennie.
1. Azonosítsa be a célfiókot, és győződjön meg arról, hogy aktív.

A fiók tulajdonjogának átvitele az összes előfizetésre vonatkozóan:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A navigációs terület bal oldalán válassza ki a **Kezelés** elemet.
1. Válassza a **Fiók** fület, és vigye a mutatót a fiók fölé.
1. Válassza a fióktulajdonos módosításának ikonját a jobb oldalon. Az ikon egy személyt ábrázol.
1. Válasszon ki egy jogosult fiókot, majd válassza a **Következő** gombot.
1. Erősítse meg az átvitelt, majd válassza a **Küldés** gombot.

![A Fióktulajdonos módosítása szimbólumot bemutató kép](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

A fiók tulajdonjogának átvitele egy előfizetésre vonatkozóan:

1. Jelentkezzen be az Azure Enterprise portálra.
1. A navigációs terület bal oldalán válassza ki a **Kezelés** elemet.
1. Válassza a **Fiók** fület, és vigye a mutatót a fiók fölé.
1. Válassza az előfizetések átvitelének ikonját a jobb oldalon. Az ikon egy lapot ábrázol.
1. Válasszon ki egy jogosult előfizetést, majd válassza a **Következő** gombot.
1. Erősítse meg az átvitelt, majd válassza a **Küldés** gombot.

![Az Előfizetések átvitele szimbólumot bemutató kép](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Tekintse meg ezt a videót az Azure Enterprise portál felhasználókezelésének áttekintéséhez:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Előfizetés létrehozása

A fióktulajdonosok megtekinthetik és kezelhetik az előfizetéseket. Az előfizetésekkel hozzáférést adhat a szervezeti csoportoknak a fejlesztési környezetekhez és a projektekhez. Például a teszteléshez, az éles környezethez, a fejlesztéshez és az előkészítéshez.

Amikor különböző előfizetéseket hoz létre az egyes alkalmazáskörnyezetekhez, elősegíti a környezetek védelmét.

- Mindegyik előfizetéshez különböző szolgáltatásadminisztrátori fiókot rendelhet.
- Az előfizetéseket korlátlan számú szolgáltatáshoz hozzárendelheti.
- A fiók tulajdonosa hoz létre előfizetéseket, és rendel hozzá egy szolgáltatásadminisztrátori fiókot a fiókjában lévő összes előfizetéshez.

### <a name="add-a-subscription"></a>Előfizetés hozzáadása

Az alábbiak alapján adhat hozzá előfizetést.

Amikor első alkalommal ad előfizetést a fiókjához, el kell fogadnia a Microsoft Online Előfizetői Szerződést (MOSA) és egy díjkonstrukciót. Bár a Nagyvállalati Szerződéssel rendelkező ügyfelekre nem vonatkoznak, a MOSA és a díjkonstrukció szükséges az előfizetés létrehozásához. A Microsoft Azure Nagyvállalati Szerződés regisztrációs kiegészítése felülírja a fenti elemeket, így az Ön szerződéses jogviszonya nem módosul. Amikor a rendszer kéri, jelölje be a feltételek elfogadására szolgáló jelölőnégyzetet.

Előfizetés létrehozásakor a _Microsoft Azure Enterprise_ az alapértelmezett név. Megváltoztathatja a nevet, hogy megkülönböztesse az új előfizetést a regisztráció többi előfizetésétől, és gondoskodhat arról, hogy az felismerhető legyen a vállalati szintű jelentésekben.

Előfizetés hozzáadásához:

1. Az Azure Enterprise portálon jelentkezzen be a fiókba.
1. Válassza a **Rendszergazda** fület, majd az **Előfizetés** elemet az oldal tetején.
1. Ellenőrizze, hogy a fiók tulajdonosaként jelentkezett-e be.
1. Válassza az **+Előfizetés hozzáadása**, majd a **Vásárlás** elemet.

   Amikor első alkalommal ad előfizetést a fiókjához, meg kell adnia a kapcsolattartási adatait. További előfizetések hozzáadásakor a rendszer hozzáadja a megadott kapcsolattartási adatait.

1. Válassza az **Előfizetések** elemet, majd válassza ki a létrehozott előfizetést.
1. Válassza **Az előfizetés adatainak szerkesztése** elemet.
1. Módosítsa az **Előfizetés neve** és a **Szolgáltatásadminisztrátor** adatait, majd jelölje be a jelölőnégyzetet.

   Az előfizetés neve megjelenik a jelentéseken. Ez a fejlesztési portálon az előfizetéshez rendelt projekt neve.

Akár 24 órába is telhet, amíg az új előfizetések megjelennek az előfizetések listáján. Az előfizetés létrehozása után elvégezheti az alábbiakat:

- [Az előfizetés adatainak szerkesztése](https://account.azure.com/Subscriptions)
- [Az előfizetési szolgáltatások kezelése](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Vállalati regisztráció átvitele egy használatalapú fizetéses előfizetésbe

Ha egy Enterprise-előfizetést használatalapú fizetésű önálló előfizetésbe szeretne átvinni, új támogatási kérést kell létrehoznia az Azure Enterprise portálon. Támogatási kérés létrehozásához válassza az **+ Új támogatási kérés** elemet a **Súgó és támogatás** területen.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Meglévő fiók társítása használatalapú előfizetéssel

Ha már rendelkezik meglévő Microsoft Azure-fiókkal az Azure Portalon, akkor adja meg a társított iskolai, munkahelyi vagy Microsoft-fiókot, hogy a Nagyvállalati Szerződés regisztrációhoz rendelhesse.

### <a name="associate-an-existing-account"></a>Meglévő fiók hozzárendelése

1. Az Azure Enterprise portálon válassza a **Kezelés** elemet.
1. Válassza a **Fiók** fület.
1. Válassza a **+Fiók hozzáadása** elemet.
1. Adja meg a meglévő Azure-fiókhoz társított munkahelyi, iskolai vagy Microsoft-fiókot.
1. Erősítse meg a meglévő Azure-fiókhoz társított fiókot.
1. Adjon meg egy nevet a fiók jelentéskor történő azonosításához.
1. Válassza a **Hozzáadás** lehetőséget.
1. További fiók hozzáadásához válassza újra a **+ Fiók hozzáadása** lehetőséget, vagy térjen vissza a kezdőlapra a **Rendszergazda** gomb kiválasztásával.
1. Ha megtekinti a **Fiók** lapot, az újonnan hozzáadott fiók **Függőben** állapotban fog megjelenni.

### <a name="confirm-account-ownership"></a>Fiók tulajdonjogának megerősítése

1. Jelentkezzen be a munkahelyi, iskolai vagy Microsoft-fiókhoz társított e-mail-fiókba.
1. Nyissa meg az _„Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing”_ (A Microsoft mennyiségi licencelés meghívója fiókja aktiválására a Microsoft Azure Service-ben) című e-mail-értesítést.
1. Válassza a meghívóban a **Log into the Microsoft Azure Enterprise Portal** (Bejelentkezés a Microsoft Azure Enterprise Portalra) hivatkozást.
1. Válassza a **Bejelentkezés** lehetőséget.
1. Adja meg a munkahelyi, iskolai vagy Microsoft-fiók nevét és jelszavát a bejelentkezéshez és annak megerősítéséhez, hogy Ön a fiók tulajdonosa.

### <a name="azure-marketplace"></a>Azure Piactér

Bár a legtöbb előfizetés a használatalapú fizetési környezetből átváltható Azure Nagyvállalati Szerződésre, az Azure Marketplace szolgáltatásai nem. Az összes előfizetés és díj egyesített nézetéhez javasoljuk, hogy adja hozzá az Azure Marketplace-szolgáltatásokat az Azure Enterprise portálhoz.

1. Jelentkezzen be az Azure Enterprise portálra.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Regisztráció** fület.
1. Tekintse meg a **Regisztráció részletei** szakaszt.
1. Kattintson az Azure Marketplace mező jobb oldalán lévő ceruza ikonra az engedélyezéshez. Kattintson a **Mentés** gombra.

A fiók tulajdonosa így már megvásárolhatja a korábban használatalapú előfizetésben tulajdonolt Azure Marketplace-szolgáltatásokat.

Miután az új Azure Marketplace-előfizetéseket aktiválta az Azure EA-regisztrációjában, mondja le a használatalapú környezetben létrehozott Azure Marketplace-szolgáltatásokat. Ez a lépés kritikus fontosságú, így az Azure Marketplace-előfizetések nem válnak rossz állapotúvá, amikor a használatalapú fizetési eszköz lejár.

### <a name="msdn"></a>MSDN

Az MSDN-előfizetések automatikusan az MSDN Dev/Test szolgáltatássá alakulnak át, és az Azure EA-ajánlat elveszít minden meglévő pénzügyi kreditet.

### <a name="azure-in-open"></a>Azure in Open

Az Azure in Open licencprogramon keresztül vásárolt Azure-előfizetések Nagyvállalati Szerződéssel való társításával a fel nem használt kreditek elvesznek. Így azt javasoljuk, hogy használja fel az Azure in Open-előfizetéshez tartozó összes kreditet, mielőtt Nagyvállalati Szerződéshez adná hozzá a fiókját.  

### <a name="accounts-with-support-subscriptions"></a>Támogatási előfizetésekkel rendelkező fiókok

Ha az Ön Nagyvállalati Szerződése nem rendelkezik támogatási előfizetéssel, és egy meglévő fiókot támogatási előfizetéssel ad hozzá az Azure Enterprise portálhoz, akkor a MOSA támogatási előfizetésének átvitele automatikusan nem történik meg. Újra meg kell vásárolnia egy támogatási előfizetést az Azure EA-ban a türelmi idő alatt a következő hónap végéig.

## <a name="view-usage-summary-and-download-reports"></a>Használati adatok összegzésének megtekintése és jelentések letöltése

A vállalati rendszergazdák az Azure Enterprise portálon megtekinthetik a használati adataik összegzését, a felhasznált Azure-előrefizetési keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az egyes fiókok részletes használati adatainak megtekintéséhez töltse le a használati adatokról szóló jelentést:

1. Jelentkezzen be az Azure Enterprise portálra.
1. Válassza a **Jelentések** elemet.
1. Válassza a **Használati adatok letöltése** fület.
1. A jelentések listáján válassza a **Letöltés** elemet annál a havi jelentésnél, amelyet le szeretne tölteni.

   > [!NOTE]
   > A használati adatokról szóló jelentés nem tartalmazza a vonatkozó adókat.
   >
   > A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

A használati adatokat összegző jelentések és ábrák megtekintése:

1. Jelentkezzen be az Azure Enterprise portálra.

1. Válasszon ki egy előrefizetési időszakot.

   Váltson az **M** (Havi) és a **C** (Egyéni) módok között az oldal jobb felső részén, hogy módosítsa a **Használati adatok összegzésének** dátumtartományát, majd adjon meg egyéni kezdő és záró dátumokat.

   ![Használati adatok összegzésének létrehozása és megtekintése, illetve jelentések letöltése egyéni nézetben](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon a további részletek megtekintéséhez.

   - A grafikon a havi használatot mutatja a használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és az Azure Marketplace-költségek megjelenítésével.
   - A kiválasztott hónapra vonatkozóan a grafikon alatti mezők segítségével részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
   - Válthat a díjak **szolgáltatások** és **hierarchia** szerinti megjelenítése között.
   - A részletek megtekintéséhez kibonthatja vagy összecsukhatja az **Azure-szolgáltatások**, a **Külön számlázott díjak** és az **Azure Marketplace** területet.

Tekintse meg az alábbi videót a használati adatok megtekintési módjának megismeréséhez:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják az alábbi jelentések CSV-fájlként való letöltéséhez:

- Egyenleg és díjak
- Használati adatok
- Az Azure Marketplace díjai
- Árlista

A jelentések letöltéséhez:

1. Az Azure Enterprise portálon válassza a **Jelentések** elemet.
2. Válassza a lap tetején található **Használati adatok letöltése** elemet.
3. Válassza a **Letöltés** elemet a havi jelentés mellett.

   > [!NOTE]
   > A használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.
   >
   > A CSV-fájlokat a Safari böngészőből az Excelbe letöltő felhasználók formázási hibákat tapasztalhatnak. A hibák elkerülése érdekében szövegszerkesztővel nyissa meg a fájlt.

![A Használati adatok letöltése oldalt bemutató példa](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Tekintse meg az alábbi videót a használati adatok letöltési módjának megismeréséhez:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. A kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében a .csv.

1. Az Azure Enterprise portálon válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a megfelelő dátumtartományt és a megfelelő fiókokat.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Használja a **Frissítés** gombot, amíg a jelentés állapota **Letöltés** értékűre nem frissül.
1. Töltse le a jelentést.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Használati jelentések és a számlázási információk letöltése egy korábbi regisztrációhoz

A regisztrációt követően letölthet a korábbi regisztrációk egyikére vonatkozó használati jelentéseket és számlázási információkat. Az előzményeket feldolgozó jelentések az Azure Enterprise portálon és a Cost Management felületén egyaránt elérhetők.

Az Azure Enterprise portál kiszűri az inaktív regisztrációkat. Az inaktív átvitt regisztrációk megtekintéséhez törölnie kell az **Aktív** jelölőnégyzet jelölését.  

![Az aktív jelölőnégyzet jelölésének törlésével a felhasználó láthatja az inaktív regisztrációkat is](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Azure EA-kifejezések szószedete

- **Fiók**: Az Azure Enterprise portálon található szervezeti egység. Az előfizetések felügyeletére, valamint jelentéskészítésre szolgál.
- **Fióktulajdonos**: Az Azure-ban az előfizetések és a szolgáltatásadminisztrátorok kezeléséhez azonosított személy. Megtekinthetik ezen fiók és a társított előfizetéseinek használati adatait.
- **Módosítási előfizetés**: A regisztrációs módosítás alá eső egy egyéves vagy egybeeső előfizetés.
- **Előrefizetés**: Az Azure-szolgáltatásokra kedvezményes előrefizetési díjszabással fizetett éves pénzügyi összeg előrefizetése az előrefizetéssel való használathoz.
- **Részlegszintű rendszergazda**: A részlegek kezelésére, új fiókok és fióktulajdonosok létrehozására, az általuk kezelt részlegek használati adatainak megtekintésére és megadott engedélyek esetén a költségek megtekintésére jogosult személy.
- **Regisztrációs szám**: A Microsoft által a Nagyvállalati Szerződéshez társított adott regisztráció azonosítására megadott egyedi azonosító.
- **Vállalati rendszergazda**: Az Azure-on a részlegek, részlegtulajdonosok, fiókok és fióktulajdonosok kezelésére jogosult személy. Képesek kezelni a vállalati rendszergazdákat, valamint megtekinthetik a használati adatokat, a számlázott mennyiségeket és a nem számlázott díjakat a nagyvállalati regisztrációhoz társított összes fiókra és előfizetésre vonatkozóan.
- **Nagyvállalati szerződés**: Központosított vásárlást használó Microsoft-licencszerződés olyan ügyfelek számára, akik a Microsoft technológiájára alapozva szeretnék szabványosítani teljes szervezetüket, és szabványos Microsoft-szoftverekkel szeretnék fenntartani az informatikai infrastruktúrájukat.
- **Nagyvállalati szerződéses regisztráció**: Regisztráció a Nagyvállalati Szerződési programba, amely kedvezményes áron nyújt Microsoft-termékeket nagy mennyiségben.
- **Microsoft-fiók**: Webalapú szolgáltatás, amellyel a részt vevő helyek egyetlen hitelesítőadat-készlettel hitelesíthetik a felhasználókat.
- **Microsoft Azure nagyvállalati regisztrációs módosítás (regisztrációs módosítás)** : Egy vállalat által aláírt módosítás, amely a vállalati regisztráció részeként hozzáférést biztosít számukra az Azure-hoz.
- **Azure Enterprise portál**: A vállalati ügyfeleink által az Azure-fiókok és a kapcsolódó előfizetések kezeléséhez használt portál.
- **Felhasznált erőforrások mennyisége**: Az egy adott hónapban felhasznált egyes Azure-szolgáltatások mennyisége.
- **Szolgáltatásadminisztrátor**: Az Azure Enterprise portálon az előfizetések és fejlesztési projektek elérésére és kezelésére jogosult személy.
- **Előfizetés**: Egy Azure Enterprise portál-előfizetést jelöl, és az ugyanazon szolgáltatásadminisztrátor által kezelt Azure-szolgáltatások tárolója.
- **Munkahelyi vagy iskolai fiók**: Olyan szervezetek esetén, amelyek felhőbeli összevonással állították be az Active Directoryt, és amelyek esetében az összes fiók egyetlen bérlőn található.

### <a name="enrollment-statuses"></a>Regisztrációs állapotok

- **Újdonság**: Ez az állapot egy 24 órán belül létrehozott regisztrációhoz van hozzárendelve, és 24 órán belül Függőben állapotba kerül.
- **Függőben**: A regisztrációs rendszergazdának be kell jelentkeznie az Azure Enterprise portálra. Ha bejelentkezett, a regisztráció aktív állapotra vált.
- **Aktív**: A regisztráció aktív, és az Azure Enterprise portálon fiókok és előfizetések hozhatók létre. A regisztráció a Nagyvállalati Szerződés záró dátumáig aktív marad.
- **Határozatlan időre meghosszabbítva**: A határozatlan időre meghosszabbítva állapot a Nagyvállalati Szerződés záró dátuma után jelenik meg. Lehetővé teszi a meghosszabbított időszakot választó, Nagyvállalati Szerződéssel rendelkező Azure EA-ügyfelek számára, hogy a Nagyvállalati Szerződésük végén továbbra is határozatlan ideig használják az Azure-szolgáltatásokat.

   Mielőtt az Azure EA-regisztráció elérné a Nagyvállalati Szerződés záró dátumát, a regisztrációs rendszergazdának el kell döntenie, hogy az alábbi lehetőségek melyikével szeretne élni:

  - Megújítja a regisztrációt további Azure-előrefizetés hozzáadásával.
  - Áttér egy új regisztrációba.
  - Migrálást végez a Microsoft Online Előfizetési Programba (MOSP).
  - Jóváhagyja a regisztrációhoz társított szolgáltatások letiltását.
- **Lejárt**: Az Azure EA-ügyfél nem kérte az időtartam meghosszabbítását, és az Azure EA-regisztráció elérte a Nagyvállalati Szerződés záró dátumát. A regisztráció le fog járni, és az összes kapcsolódó szolgáltatás le lesz tiltva.
- **Átvitt**: Az átvitt állapotban jelennek meg azok a regisztrációk, amelyek társított fiókjainak és szolgáltatásainak mindegyikét egy új regisztrációba vitték át.
  >[!NOTE]
  > A regisztrációk nem kerülnek át automatikusan, ha a megújítás során új regisztrációs szám jön létre. Az előző regisztrációs számának szerepelnie kell a megújítási dokumentumaiban az automatikus átvitel megkönnyítése érdekében.

## <a name="get-started-on-azure-ea---faq"></a>Bevezetés az Azure EA használatába – gyakori kérdések

Ez a szakasz részletesen ismerteti az ügyfelek által az előkészítési folyamat során feltett gyakori kérdéseket.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Társíthatom a meglévő Azure-fiókomat Azure EA-regisztrációhoz?

Igen. Az összes olyan Azure-előfizetés, amely esetén Ön a fióktulajdonos, a Nagyvállalati Szerződésre lesz átváltva. Ez magában foglalja a havi kreditet (pl. Visual Studio, AzurePass, MPN, BizSpark stb.) használó előfizetéseket. Az ilyen előfizetésekre való átváltással elveszíti a havi kreditet.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Véletlenül társítottam a meglévő Azure-fiókot az Azure EA-regisztrációval. Ennek eredményeképpen elveszítettem a havi kreditet. Vissza tudom szerezni a havi kreditjeimet?

Ha ugyanazokkal a hitelesítő adatokkal jelentkezett be az Azure EA-fiók tulajdonosaként, mint amikkel a Visual Studio-előfizetésébe szokott, a következő műveletek valamelyikével helyreállíthatja a Visual Studio-előfizetés Azure-előnyeit:

- Távolítsa el a fióktulajdonosát az Azure Enterprise portálról a kapcsolódó Azure-előfizetések eltávolítása vagy áthelyezése után. Ezután regisztráljon újból a Visual Studio Azure-előnyeire.
- Törölje a Visual Studio-előfizetőt a felügyeleti webhelyen a VLSC-ben, majd rendelje hozzá újra az előfizetést egy eltérő hitelesítési adatokkal rendelkező fiókhoz. Ezután regisztráljon újból a Visual Studio Azure-előnyeire.

### <a name="what-type-of-subscription-should-i-create"></a>Milyen típusú előfizetést kell létrehoznom?

Az Azure Enterprise portál két előfizetés-típust kínál a vállalati ügyfelek számára:

- A Microsoft Azure Enterprise a következőkhöz ideális:
  - Minden éles használat
  - A legjobb árak az infrastruktúrakiadások alapján

  Bővebb információkért [lépjen kapcsolatba az Azure értékesítési csapatával](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Az Enterprise Dev/Test a következőkhöz ideális:
  - Minden, csapatban zajló fejlesztési/tesztelési számítási feladat
  - A közepestől a nagy méretűig terjedő egyéni fejlesztési/tesztelési számítási feladatok
  - Hozzáférés különleges MSDN-lemezképekhez és kedvezményes szolgáltatási díjakhoz

  Bővebb információért lásd az [Enterprise Dev/Test ajánlatot](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Lehetséges az előfizetés tulajdonjogának átadása egy másik fióknak?

Igen, az előfizetés tulajdonjoga átadható egy másik fióknak. Ha például az „A” fiók három előfizetéssel rendelkezik, a vállalati rendszergazda átvihet egy-egy előfizetést a „B”, a „C” és a „D” fiókba, vagy mindegyiket az „E” fiókba.

Előfizetések átvitele:

1. Az Azure Enterprise portálon válassza a **Kezelés** > **Fiók** elemet.
1. Vigye a mutatót a **Fiók** fölé a jobb szélen, és látni fogja a **Tulajdonjogok átadása** (portré ikon) és az **Előfizetés átadása** (lista ikon) lehetőséget. Ezek a lehetőségek csak aktív fiókok esetén jelennek meg.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Az előfizetésem neve megegyezik az ajánlat nevével. Módosítsam az előfizetés nevét a vállalatom számára jelentéssel bíró névre?

A létrehozott előfizetések alapértelmezés szerint a kiválasztott ajánlattípus nevét kapják. Javasoljuk, hogy módosítsa az előfizetés nevét olyan névre, amellyel könnyebben követheti nyomon az előfizetést.

A név módosítása:

1. Jelentkezzen be itt: [https://account.windowsazure.com](https://account.windowsazure.com).
1. Válassza ki az előfizetések listáját.
1. Válassza ki a módosítani kívánt előfizetést.
1. Válassza az **Előfizetés kezelése** ikont.
1. Szerkessze az előfizetés adatait.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Hogyan követhetem nyomon a felmerült költségeket költséghely alapján?

A költség költséghely alapján történő nyomon követéséhez a következő szintek egyikén meg kell adnia egy költséghelyet:

- Részleg
- Fiók
- Előfizetés

Ha szükséges, ugyanezzel a költséghellyel követheti nyomon az adott költséghelyhez kapcsolódó használatot és költségeket.

Ha például egy olyan különleges projekt költségeit szeretné nyomon követni, amelyben több részleg is részt vesz, a költséghelyet célszerű előfizetési szinten használni a használat és a költség nyomon követéséhez.

Költséghely nem definiálható szolgáltatási szinten. Ha szolgáltatási szinten szeretné nyomon követni a használatot, a szolgáltatási szinten elérhető _Címke_ funkciót használhatja.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hogyan követhetem nyomon a használatot és a kiadást a szervezet különböző részlegei alapján?

Az Azure EA-regisztrációban tetszőleges számú részleget hozhat létre. A használat megfelelő nyomon követéséhez gondoskodnia kell arról, hogy az előfizetések ne legyenek megosztva a részlegek között.

A részleg és az előfizetés létrehozásának befejezése után a használati jelentésben láthatja az információkat. Ezek az információk segítséget nyújtanak a használat nyomon követésében és a költségek kezelésében, valamint a részlegszinten való költésben.

A jelentéskészítő API-n keresztül is hozzáférhet a használati adatokhoz. Részletes információkért és mintakódért lásd az [Azure Enterprise REST API-k](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis) dokumentációját.

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Megadhatom a költségkvótát, és kérhetek riasztásokat, ha közelítek a korlát felé?

Költségkvótát a részleg szintjén állíthat be, és a rendszer automatikusan értesíti Önt, amikor a költségkeret eléri a megadott kvóta 50%-át, 75%-át, 90%-át és 100%-át.

A költségkvóta meghatározásához válasszon egy részleget, majd válassza a Szerkesztés ikont. A költségkeret részleteinek szerkesztése után válassza a **Mentés** parancsot.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Erőforráscsoportokat használtam az RBAC implementálásához és a használat nyomon követéséhez. Hogyan tekinthetem meg a kapcsolódó használati adatokat?

Ha használ _erőforráscsoportokat_ és _címkéket_, ezen információk nyomon követése a szolgáltatás szintjén történik, és az információk a részletes használati adatokat tartalmazó (.csv) fájlban érhetők el. Lásd a [Használati jelentés letöltése](https://ea.azure.com/report/downloadusage) szakaszt az Azure Enterprise portálon.

Az API-n keresztül is hozzáférhet a használati adatokhoz. Részletes információkért és mintakódért lásd az [Azure Enterprise REST API-k](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis) dokumentációját.

> [!NOTE]
> Csak olyan erőforrásokra alkalmazhat címkéket, amelyek támogatják az Azure Resource Manager-műveleteket. Ha egy virtuális gépet, virtuális hálózatot vagy tárolót a klasszikus üzemi modellel (például a klasszikus portált használva) hozott létre, akkor nem alkalmazhat címkét az adott erőforrásra. A címkézés támogatásához ismételten üzembe kell helyeznie ezeket az erőforrásokat a Resource Managerrel. Minden más erőforrás támogatja a címkézést.

### <a name="can-i-perform-analyses-using-power-bi"></a>Végezhetek elemzéseket Power BI használatával?

Igen. A Power BI-hoz készült Microsoft Azure Enterprise tartalomcsomaggal:

- Könnyen importálhatja és elemezteti a vállalati regisztrációjának Azure-használatát.
- Kiderítheti, melyik részleg, fiók vagy előfizetés idézte elő a legtöbb használatot.
- Megtudhatja, melyik szolgáltatást használta a legtöbbet a vállalata.
- Nyomon követheti a költekezési és használati trendeket.

A Power BI használata:

1. Lépjen a Power BI webhelyére.
1. Jelentkezzen be egy érvényes munkahelyi vagy iskolai fiókkal.

   A munkahelyi vagy iskolai fiók lehet ugyanaz vagy egy másik fiók, mint amelyet a regisztrációnak az Azure Enterprise portálon keresztül történő eléréséhez használ.
1. A szolgáltatások irányítópultján válassza a Microsoft Azure Enterprise elemet, majd válassza a **Csatlakozás** elemet.
1. Az **Azure Enterprise-hoz való kapcsolódás** képernyőjén adja meg a következőt:
    - Az Azure-környezet URL-címe: [https://ea.azure.com](https://ea.azure.com)
    - Hónapok száma: 1 és 36 között
    - Regisztrációs szám: az Ön regisztrációs száma
1. Kattintson a **Tovább** gombra.
1. Adja meg az API-kulcsot a **Fiókkulcs** mezőben.

   Az API-kulcsot az Azure Enterprise portálon találhatja meg. Kattintson a **Használati adatok letöltése** elemre, majd válassza az **API hozzáférési kulcsa** elemet. Másolja és illessze be a kulcsot a **Fiókkulcs** mezőbe a Power BI-ban.

Az adathalmaz méretétől függően az adatok a Power BI-ba történő betöltése 5–30 percet vesz igénybe.

A Power BI-jelentések készítése olyan közvetlen, partneri és közvetett Azure EA-szerződéssel rendelkező ügyfelek számára érhető el, akik meg tudják tekinteni a számlázási adatokat.

## <a name="next-steps"></a>További lépések

- Az Azure Enterprise portál rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Enterprise portál adminisztrációjáról](ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
- Ha segítségre van szüksége az Azure Enterprise portállal kapcsolatos hibák elhárításához, olvassa el [a nagyvállalati szerződéses Azure Enterprise portál elérésével kapcsolatos hibák elhárítását](ea-portal-troubleshoot.md) ismertető szakaszt.
- Az Azure EA előkészítési útmutatóját az [Azure EA előkészítési útmutatóját (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) tartalmazó fájlban tekintheti meg.
