---
title: A Azure Portal beállításainak megadása | Microsoft Docs
description: Módosíthatja Azure Portal alapértelmezett beállításait, hogy megfeleljen a saját preferenciáinak. A beállítások közé tartozik az inaktív munkamenet időkorlátja, az alapértelmezett nézet, a menü mód, a kontraszt, a téma, az értesítések és a nyelv és a területi
services: azure-portal
keywords: beállítások, időtúllépés, nyelv, regionális
author: mgblythe
ms.author: mblythe
ms.date: 07/30/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: a8ce638bf61c69fb732d94d537218aabaeaa9a7c
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461932"
---
# <a name="set-your-azure-portal-preferences"></a>Az Azure Portal-beállítások megadása

Módosíthatja a Azure Portal alapértelmezett beállításait, hogy azok megfeleljenek a saját preferenciáinak. Az alábbiakban felsorolt beállítások mindegyike módosítható:

* [Inaktív munkamenet időkorlátja](#change-the-idle-duration-for-inactive-sign-out)
* [Alapértelmezett előfizetés](#choose-your-default-subscription)
* [Alapértelmezett nézet](#choose-your-default-view)
* [Portál menü mód](#choose-a-portal-menu-mode)
* [Szín és kontrasztos téma](#choose-a-theme)
* [Előugró értesítések](#enable-or-disable-pop-up-notifications)
* [Nyelv és területi formátum](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Általános portál beállításainak módosítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A globális oldal fejlécében válassza a **Beállítások** lehetőséget.

    ![Képernyőfelvétel a globális oldal fejlécének ikonjairól a Kiemelt beállításokkal](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Inaktív kijelentkezés üresjárati időtartamának módosítása

Az inaktivitás időtúllépési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha elfelejti a munkaállomás védelmét. Miután elvégezte a tétlenséget, automatikusan kijelentkezik a Azure Portal-munkamenetből.

Válassza ki a legördülő menüből a kijelentkezés az **inaktív állapotból**lehetőséget. Válassza ki azt az időtartamot, amely után a Azure Portal-munkamenet ki van jelentkezve, ha tétlen.

   ![Képernyőkép a portál beállításairól az inaktív időtúllépési beállítások kiemelésével](./media/set-preferences/inactive-signout-user.png)

A rendszer automatikusan menti a változást. Ha tétlen, akkor a Azure Portal-munkamenet a beállított időtartam után kijelentkezik.

Ezt a beállítást egy rendszergazda is elvégezheti a címtár szintjén a maximális üresjárati idő érvényesítéséhez. Ha egy rendszergazda címtár-szintű időtúllépési beállítást adott meg, akkor továbbra is beállíthatja a saját inaktív kijelentkezési időtartamát. Olyan időbeállítást válasszon, amely kisebb, mint a könyvtár szintjén beállított érték.

Ha a rendszergazda engedélyezte a tétlenségi időtúllépési házirendet, jelölje be a **könyvtár inaktivitás időtúllépési házirendjének felülbírálása** jelölőnégyzetet. Állítsa be a házirend-beállításnál kisebb időintervallumot.

   ![A portál beállításait megjelenítő képernyőkép, amely felülbírálja a címtár inaktivitási időtúllépési házirend-beállítását.](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Ha Ön rendszergazda, és szeretné kényszeríteni a Azure Portal összes felhasználójának inaktív időtúllépési beállítását, tekintse meg a következő témakört: [a könyvtár szintű inaktivitás időkorlátjának beállítása a Azure Portal felhasználói](admin-timeout.md) számára.
>

### <a name="choose-your-default-subscription"></a>Válassza ki az alapértelmezett előfizetést

A Azure Portalba való bejelentkezéskor alapértelmezés szerint megnyithatja az előfizetést. Ez akkor hasznos, ha rendelkezik egy elsődleges előfizetéssel, de alkalmanként másokat használ. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Erőforrások listájának szűrése előfizetés alapján.":::

1. Válassza ki a címtár és előfizetés szűrő ikont a felső navigációs sávon.
1. A portál indításakor válassza ki az alapértelmezett előfizetésként használni kívánt előfizetéseket. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="A portál indításakor válassza ki az alapértelmezett előfizetésként használni kívánt előfizetéseket."::: 


### <a name="choose-your-default-view"></a>Válassza ki az alapértelmezett nézetet 

A Azure Portalba való bejelentkezéskor alapértelmezés szerint a megnyíló lap is módosítható.

   ![Képernyőfelvétel – az alapértelmezett nézettel Azure Portal beállítások megjelenítése](./media/set-preferences/default-view.png)

Az alapértelmezett nézet beállítás azt határozza meg, hogy mely Azure Portal nézet jelenik meg a bejelentkezéskor. Dönthet úgy, hogy alapértelmezés szerint megnyitja az Azure kezdőlapot vagy az irányítópult nézetet.

* A **Kezdőlap** nem szabható testre.  Megjeleníti a népszerű Azure-szolgáltatásokra mutató parancsikonokat, és felsorolja a legutóbb használt erőforrásokat. Hasznos hivatkozásokat is biztosítunk az olyan erőforrásokhoz, mint a Microsoft Learn és az Azure-útiterv.
* Az irányítópultok testreszabhatók úgy, hogy csak Ön számára kialakított munkaterületet hozzanak létre. Létrehozhat például egy projekt, feladat vagy szerepkör fókuszban lévő irányítópultot. Ha kijelöli az **irányítópultot**, az alapértelmezett nézet a legutóbb használt irányítópultra lép.

### <a name="choose-a-portal-menu-mode"></a>Portál menü mód kiválasztása

A portál menü alapértelmezett módja határozza meg, hogy a portál menüjében mennyi lemezterületet vesz fel az oldal.

* Ha a portál menüje a **kilépési módban van** , akkor a rendszer rejtve marad, amíg nincs rá szüksége. Válassza a menü ikont a menü megnyitásához vagy bezárásához.
* Ha a portál menü **dokkolt** mód elemét választja, mindig látható. A menü összecsukásával több munkaterületet is megadhat. 

### <a name="choose-a-theme"></a>Válasszon témát

A kiválasztott téma befolyásolja a Azure Portalban megjelenő háttér-és betűszínt. A négy beállított színtéma közül választhat. Válassza ki az egyes miniatűrket, és keresse meg az Önnek legmegfelelőbb témát.

   ![A Kiemelt témákkal rendelkező Azure Portal beállításokat bemutató képernyőkép](./media/set-preferences/theme.png)

Ehelyett kiválaszthatja a nagy kontrasztú témák egyikét. A kontrasztos beállítások megadásával a Azure Portal könnyebben olvashatók azok számára, akik a vizualizációk és az összes többi téma kijelölését felülbírálják. További információt a [Kontrasztos megjelenítés bekapcsolása vagy a téma módosítása](azure-portal-change-theme-high-contrast.md)című témakörben talál.

### <a name="enable-or-disable-pop-up-notifications"></a>Előugró értesítések engedélyezése vagy letiltása

Az értesítések az aktuális munkamenethez kapcsolódó rendszerüzenetek. Olyan információkat biztosítanak, mint a jelenlegi kredit egyenleg, ha az imént létrehozott erőforrások elérhetővé válnak, vagy megerősítik az utolsó műveletet, például:. Amikor bekapcsolják az előugró értesítéseket, a képernyő felső sarkában lévő üzenetek röviden megjelennek. 

Az előugró értesítések engedélyezéséhez vagy letiltásához jelölje be az **előugró értesítések engedélyezése** jelölőnégyzetet, vagy törölje a jelölését.

   ![Az előugró értesítések kiemelve Azure Portal beállításait bemutató képernyőkép](./media/set-preferences/popup-notifications.png)

Az aktuális munkamenet során fogadott értesítések olvasásához válassza az **értesítések** elemet a globális fejlécből.

   ![A Azure Portal globális fejlécét bemutató képernyőkép](./media/set-preferences/read-notifications.png)

Ha szeretné beolvasni az előző munkamenetek értesítéseit, keresse meg az eseményeket a tevékenység naplójában. További információért olvassa el az [Azure-Tevékenységnaplók eseményeinek megtekintése és](/azure/azure-monitor/platform/activity-log-view)beolvasása című témakört.

### <a name="settings-under-useful-links"></a>A hasznos hivatkozások alatt található beállítások

Ha módosította a Azure Portal beállításokat, és el szeretné vetni őket, válassza az **alapértelmezett beállítások visszaállítása**lehetőséget. A portál beállításaiban végrehajtott módosítások elvesznek. Ez a beállítás nem befolyásolja az irányítópultok testreszabását.

További információ az **összes beállítás exportálásáról** , illetve az **összes beállítás és a privát irányítópultok törléséről**: [felhasználói beállítások exportálása vagy törlése](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Nyelvi és területi beállítások módosítása

Két beállítás szabályozza a Azure Portal szövegének megjelenését. A **nyelvi** beállítás határozza meg, hogy milyen nyelven jelenik meg a szöveg a Azure Portalban. A **területi formátum** szabályozza a dátumok, az idő, a számok és a pénznem megjelenítésének módját.

A Azure Portal használt nyelv módosításához a legördülő listából válassza ki az elérhető nyelvek listáját.

A területi formátum kiválasztása úgy változik, hogy csak a kiválasztott nyelvhez tartozó területi beállítások jelenjenek meg. Az Automatikus kijelölés módosításához a legördülő menüben válassza ki a kívánt területi formátumot.

Ha például az angol nyelvet választja, majd a területi formátumot Egyesült Államok választja, akkor a pénznem amerikai dollárban jelenik meg. Ha az angol nyelvet választja, és az Európai területi formátumot választja, akkor a pénznem euróban jelenik meg.

Az **alkalmaz** gombra kattintva frissítheti a nyelvi és a területi formátum beállításait.

   ![A nyelvi és a területi formátum beállításait bemutató képernyőkép](./media/set-preferences/language.png)

>[!NOTE]
>Ezek a nyelvi és területi beállítások csak a Azure Portal érintik. Az új lapon vagy ablakban megnyitott dokumentációs hivatkozások a böngésző nyelvi beállításait fogják használni a megjelenítendő nyelv meghatározásához.
>

## <a name="next-steps"></a>További lépések

* [Egyéni irányítópultok létrehozása és megosztása](azure-portal-dashboards.md)
* [Az Azure Portal útmutató videósorozata](azure-portal-video-series.md)
