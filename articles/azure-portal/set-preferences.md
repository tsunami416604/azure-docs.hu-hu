---
title: Az Azure Portal beállításainak beállítása | Microsoft dokumentumok
description: Módosíthatja az Azure Portal alapértelmezett beállításait, hogy megfeleljen a saját beállításait. A beállítások közé tartozik az inaktív munkamenet időtúlhasználata, az alapértelmezett nézet, a menümód, a kontraszt, a téma, az értesítések, valamint a nyelvi és regionális formátumok
services: azure-portal
keywords: beállítások, időmeghosszabbítás, nyelv, regionális
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310679"
---
# <a name="set-your-azure-portal-preferences"></a>Az Azure Portal-beállítások megadása

Módosíthatja az Azure Portal alapértelmezett beállításait, hogy megfeleljen a saját beállításait. Az alább felsorolt beállítások mindegyike módosítható:

* [Inaktív munkamenet időtúlidőpontja](#change-the-idle-duration-for-inactive-sign-out)
* [Alapértelmezett nézet](#choose-your-default-view)
* [Portál menümód](#choose-a-portal-menu-mode)
* [Szín és kontrasztos téma](#choose-a-theme)
* [Előugró értesítések](#enable-or-disable-pop-up-notifications)
* [Nyelv és területi formátum](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Általános portálbeállítások módosítása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a **beállítások lehetőséget** a globális oldalfejlécből.

    ![Képernyőkép a globális oldalfejléc-ikonokról kiemelt beállításokkal](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Az inaktív kijelentkezés tétlen időtartamának módosítása

Az inaktivitás időelintézési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha elfelejti biztosítani a munkaállomást. Miután egy ideig tétlen volt, automatikusan kijelentkezik az Azure Portal-munkamenetből.

Válassza ki a legördülő menü **Bejelentkezés inaktív állapotban csoportban.** Válassza ki azt az időtartamot, amely után az Azure Portal-munkamenet ki van jelentkezve, ha tétlen.

   ![Képernyőkép a portál beállításairól kiemelt inaktív időtúltöltési beállításokkal](./media/set-preferences/inactive-signout-user.png)

A módosítás automatikusan mentésre kerül. Ha tétlen, az Azure Portal munkamenet kijelentkezik a beállított időtartam után.

Ezt a beállítást a könyvtár szintjén lévő rendszergazda is elláthatja a maximális tétlen idő kikényszerítése érdekében. Ha egy rendszergazda címtárszintű időtúltöltési beállítást végzett, továbbra is beállíthatja a saját inaktív kijelentkezési időtartamát. Válasszon olyan időbeállítást, amely kisebb, mint a könyvtár szintjén beállított beállítás.

Ha a rendszergazda engedélyezte az inaktivitás időtúllépési házirendjét, jelölje be **a Címtár inaktivitási időtúllépési házirendjének felülbírálása** jelölőnégyzetet. Állítson be egy időintervallumot, amely kisebb, mint a házirend-beállítás.

   ![Képernyőkép a portál beállításairól a nyitott könyvtárinaktivitási időtúllépési házirend beállítás felülírásával](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Ha Ön rendszergazda, és inaktív időtúltöltési beállítást szeretne kikényszeríteni az Azure Portal összes felhasználója számára, olvassa [el a Címtárszintű inaktivitás időtúltöltésének beállítása az Azure Portal felhasználói számára című témakört.](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Az alapértelmezett nézet kiválasztása 

Módosíthatja a lapot, amely alapértelmezés szerint megnyílik, amikor az Azure Portalra való bejelentkezés.

   ![Képernyőkép az Azure Portal beállításairól kiemelt alapértelmezett nézetben](./media/set-preferences/default-view.png)

Az alapértelmezett nézetbeállítás határozza meg, hogy melyik Azure Portal-nézet jelenjen meg bejelentkezéskor. Beállíthatja, hogy alapértelmezés szerint nyissa meg az Azure Home vagy az Irányítópult nézetet.

* **Az otthon** nem szabható testre.  Megjeleníti a népszerű Azure-szolgáltatások parancsikonjait, és felsorolja a legutóbb használt erőforrásokat. Hasznos hivatkozásokat is biztosítunk olyan erőforrásokhoz, mint a Microsoft Learn és az Azure ütemterve.
* Az irányítópultok testre szabhatók, hogy ön számára tervezett munkaterületet hozzanak létre. Létrehozhat például egy projekt-, tevékenység- vagy szerepkörközpontú irányítópultot. Ha az Irányítópult lehetőséget **választja,** az alapértelmezett nézet a legutóbb használt irányítópultra kerül.

### <a name="choose-a-portal-menu-mode"></a>Portálmenü mód kiválasztása

A portálmenü alapértelmezett módja határozza meg, hogy a portálmenü mennyi helyet foglal el az oldalon.

* Ha a portál menü **úszó panel** módban van, akkor a rendszer el van rejtve, amíg szükség nem lesz rá. A menü megnyitásához vagy bezárásához válassza a menü ikonját.
* Ha a portál **menüben a dokkolt** módot választja, az mindig látható lesz. A menü összecsukható, hogy több munkaterületet biztosítson. 

### <a name="choose-a-theme"></a>Válasszon témát

A kiválasztott téma hatással van az Azure Portalon megjelenő háttér- és betűszínekre. A négy előre beállított színtéma közül választhat. Jelölje ki az egyes miniatűröket, hogy megtalálja az Önnek leginkább megfelelő témát.

   ![Képernyőkép az Azure Portal beállításairól kiemelt témákkal](./media/set-preferences/theme.png)

Ehelyett választhat a kontrasztos témák közül. A kontrasztos beállítások megkönnyítik az Azure Portal olvasását a csökkent látóképességű felhasználók számára, és felülírják az összes többi témaválasztást. További információt a [Kontrasztos megjelenítés bekapcsolása vagy téma módosítása című témakörben talál.](azure-portal-change-theme-high-contrast.md)

### <a name="enable-or-disable-pop-up-notifications"></a>Előugró értesítések engedélyezése vagy letiltása

Az értesítések az aktuális munkamenethez kapcsolódó rendszerüzenetek. Olyan információkat szolgáltatnak, mint például az aktuális hitelegyenlege, amikor az imént létrehozott erőforrások elérhetővé válnak, vagy megerősítik például az utolsó műveletet. Ha az előugró értesítések be vannak kapcsolva, az üzenetek rövid en jelennek meg a képernyő felső sarkában. 

Az előugró értesítések engedélyezéséhez vagy letiltásához jelölje be az **Előugró értesítések engedélyezése** jelölőnégyzetet, vagy törölje belőle a jelet.

   ![Képernyőkép az Azure Portal beállításairól kiemelt előugró értesítésekkel](./media/set-preferences/popup-notifications.png)

Az aktuális munkamenet során kapott összes értesítés olvasásához válassza az **Értesítések** elemet a globális fejlécből.

   ![Az Azure Portal globális fejlécét ábrázoló képernyőkép kiemelt értesítésekkel](./media/set-preferences/read-notifications.png)

Ha el szeretné olvasni az előző munkamenetek értesítéseit, keresse meg az eseményeket a tevékenységnaplóban. További információért olvassa el [az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása.](/azure/azure-monitor/platform/activity-log-view)

### <a name="settings-under-useful-links"></a>Beállítások a hasznos hivatkozások alatt

Ha módosította az Azure Portal beállításait, és el szeretné vetni őket, válassza **az Alapértelmezett beállítások visszaállítása**lehetőséget. A portál beállításaiban végzett módosítások elvesznek. Ez a beállítás nincs hatással az irányítópult okán.

Az összes **beállítás exportálásáról** vagy **az összes beállítás és privát irányítópult törlése című**témakörben további információt a Felhasználói beállítások [exportálása vagy törlése](azure-portal-export-delete-settings.md)című témakörben talál.

## <a name="change-language-and-regional-settings"></a>Nyelvi és területi beállítások módosítása

Két beállítás szabályozza, hogy a szöveg az Azure Portalon jelenik meg. A **Nyelvi** beállítás határozza meg az Azure Portalon a szöveghez látható nyelvet. **A területi formátum** határozza meg a dátumok, az idő, a számok és a pénznem ekként való megjelenítését.

Az Azure Portalon használt nyelv módosításához a legördülő menüsegítségével válasszon az elérhető nyelvek listájából.

A területi formátum kijelölése úgy változik, hogy csak a kiválasztott nyelv területi beállításai jelenjenek meg. Az automatikus kijelölés módosításához a legördülő menüsegítségével válassza ki a kívánt területi formátumot.

Ha például az angol nyelvet választja, majd az Egyesült Államokat választja regionális formátumként, a pénznem amerikai dollárban jelenik meg. Ha az angol nyelvet választja, majd Európát választja regionális formátumként, a pénznem euróban jelenik meg.

Válassza az **Alkalmaz** lehetőséget a nyelvi és területi formátumbeállítások frissítéséhez.

   ![Képernyőkép a nyelvi és területi formázási beállításokról](./media/set-preferences/language.png)

>[!NOTE]
>Ezek a nyelvi és területi beállítások csak az Azure Portalra vannak hatással. Az új lapon vagy ablakban megnyíló dokumentációs hivatkozások a böngésző nyelvi beállításait használják a megjelenítendő nyelv meghatározásához.
>

## <a name="next-steps"></a>További lépések

* [Egyéni irányítópultok létrehozása és megosztása](azure-portal-dashboards.md)
* [Az Azure Portal útmutató videósorozata](azure-portal-video-series.md)
