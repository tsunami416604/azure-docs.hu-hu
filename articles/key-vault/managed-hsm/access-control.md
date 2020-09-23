---
title: Azure Managed HSM hozzáférés-vezérlés
description: Az Azure Managed HSM és kulcsok hozzáférési engedélyeinek kezelése. A felügyelt HSM hitelesítési és engedélyezési modelljét, valamint a HSM védelmét ismerteti.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a21d0db383e8c563f0b187061a95ac818dd2a4f0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995366"
---
# <a name="managed-hsm-access-control"></a>Felügyelt HSM hozzáférés-vezérlés

> [!NOTE]
> Key Vault **erőforrás-szolgáltató** két erőforrástípust támogat: tárolókat és **felügyelt HSM**. A jelen cikkben leírt hozzáférés-vezérlés csak a **felügyelt HSM**vonatkozik. A felügyelt HSM hozzáférés-vezérlésével kapcsolatos további tudnivalókért lásd: [hozzáférés biztosítása Key Vault kulcsokhoz, tanúsítványokhoz és titkokhoz egy Azure szerepköralapú hozzáférés-vezérléssel](../general/rbac-guide.md).

Azure Key Vault felügyelt HSM egy olyan felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsoknak. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférést kell biztosítani a felügyelt HSM ahhoz, hogy csak a jogosult alkalmazások és felhasználók férhessenek hozzá. Ez a cikk áttekintést nyújt a felügyelt HSM hozzáférés-vezérlési modelljéről. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a felügyelt HSM való hozzáférés biztonságossá tételét.

## <a name="access-control-model"></a>Hozzáférés-vezérlési modell

A felügyelt HSM-hez való hozzáférést két interfész szabályozza: a **felügyeleti síkon** és az **adatsíkon**. A felügyeleti sík a HSM kezelését végzi. Az ebben a síkban található műveletek közé tartozik a felügyelt HSM létrehozása és törlése, valamint a felügyelt HSM-tulajdonságok beolvasása. Az adatsíkon a felügyelt HSM-ben tárolt, a HSM-alapú titkosítási kulcsokat tartalmazó adatközpontok működnek. Hozzáadhat, törölhet, módosíthat és használhat kulcsokat titkosítási műveletek végrehajtásához, szerepkör-hozzárendelések kezeléséhez a kulcsok elérésének vezérléséhez, egy teljes HSM biztonsági mentés létrehozásához, a teljes biztonsági mentés visszaállításához és a biztonsági tartomány felügyeletéhez az adatközpont felületéről.

A felügyelt HSM mindkét síkon való eléréséhez minden hívónak megfelelő hitelesítéssel és engedélyezéssel kell rendelkeznie. A hitelesítés létrehozza a hívó identitását. Az engedélyezés meghatározza, hogy a hívó milyen műveleteket hajthat végre. A hívó a Azure Active Directory felhasználó, csoport, szolgáltatásnév vagy felügyelt identitásban definiált [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) egyike lehet.

Mindkét síkon Azure Active Directoryt használ a hitelesítéshez. Az engedélyezéshez a következőképpen használják a különböző rendszereket:
- A felügyeleti sík Azure szerepköralapú hozzáférés-vezérlést használ – Azure RBAC – az Azure-ra épülő engedélyezési rendszer Azure Resource Manager 
- Az adatsík felügyelt HSM szintű RBAC (felügyelt HSM helyi RBAC) használ – a felügyelt HSM szintjén megvalósított és érvényesített engedélyezési rendszer.

Felügyelt HSM létrehozásakor a kérelmező az adatközpont-rendszergazdák listáját is megjeleníti (az összes [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) támogatott). Csak ezek a rendszergazdák férhetnek hozzá a felügyelt HSM-adatsíkon a legfontosabb műveletek végrehajtásához és az adatsík szerepkör-hozzárendelések kezeléséhez (felügyelt HSM helyi RBAC).

