---
title: "Az Azure Active Directory koncepció alkalmazástervezési bemutatása |} Microsoft Docs"
description: "Vizsgálatát, és gyorsan végrehajthatja az identitás- és kezelési helyzetek"
services: active-directory
keywords: "az Azure active directory, a forgatókönyv, a koncepció igazolása, PoC"
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Az Azure Active Directory igazolása koncepció forgatókönyv: bemutatása

Ez a cikk útmutatásokat felfedezése, mely másik Azure AD-lehetőségek a egy a koncepció igazolása (PoC). A jelen dokumentum a célközönség identitás mérnökök, informatikai szakemberek és rendszerintegrátorok

## <a name="how-to-use-this-playbook"></a>Ez a forgatókönyv használata

1. Használja a [téma](active-directory-playbook-ingredients.md#theme) szakaszt, és válassza ki az egyik fontos igényei szerint megfelelőnek.  
2. A koncepció hatókör megfelel-e az üzleti céljaihoz forgatókönyvek kiválasztásával. Minél rövidebb minél pontosabban. Javasolt azt rövid és pontos sikerkritériumokkal biztosíthatja a lehető átadja értékét az érdekelt felek, ugyanakkor minimalizálja a erősségét róla.  
3. Használja a [megvalósítási](active-directory-playbook-implementation.md) szakaszt, a forgatókönyveket, és mi azok jelentené alkalmaz környezetében. Minden esetben azt mutatják be, hogyan állítsa be (úgynevezett [építőelemeket](active-directory-playbook-building-blocks.md)), és keresse meg a forgatókönyveket. 
4. Minden egyes építőelem ismerteti a szükséges előfeltételt, valamint egy hozzávetőleges időt igényel. Ez segítséget nyújthat a tervezési folyamat során. 
5. Adja meg az 1-3 alapján, a [környezet](active-directory-playbook-ingredients.md#environment) használandó hajtható végre. Ezért törekedni éles környezetbe történő beszerzéséhez a felhasználói élmény, a helyes működését a felhasználók javasoljuk. 
6. Ha ütköző követelményeket támasztó, használja a hasznos kompromisszumot mátrix 
   * Az érték téma-központú megjelenítése  
   * Simaság előkészítése, beállításához és a forgatókönyv végrehajtásához 
   * A cél forgatókönyvek végrehajtási minimális időtartama 
   * Mivel hamarosan üzemi környezetben, a korlátozások megvalósítható 

>[!NOTE]
> Ez a cikk teljes jelenik meg néhány adott harmadik féltől származó alkalmazások és a termékek kényelmi célokat szolgál példaként említett. Az Azure AD-alkalmazások ezer támogatja a [alkalmazáskatalógusában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) használható az Ön igényeinek és a környezet alapján. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]