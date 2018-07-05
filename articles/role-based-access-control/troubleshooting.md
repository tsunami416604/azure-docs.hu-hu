---
title: Hibaelhárítás az Azure-beli RBAC |} A Microsoft Docs
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
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 186bcf26639f5cff2dcbf1e805913ac7edab7df4
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437366"
---
# <a name="troubleshooting-rbac-in-azure"></a>Az Azure-beli RBAC hibaelhárítása

Ez a cikk szerepköralapú hozzáférés-vezérlést (RBAC), kapcsolatos általános kérdéseket válaszol meg, hogy tudja, mire számítson, ha használja a szerepkörök az Azure portal és a hozzáférési problémák elhárítása. Ezek a szerepkörök minden erőforrástípus terjed ki:

* Tulajdonos  
* Közreműködő  
* Olvasó  

A felügyeleti feladatok tulajdonosai és a közreműködők is teljes hozzáférése, de közreműködői nem tud hozzáférést adni más felhasználóknak vagy csoportoknak. Dolgok beolvasása egy kicsit több érdekes, az Olvasó szerepkörhöz, hogy az, ahol azt fogjuk szánjon némi időt. További információ hozzáférést biztosítani, seee [rbac-RÓL és az Azure portal-hozzáférés kezelése](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Írási hozzáférés képességek
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

### <a name="dealing-with-related-resources"></a>Kapcsolódó erőforrások kezelése
Web apps néhány más erőforrások, amelyek kölcsönhatások jelen vannak bonyolult. Íme néhány webhelyek egy tipikus erőforráscsoportot:

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

## <a name="azure-functions"></a>Azure Functions
Az egyes funkciói [Azure Functions](../azure-functions/functions-overview.md) írási jogosultság szükséges. Például ha egy felhasználó az Olvasó szerepkör van hozzárendelve, nem tudja megtekinteni a függvényalkalmazás-függvényei. A portál megjeleníti **(nincs hozzáférés)**.

![Alkalmazások függvény nincs hozzáférés](./media/troubleshooting/functionapps-noaccess.png)

Kattintson egy olvasót a **platformfunkciók** fülre, majd **minden beállítás** bizonyos beállítások megtekintéséhez (a webalkalmazás hasonlóan) függvényalkalmazás kapcsolódó, de nem módosíthatják a beállítások.

## <a name="virtual-machine"></a>Virtuális gép
Sokkal például a web apps, a virtuális gép paneljén egyes funkcióinak írási jogosultság szükséges a virtuális gépet, vagy más az erőforráscsoportban lévő erőforrásokat.

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

## <a name="next-steps"></a>További lépések
* [Rbac-RÓL és az Azure portal-hozzáférés kezelése](role-assignments-portal.md)
* [Az RBAC-módosítások Tevékenységnaplók megtekintése](change-history-report.md)