Mindkét sík engedélyezési modellje ugyanazt a szintaxist (RBAC) használja, de a különböző szinteken és szerepkör-hozzárendeléseken eltérő hatóköröket alkalmaznak. A felügyeleti sík RBAC Azure Resource Manager kényszeríti, amíg a felügyelt HSM maga a RBAC is kényszeríti.

> [!IMPORTANT]
> Egy rendszerbiztonsági tag felügyelt HSM-hez való hozzáférésének megadása nem biztosít számukra hozzáférést az adatsíkon a kulcsok vagy adatsík szerepkör-hozzárendelések felügyelt HSM helyi RBAC való eléréséhez. Az elkülönítést úgy tervezték, hogy megakadályozza a felügyelt HSM-ben tárolt kulcsok elérését érintő jogosultságok véletlen kiterjesztését.

Például egy előfizetés-rendszergazda (mivel az előfizetéshez tartozó összes erőforráshoz "közreműködői" engedéllyel rendelkezik) törölheti a felügyelt HSM-et az előfizetésében, de ha nem rendelkeznek kifejezetten a felügyelt HSM helyi RBAC, akkor nem férhetnek hozzá a kulcsokhoz, vagy kezelhetik a szerepkör-hozzárendelést a felügyelt HSM-ben, hogy maguk vagy mások hozzáférhessenek az adatsíkon.

## <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

Ha egy Azure-előfizetésben hoz létre felügyelt HSM-t, az automatikusan az előfizetés Azure Active Directory bérlőhöz lesz társítva. A két síkon lévő összes hívót regisztrálni kell ebben a bérlőben, és hitelesítenie kell magát a felügyelt HSM-hez való hozzáféréshez.

