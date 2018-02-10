---
title: "Szerepköralapú hozzáférés-vezérlést Azure RBAC hibaelhárítása |} Microsoft Docs"
description: "Segítség problémák vagy a szerepköralapú hozzáférés-vezérlés erőforrások kapcsolatos kérdésekre."
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: seohack1
ms.openlocfilehash: c2589aabce86f848fa1aa3e25b3f78be180c5525
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="troubleshooting-azure-role-based-access-control"></a>Hibaelhárítási Azure szerepköralapú hozzáférés-vezérlés 

A dokumentum cikk a szerepköröket, meghatározott hozzáférési jogosultságai kapcsolatos gyakori kérdésekre ad választ, megállapításához, hogy mi történik, ha használja a szerepkörök az Azure portál és a részleg-hozzáférési problémák megoldása. Ezek a szerepkörök az összes erőforrástípus terjed ki:

* Tulajdonos  
* Közreműködő  
* Olvasó  

Tulajdonos és közreműködő szerepkörrel rendelkező személyek mindkét megoldást vezet a teljes hozzáféréssel rendelkeznek, de a közreműködői nem hozzáférést más felhasználóknak vagy csoportoknak. Részek lesznek még ennél is érdekesebb megoldást az olvasó szerepkört, hogy az adott azt fogja szánjon némi időt. Tekintse meg a [szerepköralapú hozzáférés-vezérlés a get-started cikk](role-based-access-control-configure.md) talál részletes hozzáférést.

## <a name="app-service-workloads"></a>App service munkaterhelések
### <a name="write-access-capabilities"></a>Írási képességek
Ha megadta a felhasználói csak olvasható hozzáférést egyetlen webalkalmazáshoz, néhány funkció le vannak tiltva, hogy nem várt. A következő felügyeleti képességeket szükséges **írási** egy webalkalmazást (tulajdonos vagy közreműködő) által elérhető, és nem érhetők el a olyan írásvédett forgatókönyv.

* Parancsok (például a start, stop, stb.)
* Általános konfiguráció, a skálázási beállításokat, a biztonsági mentés beállításait és a figyelési beállítások például beállítások módosítása
* Közzétételi hitelesítő adatokat és más titkos adatokat, például az alkalmazásbeállítások és kapcsolati karakterláncok használata
* Folyamatos átviteli naplók
* Diagnosztikai naplók konfiguráció
* Konzol (parancssor)
* Aktív és a legutóbbi központi telepítéseket (helyi git folyamatos üzembe helyezés)
* Becsült költés
* Webtesztek
* Virtuális hálózat (csak egy olvasó, ha egy virtuális hálózat már be lett állítva egy írási hozzáféréssel rendelkező felhasználó számára látható).

Ha sem tudja már használni ezen csempék, kérje a rendszergazdától, közreműködő eléréséhez a webalkalmazás szeretné.

### <a name="dealing-with-related-resources"></a>Kapcsolódó erőforrások kezelése
Webalkalmazások vannak bonyolítja néhány különböző erőforrások együttműködés jelenlétét. Íme néhány webhelyekkel tipikus erőforráscsoport:

![Webes alkalmazás erőforráscsoport](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Ennek eredményeképpen ha biztosít valaki csak a web app, nagy részét a funkciói a webhely paneljén az Azure-portálon való hozzáférés le van tiltva.

Ezek az elemek szükség **írási** a hozzáférést a **App Service-csomag** , amely megfelel a webhely:  

* A webes alkalmazás megtekintése a tarifacsomag (ingyenes vagy normál)  
* Skála configuration (a példányok, a virtuális gép méretét, az automatikus skálázási beállítások száma)  
* Kvóták (tároló, a sávszélesség, a CPU)  

Ezek az elemek szükség **írási** a teljes hozzáférés **erőforráscsoport** , amely tartalmazza a webhely:  

* SSL-tanúsítványokat és a kötések (SSL-tanúsítványok megoszthatók ugyanabban az erőforráscsoportban a helyek és a földrajzi helyhez között)  
* Riasztási szabályok  
* automatikus skálázási beállításokat  
* Application Insights-összetevők  
* Webtesztek  

## <a name="virtual-machine-workloads"></a>Virtuális gépek terheléséhez
Sokkal hasonlóan a web apps, a virtuális gépek paneljét bizonyos funkcióinak írási hozzáférés szükséges a virtuális gépet, vagy további erőforrások az erőforráscsoportban.

Virtuális gépek tartomány nevét, virtuális hálózatok, storage-fiókok és a riasztási szabályok kapcsolódnak.

Ezek az elemek szükség **írási** a hozzáférést a **virtuális gép**:

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Szükséges **írási** is a hozzáférést a **virtuális gép**, és a **erőforráscsoport** (valamint a tartomány nevét), hogy az informatikai van:  

* Rendelkezésre állási csoport  
* Elosztott terhelésű készlethez  
* Riasztási szabályok  

Ha sem tudja már használni ezen csempék, kérje meg a rendszergazdát az erőforráscsoport közreműködői elérésére.

## <a name="see-more"></a>Részletek
* [Szerepköralapú hozzáférés-vezérlés](role-based-access-control-configure.md): az RBAC első lépései az Azure portálon.
* [Beépített szerepkörök](role-based-access-built-in-roles.md): részletes információkat szolgáltatva a szerepköröket, az RBAC szabványos tartalmazza.
* [Egyéni szerepkörök az Azure RBAC](role-based-access-control-custom-roles.md): megtudhatja, hogyan hozzon létre egyéni szerepkörök az access igényeihez.
* [Access módosítási előzményeit jelentés létrehozása](role-based-access-control-access-change-history-report.md): nyomon követjük, hogy az RBAC más szerepkörök hozzárendeléséről.

