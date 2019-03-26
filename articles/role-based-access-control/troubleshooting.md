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
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407589"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Az RBAC hibaelhárítása az Azure-erőforrásokhoz

Ez a cikk szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz, kapcsolatos általános kérdéseket válaszol, hogy tudja, mire számítson, ha használja a szerepkörök az Azure portal és a hozzáférési problémák elhárítása.

## <a name="problems-with-rbac-role-assignments"></a>Problémák az RBAC-beli szerepkör-hozzárendelésekkel

- Ha nem tudja az Azure Portalon a szerepkör-hozzárendelés hozzáadása a **hozzáférés-vezérlés (IAM)** mert a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása** lehetőség le van tiltva, vagy Ellenőrizze, hogy jelenleg jelentkezett be, amely hozzá van rendelve egy szerepkör, amely rendelkezik egy felhasználó az engedélyekkel kapcsolatos hibát kap, "az ügyfél nem rendelkezik a művelet végrehajtására", mert a `Microsoft.Authorization/roleAssignments/write` engedély például [tulajdonosa](built-in-roles.md#owner) vagy [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) a szerepkör hozzárendelése a kívánt hatókörben.
- Ha hibaüzenet jelenik meg a "További szerepkör-hozzárendelések nem hozható létre (kód: RoleAssignmentLimitExceeded) "szerepkör hozzárendelése meg, próbálja meg úgy csökkenthető a szerepkör-hozzárendelések száma szerepkörök hozzárendelése a csoportokhoz helyette. Az Azure támogatja az akár **2000** szerepkör-hozzárendelések száma előfizetésenként.

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha ismertetik, hogyan hozhat létre egy egyéni szerepkört van szüksége, tekintse meg az egyéni szerepkör oktatóanyagok segítségével [Azure PowerShell-lel](tutorial-custom-role-powershell.md) vagy [Azure CLI-vel](tutorial-custom-role-cli.md).
- Ha Ön nem lehet frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy jelenleg jelentkezett be, amely hozzá van rendelve egy szerepkör, amely rendelkezik egy felhasználó a `Microsoft.Authorization/roleDefinition/write` engedély például [tulajdonosa](built-in-roles.md#owner) vagy [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator).
- Ha nem sikerül, egy egyéni szerepkör törléséhez, és a hibaüzenet a "meglévő szerepkör-hozzárendelések hivatkoznak szerepkör vannak (kód: RoleDefinitionHasAssignments) ", akkor vannak a szerepkör-hozzárendeléseit továbbra is használja az egyéni szerepkör. Távolítsa el azokat a szerepkör-hozzárendeléseket, és próbálja meg újra törölni az egyéni szerepkör.
- Ha a hibaüzenet jelenik meg "szerepkör-definíciók korlátját túllépve. Nincsenek további szerepkör-definíciók hozhatók létre (hibakód: RoleDefinitionLimitExceeded) "Amikor megpróbál létrehozni egy új egyéni biztonsági szerepkört, törölje a nem használt egyéni szerepköröket. Az Azure támogatja az akár **2000** egyéni szerepkörök a bérlőben.
- Ha hibaüzenetet kap hasonló "az ügyfél jogosult végrehajtani a műveletet"Microsoft.Authorization/roleDefinitions/write"hatókör"/ subscriptions / {subscriptionid}", azonban a társított előfizetés nem található" Amikor egy egyéni szerepkör frissítése meg, ellenőrizze hogy egy vagy több [hozzárendelhető hatókörökkel](role-definitions.md#assignablescopes) törölve lett, a bérlőben. Ha a hatókör törölve lett, hozzon létre egy támogatási jegyet nincs jelenleg elérhető önkiszolgáló megoldás nem.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Az RBAC helyreállítása az előfizetések bérlők közötti áthelyezésekor

- Ha egy előfizetés átadására irányuló szükséges lépéseket kell egy másik Azure ad-bérlőhöz, lásd: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md).
- Ha egy előfizetés átadására egy másik Azure ad-bérlőben, az összes szerepkör-hozzárendelések véglegesen törlődnek a forrás az Azure AD-bérlőből, és nem lesznek áttelepítve a cél az Azure AD-bérlővel. Újra létre kell hoznia a célbérlőhöz a szerepkör-hozzárendelések.
- Ha Ön egy Azure AD globális rendszergazda, és nem fér hozzá egy előfizetést után a bérlők között át lett helyezve, használja a **Access management az Azure-erőforrások** ideiglenes váltógomb [ahozzáférésiszintjénekemelése](elevate-access-global-admin.md) az előfizetés eléréséhez.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémák a szolgáltatásadminisztrátorokkal vagy társadminisztrátorokkal

- Ha problémák merülnek fel a szolgáltatás-rendszergazdaként vagy társ-rendszergazda, lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) és [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD rendszergazdai szerepkörök](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>A hozzáférés megtagadva vagy jogosultsági hibák

- Ha a hiba az engedélyeknél "az ügyfél nem jogosult a művelet végrehajtásához a hatókörben (kód: AuthorizationFailed) "Amikor megpróbál létrehozni egy erőforrást, ellenőrizze, hogy jelenleg bejelentkezve a felhasználó, amely hozzá van rendelve egy szerepkör, amely az erőforrás írási engedéllyel rendelkezik a kijelölt hatókörben. Például egy erőforráscsoportba tartozó virtuális gépek kezeléséhez, meg kell rendelkeznie a [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör az erőforráscsoport (vagy szülő hatókörben). Minden beépített szerepkör esetében az engedélyek listáját lásd: [beépített szerepkörök az Azure-erőforrások](built-in-roles.md).
- Ha a hiba az engedélyeknél "Nincs engedélye támogatási kérelmet szeretne létrehozni" Amikor megpróbálja létrehozni vagy frissíteni egy támogatási jegyet, ellenőrizze, hogy jelenleg jelentkezett be, amely hozzá van rendelve egy szerepkör, amely rendelkezik egy felhasználó a `Microsoft.Support/supportTickets/write` engedéllyel, például [Támogatáskérési közreműködő támogatja](built-in-roles.md#support-request-contributor).

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

