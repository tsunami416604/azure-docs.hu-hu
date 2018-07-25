---
title: Az Azure-beli RBAC hibaelhárítása |} A Microsoft Docs
description: Azure szerepköralapú hozzáférés-vezérlés (RBAC) kapcsolatos hibaelhárítás.
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
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223296"
---
# <a name="troubleshoot-rbac-in-azure"></a>Az Azure-beli RBAC hibaelhárítása

Ez a cikk szerepköralapú hozzáférés-vezérlést (RBAC), kapcsolatos általános kérdéseket válaszol meg, hogy tudja, mire számítson, ha használja a szerepkörök az Azure portal és a hozzáférési problémák elhárítása.

## <a name="web-app-features-that-require-write-access"></a>Webes alkalmazás írási hozzáférést igénylő szolgáltatások

Egyetlen webalkalmazásban; a felhasználó csak olvasható hozzáférést ad, ha néhány funkció le van tiltva, hogy nem várt. A következő felügyeleti képességeket igényelnek **írási** nem érhetők el az összes csak olvasható forgatókönyv és a egy web app (tulajdonos vagy közreműködő) hozzáférést.

* Parancsok (például Indítás, Leállítás, stb.)
* Beállítások módosítása, például általános konfigurációs, a skálázási beállítás, a biztonsági mentési beállítások és a figyelési beállítások
* Közzétételi hitelesítő adatok és egyéb titkos adatait, például az alkalmazásbeállítások és a kapcsolati karakterláncok elérése
* Folyamatos átviteli naplók
* Diagnosztikai naplók konfigurálása
* Konzol (parancssor)
* Aktív és a legutóbbi központi telepítéseket (helyi git folyamatos üzembe helyezés)
* Becsült költés
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
* Elosztott terhelésű készlethez  
* Riasztási szabályok  

Ha ezek a csempék nem sikerül elérnie, kérje meg a rendszergazdát a közreműködői hozzáférés az erőforráscsoporthoz.

## <a name="azure-functions-and-write-access"></a>Az Azure Functions és az írási hozzáférés

Az egyes funkciói [Azure Functions](../azure-functions/functions-overview.md) írási jogosultság szükséges. Például ha egy felhasználó az Olvasó szerepkör van hozzárendelve, nem tudja megtekinteni a függvényalkalmazás-függvényei. A portál megjeleníti **(nincs hozzáférés)**.

![Alkalmazások függvény nincs hozzáférés](./media/troubleshooting/functionapps-noaccess.png)

Kattintson egy olvasót a **platformfunkciók** fülre, majd **minden beállítás** bizonyos beállítások megtekintéséhez (a webalkalmazás hasonlóan) függvényalkalmazás kapcsolódó, de nem módosíthatják a beállítások.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC módosításait nem észleli a folyamatban

Az Azure Resource Manager néha gyorsítótárazza a konfigurációkat és a teljesítmény javítása az adatokat. Létrehozásakor, vagy törlése a szerepkör-hozzárendeléseit, a módosítások érvénybe léptetéséhez akár 30 percet is igénybe vehet. Az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatakor kényszerítheti a szerepkör-hozzárendelési módosítások frissítését kijelentkezés és bejelentkezés. A REST API-hívások szerepkör-hozzárendelés módosításokat végez, ha egy frissítés kényszerítheti a hozzáférési jogkivonat frissítésével.

## <a name="next-steps"></a>További lépések
* [Hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
* [Az RBAC-módosítások Tevékenységnaplók megtekintése](change-history-report.md)

