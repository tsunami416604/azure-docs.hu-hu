---
title: "Munkafolyamatok létrehozása sablonból - Azure Logic Apps |} Microsoft Docs"
description: "Első lépések - sablonokkal hozhatók létre munkafolyamatok gyorsan Azure Logic Apps alkalmazások és adatok integrálására."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89272869f7dfaa34cbd2ad32d67dca0955e6158b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-workflow-using-a-pre-built-template-or-pattern-to-get-started-quickly"></a>A munkafolyamat egy előre elkészített sablon vagy a minta segítségével gyorsan konfigurálása

## <a name="what-are-logic-app-templates"></a>Mik azok a logic app sablonok
A logic app-sablon egy előre létrehozott logikai alkalmazás, amely segítségével gyorsan megismerkedhet a saját munkafolyamat létrehozása. 

Egy jó módszer különböző mintákat, amelyek használatával logic apps építhetők felderítésére. Használhatja ezeket a sablonokat,-, vagy módosítsa őket a forgatókönyvnek megfelelően.

## <a name="overview-of-available-templates"></a>Az elérhető sablonok – áttekintés
Nincsenek közzétett a logic app platform számos elérhető sablonok. Néhány példa kategóriák, valamint azokat, összekötői típusú alább láthatók.

### <a name="enterprise-cloud-templates"></a>Vállalati felhő sablonok
Sablonok, amelyek integrálják a Dynamics CRM-hez, a Salesforce, a mezőbe, a Azure Blob és a többi összekötőt, a vállalati felhő igényeinek. Néhány példa arra, mi ezekkel végezhető-sablonjai tartalmazzák a érdeklődők rendszerezése és a vállalati fájladatok biztonsági mentésekor.

### <a name="enterprise-integration-pack-templates"></a>Vállalati integrációs felügyeleticsomag-sablonok
VETER beállításait (ellenőrzése, kinyerési, átalakítási, kiegészítése, útvonal-) folyamatok, egy X12 EDI keresztül AS2-dokumentum, és átalakítása a XML-, valamint X12 és AS2 üzenet kezelési fogadásakor.

### <a name="protocol-pattern-templates"></a>Protokoll mintát sablonok
Ezeket a sablonokat a logic apps tartalmazó protokoll kombinációját, például a kérés-válasz HTTP, valamint a Integrációk keresztül FTP és az SFTP áll. A ezek léteznek vagy alapjaként protokoll összetettebb létrehozásához.  

### <a name="personal-productivity-templates"></a>Egyéni hatékonyságnövelő sablonok
Minták egyéni hatékonyságnövelő javítása érdekében például sablonokat, napi Emlékeztető beállítása, fontos munkaelemek kapcsolja be a feladatlisták, és le egy egyetlen felhasználói jóváhagyás lépés hosszadalmas feladatok automatizálásához.

### <a name="consumer-cloud-templates"></a>Fogyasztó felhő sablonok
Egyszerű sablonokat, amelyekbe beépül az közösségi szolgáltatások, például a Twitter, Slackhez és e-mailek, végső soron képes a közösségi média kezdeményezések marketing megerősítése. Ide tartoznak például zavaros másolja, sablonok, amelyek segíthetnek a termelékenység növelése által hagyományosan ismétlődő feladatok mentik a fordított időt is. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Egy sablon használatával logikai alkalmazás létrehozása
Első lépésként a logic app sablon segítségével, nyissa meg a logic app Tervező be. Ha egy meglévő logikai alkalmazás megnyitásával adta meg a Tervező, a logikai alkalmazás automatikusan betölti a Tervező nézetben. Azonban ha egy új logikai alkalmazást hoz létre, megjelenik az alábbi képernyőn.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

Ezen a képernyőn megadhatja egy üres logikai alkalmazást vagy egy előre elkészített sablon elindítása. Ha a sablonok valamelyikét választja, további információkkal szolgálnak. A jelen példában használjuk a *Dropbox egy új fájl jön létre, amikor másolásához OneDrive* sablont.  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Ha a sablon használatát választja, csak, jelölje be a *ezzel a sablonnal* gombra. Kéri bejelentkezni a partnereket a mely összekötőket a sablont használja. Vagy, ha korábban már létrehozta a kapcsolatot az összekötők, válassza alább látható módon folytatódik.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

A kapcsolat létrehozása és kijelölése után *továbbra is*, megnyílik a logikai alkalmazás Tervező nézetben.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

A fenti példában mint sok sablonok esetében néhány kötelező tulajdonság mező lehet, hogy tölti ki az összekötők; belül azonban néhány továbbra is szükség lehet egy érték előtt tudni megfelelően telepíteni a logikai alkalmazást. Ha megpróbálja telepíteni néhányat a hiányzó mezőket megadása nélkül, értesítést is, egy hibaüzenet.

Ha szeretne térni az sablon használatával, jelölje be a *sablonok* a felső navigációs sáv gombjára. Váltás a sablon megjelenítő, elvesznek a nem mentett végrehajtási. Váltás sablon viewer programba, mielőtt megjelenik egy figyelmeztető üzenet, amely értesíti erről.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Egy sablonból létrehozott logikai alkalmazás központi telepítése
Amennyiben az a sablon betöltése, és a kívánt módosításokat végzett, válassza a Mentés gombra a bal felső sarokban. Ez menti, és teszi közzé a Logic Apps alkalmazást.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Ha szeretne további tájékoztatást hozzáadása egy meglévő logic app-sablon a további lépéseket, vagy a Szerkesztés általában, további információ: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

