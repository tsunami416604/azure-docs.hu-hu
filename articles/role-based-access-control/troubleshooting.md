---
title: Az RBAC hibaelhárítása az Azure-erőforrások |} A Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások kapcsolatos hibaelhárítás.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 7b27c811214def7f5646f886b955d035a50c0725
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342473"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Az RBAC hibaelhárítása az Azure-erőforrásokhoz

Ez a cikk szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz, kapcsolatos általános kérdéseket válaszol, hogy tudja, mire számítson, ha használja a szerepkörök az Azure portal és a hozzáférési problémák elhárítása.

## <a name="problems-with-rbac-role-assignments"></a>Problémák az RBAC-beli szerepkör-hozzárendelésekkel

- Ha nem tudja felvenni a szerepkör-hozzárendelés, mert a **szerepkör-hozzárendelés hozzáadása** lehetőség le van tiltva, vagy mert engedélyekkel kapcsolatos hibát kap, ellenőrizze, hogy használja egy szerepkör, amely rendelkezik a `Microsoft.Authorization/roleAssignments/*` engedéllyel a kívánt hatókörben rendelje hozzá a szerepkört. Ha nem rendelkezik ezzel az engedéllyel, forduljon az előfizetés-rendszergazdához.
- Ha engedélyekkel kapcsolatos hibát amikor megpróbál létrehozni egy erőforrást, ellenőrizze, hogy használja egy szerepkör, amely jogosult arra, hogy az erőforrások létrehozása a kijelölt hatókörben. Például előfordulhat, hogy szeretne közreműködő lenni. Ha nem rendelkezik engedéllyel, ellenőrizze az előfizetés-rendszergazdától.
- Ha engedélyekkel kapcsolatos hibát létrehozni vagy frissíteni egy támogatási jegyet észlel, ellenőrizze, hogy használja egy szerepkör, amely rendelkezik a `Microsoft.Support/*` engedéllyel, mint például [támogatási kérelem közreműködői](built-in-roles.md#support-request-contributor).
- Ha egy szerepkör hozzárendelésekor hibaüzenetet kap, amely szerint a szerepkör-hozzárendelések száma átlépte a határértéket, próbálja meg csökkenteni a szerepkör-hozzárendelések számát a szerepkörök csoportokhoz történő hozzárendelésével. Az Azure támogatja az akár **2000** szerepkör-hozzárendelések száma előfizetésenként.

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha Ön nem lehet frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy rendelkezik-e a `Microsoft.Authorization/roleDefinition/write` engedéllyel.
- Ha Ön nem lehet frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy legalább egy hozzárendelhető hatókörökkel törölve lett a bérlőben. A `AssignableScopes` tulajdonságot egy egyéni szerepkör vezérlők [akik létrehozása, törlése, módosítása vagy megtekintése az egyéni szerepkör](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Ha a szerepkör-definíciók korlátját meghaladó új szerepkör létrehozása, törlése nem egyéni szerepköröket megkísérlésekor hibaüzenetet kap használható. Is megpróbálhatja konszolidálhatja vagy felhasználhatják a meglévő egyéni szerepköröket. Az Azure támogatja az akár **2000** egyéni szerepkörök a bérlőben.
- Ha egy egyéni szerepkör törlése nem sikerült, ellenőrizze, hogy egy vagy több szerepkör-hozzárendeléseit továbbra is használja az egyéni szerepkör.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Az RBAC helyreállítása az előfizetések bérlők közötti áthelyezésekor

- Ha szeretné megismerni egy előfizetés másik bérlőre való áthelyezésének lépéseit, tekintse meg az [Azure-előfizetés tulajdonjogának másik fiókra történő átruházását](../billing/billing-subscription-transfer.md) bemutató cikket.
- Előfizetését átadhatja egy másik bérlőben, ha minden szerepkör-hozzárendelések véglegesen törlődnek a forrás-bérlőből, és a célbérlőhöz nem települnek át. Újra létre kell hoznia a célbérlőhöz a szerepkör-hozzárendelések.
- Ha egy globális felügyeleti és hozzáférési elvesztette előfizetésre, használja a **Access management az Azure-erőforrások** ideiglenes váltógomb [a hozzáférési szintjének emelése](elevate-access-global-admin.md) alapján visszaszerezhetik hozzáférésüket a az előfizetés.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC módosításait nem észleli a folyamatban

Az Azure Resource Manager néha gyorsítótárazza a konfigurációkat és a teljesítmény javítása az adatokat. Létrehozásakor, vagy törlése a szerepkör-hozzárendeléseit, a módosítások érvénybe léptetéséhez akár 30 percet is igénybe vehet. Az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatakor kényszerítheti a szerepkör-hozzárendelési módosítások frissítését kijelentkezés és bejelentkezés. A REST API-hívások szerepkör-hozzárendelés módosításokat végez, ha egy frissítés kényszerítheti a hozzáférési jogkivonat frissítésével.

## <a name="web-app-features-that-require-write-access"></a>Webes alkalmazás írási hozzáférést igénylő szolgáltatások

Egyetlen webalkalmazásban; a felhasználó csak olvasható hozzáférést ad, ha néhány funkció le van tiltva, hogy nem várt. A következő felügyeleti képességeket igényelnek **írási** nem érhetők el az összes csak olvasható forgatókönyv és a egy web app (tulajdonos vagy közreműködő) hozzáférést.

* Parancsok (például Indítás, Leállítás, stb.)
* Beállítások módosítása, például általános konfigurációs, a skálázási beállítás, a biztonsági mentési beállítások és a figyelési beállítások
* Közzétételi hitelesítő adatok és egyéb titkos adatait, például az alkalmazásbeállítások és a kapcsolati karakterláncok elérése
* Folyamatos átviteli naplók
* Diagnosztikai naplók konfigurálása
* Konzol (parancssor)
* Aktív és a legutóbbi központi telepítéseket (helyi git folyamatos üzembe helyezés)
* Becsült költség
* Webtesztek
* Virtuális hálózat (egy olvasó, ha egy virtuális hálózat már be lett állítva egy írási hozzáféréssel rendelkező felhasználónak csak látható).

Ha ezek a csempék nem sikerül elérnie, kérje meg a rendszergazdát a közreműködői hozzáférés szükséges a webes alkalmazás szeretné.

## <a name="web-app-resources-that-require-write-access"></a>Írási hozzáférést igénylő webes alkalmazás-erőforrások

Web apps néhány más erőforrások, amelyek kölcsönhatások jelen vannak bonyolult. Íme egy tipikus erőforráscsoportot, a websites néhány:

![Webes alkalmazás erőforráscsoportjának](./media/troubleshooting/website-resource-model.png)

Emiatt, ha biztosít valaki csak a web app, az funkciók a webhely paneljén, az Azure Portalon való hozzáférés le van tiltva.

Ezeket az elemeket szükség **írási** való hozzáférést a **App Service-csomag** , amely megfelel a webhelyét:  

* A webes alkalmazás a tarifacsomag (ingyenes vagy Standard)  
* Méretezés konfigurálása (példányok, a virtuális gép méretét, az automatikus méretezési beállítások száma)  
* Kvóták (storage, a sávszélesség, a Processzor)  

Ezeket az elemeket szükség **írási** a teljes hozzáférés **erőforráscsoport** , amely tartalmazza a webhely:  

* SSL-tanúsítványok és -kötések (SSL-tanúsítványok megoszthatók között helyek ugyanabban az erőforráscsoportban és földrajzi hely)  
* Riasztási szabályok  
* Az automatikus méretezési beállítások  
* Application Insights-összetevők  
* Webtesztek  

## <a name="virtual-machine-features-that-require-write-access"></a>Írási hozzáférést igénylő virtuális gépek jellemzői

Hasonlóan a web apps, néhány funkció a virtuális gép paneljén szükséges írási hozzáférést a virtuális gépet, vagy más az erőforráscsoportban lévő erőforrásokat.

Virtuális gépek kapcsolódó tartomány nevét, virtuális hálózatok, storage-fiókok és riasztási szabályok.

Ezeket az elemeket szükség **írási** való hozzáférést a **virtuális gép**:

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Ilyen esetekben **írási** elérését a **virtuális gép**, és a **erőforráscsoport** (valamint a tartomány nevét), hogy az informatikai van:  

* Rendelkezésre állási csoport  
* Elosztott terhelésű készlet  
* Riasztási szabályok  

Ha ezek a csempék nem sikerül elérnie, kérje meg a rendszergazdát a közreműködői hozzáférés az erőforráscsoporthoz.

## <a name="azure-functions-and-write-access"></a>Az Azure Functions és az írási hozzáférés

Az egyes funkciói [Azure Functions](../azure-functions/functions-overview.md) írási jogosultság szükséges. Például ha egy felhasználó az Olvasó szerepkör van hozzárendelve, nem tudja megtekinteni a függvényalkalmazás-függvényei. A portál megjeleníti **(nincs hozzáférés)**.

![Alkalmazások függvény nincs hozzáférés](./media/troubleshooting/functionapps-noaccess.png)

Kattintson egy olvasót a **platformfunkciók** fülre, majd **minden beállítás** bizonyos beállítások megtekintéséhez (a webalkalmazás hasonlóan) függvényalkalmazás kapcsolódó, de nem módosíthatják a beállítások.

## <a name="next-steps"></a>További lépések
* [Rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése](role-assignments-portal.md)
* [A Tevékenységnaplók megtekintése az Azure-erőforrások RBAC módosítások](change-history-report.md)