Az alkalmazás a Azure Active Directory a sík hívása előtt hitelesíti a következővel:. Az alkalmazás az alkalmazás típusától függően bármilyen [támogatott hitelesítési módszert](../../active-directory/develop/authentication-scenarios.md) alkalmazhat. Az alkalmazás egy jogkivonatot szerez be a síkon lévő erőforráshoz, hogy hozzáférjen. Az erőforrás az Azure-környezet alapján a felügyelet vagy az adatsík végpontja. Az alkalmazás a jogkivonatot használja, és REST API kérelmet küld a felügyelt HSM-végpontnak. További információért tekintse át a [teljes hitelesítési folyamatot](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Egyetlen hitelesítési mechanizmus használata mindkét síkon számos előnnyel jár:

- A szervezetek központilag vezérelhetik a hozzáférést a szervezeten belüli összes felügyelt HSM.
- Ha a felhasználó elhagyja, azonnal elvesztik a hozzáférést a szervezet összes felügyelt HSM.
- A szervezetek a Azure Active Directory lehetőségeit használva szabhatják testre a hitelesítést, például a többtényezős hitelesítés engedélyezésével a további biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

A rendszerbiztonsági tag végpontokon keresztül fér hozzá a síkokhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy egy alkalmazás hozzáférést biztosítson a felügyelt HSM-ben lévő kulcsok használatához, a felügyelt HSM helyi RBAC használatával biztosíthatja az adatsíkok elérését. Ha hozzáférést szeretne biztosítani a felügyelt HSM-erőforráshoz, hozzon létre, olvassa el, törölje, helyezze át a felügyelt HSM, és szerkessze az Azure RBAC-t használó egyéb tulajdonságokat és címkéket.

A következő táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális**<br> management.azure.com:443<br> | Felügyelt HSM létrehozása, olvasása, frissítése, törlése és áthelyezése<br>Felügyelt HSM-címkék beállítása | Azure RBAC-vel |
| Adatsík | **Globális**<br> &lt;HSM – név &gt; . Vault.Azure.net:443<br> | **Kulcsok**: visszafejtés, titkosítás,<br> kicsomagolás, becsomagolás, ellenőrzés, aláírás, beolvasás, Listázás, frissítés, létrehozás, importálás, törlés, biztonsági mentés, visszaállítás, kiürítés<br/><br/> **Adatsík szerepkör-felügyelet (felügyelt HSM helyi RBAC) * * *: szerepkör-definíciók listázása, szerepkörök hozzárendelése, szerepkör-hozzárendelések törlése, <br/> <br/> Egyéni szerepkörök definiálása** Backup/Restore **: Backup, Restore, status Backup/Restore Operations <br/> <br/> **Security domain * *: a biztonsági tartomány letöltése és feltöltése | Felügyelt HSM helyi RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>Felügyeleti sík és Azure RBAC

A felügyeleti síkon az Azure RBAC segítségével engedélyezheti a hívó által végrehajtható műveleteket. A RBAC modellben minden Azure-előfizetés Azure Active Directory egy példányával rendelkezik. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak ebben a címtárban. A hozzáférés a Azure Resource Manager üzemi modellt használó Azure-előfizetés erőforrásainak kezeléséhez van megadva. A hozzáférés megadásához használja a [Azure Portal](https://portal.azure.com/), az [Azure CLI](../../cli-install-nodejs.md), a [Azure PowerShell](/powershell/azureps-cmdlets-docs)vagy a [Azure Resource Manager REST API-kat](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Hozzon létre egy kulcstartót egy erőforráscsoporthoz, és kezelje a hozzáférést Azure Active Directory használatával. A felhasználók vagy csoportok számára engedélyezheti az erőforráscsoport kulcstárolóinak kezelését. A hozzáférést adott hatóköri szinten kell megadni a megfelelő RBAC-szerepkörök hozzárendelésével. Ahhoz, hogy hozzáférést biztosítson egy felhasználónak a kulcstartók kezeléséhez, egy előre meghatározott `key vault Contributor` szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. A következő hatókörökhöz rendelhet hozzá RBAC-szerepköröket:

- **Felügyeleti csoport**: az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott felügyeleti csoportban lévő összes előfizetésre vonatkozik.
- **Előfizetés**: az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott előfizetésen belüli összes erőforráscsoport és erőforrás esetében érvényes.
- **Erőforráscsoport**: az erőforráscsoport szintjén HOZZÁRENDELt RBAC-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott**erőforrás: egy adott erőforráshoz hozzárendelt RBAC-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkör létezik. Ha egy előre meghatározott szerepkör nem felel meg az igényeinek, megadhatja saját szerepkörét. További információt a [RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)című témakörben talál.

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Adatsík és felügyelt HSM helyi RBAC

Egy szerepkör kiosztásával egy rendszerbiztonsági tag számára biztosít hozzáférést egy adott kulcsfontosságú művelet végrehajtásához. Minden szerepkör-hozzárendeléshez meg kell adnia egy szerepkört és hatókört, amelyre a hozzárendelés vonatkozik. A felügyelt HSM helyi RBAC két hatókör érhető el.

- **"/" vagy "/Keys"**: HSM szintű hatókör. Az ezen a hatókörben szerepkört hozzárendelő rendszerbiztonsági tag a felügyelt HSM-ben a szerepkörben meghatározott műveleteket hajthatja végre.
- **"/Keys/ &lt; Key-name &gt; "**: kulcs szintű hatókör. Az ebben a hatókörben szerepkört hozzárendelő rendszerbiztonsági tag csak a megadott kulcs összes verziójára vonatkozóan tudja végrehajtani a szerepkörben definiált műveleteket.

## <a name="next-steps"></a>Következő lépések

- Az első lépéseket ismertető oktatóanyagért lásd: [Mi a felügyelt HSM?](overview.md).
- A szerepkör-kezelési oktatóanyagért lásd: [felügyelt HSM helyi RBAC](role-management.md)
- A felügyelt HSM-naplózás használati naplózásával kapcsolatos további információkért lásd: [felügyelt HSM naplózása](logging.md).
